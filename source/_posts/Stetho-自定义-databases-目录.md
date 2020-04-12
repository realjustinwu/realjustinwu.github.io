---
title: Stetho 自定义 databases 目录
date: 2017-03-17 20:33:47
tags: [Stetho, 修改默认databases目录]
categories: Android
blog: true
---



以前做小项目，特别懒，数据存储直接放在 SharePreferences 中，开发的时候，使用 Stetho 直接在 Chrome 中修改存储的数据。现在的项目就不一样了，数据都存在数据库中。

今天想修改一下数据，测试一下显示效果，结果傻眼了。公司项目里没有加入 Stetho 库，使用其他方式需要破解手机，太麻烦了。然后就在公司项目中加入了 Stetho，然后踩了一天的坑。

加入 Stetho 之后，在 Chrome 中不显示数据库，网上搜了一下，有说更新 Stetho 之后不显示的，以为是 Stetho 版本问题，1.4.2; 1.4.0; 1.3.1 都试了，还是没效果。。

然后自己写一个 test 数据库放在 databases 目录下，居然显示了。再把我们项目的数据库从 files 目录下拷贝到 databases 目录中，也显示了。。

Stetho 默认只读取内部存储中 databases 中的数据库，然而我们的项目把数据库文件放在 files 文件夹下。网上没有找到如何配置 databases 的方法。于是乎下载了 Stetho 源代码。

<!-- more -->

目的是修改默认 databases 目录，有目标之后就简单了。全局找 database，找到 DatabaesFileProvider 类，这个类包含 getDatabaseFiles() 方法，基本可以肯定是需要找的类了。再找这个类的使用，可以找到 DefaultDatabaseFilesProvider，其 getDatabseFiles() 的实现是，读取内部存储的 databases 文件夹下的所有文件。

替换这个类，应该就能达到自己的目标了。然后查找这个类的使用。有两个地方创建这个对象，一个在 SqliteDatabaseDriver 构造方法中，一个在 Stetho 类中。Stetho 中的当 hasSqliteDatabaseDriver 为 false 才创建 DefaultDatabaseFilesProvider 对象。先替换 SqliteDatabaseDriver 中的 DefaultDatabaseFilesProvider 吧，再全局查找一下。发现只有 javadoc 中有 new SqliteDatabaseDriver 对象。如下：

```java
    /**
     * Customize the location of database files that Stetho will propogate in the UI.  Android's
     * {@link Context#getDatabasePath} method will be used by default if not overridden here.
     *
     * <p>This method is deprecated and instead it is recommended that you explicitly
     * configure the {@link SqliteDatabaseDriver} as with:</p>
     * <pre>
     *   provideDatabaseDriver(
     *     new SqliteDatabaseDriver(
     *       context,
     *       new MyDatabaseFilesProvider(...),
     *       new DefaultDatabaseConnectionProvider(...)))
     * </pre>
     *
     * @deprecated See example alternative above.
     */
    @Deprecated
    public DefaultInspectorModulesBuilder databaseFiles(DatabaseFilesProvider provider) {
      mDatabaseFilesProvider = provider;
      return this;
    }
```

javadoc 中写的很清楚了。默认使用 Android 默认的数据库目录，如果要更改默认路径可以使用这个方法实现。最终修改如下：

```java
SqliteDatabaseDriver sqliteDriver = new SqliteDatabaseDriver(
            getContextRC(),
            new DatabaseFilesProvider() {
                @Override
                public List<File> getDatabaseFiles() {
                    List<File> files = new ArrayList<File>();
                    files.add(new File(getContextRC().getFilesDir() + "/user/abc.db")); // files/user/abc.db
                    files.add(new File(getContextRC().getFilesDir() + "/settings.db")); // files/settings.db
                    return files;
                }
            },
            new DefaultDatabaseConnectionProvider());
Stetho.initialize(Stetho.newInitializerBuilder(this)
        .enableDumpapp(Stetho.defaultDumperPluginsProvider(this))
        .enableWebKitInspector(new InspectorModulesProvider() {
            @Override
            public Iterable<ChromeDevtoolsDomain> get() {
                return new Stetho.DefaultInspectorModulesBuilder(getContextRC())
                        .provideDatabaseDriver(sqliteDriver)
                        .finish();
            }
        })
        .build());
```

就可以使用 Chrome 浏览数据库了。



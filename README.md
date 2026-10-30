# 心意 DDD 脚手架（xinyi-frame）

维护者：心意（xinyi）。项目坐标：`com.xinyi:xinyi-frame:1.0-SNAPSHOT`，基础包名：`com.xinyi`。

## 目录说明

```text
├── pom.xml                       父工程、依赖版本和模块管理
├── archetype.properties          生成模板时排除本地配置与构建产物
├── xinyi-frame-app               启动入口、装配配置、应用配置、测试
├── xinyi-frame-trigger           HTTP 接口、消息监听、定时任务
├── xinyi-frame-domain            领域模型、领域服务、仓储接口
├── xinyi-frame-infrastructure    仓储实现、数据库访问、外部服务适配
├── xinyi-frame-types             公共类型、常量、枚举、异常
└── docs/dev-ops                  SQL、Docker 和部署脚本
```

各模块 Java 源码位于 `src/main/java/com/xinyi`；测试位于 `src/test/java/com/xinyi`。
领域中的 `xxx`、`yyy` 是占位目录，开发具体业务时改为实际领域名称。

## 构建

在根目录执行（PowerShell）：

```powershell
mvn -B -ntp -DskipTests package
```

应用产物为 `xinyi-frame-app/target/xinyi-frame-app.jar`。
保持 Java 8 编译目标；Lombok 使用 1.18.30，支持本机 JDK 21 编译。
原有 POM 默认跳过测试；打包成功不代表数据库连接或应用启动已经验证。

启动前修改 `xinyi-frame-app/src/main/resources/application-dev.yml` 中的数据库连接；
数据库名为 `xinyi_frame`，初始化脚本为 `docs/dev-ops/mysql/sql/xinyi-frame.sql`。
环境 Compose 映射 MySQL 到宿主机 `13306`，使用该环境时须同步修改 JDBC 端口。
MyBatis Mapper 仍是示例占位文件，启用 Mapper 扫描前需要实现对应 DAO、PO 和 SQL。

## 制作并安装自己的 Maven 脚手架

在本项目根目录生成模板：

```powershell
mvn -B -ntp -s .mvn/archetype-settings.xml org.apache.maven.plugins:maven-archetype-plugin:3.2.0:create-from-project "-DpackageName=com.xinyi" "-Darchetype.properties=archetype.properties"
```

命令显式指定项目内的空用户设置文件，避免插件因本机缺少 `~/.m2/settings.xml` 而失败；
若你已有自定义镜像、仓库或认证配置，请把 `-s` 改为自己的设置文件路径。
生成的模板工程位于 `target/generated-sources/archetype`。将它安装到本机 Maven 仓库：

```powershell
mvn -B -ntp -f target/generated-sources/archetype/pom.xml install
```

脚手架坐标为 `com.xinyi:xinyi-frame-archetype:1.0-SNAPSHOT`。
修改源项目后重新执行上述两步即可更新本机模板。

## 使用脚手架创建新项目

切换到用于存放新项目的目录（不要在当前父工程内部执行），例如 `D:/Data/project/java`：

```powershell
mvn -B -ntp org.apache.maven.plugins:maven-archetype-plugin:3.2.0:generate "-DarchetypeGroupId=com.xinyi" "-DarchetypeArtifactId=xinyi-frame-archetype" "-DarchetypeVersion=1.0-SNAPSHOT" "-DgroupId=com.xinyi" "-DartifactId=my-project" "-Dversion=1.0-SNAPSHOT" "-Dpackage=com.xinyi.myproject" "-DinteractiveMode=false"
```

`artifactId` 是新项目名称，`groupId` 是 Maven 组织标识，`package` 是 Java 基础包名。
Maven 将创建 `my-project` 及其子模块，并替换 Java 包声明和关联引用。
数据库名称、Docker 镜像名、部署脚本等业务配置，在生成后按实际项目检查调整。
使用 IDEA 打开新项目的根 `pom.xml` 即可导入。

脚手架用于生成项目，不需要把它作为业务项目的 `<dependency>` 引入。
本机安装仅供本机使用；其他电脑使用时需要安装同一模板，或另行配置并发布到自己的 Maven 仓库。

## Docker 镜像

完成 Maven 打包后，进入 `xinyi-frame-app` 执行 `sh build.sh`。
构建脚本、Compose 和启动脚本统一使用 `xinyi/xinyi-frame-app:1.0-SNAPSHOT`。
`xinyi` 是当前使用的镜像命名空间，实际推送时请改为你拥有的仓库地址。

## 维护说明

源码注释维护者使用“心意（xinyi）”。保留了原有许可证声明和外部教程链接。
若上游另有 LICENSE、NOTICE 或版权声明，分发时也应保留。
当前工作区外层文件夹名称不影响 Maven 坐标；需要时关闭 IDE 后再手动重命名。
修改模块目录后，在 IDEA 中重新加载根 Maven 工程以刷新模块列表。

参考：[Maven 官方脚手架生成说明](https://maven.apache.org/archetype/maven-archetype-plugin/create-from-project-mojo.html)、[Lombok 版本说明](https://projectlombok.org/changelog)。


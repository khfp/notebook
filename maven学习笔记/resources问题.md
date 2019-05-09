# maven的resources文件夹问题

## 1. /src/main/resources

**问题描述：**
在用vscode的maven插件创建maven项目时，缺少/src/main/resources文件夹。

手动创建/src/main/sources文件夹，并在pom.xml文件中添加：

    <resources>
      <resource>
        <directory>${project.basedir}/src/main/resources</directory>
      </resource>
    </resources>

或者在IDEA中File->project struction设置中创建并指定resources文件夹。
**问题遗留：**
编译后/src/main/resources文件中的资源文件直接生成到了/target/classes中，而不是/target/classes/resources文件夹中。
**ps:**
搞了两天勉强弄好。。。。就当做是熟悉maven和java的基本内容了吧，没人带真鸡儿累。

2019/5/9 16:12

# Okcheck

差量扫描，自动集成Lint、KtLint、Checkstyle、Findbugs、Pmd 5大互补静态扫描工具，灵活配置

## 基本差量扫描

- 基于Git仓库，对比最近一次扫描成功的记录commit id => 扫描差量的module
- 基于本地代码, 对比最近以此扫描成功的缓存 => 已经处理的任务直接`up-to-data`

## 如何引入

在根项目的`build.gradle`中配置:

```groovy
buildscript {
    dependencies {
        classpath 'com.liulishuo.okcheck:gradle:0.1.2'
    }
}

allprojects {
    apply plugin: 'okcheck'
}
```

至此，就已经完全整合，并且采用我们定制的统一规则生效5大静态扫描工具，以及可以通过`./gradlew okcheck`差分静态扫描，并且默认所有报告会整合到根项目的`build/reports`目录下，方便统一导出。


## 任务说明

当进行`okcheck`任务的时候，会对所有的`variant`进行编译与扫描（如有一般都有`debug`与 `release`两个编译类型导致，存在至少两个`variant`)，因此通常来说我们只需要对某一个`variant`进行编译扫描即可: 如`okcheckDebug`。

- `./gradlew okcheckDebug`: 执行差量的扫描
- `./gradlew cleanOkcheckDiff`: 清除所有缓存的差量数据，下次会全量扫描
- `./gradlew -PignoreOkcheckDiff okcheck`: 忽略差量数据进行全量的扫描
- `./gradlew -PignoreOkCheckDiff :module1:okcheck`: 忽略差量数据进行`module1`模块的扫描

## 如何配置

为了方便说明下面所有提到的，都采用默认值(当没有提供时所采用的默认值)作为案例，以下是在根项目的`build.gradle`中配置:

```
allprojects {
    exclude = ['**/proto/*.java']
    destination = project.rootProject.buildDir
    okcheck {
        unittest {
            enabled = true
            exclude = ['**/proto/*.java']
        }
        lint {
            enabled = true
            exclude = ['**/proto/*.java']

        }
        ktlint {
            enabled = true
            exclude = ['**/proto/*.java']
        }
        checkstyle {
            enabled = true
            exclude = ['**/proto/*.java']
            // 采用默认的统一配置
            configFile = null
        }
        findbugs {
            enabled = true
            exclude = ['**/proto/*.java']

            effort = "default"

            // Warning级别的错误默认会终止扫描
            ignoreFailures = false
            // 采用默认的统一配置
            excludeBugFilter = null
        }
        pmd {
            enabled = true
            exclude = ['**/proto/*.java']

            // Warning级别的错误默认会终止扫描
            ignoreFailures = false
            // 采用默认的统一配置
            ruleSetFiles = null
        }
    }
}
```


## 其他

- 首次执行`okcheck`任务会进行全量扫描

#### 已经忽略包

在`checkstyle`,`findbugs`,`pmd`中忽略了以下路径的扫描:

```
**/gen/**
**/test/**
**/proto/*.java
**/protobuf/*.java
**/com/google/**/*.java
```

## LICENSE

```
Copyright (c) 2018 LingoChamp Inc.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

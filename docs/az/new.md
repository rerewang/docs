# 新人

## 安装常用的软件
### PHPstorm
设置以下配置、安装以下插件，让PHPstorm更好用
- 相关配置
- 插件

### sourcetree
熟悉下sourcetree上的checkout、创建分支、提交、diff、合并、push/pull等操作。

why sourcetree，为什么不直接用命令行呢?
主要在于提交和合并操作时，使用命令行不方便清楚的看到代码究竟做了哪些修改，所以我们用sourcetree养成一个提交前diff的习惯。
*ps:如果你是git命令大神, 你清楚你的每一个操作会对代码仓库产生什么样的影响，直接用命令行吧*

### postman
用于模拟请求，方便api调试。
替代软件: postwoman等

### datagrip
一个mysql客户端
替代软件：navicat等

### xshell
终端模拟软件，window系统装一下，方便使用命令行


## 第一个需求
### 创建功能分支
- 基础开发分支为:develop
- 每个需求创建一个功能分支

#### 功能分支命名规则：
- 格式说明：`feature/my_git_username/123_xxx`
- `my_git_username` 为开发人员的`git`帐号
- `123` 为jira需求编号(例：jira编号为AZWEB-123，只写123即可)
- `xxx`为对本次需求的简单描述

#### 代码提交规范:
- 格式说明：`#123:message`
- `123` 为jira编号(例：jira编号为AZWEB-123，只写123即可，若需求AZWEB-123提测后产生了bug AZWEB-124，则写bug的编号 124)
- `message` 为对本次提交的简单描述

#### 注意
- 不要随意合并无关分支进入功能分支，保证一个分支内代码是干净的(即与develop分支比对，只包含对本次需求的改动)

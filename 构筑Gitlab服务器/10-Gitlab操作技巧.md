# Gitlab 操作技巧

## 1. 项目添加成员

1. 进入相应的项目仓库中，依次点击 `Settings ` →  `Members`。
2. 进入 `Project members` 页面后，在 `Invite member` 中，填写 需要添加的成员（邮件地址）、赋予该成员的权限、该成员的访问过期日期（该选项可填可不填），最后点击 `Invite` 即可。  
   其中，在 `Choose a role permission` 一栏中，是指定被邀请成员的权限。  
   Gitlab用户在组中一般有五种权限：Guest、Reporter、Developer、Maintainer、Owner。  
   1）Guest：可以创建issue、发表评论，但不能读写版本库；  
   2）Reporter：可以克隆代码，但不能提交，QA、PM可以赋予这个权限；  
   3）Developer：可以克隆代码、开发、提交、push，RD可以赋予这个权限；  
   4）Maintainer：可以创建项目、添加tag、保护分支、添加项目成员、编辑项目，核心RD负责人可以赋予这个权限；  
   5）Owner：可以设置项目访问权限 - Visibility Level、删除项目、迁移项目、管理组成员，开发组leader可以赋予这个权限。  
   如果个人权限最高是 `Maintainer`，仅能显示前4种权限可选。
3. 在 `Existing members and groups` 中可以看到添加成员后的情况，这里还可以修改成员的权限、访问有效期或者移除成员。
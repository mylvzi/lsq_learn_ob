![[Pasted image 20251221093448.png]]
* 使用DEVOPS可以有效的将开发人员的快速迭代和运维人云的稳定追求
![[Pasted image 20251221095248.png]]
# 系统开发环境
![[Pasted image 20251221093604.png]]* 对于测试环境, 其实是有多套的(适配不同的终端, 除了测试环境, 预发布环境, 可能还有灰度环境  即让少量的用户来进行小范围的测试)



# GIT分支设计
>GIT FLow模型设计
![[Pasted image 20251221095330.png]]


### master分支
* master分支是**主分支**, 该分支**只读且唯一**, 用于部署到正式环境, 一般由合并release分支得到
* master分支不允许直接修改代码
* 产品的发布是在master分支上发布的, 所以,在master分支上提交的发布, 应该打标签
* master分支不可删除

### release分支
* release分支为**预发布分支**, 基于本次上线的所有`feature`分支合并到`devleop`分支之后, 基于develop分支创建, 部署到测试环境
* 命名以 release/ 开头，建议的命名规则: release/version_publishtime
* 如果在release分支测试出问题, 开发人需要再master分支进行回归验证
* release分支属于临时分支, 测试完成就可删除

### develop分支
* develop分支是**开发分支**, 基于master分支创建, 唯一且只读, 始终保持最新完成以及bug修复后的代码, 部署到开发环境

### feature分支
* feature分支通常为新功能/新特性开发的分支, 以develop分支为基础创建feature分支
*  feature 分支 feature/ 开头，建议的命名规则： feature/user_createtime_feature
* 新功能开发完毕, 需要开发人员将feature分支合并到develop分支

### hotfix分支
* hotfix分支为线上bug修复, 主要用于对线上的版本进行bug修复, 线上出现的问题一般都是比较紧急的问题, 需要直接基于master分支创建hotfix分支, 对bug进行修复


![[Pasted image 20251221100042.png]]

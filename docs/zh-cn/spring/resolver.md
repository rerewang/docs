# 解决方案

## 分布式系统中的session问题
常见的会话解决方案
- 粘性会话 Sticky Session
	- 利用loadBalance将会话分配至固定的机器，若机器下线，session会丢失
- 会话复制 Session Replication
	- 副本过大，且副本间可能不同步
- 集中会话 Centralized Session

### Spring Session
解决集群中的用户管理，无需绑定容器特定解决方案
支持的存储:
- Redis
- MongoDB
- JDBC
- Hazelcast

#### 实现原理
定制 HttpSession
- 通过定制的 HttpServletRequest 返回定制的 HttpSession
	- SessionRepositoryRequestWrapper
	- SessionRepositoryFilter
	- DelegatingFilterProxy
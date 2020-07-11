# spring AOP

## 相关概念
- Aspect  	切面
- Join Ponit 	连接点，spring AOP里总是代表一次方法执行
- Advice 	通知，在连接点执行的动作
- Pointcut 	切入点，说明如何匹配连接点
- Introduction 	引入，为现有类型声明额外的方法和属性
- Target object 	目标对象
- AOP proxy 	AOP代理对象，可以使JDK动态代理，也可以是CGLIB代理
- Weaving 	织入，连接切面与目标对象或类型创建代理的过程
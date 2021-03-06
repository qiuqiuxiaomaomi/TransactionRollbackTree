# TransactionRollbackTree
事务回滚技术研究

<pre>
    事务分类：
	       1）编码方式；
           2）声明式事务管理方式。

     声明式事务管理是通过AOP技术实现的，实质就是：在方法执行前后进行拦截，然后在目标方法
     开始之前创建并加入事务，执行完成目标方法后根据执行情况提交后回滚事务。

     声明式事务管理有两种方式：基于XML配置文件（拦截器 AOP），通过标签@Transaction注解
</pre>

###事务的传播属性
<pre>
PROPAGATION_REQUIRED
      当前方法必须开启事务，如果当前线程不存在事务，则开启新的事务，如果当前线程已经存在事
  务，就加入到当前事务。
      这个是经常使用的。但是要注意的就是一旦事务中的某一个方法回滚，当前事务上下文里所有的
  操作都回滚。

PROPAGATION_REQUIRES_NEW:
      当前方法必须要求开启新的事务，如果当前线程已经存在事务上下文，就暂停当前事务，等待新
  事务执行结束之后，再继续恢复之前的事务。

PROPAGATION_MANDATORY:
      当前方法必须要求事务，如果当前线程不存在事务，就抛出异常，如果存在，就加入事务里。

PROPAGATION_SUPPORTS:
      当前方法支持事务，如果当前线程存在事务，就加入到事务中去，如果不存在，不做任何操作。

PROPAGATION_NOT_SUPPORTED:
      当前方法不支持事务，如果当前线程存在事务，就挂起当前事务，执行完当前方法，恢复事务，
   一般情况下载查询的时候使用，并且非常耗时就可以使用Not Support,避免事务时间超长。

PROPAGATION_NEVER:
      当前方法不支持事务，如果当前线程存在事务，则抛出异常，这种用的很少。

PROPAGATION_NESTED：
      他与PROPAGATION_REQUIRES_NEW的区别是，PROPAGATION_REQUIRES_NEW另起一个事务，
   将会与他的父事务相互独立，
      而Nested的事务和他的父事务是相依的，他的提交是要等和他的父事务一块提交的。也就是
   说，如果父事务最后回滚，他也要回滚的
</pre>

<pre>
回滚类型：

      1）rollbackFor：一组异常类的实例，遇到时必须进行回滚
      2）rollbackForClassname：一组异常类的名字，遇到时必须进行回滚
      3）noRollbackFor：一组异常类的实例，遇到时必须不回滚
      5）noRollbackForClassname：一组异常类的名字，遇到时必须不回滚
</pre>

<pre>
Spring事务回滚策略
      spring的事务管理默认只对出现运行期异常(Java.lang.RuntimeException及其子类)进行回滚

      改变默认方式  
          在@Transaction注解中定义noRollbackFor和RollbackFor指定某种异常是否回滚。  
          @Transaction(noRollbackFor=RuntimeException.class)  
          @Transaction(RollbackFor=Exception.class)  
这样就改变了默认的事务处理方式。

例子：
    @Override
    @Transactional(propagation = Propagation.REQUIRED, isolation = Isolation.READ_COMMITTED, rollbackFor = Exception.class)
    public synchronized int addAuthToRoles(List<UcRoleAuth> list, List<Integer> ids) {
</pre>

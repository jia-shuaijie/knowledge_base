
```java
private final PlatformTransactionManager platformTransactionManager;  
private final TransactionDefinition transactionDefinition;

public class Test(){
	public void test() {  
	  
	    TransactionStatus transactionStatus = platformTransactionManager.getTransaction(transactionDefinition);  
	    try {  
		    // 业务代码
		    // 事务提交
			platformTransactionManager.commit(transactionStatus);
	    } catch (Exception e) {  
		    // 事务回滚
	        platformTransactionManager.rollback(transactionStatus);
	    }  
	}
}
```
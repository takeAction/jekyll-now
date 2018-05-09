---
layout : post
title : Spring Transaction Propagation
categories : Spring
---

Spring has following transaction propagation behaviors:

1. REQUIRED

   The same transaction will be used if there is an already opened transaction in the current bean method execution context. 
   If there is no existing transaction the Spring will create a new one.
   
   This means that if an inner method causes a transaction to rollback, the outer method will fail to commit 
   and will also rollback the transaction.
   
   ```Java
	 @Transactional(propagation=Propagation.REQUIRED)
	 public void test1(Teacher t) {
		
		       this.dao.testA(t);		
		       service2.updateStudent();
	 }   
	
	 @Transactional(propagation=Propagation.REQUIRES_REQUIRED)
	 public void updateStudent() {
		
		       throw new RuntimeException("Rollback!");
	 } 
   ```
   
   **Note : - The only exceptions that set a transaction to rollback state by default are the unchecked exceptions 
              (like RuntimeException). If you want checked exceptions to also set transactions to rollback 
              you must configure them to do so.
            - When using declarative transactions, ie by using only annotations, and calling methods from the same bean 
              directly (self-invocation), the @Transactional annotation will be ignored by the container. 
              If you want to enable transaction management in self-invocations you must configure the transactions 
              using aspectj**
              
2. REQUIRES_NEW

   Spring will create a new physical transaction. That is outter transaction will not be affected by inner transaction result.
   
   ```
   @Transactional(propagation=Propagation.REQUIRED)
	 public void test1(Teacher t) {
		
	     this.dao.testA(t);	
       try {   
		       service2.updateStudent();
       }catch(Exception e) {
           //handle exception
       }
	 }   
	
	 @Transactional(propagation=Propagation.REQUIRES_NEW)
	 public void updateStudent() {
		
	     throw new RuntimeException("Rollback!");
	 } 
   ```
   
   The outer transaction is paused when the inner transaction starts and then resumes after the inner transaction is finished.
   
   **Note : try-catch has to be added when calling inner transaction method, otherwise outter transaction still can be roll back **
   
3. NESTED

4. MANDATORY

   An existing opened transaction must already exist. If not an exception will be thrown.

5. NEVER

   An existing opened transaction must not already exist. If exists an exception will be thrown.

6. NOT_SUPPORTED

7. SUPPORTS

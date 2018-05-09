---
layout : post
title : Spring Transaction Propagation
categories : Spring
---

Spring has following transaction propagation behaviors:

1. REQUIRED - default behavior

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
   
   > The only exceptions that set a transaction to rollback state by default are the unchecked exceptions 
   > (like RuntimeException). If you want checked exceptions to also set transactions to rollback 
   > you must configure them to do so.
	      
   > When using declarative transactions, ie by using only annotations, and calling methods from the same bean 
   > directly (self-invocation), the @Transactional annotation will be ignored by the container. 
   > If you want to enable transaction management in self-invocations you must configure the transactions 
   > using aspectj
              
2. REQUIRES_NEW

   Spring will create a new physical transaction. That is outter transaction will not be affected by inner transaction result.
   
   ```Java
   @Transactional(propagation=Propagation.REQUIRED)
   public void test1(Teacher t) {
		
       this.dao.testA(t);	
       try {   
           service2.updateStudent();
       }catch(Exception e) {
           //handle exception
       }
       
   //    int i = 1/0;
   }   
	
    @Transactional(propagation=Propagation.REQUIRES_NEW)
    public void updateStudent() {
		
	throw new RuntimeException("Rollback!");
   } 
   ```
   
   The outer transaction is paused when the inner transaction starts and then resumes after the inner transaction is finished.
   
   If there is exception after executing `service2.updateStudent`, then `service2.updateStudent` can commit success while
   `this.dao.testA` will rolle back.
   
   **Note : try-catch has to be added when calling inner transaction method, otherwise outter transaction still can be roll back**
   
3. NESTED

   Execute within a nested transaction if a current transaction exists, otherwise create a new one.
   
   ```Java
   @Transactional(propagation=Propagation.REQUIRED)
   public void test1(Teacher t) {
		
       this.dao.testA(t);	
       try {   
           service2.updateStudent();
       }catch(Exception e) {
           //handle exception
       }
       
   //    int i = 1/0;
   }   
	
    @Transactional(propagation=Propagation.NESTED)
    public void updateStudent() {
		
	throw new RuntimeException("Rollback!");
   } 
   ```
   
   if there is exception in `service2.updateStudent()`, its behavior is the same as `REQUIRES_NEW`.
   
   However if there is exception after `service2.updateStudent()`(e.g. add `int i = 1/0` after `service2.updateStudent()`)
   both `this.dao.testA` and `service2.updateStudent()` roll back.
   
   > A savepoint will be taken at the start of the nested transaction. 
   > Íf the nested transaction fails, we will roll back to that savepoint. 
   > The nested transaction is part of of the outer transaction, so it will only be committed 
   > at the end of of the outer transaction.

4. MANDATORY

   Support a current transaction, throw an exception if none exists.

5. NEVER

   Execute non-transactionally, throw an exception if a transaction exists.

6. NOT_SUPPORTED

   Execute non-transactionally, suspend the current transaction if one exists.

7. SUPPORTS

   Support a current transaction, execute non-transactionally if none exists.

# Designing Data-intensive Application  
## Chapter 5. Replication  
- leaderless replication
- how gen UUID?
- what is quorum consistency?
- about Dynamo style system?
- what is anti-entropy?
- 复习ACID BASE
- dotted version vector & VECTOR CLOCKS?
- Byzantine faults

### think:  
数据的一致性已经无法光从server端保证，现在已经需要client端一起协作。

## Chapter 7. Transactions  
- ACID: Atomicity, Consistency, Isolation and Durability.  
Isolation: 事物之间不互相影响.不要踩到别人的脚.
- 各个数据库都宣称支持ACID，其实各家ACID的定义都有所不同，这点需要注意
- Systems that do not meet the ACID criteria are sometimes called BASE, which stands for Basically Available, Soft state and Eventual consistency.
Atomicity, isolation and durability are properties of the database, whereas consistency (in the ACID sense) is a property of the application. The application may rely on the database’s atomicity and isolation properties in order to achieve consistency, but it’s not up to the database alone.
- by using a transaction, the application can pretend that there are no crashes (atomicity), that nobody else is concurrently accessing the database (isolation), and that storage devices are perfectly reliable (durability). Even though crashes, race conditions and disk failures do occur, the transaction abstraction hides those problems so that the application doesn’t need to worry about them.  
- A key feature of a transaction is that in the case of a problem, it can be aborted and retried. ACID databases are based on this philosophy: if the database is in danger of violating its guarantee of atomicity, isolation or durability, it would rather abandon the transaction entirely than allow it to continue. 

### Single-object writes  
- Atomicity can be implemented using a log for crash recovery.
- Isolation can be implemented using a lock on each object (allowing only one thread to access an object at any one time).

### Weak isolation levels  
There are different levels of isolation in the world.
#### Read commited  
The most basic level of transaction isolation is read committed.  
Two guarantees: No dirty reads, No dirty writes.  
**Dirty read**: one transaction has written some data to the database, but has not yet committed or aborted. Can another transaction see that uncommitted data? If yes, that is called a dirty read.  
**Dirty write**: However, what happens if the earlier write is part of a transaction that has not yet committed, so the later write overwrites an uncommitted value? This is called a dirty write.  


## Chapter 9. Consistency and Consensus  
- THE IMPOSSIBILITY OF CONSENSUS: FLP--which proves that there is no algorithm which reliably achieves consensus if there is a risk that a node may crash.
- Most “NoSQL” distributed datastores do not support distributed transactions, but various clustered relational systems do.  
- 2PC过程完全可以比作西方婚礼  

### 2PC workflow  
1. When the application wants to begin a distributed transaction, it requests a transaction ID from the coordinator. This transaction ID is globally unique.
2. The application begins a single-node transaction on each of the participants, and attaches the globally unique transaction ID to the single-node transaction.
3. When the application is ready to commit, the coordinator sends a prepare request to all participants, tagged with the global transaction ID.
4. When a participant receives the prepare request, it makes sure that it can definitely commit the transaction in all circumstances.
5. When the coordinator has received responses to all prepare requests, it makes the definitive decision whether to commit or abort the transaction. The coordinator must write that decision to its transaction log on disk, so that it knows which way it decided in case it subsequently crashes. This is called the commit point.
6. Once the coordinator’s decision has been written to disk, the commit or abort request is sent to all participants. If the request fails or times out, the coordinator must retry forever until it succeeds. 

### Types of distributed transaction  

- Database-internal distributed transactions (replication and partitioning)  
- Heterogeneous(异构) distributed transactions (different DB, MQ and so on)  

### XA Transactions  
XA is a standard for implementing two-phase commit across heterogeneous technologies.  

### Holding locks while in doubt  
- orphaned in-doubt transactions do occur  

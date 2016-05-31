# Designing Data-intensive Application  
## Chapter 5. Replication  
- leaderless replication
- how gen UUID?
- what is quorum consistency?
- about Dynamo style system?
- what is anti-entropy?
- 复习ACID BASE
- dotted version vector & VECTOR CLOCKS?

### think:  
数据的一致性已经无法光从server端保证，现在已经需要client端一起协作。

## Chapter 7. Transactions  
- ACID: Atomicity, Consistency, Isolation and Durability.
- 各个数据库都宣称支持ACID，其实各家ACID的定义都有所不同，这点需要注意
- Systems that do not meet the ACID criteria are sometimes called BASE, which stands for Basically Available, Soft state and Eventual consistency.
Atomicity, isolation and durability are properties of the database, whereas consistency (in the ACID sense) is a property of the application. The application may rely on the database’s atomicity and isolation properties in order to achieve consistency, but it’s not up to the database alone.
- by using a transaction, the application can pretend that there are no crashes (atomicity), that nobody else is concurrently accessing the database (isolation), and that storage devices are perfectly reliable (durability). Even though crashes, race conditions and disk failures do occur, the transaction abstraction hides those problems so that the application doesn’t need to worry about them.

## Chapter 9. Consistency and Consensus  
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



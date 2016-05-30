#Designing Data-intensive Application
## Chapter 5. Replication
- leaderless replication
- how gen UUID?
- what is quorum consistency?
- about Dynamo style system?
- what is anti-entropy?
- 复习ACID
- dotted version vector & VECTOR CLOCKS?

###think:
数据的一致性已经无法光从server端保证，现在已经需要client端一起协作。

##Chapter 7. Transactions
- ACID: Atomicity, Consistency, Isolation and Durability.
- 各个数据库都宣称支持ACID，其实各家ACID的定义都有所不同，这点需要注意
- Systems that do not meet the ACID criteria are sometimes called BASE, which stands for Basically Available, Soft state and Eventual consistency.
Atomicity, isolation and durability are properties of the database, whereas consistency (in the ACID sense) is a property of the application. The application may rely on the database’s atomicity and isolation properties in order to achieve consistency, but it’s not up to the database alone.
- by using a transaction, the application can pretend that there are no crashes (atomicity), that nobody else is concurrently accessing the database (isolation), and that storage devices are perfectly reliable (durability). Even though crashes, race conditions and disk failures do occur, the transaction abstraction hides those problems so that the application doesn’t need to worry about them.

##Chapter 9. Consistency and Consensus

# **Intoduction to Amazon Aurora**
<br>

*Amazon Aurora*

Aurora is a fully managed, MySQL-compatible, relational database engine that combines the performance and reliability of high-end commercial databases with the simplicity and cost-effectiveness of open-source databases. It delivers up to five times the performance of MySQL without requiring changes to most of our existing applications that use MySQL databases.

*Amazon Elastic Compute Cloud (Amazon EC2)*

Amazon EC2 is a web service that provides resizable compute capacity in the cloud. It is designed to make web-scale cloud computing easier for developers. Amazon EC2 reduces the time required to provision new server instances to minutes, giving us the ability to quickly scale capacity, both up and down, as our computing requirements change.

*Amazon Relational Database Service (Amazon RDS)*

Amazon RDS makes it easy to set up, operate, and scale a relational database in the cloud. It provides cost-efficient and resizable capacity while managing time-consuming database administration tasks, freeing us up to focus on our applications and business. Amazon RDS provides us with six database engines to choose from, including Aurora, Oracle, Microsoft SQL Server, PostgreSQL, MySQL, and MariaDB.

We will introduce Amazon Aurora and have a basic understanding of how to use it

## **Objectives**
<br>

- Create an Aurora instance

- Connect to a pre-created Amazon Elastic Compute Cloud (Amazon EC2) instance

- Configure the Amazon EC2 instance to connect to Aurora

- Query the Aurora instance

## **Create an Aurora Instance**
<br>

1. At AWS Management Console, in the search bar, search for and choose RDS.

2. In the left navigation menu, choose **Databases**.

3. Choose **Create database** and then configure the following options:

    - For **Choose a database creation method**, choose **Standard create.**

    - For **Engine type**, choose **Aurora (MySQL Compatible)**.

    - For **Engine version**, choose the version specified as the **default for major version 8.0.**

    - For **Templates**, choose **Dev/Test**.

5. In the **Settings** section, configure the following options:

    - For **DB cluster identifier**, enter *aurora*.

    - For **Master username**, enter* admin*.

    - For **Master password**, enter *admin123*.

    - For **Confirm password**, enter *admin123*.

6. In the **Instance configuration** section for the **DB instance class** section, **choose Burstable classes (includes t classes)**, and choose **db.t3.medium** from the dropdown list.

7. In the **Availability & durability** section for **Multi-AZ deployment**, choose **Don't create an Aurora Replica**.

    **Note**:  Amazon RDS Multi-AZ deployments provide enhanced availability and durability for DB instances, making them a natural fit for production database workloads. When we provision a Multi-AZ DB instance, Amazon RDS automatically creates a primary DB instance and synchronously replicates the data to a standby instance in a different Availability Zone.

    Since this is a demo environment, we do not need to perform a multi-AZ deployment.

8. In the **Connectivity** section, configure the following options and leave any not mentioned with their default value:

    - For **Virtual private cloud (VPC)**, choose **LabVPC**.
    - For **Subnet group**, choose **dbsubnetgroup**.
    - For **Public access**, select **No**.
    - For **VPC security group**, select **Choose existing**.
    - For **Existing VPC security groups**, remove the **default** security group.
    - From the **Existing VPC security groups** dropdown list, choose **DBSecurityGroup**.

    **Note**: Subnets are segments of a virtual private cloud (VPC) IP address range that we designate to group our resources based on security and operational needs. A DB subnet group is a collection of subnets (typically private) that we create in a VPC and that we then designate for our DB instances. With a DB subnet group, we can specify a particular VPC when creating DB instances using the command line interface (CLI) or application programming interface (API); if we use the console, we can select the VPC and subnets that we want to use.

    The aurora subnet group was created for we when we launched the demo using AWS CloudFormation.

    **Consider**:  We can use the Amazon Virtual Private Cloud (Amazon VPC) service to launch AWS resources into a virtual network that we've defined. This virtual network closely resembles a traditional network that we'd operate in our own data center, with the benefits of using the scalable infrastructure of AWS.

9. In the **Monitoring section**, clear the check box for **Enable Enhanced monitoring**.

10. Expand **Additional configuration** section. For Initial **database name**, enter *world*

11. In the** Encryption** section, clear the check box for **Enable encryption**.

    **Note:** we can encrypt our Amazon RDS instances and snapshots at rest by enabling the encryption option for our RDS DB instance. Data that is encrypted at rest includes the underlying storage for a DB instance, its automated backups, read replicas, and snapshots.

12. In the **Maintenance** section, clear the check box for **Enable auto minor version upgrade**.

13. Scroll to the bottom of the screen, and then choose **Create database** .

    **Note:**: Our Aurora DB instance is in the process of launching and can take up to 5 minutes to launch. However, we can continue to the next task.

    If we encounter the *Suggested add-ons for aurora pop-up window*, we can ignore it and choose Close .

    Once the database has completed creating, we should see a similar notification message: *Successfully created database aurora*.

    We have successfully created an Aurora instance


## **Connect to an Amazon EC2 Linux instance**
<br>

14. At the AWS Management Console, in the search bar, search for and choose *EC2*.

15. In the left navigation menu, choose **Instances**.

16. Next to the instancdemoelled **Command Host**, select the check box, and then choose **Connect**.

    **Note:** If we do not see the **Command Host** instance, thdemo is possibly still being provisioned, or we may be using another Region.

17. For **Connect to instance**, choose **Session Manager**.

18. Choose **Connect** to open a terminal window.

    **Note:** If the **Connect** button is not avdemole, wait for a few minutes and try again.

    We have successfully connected to the Amazon EC2 instance named Command Host.

## **Configure the Amazon EC2 Linux instance to connect to Aurora.**
<br>

Here we are to use the yum package manager to install the MariaDB client and then configure the Amazon EC2 Linux instance to connect to the Aurora database.

19. **Command**: To install the MariaDB client, run the following command. The MariaDB client is what we use in later steps to connect to the Aurora instance that we just created.

    `sudo yum install mariadb -y`

    **Expected output**: Output has been truncated.

    ```sql
    ```******************************
    **** This is OUTPUT ONLY. ****
    ******************************

    Install  1 Package

    Total download size: 8.8 M
    Installed size: 49 M
    Downloading packages:
    mariadb-5.5.68-1.amzn2.0.1.x86_64.rpm                    |  8.8 MB  00:00:00
    Running transaction check
    Running transaction test
    Transaction test succeeded
    Running transaction
        Installing : 1:mariadb-5.5.68-1.amzn2.0.1.86_64       1/1
        Verifying  : 1:mariadb-5.5.68-1.amzn2.0.1.x86_64      1/1

    Installed:
    mariadb.x86_64 1:5.5.68-1.amzn2.0.1

    Complete! ```
    ```  
    
20. Using a different browser tab, go back to the AWS Management Console and in the search bar, search for and choose *RDS*.

21. In the left navigation menu, choose *Databases*.

22. Wait for **aurora-instance-1** to display  **Available**.

23. Choose **aurora**.

24. Choose the **Connectivity & security tab**, and in the **Endpoints** section, copy the **Endpoint name** for the **Writer instance** to our text editor.

    The endpoint should look similar to the following: *aurora.cluster-cabcdefghijklm.us-west-2.rds.amazonaws.com.*

    **Note**: An endpoint is represented as an Aurora specific URL that contains a host address and a port. The following types of endpoints are available from an Aurora DB cluster.

    - *Cluster endpoint*:
    A cluster endpoint for an Aurora DB cluster connects to the current primary DB instance for that DB cluster. This endpoint is the only one that can perform write operations such as DDL statements. Because of this, the cluster endpoint is the one that we connect to when we first set up a cluster or when our cluster contains only a single DB instance.

    Each Aurora DB cluster has one cluster endpoint and one primary DB instance.

    We use the cluster endpoint for all write operations on the DB cluster, including inserts, updates, deletes, and DDL changes. we can also use the cluster endpoint for read operations, such as queries.

    The cluster endpoint provides failover support for read/write connections to the DB cluster. If the current primary DB instance of a DB cluster fails, Aurora automatically fails over to a new primary DB instance. During a failover, the DB cluster continues to serve connection requests to the cluster endpoint from the new primary DB instance, with minimal interruption of service.

    The following example illustrates a cluster endpoint for an Aurora MySQL DB cluster.

    *mydbcluster.cluster-123456789012.us-west-2.rds.amazonaws.com:3306*

    - *Reader endpoint*:
    A reader endpoint for an Aurora DB cluster connects to one of the available Aurora replicas for that DB cluster. Each Aurora DB cluster has one reader endpoint. If there is more than one Aurora replica, the reader endpoint directs each connection request to one of the Aurora replicas.

    The reader endpoint provides load-balancing support for read-only connections to the DB cluster. Use the reader endpoint for read operations, such as queries. we can't use the reader endpoint for write operations.

    The DB cluster distributes connection requests to the reader endpoint among the available Aurora replicas. If the DB cluster contains only a primary DB instance, the reader endpoint serves connection requests from the primary DB instance. If one or more Aurora replicas are created for that DB cluster, subsequent connections to the reader endpoint are load balanced among the replicas.

    The following example represents a reader endpoint for an Aurora MySQL DB cluster.

    mydbcluster.cluster-ro-123456789012.us-west-2.rds.amazonaws.com:3306

    Next, log into the database.

25. **Copy edit**: In the following command, replace <endpoint_goes_here> with the endpoint that we copied to our text editor.

    `mysql -u admin --password='admin123' -h <endpoint_goes_here>`

    Our command should look similar to the following:

    `mysql -u admin --password='admin123' -h mydbcluster.cluster-123456789012.us-west-2.rds.amazonaws.com`

    The MySQL Command-Line Client is a SQL shell which enables interaction with database engines. More information is available [here](https://dev.mysql.com/doc/refman/8.0/en/mysql.html).

    | Switch | Description |
    |--------|-------------|
    | -u or --user | The MySQL username used to connect to a database instance. |
    | -p or --password | The MySQL password used to connect to a database instance. |
    | -h or --host | The host address of the database engine. |

    Once the command is updated, copy the command to our clipboard.

26. **Command**: Return to the **Session Manager** browser tab that was used to connect to the **Command Host**. To connect to the Aurora instance, run the command we had copied in the previous step.

    **Expected output**:

    ```plain
    ******************************
    **** This is OUTPUT ONLY. ****
    ******************************    
    Welcome to the MariaDB monitor.  Commands end with ;or \g.
    our MySQL connection id is 173
    Server version: 8.0.28 Source distribution    
    Copyright (c) 2000, 2018, Oracle, MariaDB CorporationAb and others.    
    Type 'help;' or '\h' for help. Type '\c' to clear thecurrent input statement.    
    MySQL [(none)]>
    ```
    We have successfully configured the Amazon EC2 Linux instance to connect to Aurora.

 

## **Create a table and insert and query records.**
<br>

Here, we learn how to create a table in a database, load data, and run a query.

27. **Command**: To list the available databases, run the following command.


    `SHOW DATABASES;`
    
    **Expected output**:

    ```text
    ******************************
    **** This is OUTPUT ONLY. ****
    ******************************        
    +--------------------+
    | Database           |
    +--------------------+
    | information_schema |
    | mysql              |
    | performance_schema |
    | sys                |
    | world              |
    +--------------------+
    5 rows in set (0.02 sec)    
    MySQL [(none)]>
    ```

28. To switch to the **world** database that we created in earlier when we provisioned the Aurora instance, run the following command.

    ```sql
    USE world;
    ```
    **Expected output**:

    ```text
    ******************************  
    **** This is OUTPUT ONLY. ****  
    ******************************          
    Database changed
    MySQL [world]>  
    ```
29. **Command**: To create a new table in the **world** database, run the following command.

    ```sql
    CREATE TABLE `country` (
    `Code` CHAR(3) NOT NULL DEFAULT '',
    `Name` CHAR(52) NOT NULL DEFAULT '',
    `Continent` enum('Asia','Europe','North America','Africa','Oceania','Antarctica','South America') NOT NULL DEFAULT 'Asia',
    `Region` CHAR(26) NOT NULL DEFAULT '',
    `SurfaceArea` FLOAT(10,2) NOT NULL DEFAULT '0.00',
    `IndepYear` SMALLINT(6) DEFAULT NULL,
    `Population` INT(11) NOT NULL DEFAULT '0',
    `LifeExpectancy` FLOAT(3,1) DEFAULT NULL,
    `GNP` FLOAT(10,2) DEFAULT NULL,
    `GNPOld` FLOAT(10,2) DEFAULT NULL,
    `LocalName` CHAR(45) NOT NULL DEFAULT '',
    `GovernmentForm` CHAR(45) NOT NULL DEFAULT '',
    `Capital` INT(11) DEFAULT NULL,
    `Code2` CHAR(2) NOT NULL DEFAULT '',
    PRIMARY KEY (`Code`)
    );
    ```
    **Expected output**:

    ```plain
    ******************************
    **** This is OUTPUT ONLY. ****
    ******************************

    Query OK, 0 rows affected, 7 warnings (0.02 sec)

    MySQL [world]>
    ```

30. **Command**: To insert new records into the **country** table that we just created, run the following commands.

    ```sql
    INSERT INTO `country` VALUES ('GAB','Gabon','Africa','Central Africa',267668.00,1960,1226000,50.1,5493.00,5279.00,'Le Gabon','Republic',902,'GA');

    INSERT INTO `country` VALUES ('IRL','Ireland','Europe','British Islands',70273.00,1921,3775100,76.8,75921.00,73132.00,'Ireland/Éire','Republic',1447,'IE');

    INSERT INTO `country` VALUES ('THA','Thailand','Asia','Southeast Asia',513115.00,1350,61399000,68.6,116416.00,153907.00,'Prathet Thai','Constitutional Monarchy',3320,'TH');

    INSERT INTO `country` VALUES ('CRI','Costa Rica','North America','Central America',51100.00,1821,4023000,75.8,10226.00,9757.00,'Costa Rica','Republic',584,'CR');

    INSERT INTO `country` VALUES ('AUS','Australia','Oceania','Australia and New Zealand',7741220.00,1901,18886000,79.8,351182.00,392911.00,'Australia','Constitutional Monarchy, Federation',135,'AU');
    ```
    **Expected output**:

    ```plain
    ******************************
    **** This is OUTPUT ONLY. ****
    ******************************        
    Query OK, 1 row affected (0.00 sec)    
    MySQL [world]>
    ```
31. **Command**: To query the table, run the following **SELECT** statement.

    ```sql
    SELECT * FROM country WHERE GNP > 35000 and Population > 10000000;
    ```
    **Expected output**:

    ```plain
    ******************************
    **** This is OUTPUT ONLY. ****
    ******************************        
    ------+-----------+-----------+---------------------------+-------------+-----------+------------+----------------+-----------  +-----------+--------------+------------------------------------+---------+-------+
        | Code | Name      | Continent | Region                    | SurfaceArea | IndepYear | Population | LifeExpectancy | GNP       | GNPOld    | LocalName    | GovernmentForm                      |Capital | Code2 |
        +------+-----------+-----------+---------------------------+-------------+-----------+------------+----------------+-----------+-----------+--------------+------------------------------------+---------+-------+
        | AUS  | Australia | Oceania   | Australia and New Zealand |  7741220.00 |      1901 |   18886000 |           79.8 | 351182.00 | 392911.00 | Australia    | Constitutional Monarchy,Federation |     135 | AU    |
        | THA  | Thailand  | Asia      | Southeast Asia            |   513115.00 |      1350 |   61399000 |           68.6 | 116416.00 | 153907.00 | Prathet Thai | ConstitutionalMonarchy             |    3320 | TH    |
        +------+-----------+-----------+---------------------------+-------------+-----------+------------+----------------+-----------+-----------+--------------+------------------------------------+---------+-------+
        2 rows in set (0.00 sec)
        MySQL [world]>
        ```

    The query should return two records for Australia and Thailand.

    We have successfully created a table named country, inserted data into the table, and queried records returning two results.

 
## **Conclusion**
<br>

We have now successfully:

    - Created an Aurora instance.

    - Connected to a pre-created Amazon EC2 instance.

    - Configured the Amazon EC2 instance to connect to Aurora.

    - Queried the Aurora instance.

# Learning Outcomes

	• How to access MySQL databases through Java
	• DDL, DML using Java APIs


## Connectors and Drivers

In order to connect to any database, Java needs an additional library/jar called the Connectors to ensure the Java API's are translated to the native SQL queries of the specific database. 

Even though SQL as a language is standardized, the implementation of a query especially the DDL (Data Definition) commands might vary slightly depending on the database.

![Connectors and Databases](https://github.com/rama-swuni/sqlprogramming/blob/main/sqlprog1.png "")

 

### Drivers

These connectors can be of type ODBC or JDBC. ODBC stands for Open Database Connectivity and JDBC stands for Java Database Connectivity. They work in similar way, but ODBC was mostly used by Microsoft for connecting its own database types (DB2, Access etc.) and does not provide access via Java. JDBC is a Java API for connecting programs written in Java to the data in databases. It consists of a set of classes and interfaces written in the Java programming language. 

Even though it isn't important, it is good to know that historically there are 4 different types of JDBC drivers. 

	• JDBC-ODBC Bridge (Type 1)
	• JDBC-Native API (Type 2)
	• JDBC-Net API (Type 3)
	• JDBC-Pure Java API (Type 4) 
	
The MySQL Connector/J driver is of Type 4. They translate the Java API calls connect to the MySQL database using Sockets and execute the commands.

### Additional Libraries 

We are using MySQL for our database (which will not be covered here) and in order to access MySQL database through java, we need the MySQL Connector for Java which can be accessed from the location given below. The JDBC Driver download link is also provided.

https://www.mysql.com/products/connector/

JDBC Driver for MySQL (Connector/J)	Download

Unzip the file and save the *mysql-connector-java-8.0.21.jar* file in a well known location as this need to be used for compiling and executing the Java programs.

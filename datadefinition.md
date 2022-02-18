# Data Definition - Creating Tables

## Database Metadata

As part of our Data Definition commands, the first command we usually do in MySQL is to get the list of tables available in a Database using the following command. Here is a list of tables from my employees database.

```
mysql> show tables;
+----------------------+
| Tables_in_employees  |
+----------------------+
| current_dept_emp     |//it is a view
| departments          |
| dept_emp             |
| dept_emp_latest_date |//it is a view
| dept_manager         |
| employees            |
| salaries             |
| titles               |
+----------------------+
8 rows in set (0.00 sec)

```

It is possible to retrieve the same using Java Commands too.

For that we need to use the Connection's **getMetaData** method call which will return us the [**DatabaseMetaData**](URL 'https://docs.oracle.com/javase/8/docs/api/java/sql/DatabaseMetaData.html') object. We can retrieve the list of tables (along with many other information like catalog, schema etc.) using the **getTables(…)** method call. 

```
ResultSet getTables(String catalog,
                    String schemaPattern,
                    String tableNamePattern,
                    String[] types)
             throws SQLException
```
A simple way to do it in the java code is as follows:
```
   rs = conn.getMetaData().getTables(null, null, null, new String[] {"TABLE"});//retrieve all tables belong to all catalogs and all schemas and all table names and store it in the resultset object
   System.out.println("List of tables available in the database");
   while(rs.next())
   {
    System.out.println(rs.getString("TABLE_NAME"));//retrieve each table name
   }
   rs.close();

```
This code returns me the following values:
```
List of tables available in the database 

departments
dept_emp
dept_manager
employees
salaries
titles
sys_config
```
If you see, we have got all the tables (excluding the views) and an additional system table called sys_config. 

## Creating Tables:

Let us say that we want to create a products table using the following MySQL command.
```
CREATE TABLE IF NOT EXISTS products (
         productID    INT UNSIGNED  NOT NULL AUTO_INCREMENT,
         productCode  CHAR(3)       NOT NULL DEFAULT '',
         name         VARCHAR(30)   NOT NULL DEFAULT '',
         quantity     INT UNSIGNED  NOT NULL DEFAULT 0,
         price        DECIMAL(7,2)  NOT NULL DEFAULT 99999.99,
         PRIMARY KEY  (productID)
       );
```
We can execute the exact SQL command in Java and create the table in the database associated with our connection object. We make use of the createStatement and create a statement object. Once the statement is ready we then make use of the Statement's [**executeUpdate**](URL 'https://docs.oracle.com/javase/8/docs/api/java/sql/Statement.html#executeUpdate-java.lang.String-') method call and provide the above SQL in a String to the method which in turn will run the command and return us an integer result. The result of this executeUpdate will return 1 for any of the DML commands, and 0 for the DDL commands. In our case for a Create Table it will return 0. 

### Sample Code:
This sample code first lists the set of tables available in the database, creates a database, and then again lists the updated set of tables.
```
public void createCommands() throws SQLException {
  String sql = "CREATE TABLE IF NOT EXISTS products (" + "productID    INT UNSIGNED  NOT NULL AUTO_INCREMENT,"
    + "productCode  CHAR(3)       NOT NULL DEFAULT ''," + "name   VARCHAR(30)   NOT NULL DEFAULT '',"
    + "quantity     INT UNSIGNED  NOT NULL DEFAULT 0,"
    + "price        DECIMAL(7,2)  NOT NULL DEFAULT 99999.99," + "PRIMARY KEY  (productID)" + ")";
  Statement stmt = null;
  ResultSet rs = null;
  try {

   rs = conn.getMetaData().getTables(null, null, null, new String[] { "TABLE" });
   System.out.println("List of tables available in the database - Before");
   while (rs.next()) {
    System.out.println(rs.getString("TABLE_NAME"));
   }
   rs.close();
   stmt = conn.createStatement();
   int result = stmt.executeUpdate(sql);
   System.out.println("Result of executeUpdate : " + result);
   System.out.println("Table Created Successfully!");
   rs = conn.getMetaData().getTables(null, null, null, new String[] { "TABLE" });
   System.out.println("List of tables available in the database - After ");
   while (rs.next()) {
    System.out.println(rs.getString("TABLE_NAME"));
   }

  } finally {
   if (null != rs) {
    rs.close();
   }
   if (null != stmt) {
    stmt.close();
   }
  }

 }
 ```

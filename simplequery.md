# Querying the Database - Simple
Once we have established the connection, the first thing we would like to do is to query a table and list the results of the same. In an ideal scenario, the creation of the tables, indexes, sequences etc, will be done in the backend and are almost never done via the programs as they can be changed and it is always best to write a SQL script to do the DCL commands. We can look at how to do them using Java a bit later. Since DML. (Data Manipulation Language) is what we would be doing mostly with our applications via the Java programs, I choose to do that first.


We have done quite a few SELECT commands in SQL. Let us start with the simplest of them all, __SELECT * FROM \<TABLENAME\>__

In order to do that we need two __Interfaces__.
- A **statement** which represents the actual SQL query command (i.e. the Select statement above).
- The __result set__ which represents the result of the execution of the SQL query command.

The connection object you retrieved earlier is the most important object. You need that to create any kind of statement. 

Use the connection object to call the __createStatement()__ to create an empty Statement object. 
```
Statement createStatement()
                   throws SQLException                 
```                   
Creates a Statement object for sending SQL statements to the database. SQL statements without parameters are normally executed using Statement objects. If the same SQL statement is executed many times, it may be more efficient to use a PreparedStatement object.
Result sets created using the returned Statement object will by default be type __TYPE_FORWARD_ONLY__ and have a concurrency level of __CONCUR_READ_ONLY__. The holdability of the created result sets can be determined by calling getHoldability().
	
Returns:
  
  a new default Statement object
	
Throws:
 
 SQLException - if a database access error occurs or this method is called on a closed connection
  
  
Use the statement object to call the **executeQuery** and pass the SQL SELECT query to it and get the result in a ResultSet object. This resultset object is scrollable and you can go through the results of the query one by one. 

```
ResultSet executeQuery(String sql)
                throws SQLException
```

Executes the given SQL statement, which returns a single ResultSet object.
Note:This method cannot be called on a PreparedStatement or CallableStatement.

Parameters:
	
  sql - an SQL statement to be sent to the database, typically a static SQL SELECT statement
  
Returns:
	
  a ResultSet object that contains the data produced by the given query; never null
  
Throws:
	
  SQLException - if a database access error occurs, this method is called on a closed Statement, the given SQL statement produces anything other than a single ResultSet object, the method is called on a PreparedStatement or CallableStatement
	
  SQLTimeoutException - when the driver has determined that the timeout value that was specified by the setQueryTimeout method has been exceeded and has at least attempted to cancel the currently running Statement

You can also use **execute** method of Statement which returns true if there was a resultset to return and false if there is no result to return. If the return value is true, you can then call the **getResultSet** method to retrieve the resultset.

We will use the executeQuery because of its ease of use. The ResultSet has a next() method which helps us to read the resulting records one by one. Use that in a while loop.

### Closing the resultset and statement:

**_You should always close the resultset and the statement once it is done_**. Close the resultset first and then the statement. They should be closed in the reverse order of their opening. It is an important practice because for large resultsets, if these connections are left open, they might hog the memory and in most cases, the number of connections allowed might also be limited. So if you open it, don’t forget to close it. Use the try{} finally{}  block to do so. 

### Points to remember:
In SQL, the index starts at 1
While retrieving the data from the resultset, you need to know the data types of each column. You need to use the specific API like getInt for numbers, getString for varchar, getDate for datetime etc. If you mix them up, Java will throw errors.
Do not forget to close the resultset and the statement.


### Sample Code:
```
public void executeSelectQueries() throws SQLException {
  Statement stmt = null;
  ResultSet rs  = null;
  try {
   stmt = conn.createStatement();
   // limiting the rows to 10 as this database has 300024 rows.
   // I dont want my java program to crash :D
   rs = stmt.executeQuery("SELECT * FROM employees LIMIT 10");
   if (null != rs) {
    while (rs.next()) {
     // Since I know that the table has the following columns,
     // emp_no | birth_date | first_name | last_name | gender | hire_date
     // I can retrieve the data using the column names like this
     System.out.print("Employee Number: " + rs.getInt("emp_no"));
     // or I can retrieve it using the index of the columns, e.g. first_name is index
     // 3
     // remember that the index in SQL starts with 1 and *not* zero.
     System.out.print("\tFirst Name: " + rs.getString(3));
     System.out.println("");
    }
   }
  } finally {
   if(null!=rs)
   {
    rs.close();
   }
   if (null != stmt) {
    stmt.close();
   }
  }

```

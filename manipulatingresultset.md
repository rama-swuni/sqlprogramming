# Manipulting the ResultSet

We have seen how we can get the result of a SQL query executed using a Statement or a PreparedStatement in the ResultSet object and scroll through the object to retrieve individual rows.

In our first example, where we were querying for the first 10 employees and were scrolling the resultset one by one to get their data, the way the records can be accessed is forward only. If you want to visualise the following as your set of records, they can be accessed from left to right in the forward fashion only. 
 
![ResultSet movement](https://github.com/rama-swuni/sqlprogramming/blob/main/resultset.png)

```
try {
   stmt = conn.createStatement( );
   rs = stmt.executeQuery("SELECT * FROM employees LIMIT 10");
   if (null != rs) {
    while (rs.next()) {
     ….
      
    }
   }
  } finally {
 …  }
```
Now if we wanted to move our cursor (or pointer) to a record which is to the left of the current one (e.g. if we are in the 8th employee and we wanted to check the entry of the 7th employee) then we cannot do a previous() method call on the ResultSet object. It will throw an error. This is because by default when the Statement object is created, it is set to TYPE_FORWARD_ONLY option by default.
```
try {
   stmt = conn.createStatement( );
   rs = stmt.executeQuery("SELECT * FROM employees LIMIT 10");
   if (null != rs) {
    while (rs.next()) {
     ….
     rs.previous(); //This line throws an error.
    }
   }
  } finally {
 …  }

```
If we want to randomly move around the ResultSet object to fetch the records not in a serial fashion but random fashion, then we need to set the type of cursor (or pointer) to the Statement object when we create one. 
For example, the following code will allow me to move to the next in the queue of records, move to the last one, move to the first one, move to the previous one etc. Please see the comments in red. 
```
private void printData(ResultSet rs) throws SQLException {
  System.out.print("Employee Number: " + rs.getInt("emp_no"));
  // or I can retrieve it using the index of the columns, e.g. first_name is index
  // 3
  // remember that the index in SQL starts with 1 and *not* zero.
  System.out.print("\tFirst Name: " + rs.getString(3));
  System.out.println("");
 }

 public void executeSelectQueriesFR() throws SQLException {
  Statement stmt = null;
  ResultSet rs = null;
  try {
   stmt = conn.createStatement(ResultSet.TYPE_SCROLL_INSENSITIVE, ResultSet.FETCH_REVERSE);//Informs the Statement to create a resultset which is insensitive to how it can be scrolled and to be able to fetch in reverse too. Without this setting, the previous(), last() method calls will fail
   // limiting the rows to 10 as this database has 300024 rows.
   // I dont want my java program to crash :D
   rs = stmt.executeQuery("SELECT * FROM employees LIMIT 10");
   if (null != rs) {
    rs.next();//Moves to the first record
    printData(rs);
    rs.next();////Moves to the 2nd record
    printData(rs);
    rs.previous();//Moves to the first record again
    printData(rs);
    rs.last();//Moves to the last record in the set
    printData(rs);

   }
  } finally

  {
   if (null != rs) {
    rs.close();
   }
   if (null != stmt) {
    stmt.close();
   }
  }

 }

```
## Why is this important?
Manipulating the ResultSet may not be important for a simple JDBC application which is not executed in a concurrent environment and does not update data as and when it fetches it, but if we want to scroll through the ResultSet, fetch a data, check its value and update it immediately, then we need to update the ResultSet settings for TYPE and SCROLL in order to do that. It is highly important to ensure that the correct ResultSet type is set for concurrent executions during transactions.

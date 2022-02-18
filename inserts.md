# Data Definition - Inserts

Once we have created a new table, we would want to insert some values to it too. This is where our precious little **PreparedStatement** comes to our rescue. We also use our **executeUpdate()** prepared statement which in turn returns us the value 1 (as opposed to 0 for a create table command). From now on, the code would look very familiar with a higher degree of PreparedStatement's being used and either executeQuery or execute or executeUpdate being used for different purposes.

By default the auto commit on our commands is set to true. We might want to set it to false in the Connection object. Here is a sample code that does a single update to our newly created products table.

## Sample Code:
```
public void insertIntoTable() throws SQLException {
  conn.setAutoCommit(false);//it is best to set it to false and do the commit once we are sure everything has gone smoothly, especially with bigger batch updates
  PreparedStatement stmt = null;
  try {
   stmt = conn.prepareStatement("INSERT INTO products VALUES (?, ?, ?, ?, ?)");

   stmt.setInt(1, 1000);
   stmt.setString(2, "PEN");
   stmt.setString(3, "Red Pen");
   stmt.setInt(4, 5000);
   stmt.setFloat(5, (float) 5.23);
   int result = stmt.executeUpdate();
   conn.commit();//if you skip this, then it will not update it in the backend. 
   System.out.println("Result of insert: " + result);
  } finally {

   if (null != stmt) {
    stmt.close();
   }
  }

 }
```

The code starts to look very familiar, doesn't it. 

## Batch Updates:
Now what if we want to do a batch insert like we could do in MySQL command prompt. We could do that too using Java. Instead of doing a **executeUpdate** everytime, we add each statement to a batch after setting their values. Once we are sure that all the rows have been added, we call the **executeBatch** method on the PreparedStatement object which in turn provides us with the integer array listing the value for each and every row that has been added (if it was successful or not)

The following code sample will help us understand the methods used.

## Sample Code:
```
public void insertIntoTablebatch() throws SQLException {
  conn.setAutoCommit(false);
  PreparedStatement stmt = null;
  try {
   stmt = conn.prepareStatement("INSERT INTO products VALUES (?, ?, ?, ?, ?)");

   stmt.setInt(1, 1001);
   stmt.setString(2, "PEN");
   stmt.setString(3, "Blue Pen");
   stmt.setInt(4, 8000);
   stmt.setFloat(5, (float) 1.23);
   stmt.addBatch();
   stmt.setInt(1, 1002);
   stmt.setString(2, "PEC");
   stmt.setString(3, "Pencil 2H");
   stmt.setInt(4, 2000);
   stmt.setFloat(5, (float) 0.49);
   stmt.addBatch();
   int[] results = stmt.executeBatch();
   conn.commit();

  } finally {

   if (null != stmt) {
    stmt.close();
   }
  }

 }
```
If I check my MySQL it returns me the updated values.

```
mysql> select * from products;
+-----------+-------------+-----------+----------+-------+
| productID | productCode | name      | quantity | price |
+-----------+-------------+-----------+----------+-------+
|      1000 | PEN         | Red Pen   |     5000 |  5.23 |
|      1001 | PEN         | Blue Pen  |     8000 |  1.23 |
|      1002 | PEC         | Pencil 2H |     2000 |  0.49 |
+-----------+-------------+-----------+----------+-------+
3 rows in set (0.00 sec)
```

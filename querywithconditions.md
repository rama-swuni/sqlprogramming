# Querying the Database -  Conditions

## Using String concatenation

We use the SELECT queries with lot of conditions where the conditions might be varied. We may not want to always hardcode the conditions in our code and use the executeQuery on a Statement. It is not a good practice either. Statement and its executeQuery should be used when the SQL queries are static and do not change much. 

But if we have a SQL statement like this
```
select * from employees where emp_no > 10000 and emp_no < 10100 and gender='F';
```

In this case, I am interested in finding the employees whose employee numbers fall within a range and based on a gender. Now my values for 10000, 10100 and F might change depending on what range and what gender I want to query on. If such queries are common and if we want to vary the values that we pass to it, we could use the Statement and construct a SQL query statement by String concatenation like the code below and it would work.

```
public void executeSelectQueriesConditions(int lowerEmpNo, int higherEmpNo, String gender) throws SQLException {
  Statement stmt = null;
  ResultSet rs  = null;
  try {
   stmt = conn.createStatement();
   // limiting the rows to 10 as this database has 300024 rows.
   // I dont want my java program to crash :D
   String sql = "SELECT * FROM employees WHERE emp_no>"+lowerEmpNo+" AND emp_no < "+higherEmpNo+" AND gender='"+gender+"'";
   
   
   rs = stmt.executeQuery(sql);
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

 }
 
```
But it is tedious when there are too many conditions and String Concatenation is always tricky. You have to ensure that no mistakes are made and if there was a quote (') missing for String comparison, it might lead you down the rabbit hole quite fast. Also, look at SQLInjection, if you have read about it or if we have discussed it already.

## Using PreparedStatements: (Preferred)

The best practice would be to use a PreparedStatement which is another interface that Java has. The PreparedStatement will help you set the values to the variable parameters without you have to deal with the complex string creation. 
It is also used in the Insert, Update and Delete SQL commands. If in doubt, always use **PreparedStatement** instead of Statement.

Let us see our code now with the PreparedStatement. 

```
public void executeQueriesWithParameters(int lowerEmpNo, int higherEmpNo, String gender) throws SQLException
 {
  PreparedStatement stmt = null;
  ResultSet rs  = null;
  try {
   
   stmt = conn.prepareStatement("SELECT * FROM employees WHERE emp_no > ? AND emp_no < ? AND gender=?");
   stmt.setInt(1, lowerEmpNo);
   stmt.setInt(2, higherEmpNo);
   stmt.setString(3, gender);
   rs = stmt.executeQuery();
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

 }
```

Let us look at each line of the important lines one by one. 

I am creating a prepared statement where I specify the input parameters using a ? Which would tell the compiler that there will be values that need to be filled in at runtime.
```
stmt = conn.prepareStatement("SELECT * FROM employees WHERE emp_no > ? AND emp_no < ? AND gender=?");
```
Each of these ? Will be assigned an index value starting from 1 and going from left to right. 
To set the values, we need to know the type of value that will go in the place of a ? In our example, the first two will be an integer and the 3rd one will be a string. So we set the values accordingly. 
```
  stmt.setInt(1, lowerEmpNo);
  stmt.setInt(2, higherEmpNo);
  stmt.setNString(3, gender);
```
Once the values for each of the ? Is set we then execute the query and get the resultset. 

```
   rs = stmt.executeQuery();
```
There is practically no difference in the resultset from using the string concatenation and the preparedstatement. But the PreparedStatement is a much cleaner way of executing the queries with different inputs.

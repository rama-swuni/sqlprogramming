# Establishing the Connection
The first step is to establish the connection to the Database and see if we can do it successfully.

The API's and Interfaces we need to connect to any database will be available in the [java.sql.*](URL 'https://docs.oracle.com/javase/8/docs/api/java/sql/package-summary.html') directory. 

In order to establish a connection to the MySQL database, we use the __DriverManager__ class. This class will tell the JVM (Java Virtual Machine) which driver to load and the JVM will pick the respective jar from the classpath and load the JDBC API's. That is why we do not see any MySQL specific classes in any of the JDBC programs. As long as you replace the JDBC driver's jar file and the connection string in the DriverManager's API call, you can change your database from MySQL to Oracle Database later. That is how it is encapsulated from the developers.

The getConnection method of DriverManager has the API description as follows:


`public static Connection getConnection(String url)
                                throws SQLException`
                                
Attempts to establish a connection to the given database URL. The DriverManager attempts to select an appropriate driver from the set of registered JDBC drivers.
	
 __Parameters:__
  
  url - a database url of the form _jdbc:subprotocol:subname_
  
__Returns:__

  a connection to the URL
  
__Throws:__

SQLException - if a database access error occurs or the url is null

SQLTimeoutException - when the driver has determined that the timeout value specified by the setLoginTimeout method has been exceeded and has at least tried to cancel the current database connection attempt
  

### What does the url contain?

The url starts with jdbc because we are using a JDBC Type 4 driver.
This is followed by a protocol which in our case is mysql because we are calling the MySQL database.
Which is then followed by the subprotocol which gives the connection string to the actual MySQL database itself, which in our case would be __localhost/DATABASENAME?user=USERNAME&password=PASSWORD__. This URL is similar to a GET query in HTTP protocol. 

The final url string for the DriverManager's getConnection would look like

`jdbc:mysql://localhost/employees?user=123455&password=welcome`

This connects to the MySQL database in the localhost (the same machine where the java program is running) and logs in to the database using the user and password values and uses the employees database for any further API calls.

### How does DriverManager know which class to load from the mysql-connector-java-8.0.21.jar ?
In the mysql-connector-java-8.0.21.jar there is a file named META-INF/services/java.sql.Driver which tells the JVM which class to load. In the case of MySQL the starter class is set as com.mysql.cj.jdbc.Driver

Every driver jar file will have this file in the META-INF folder and the file will have the starter class set in them. 


### Sample Code:
Let us write a test code to see if the Jar file is set correctly in the classpath and it does indeed help us login to the MySQL using the username and password successfully. 
Note: Do not forget to set the correct database name, username and password in the code. Test the following in the command line prompt first.

	• mysql -u <username> -p <this will prompt you for the password>
	• show databases; <this will list all the databases available>
	• Use <databasename>; <if you use one single database for our testing it should work> This is the same databasename you will use in the following program.
  
```
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class DBConnect {
 
 private static Connection conn = null;

 public static void main(String[] args) {
  try {
   
   conn =
           DriverManager.getConnection("jdbc:mysql://localhost/<DATABASENAME>?" +
                                       "user=<USERNAME>&password=<PASSWORD>");
  } catch (SQLException e) {
   
   System.out.println("Something went wrong with the connection!");
   e.printStackTrace(System.out);
  }

  System.out.println("I have successfully connected with the MySQL database");
 }

}
  ```


**Go To** *[Querying the database - Simple](simplequery.md)*

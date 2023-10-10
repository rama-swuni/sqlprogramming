# Data Definitions - Update and Delete

Without having to repeat the commands again, we can do an update and a delete SQL command execution with the help of the PreparedStatement and its executeUpdate method call. The only different between them would be the SQL command that will be used. Instead of INSERT INTO command we need to use a **UPDATE \<TABLE\> SET …*** and ***DELETE FROM \<TABLE\> WHERE …*** command as our SQL string with the appropriate ? (question marks) set for the values that needs to be used for either Update or the Delete conditions.

**Go To** *[Futher ahead](further.md)*

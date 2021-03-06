## How to Specify Database Files ##

Here is an example to establishing a connection to a database file `C:\work\mydatabase.db` (in Windows)

```java
Connection connection = DriverManager.getConnection("jdbc.spatialite:C:/work/mydatabase.db");
```

Opening a UNIX (Linux, Mac OS X, etc.) file `/home/leo/work/mydatabase.db`
```java
Connection connection = DriverManager.getConnection("jdbc.spatialite:/home/leo/work/mydatabase.db");
```

## How to Use Memory Databases ##

SQLite supports on-memory database management, which does not create any database files. To use a memory database in your Java code, get the database connection as follows:


```java
Connection connection = DriverManager.getConnection("jdbc.spatialite::memory:");
```

And also, you can create memory database as follows:
```java
Connection connection = DriverManager.getConnection("jdbc.spatialite:");
```

## How to use Online Backup and Restore Feature ##

Take a backup of the whole database to `backup.db` file:


```java

// Create a memory database
Connection conn = DriverManager.getConnection("jdbc.spatialite:");
Statement stmt = conn.createStatement();
// Do some updates
stmt.executeUpdate("create table sample(id, name)");
stmt.executeUpdate("insert into sample values(1, \"leo\")");
stmt.executeUpdate("insert into sample values(2, \"yui\")");
// Dump the database contents to a file
stmt.executeUpdate("backup to backup.db");
Restore the database from a backup file:
// Create a memory database
Connection conn = DriverManager.getConnection("jdbc.spatialite:");
// Restore the database from a backup file
Statement stat = conn.createStatement();
stat.executeUpdate("restore from backup.db");

```

## Creating BLOB data ##

1. Create a table with a column of blob type: `create table T (id integer, data blob)`
1. Create a prepared statement with `?` symbol: `insert into T values(1, ?)`
1. Prepare a blob data in byte array (e.g., `byte[] data = ...`)
1. `preparedStatement.setBytes(1, data)`
1. `preparedStatement.execute()...`

## Reading Database Files in classpaths or network (read-only) ##

To load db files that can be found from the class loader (e.g., db 
files inside a jar file in the classpath), 
use `jdbc.spatialite::resource:` prefix.

For example, here is an example to access an SQLite DB file, `sample.db` 
in a Java package `org.yourdomain`: 


```java

Connection conn = DriverManager.getConnection("jdbc.spatialite::resource:org/yourdomain/sample.db");

```

In addition, external DB resources can be used as follows: 


```java

Connection conn = DriverManager.getConnection("jdbc.spatialite::resource:http://www.xerial.org/svn/project/XerialJ/trunk/sqlite-jdbc/src/test/java/org/sqlite/sample.db");

```

To access db files inside some specific jar file (in local or remote), 
use the [JAR URL](http://java.sun.com/j2se/1.5.0/docs/api/java/net/JarURLConnection.html):


```java

Connection conn = DriverManager.getConnection("jdbc.spatialite::resource:jar:http://www.xerial.org/svn/project/XerialJ/trunk/sqlite-jdbc/src/test/resources/testdb.jar!/sample.db");

```

DB files will be extracted to a temporary folder specified in `System.getProperty("java.io.tmpdir")`.

## Configure Connections #


```java

SQLiteConfig config = new SQLiteConfig();
// config.setReadOnly(true);   
config.setSharedCache(true);
config.recursiveTriggers(true);
// ... other configuration can be set via SQLiteConfig object
Connection conn = DriverManager.getConnection("jdbc.spatialite:sample.db", config.toProperties());
```
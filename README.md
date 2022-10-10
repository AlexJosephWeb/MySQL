# MySQL

This repository is a sandbox to learn, play, and show skills with examples.

## What is MySQL

MySQL is a RDMS (relational database management system), open-source, free, is ideal for both small and large applications.
MySQL is very fast, reliable, scalable, and easy to use, is cross-platform, and is compliant with the ANSI SQL standard.
MySQL was first released in 1995 and is developed, distributed, and supported by Oracle Corporation.

*I learned MySQL almost 2 decades ago while building a tool to make my thesis project "EcoLodgical" interactive!*

## Who uses MySQL?

*Everyone*

Huge websites like Facebook, Twitter, Airbnb, Booking.com, Uber, GitHub, YouTube, etc.
Content Management Systems like WordPress, Drupal, Joomla!, Contao, etc.
A very large number of web developers around the world!

## Database Design Considerations

1. Always use proper datatype
Using varchar (20) to store date time values instead of DATETIME datatype will lead to errors during date time-related calculations and there is also a possible case of storing invalid data.

2. Use CHAR (1) over VARCHAR(1) because VARCHAR(1) will take extra byte to store information

3. Use CHAR datatype to store only fixed length data
Example: Using char(1000) instead of varchar(1000) will consume more space if the length of data is less than 1000

4. Avoid using regional date formats
When you use DATETIME or DATE datatype always use YYYY-MM-DD date format or ISO date format that suits your SQL Engine. Other regional formats like DD-MM-YYY, MM-DD-YYYY will not be stored properly.

5. Index key columns
Make sure to index the columns which are used in JOIN clauses so that the query returns the result fast.
If you use UPDATE statement that involves more than one table make sure that all the columns which are used to join the tables are indexed

6. Do not use functions over indexed columns
Using functions over indexed columns defeats the purpose of the index. Suppose you want to get data where first two character of customer code is AK, do not write
SELECT columns FROM table WHERE left (customer_code,2)=’AK’
but rewrite it using
SELECT columns FROM table WHERE customer_code like ‘AK%’
which will make use of index which results in faster response time.

7. Use SELECT * only if needed
Do not just blindly use SELECT * in the code. If there are many columns in the table, all will get returned which will slow down the response time particularly if you send the result to a front-end application.
Explicitly type out the column names which are actually needed.

8. Use ORDER BY Clause only if needed
If you want to show the result in front-end application, let it ORDER the result set. Doing this in SQL may slow down the response time in the multi-user environment.

9. Choose proper Database Engine
If you develop an application that reads data more often than writing (ex: search engine), choose MyISAM storage engine.
If you develop an application that writes data more often than reading (ex: real-time bank transactions), choose INNODB storage engine.
Choosing wrong storage engine will affect the performance

10. Use EXISTS clause wherever needed
If you want to check the existence of data, do not use
If (SELECT count(*) from Table WHERE col=’some value’)>0
instead, use EXISTS clause
If EXISTS(SELECT * from Table WHERE col=’some value’)
which is faster in response time.

*Reference: https://www.crayondata.com/top-10-best-practices-in-mysql/*

## Connect to a Database

1. Create a new PHP file and name it db_connnection.php 

```
<?php
function OpenCon()
 {
 $dbhost = "localhost";
 $dbuser = "root";
 $dbpass = "1234";
 $db = "example";
 $conn = new mysqli($dbhost, $dbuser, $dbpass,$db) or die("Connect failed: %s\n". $conn -> error);
 
 return $conn;
 }
 
function CloseCon($conn)
 {
 $conn -> close();
 }
   
?>
```

2. Connect to database by including in each PHP file (include ‘connection.php’) 

```
<?php
include 'db_connection.php';
$conn = OpenCon();
echo "Connected Successfully";
CloseCon($conn);
?>
```

## Manipulate Data

### Add Data

Insert a row...

```
$sql = "INSERT INTO Students (first_name, last_name, email) VALUES ('Test', 'Testing', 'Testing@tesing.com')";
if (mysqli_query($conn, $sql)) {
      echo "New record created successfully";
} else {
      echo "Error: " . $sql . "<br>" . mysqli_error($conn);
}
```

Insert multiple rows...

```
$sql = "INSERT INTO Students (first_name, last_name, email) VALUES
            ('John', 'Rambo', 'johnrambo@mail.com'),
            ('Clark', 'Kent', 'clarkkent@mail.com'),
            ('John', 'Carter', 'johncarter@mail.com'),
            ('Harry', 'Potter', 'harrypotter@mail.com')";
if(mysqli_query($conn, $sql)){
    echo "Records added successfully.";
} else{
    echo "ERROR: Could not able to execute $sql. " . mysqli_error($link);
}
```

### Read the data

```
$sql = "SELECT * FROM persons";
if($result = mysqli_query($link, $sql)){
    if(mysqli_num_rows($result) > 0){
        echo "<table>";
            echo "<tr>";
                echo "<th>id</th>";
                echo "<th>first_name</th>";
                echo "<th>last_name</th>";
                echo "<th>email</th>";
            echo "</tr>";
        while($row = mysqli_fetch_array($result)){
            echo "<tr>";
                echo "<td>" . $row['id'] . "</td>";
                echo "<td>" . $row['first_name'] . "</td>";
                echo "<td>" . $row['last_name'] . "</td>";
                echo "<td>" . $row['email'] . "</td>";
            echo "</tr>";
        }
        echo "</table>";
        // Free result set
        mysqli_free_result($result);
    } else{
        echo "No records matching your query were found.";
    }
} else{
    echo "ERROR: Could not able to execute $sql. " . mysqli_error($link);
}
```

Limit the SELECT using WHERE...

```
$sql = "SELECT * FROM persons WHERE first_name='john'";
if($result = mysqli_query($link, $sql)){
    if(mysqli_num_rows($result) > 0){
        echo "<table>";
            echo "<tr>";
                echo "<th>id</th>";
                echo "<th>first_name</th>";
                echo "<th>last_name</th>";
                echo "<th>email</th>";
            echo "</tr>";
        while($row = mysqli_fetch_array($result)){
            echo "<tr>";
                echo "<td>" . $row['id'] . "</td>";
                echo "<td>" . $row['first_name'] . "</td>";
                echo "<td>" . $row['last_name'] . "</td>";
                echo "<td>" . $row['email'] . "</td>";
            echo "</tr>";
        }
        echo "</table>";
        // Close result set
        mysqli_free_result($result);
    } else{
        echo "No records matching your query were found.";
    }
} else{
    echo "ERROR: Could not able to execute $sql. " . mysqli_error($link);
}
```

LIMIT the SELECT...

```
$sql = "SELECT * FROM persons LIMIT 3";
if($result = mysqli_query($link, $sql)){
    if(mysqli_num_rows($result) > 0){
        echo "<table>";
            echo "<tr>";
                echo "<th>id</th>";
                echo "<th>first_name</th>";
                echo "<th>last_name</th>";
                echo "<th>email</th>";
            echo "</tr>";
        while($row = mysqli_fetch_array($result)){
            echo "<tr>";
                echo "<td>" . $row['id'] . "</td>";
                echo "<td>" . $row['first_name'] . "</td>";
                echo "<td>" . $row['last_name'] . "</td>";
                echo "<td>" . $row['email'] . "</td>";
            echo "</tr>";
        }
        echo "</table>";
        // Close result set
        mysqli_free_result($result);
    } else{
        echo "No records matching your query were found.";
    }
} else{
    echo "ERROR: Could not able to execute $sql. " . mysqli_error($link);
}
```

Order the SELECT...

```
$sql = "SELECT * FROM persons ORDER BY first_name";
if($result = mysqli_query($link, $sql)){
    if(mysqli_num_rows($result) > 0){
        echo "<table>";
            echo "<tr>";
                echo "<th>id</th>";
                echo "<th>first_name</th>";
                echo "<th>last_name</th>";
                echo "<th>email</th>";
            echo "</tr>";
        while($row = mysqli_fetch_array($result)){
            echo "<tr>";
                echo "<td>" . $row['id'] . "</td>";
                echo "<td>" . $row['first_name'] . "</td>";
                echo "<td>" . $row['last_name'] . "</td>";
                echo "<td>" . $row['email'] . "</td>";
            echo "</tr>";
        }
        echo "</table>";
        // Close result set
        mysqli_free_result($result);
    } else{
        echo "No records matching your query were found.";
    }
} else{
    echo "ERROR: Could not able to execute $sql. " . mysqli_error($link);
}
```

### Join the data

```
$array = array("one", "two", "three", "four", "five");

// Imploding array with different separators
echo join(", ", $array); // comma plus space
echo join("_", $array);  // underscore
echo join("-", $array);  // hyphen
echo join(" ", $array);  // space
```

### Edit the data

```
$sql = "UPDATE persons SET email='peterparker_new@mail.com' WHERE id=1";
if(mysqli_query($link, $sql)){
    echo "Records were updated successfully.";
} else {
    echo "ERROR: Could not able to execute $sql. " . mysqli_error($link);
}
```

### Delete the data

```
$sql = "DELETE FROM persons WHERE first_name='John'";
if(mysqli_query($link, $sql)){
    echo "Records were deleted successfully.";
} else{
    echo "ERROR: Could not able to execute $sql. " . mysqli_error($link);
}
```

## Optimize

## Good References

https://www.crayondata.com/top-10-best-practices-in-mysql/
https://pankajconnect.medium.com/best-practices-for-mysql-performance-optimization-and-mysql-use-indexes-cd3dda51b8b0

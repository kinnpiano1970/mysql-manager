# Read Me Template

![Project Image](project-image-url)

> This is a ReadMe template to help save you time and effort.

---

### Table of Contents
You're sections headers will be used to reference location of destination.

- [Read Me Template](#read-me-template)
    - [Table of Contents](#table-of-contents)
  - [Description](#description)
      - [Built With](#built-with)
  - [How To Use](#how-to-use)
    - [Installation](#installation)
    - [Usage](#usage)
      - [Library Setup](#library-setup)
      - [Library Usage Reference](#library-usage-reference)
    - [Copy & Paste Examples](#copy--paste-examples)
        - [GENERAL QUERIES](#general-queries)
        - ["CREATE" & "ALTER" TABLES](#create--alter-tables)
        - ["DELETE" records from a table](#delete-records-from-a-table)
        - ["SELECT" Queries](#select-queries)
        - [Escaped arguments queries](#escaped-arguments-queries)
        - [Wildcard example queries](#wildcard-example-queries)
        - ["INSERT" Table Queries](#insert-table-queries)
        - ["UPDATE" Tables Queries](#update-tables-queries)
    - [Live Code Examples](#live-code-examples)
          - [JSB Harpsichord Application](#jsb-harpsichord-application)
  - [References](#references)
  - [License](#license)
  - [Author Info](#author-info)

    - [Copy & Paste Examples](#copy--paste-examples)
        - [GENERAL QUERIES](#general-queries)
        - ["CREATE" & "ALTER" TABLES](#create--alter-tables)
        - ["DELETE" records from a table](#delete-records-from-a-table)
        - ["SELECT" Queries](#select-queries)
        - [Escaped arguments queries](#escaped-arguments-queries)
        - [Wildcard example queries](#wildcard-example-queries)
        - ["INSERT" Table Queries](#insert-table-queries)
        - ["UPDATE" Tables Queries](#update-tables-queries)
    - [Live Code Examples](#live-code-examples)
          - [JSB Harpsichord Application](#jsb-harpsichord-application)
  - [References](#references)
  - [License](#license)
  - [Author Info](#author-info)

---

## Description

This is a promise based wrapper for the Node.js MYSQL library. The goal of this library is to abstact away the setup details for configuring a Node.js MYSQL POOL connection.

__What is a MYSQL POOL connection?__

Normally SQL queries run one after another. It means that if you want to do 15 queries and each query takes 2 seconds then it will take 30 seconds to complete the all the queries. 

The solution would be to create 15 connections and run each query in a different connection. This can be done automatically using a feature built into the Node.js MYSQL library called __*connection pooling*__. 

__*From the Node.js MYSQL library documentation:*__
>Rather than creating and managing connections one-by-one, this module also provides built-in connection pooling using mysql.createPool(config). 

>Using pool.getConnection() is useful to share connection state for subsequent queries. This is because two calls to pool.query() may use two different connections and run in parallel. 

>Connections are lazily created by the pool. If you configure the pool to allow up to 100 connections, but only ever use 5 simultaneously, only 5 connections will be made. Connections are also cycled round-robin style, with connections being taken from the top of the pool and returning to the bottom.

The code flow for connection pooling is:

`pool.getConnection() -> connection.query() -> connection.release()`


This library provides an promise based abtraction for this process as well the setup of the database connection. Also included are a few syntactic sugar methods for querying the database and quickly creating tables.

This library is a work in progress and my goal is to continue to improve and expand it with more methods.

#### Built With

* []()
* []()
* []()


[Back To The Top](#read-me-template)

---

## How To Use

### Installation

There are a few dependancies required for the library to function.

1. MYSQL library for Node.js
   ```sh
   npm install mysql -s
   ```
2. Json Format library
   ```sh
   npm install json-format -s
   ```
[Back To The Top](#read-me-template)

---

<!-- USAGE EXAMPLES -->
### Usage

#### Library Setup

Now that you have the necessary dependacies installed, then require it into your main application. The main filename is `db-manager.js`

````javascript
const DbManager = require('./db-manager');
````

The database manager is a Function Constructor. So you will need a create an instance of the database manager and pass in the `options` parameter

````javascript
const db = new DbManager(options);
````

The `options` parameter is an __object literal__ that contains the connection parameters for connecting to a MYSQL database.

To connect to a MYSQL database you will need to provide the following details:

````javascript
const options = {
        connectionLimit: 100, // Leave this at 100
        host: 'localhost', 
        port: 3306, // Default port for MYSQL Servers
        user: 'database_username', 
        password: 'database_password',
        database: 'database_name'
    }
````

The `database_username`, `database_password` and `database_name` should be replaced with the info from your database.

_For more info on using MYSQL with Node.js, please refer to this great Node.js MYSQL tutorial from  [W3Schools.com](https://www.w3schools.com/nodejs/nodejs_mysql.asp)_


#### Library Usage Reference

- ###### `db.queryDB(sql, args)`

The main method for this library is `queryDB(sql,args)`

The `queryDB()` method takes two arguments: 

* `sql` This is a valid SQL query string (Required)
* `args` Used to pass values using SQL the placeholder __`?`__(Optional) 

>__NOTE:__ You can pass arguments to the `queryDB()` method in two basic ways:

Make a single SQL query string with the SQL commands AND values:
````javascript
var sql = "INSERT INTO movies (title, release_date) VALUES ('Iron Man', 'May 2, 2008')";
````

Or use the `args` parameter to pass in values so they can be escaped:
````javascript
var args = ['Iron Man', 'May 2, 2008'];
var sql = "INSERT INTO movies (title, release_date) VALUES ?";
````


This is a promised based library, so a query would look something like this:
````javascript
db.queryDB(sql,args).then((results) => {
    console.log(results);
});
````

Basic query example with a SQL query string:
````javascript
const sql = "SELECT title FROM film WHERE title LIKE 'Iron Man'";

db.queryDB(sql).then((results) => {
    console.log(results);
});
````

Example query with escaped values

````javascript
const args = ['Iron Man', 'May 2, 2008'];
const sql = "INSERT INTO movies (title, release_date) VALUES ?";

db.queryDB(sql,args).then((results) => {
    console.log(results);
});
````

- ###### `db.quickTable(name, [columns])`

Quickly create a table with the length values for all columns preset to (varchar(255);

````javascript
// Create a table named 'blog_entries' with 
// 4 columns: 'author','date','title' and 'post'  
db.quickTable('blog_entries','author','date','title','post').then((results)=> {
  console.log(results);
})
````

---

### Copy & Paste Examples 

##### GENERAL QUERIES
Show tables from the current selected database.
````javascript
const sql = "SHOW TABLES"
````

Show columns from a table
````javascript
const sql = "SHOW COLUMNS FROM table_name"
````
`DESCRIBE` a table

What if you forget the name of a database or table, or what the structure of a given table is (for example, what its columns are called)? The `DESCRIBE` command gives table info such as column names, constraints on column names, etc...
````javascript
const sql = "DESCRIBE jason_test"
````

##### "CREATE" & "ALTER" TABLES

Create a basic table with a `PRIMARY KEY` 
````javascript
var sql = "CREATE TABLE customers (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(255), address VARCHAR(255))";
````

Add a column to a table using `ALTER TABLE`
````javascript
// Add a column named 'id' to table called 'customers'
var sql = "ALTER TABLE customers ADD COLUMN id INT AUTO_INCREMENT PRIMARY KEY";
````
````javascript
// Add a column named 'period' to the table 'mytable'
const sql = "ALTER TABLE mytable ADD COLUMN period varchar(40)";
````
Add a column to a table
````javascript
// Change the column name from 'name' to 'username'
const sql = "ALTER TABLE users CHANGE COLUMN name username VARCHAR(255)";
````
##### "DELETE" records from a table

`DELETE`  a record from a table

>__Notice the WHERE clause in the DELETE syntax:__  The WHERE clause specifies which record or records that should be deleted. If you omit the __WHERE__ clause, __ALL__ records will be deleted!

````javascript
// Delete record with the id of '1'
const sql = "DELETE FROM jason_test WHERE id = '1'";
````
````JAVASCRIPT
// DELETE all records from the table "jsbV2_user_songs"
const sql = "DELETE FROM jsbV2_user_songs"
````

##### "SELECT" Queries

Basic queries
````javascript
const sql = "SELECT title FROM film WHERE title='Iron Man'"
````
````javascript
const sql = "SELECT * FROM film WHERE title LIKE 'Iron Man'"
````
Query for __login form__
````javascript
const sql = 'SELECT username, password, id FROM users WHERE username=?'

db.queryDB(sql, [username, password]).then((results) => {
    console.log(results);
})
````

Another __Login Form__ example:
````javascript
db.queryDB('SELECT user_name, password FROM users WHERE user_name = ? AND password = ?', [username, password]).then((results) => {
    console.log(results);
})
````
##### Escaped arguments queries
Basic select with escaped values:
````javascript
const args = 'The Avengers'
const sql = 'SELECT title FROM film WHERE title=?';
````



Escape with `mysql.escape()` method.
````javascript
var args = 'Mountain 21';
var sql = 'SELECT * FROM customers WHERE address = ' + mysql.escape(args);
````

Select with the __`AND`__ operator and escaped values:
````javascript
const args = ['Tony', 'Stark'];
const sql = 'SELECT first_name, last_name FROM customer WHERE first_name=? AND last_name=?';
````
Select with the __`OR`__ operator and escaped values:
````javascript
const args s= ['iron man', '3']
const sql = 'SELECT title, release_date FROM movies WHERE title=? OR id=?'
````


Basic select with wildcard search pattern:
````javascript
const args = '%veng%' // Finds any values that have "veng" in any position
const sql = 'SELECT title FROM film WHERE title LIKE ?';
````
##### Wildcard example queries
* The `LIKE` operator is used in a `WHERE` clause to search for a specified pattern in a column.

Basic wildcard queries 
````javascript
const args = '%veng%' // Finds any values that have "veng" in any position
const sql = 'SELECT title FROM film WHERE title LIKE ?';
````

````javascript
const args = '%chopin%';
const sql = 'SELECT * FROM mytable WHERE artist LIKE ?';
````

Wildcard query with multiple arguments using the  __`AND`__ operator:
````javascript
const search = ['%ma%', '%s%'];
const sql = 'SELECT first_name, last_name FROM customer WHERE first_name LIKE ? AND last_name LIKE ?';
````
##### "INSERT" Table Queries
>When doing an INSERT query, the`arguments`parameter must be in ARRAY format!

Basic INSERT (all in one query string)
````javascript
var sql = "INSERT INTO movies (title, release_date) VALUES ('Iron Man', 'May 2, 2008')";
````
````javascript
var sql = "INSERT INTO customers (name, address) VALUES ('Company Inc', 'Highway 37')";
````

INSERT into a single column:
````javascript
var args = ['SpiderMan: Far From Home'];
var sql = "INSERT INTO movies (title) VALUES ?";
````

INSERT INTO multiple columns 
````javascript
var args = ['Iron Man', 'May 2, 2008'];
var sql = "INSERT INTO movies (title, release_date) VALUES ?";
````
INSERT MANY records
````javascript
var args = [
    ['Iron Man', 'May 2, 2008'], 
    ['The Incredible Hulk', 'June 13, 2008'],
    ['Iron Man 2', 'May 7, 2010'],
    ['Thor', 'May 6,  2011'],
    ['Captain America: The First Avenger', 'July 22, 2011'],
    ['The Avengers', 'May 4, 2012'],
    ['Iron Man 3', 'May 3, 2013'],
    ['Thor: The Dark World', 'November 8, 2013'],
    ['Captain America: The Winter Soldier', 'April 4, 2014'],
    ['Guardians of the Galaxy', 'August 1, 2014'],
    ['Avengers: Age of Ultron', 'May 1, 2015'],
    ['Ant-Man', 'July 17, 2015'],
    ['Captain America: Civil War', 'May 6, 2016'],
    ['Doctor Strange', 'November 4, 2016']
  ];
  var sql = "INSERT INTO movies (title, release_date) VALUES ?"

`````




##### "UPDATE" Tables Queries

>When doing an UPDATE query, the`arguments`parameter must be in ARRAY format!

Update a single record:
````javascript
// Change address from 'Canyon 123' to 'Valley 345'
const sql = "UPDATE customers SET address = 'Canyon 123' WHERE address = 'Valley 345'";
````
Update multiple records
````javascript
// Update ALL records where the artist contains 'chopin' 
const sql = "UPDATE mytable SET period = 'Romantic' WHERE artist LIKE '%chopin%'"
````

Use `mysql.escape()` to __*dynamically*__ set a `COLUMN` parameter
````javascript
var args = ['26']
var date = 'July 22, 2011'
var sql = "UPDATE movies SET release_date = " +mysql.escape(date)+" WHERE id = ?";
````

### Live Code Examples

Here are some examples of live code using this library:
###### JSB Harpsichord Application

This is the main route that handles uploading new songs or updating existing songs. It checks to see if the song already exists, if is does then `update()` is called. If  not then `upload()` is called.
````javascript
// Handle Save/Upload songs requests
app.post('/jsb_api/save', (req, res) => {
    const sql = 'SELECT * FROM jsbV2_user_songs WHERE id = ?';
    const args = [req.body.id];
    db.queryDB(sql, args).then((results) => {
        let parsed = JSON.parse(results);
        let parsedLen = parsed.length;
        console.log("Length: " + parsedLen);
        if (!parsedLen < 1) {
            update(req, res, parsed[0].id)
            console.log('UPDATE!!');
            return;
        }
        upload(req, res);
        console.log("UPLOAD!!");
    });
});
````

This is the insert fuction from the J.S.B. Harpsichord application backend. This route inserts a user song into the database and returns confirmation of a successfull upload.
````javascript
function upload(req, res) {
    let sql = "INSERT INTO jsbV2_user_songs (song_data) VALUES ?";
    let args = [JSON.stringify(req.body)];
    db.queryDB(sql, args).then((results) => {
        let resultsObj = {};
        resultsObj.message = `Song  was UPLOADED! Song ID: ${JSON.parse(results).insertId}`;
        res.json(resultsObj);
    });
}
````

Here is the update songs function from the J.S.B. Harpsichord application backend. This code updates songs users have already created & uploaded.
````javascript
function update(req, res, songID) {
    console.log("songID: " + songID);
    let sql = "UPDATE jsbV2_user_songs SET song_data = " + mysql.escape(JSON.stringify(req.body)) + " WHERE id = ?";
    let args = [songID];
    db.queryDB(sql, args).then((results) => {
        let resultsObj = {};
        resultsObj.message = `Song ${songID} was UPDATED!`;
        res.json(resultsObj);
        console.log(`Song ${songID} has been UPDATED!!`);
    });
}
````

[Back To The Top](#read-me-template)

---

## References

* [MySQL Module Documentation](https://www.npmjs.com/package/mysql)
* [Node.js MySQL Tutorial at w3schools.com ](https://www.w3schools.com/nodejs/nodejs_mysql.asp)
* [MySQL Reference Guide at w3schools.com](https://www.w3schools.com/nodejs/nodejs_mysql.asp)
* [Wikepedia article about Connection Pools](https://en.wikipedia.org/wiki/Connection_pool)



[Back To The Top](#read-me-template)

---

## License

MIT License

Copyright (c) [2021] [Charles D. Kinney]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

[Back To The Top](#read-me-template)

---

## Author Info

- LinkedIn - [Charles Kinney](https://www.linkedin.com/in/charles-kinney-3468311b7/)
- Website - [Charles D. Kinney](https://charlesdkinney.com)

[Back To The Top](#read-me-template)

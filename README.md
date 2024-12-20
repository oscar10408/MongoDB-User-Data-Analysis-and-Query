# Project 3: MongoDB

## Introduction

In Project 3, I worked with MongoDB, a NoSQL database system, to explore its capabilities using a dataset similar to the one from Project 2. The project is divided into two parts:

- **Part A:** I extracted data from tables in the Fakebook database and exported a JSON file (`output.json`) containing information about users.
- **Part B:** I imported `output.json` (or a provided sample file) into MongoDB and created a collection of users. I then wrote seven queries to interact with the MongoDB collection.

# Part A: Export Oracle Database to JSON

## Introduction to JSON

JSON (JavaScript Object Notation) is a lightweight data-interchange format that represents data as key-value pairs, similar to a `std::map` in C++. However, JSON values can have varying data types within the same object. Below is an example of how JSON objects can be initialized and accessed in JavaScript:

```javascript
var student1 = { "Name" : "John Doe", "Age" : 21, "Major" : ["CS", "Math"] }
student1["Name"]; // returns "John Doe"
```
You can also create JSON arrays, which are collections of JSON objects:
```javascript
var students = [
  { "Name" : "John Doe", "Age" : 21, "Major" : ["CS", "Math"] },
  { "Name" : "Richard Roe", "Age" : 22, "Major" : ["CS"] },
  { "Name" : "Joe Public", "Age" : 21, "Major" : ["CE"] }
];
students[0]["Name"]; // returns "John Doe"
```

## Export to JSON

In **Part A** of Project 3, the task is to query the **Project 3 Fakebook Oracle database** to export comprehensive information about each user into a **JSON array**. The dataset will contain **800 JSON objects**, each representing a user, and the output should be stored in a `JSONArray`. Each object will include detailed user information such as personal details, friends, current location, and hometown.

## Data Fields

Each JSON object representing a user should contain the following fields:

- **user_id**: (int) The unique identifier for the user.
- **first_name**: (string) The first name of the user.
- **last_name**: (string) The last name of the user.
- **gender**: (string) The gender of the user.
- **YOB**: (int) The year of birth of the user.
- **MOB**: (int) The month of birth of the user.
- **DOB**: (int) The day of birth of the user.
- **friends**: (JSONArray) A list containing the user IDs of the user's friends who have a larger user ID than the current user. The friends' relationships are assumed to be symmetric, so if user 700 is friends with user 25, it will only appear in the list for user 25 and not for user 700.
- **current**: (JSONObject) Contains information about the user's current location:
  - **city**: (string) The current city of the user.
  - **state**: (string) The current state of the user.
  - **country**: (string) The current country of the user.
- **hometown**: (JSONObject) Contains information about the user's hometown:
  - **city**: (string) The hometown city of the user.
  - **state**: (string) The hometown state of the user.
  - **country**: (string) The hometown country of the user.

### Special Considerations:
- If a user has no friends, the `friends` key should contain an empty `JSONArray` (`[]`).
- If the user has no `current` or `hometown` information, those keys should contain empty JSON objects (`{}`).

## Example JSON Output

Below is an example of one user element in the resulting JSON array:
```javascript
 {
 "MOB": 10,
 "hometown": {
 "country": "Middle Earth",
 "city": "Linhir",
 "state": "Gondor"
 },
 "current": {
 "country": "Middle Earth",
 "city": "Caras Galadhon",
 "state": "Lothlorien"
 },
 "gender": "female",
 "user_id": 744,
 "DOB": 14,
 "last_name": "MARTINEZ",
 "first_name": "Lily",
 "YOB": 516,
 "friends": [754, 760, 772, 782]
 }
```
---
## Starter Files

### GetData.java
- **Purpose**: This file contains the core implementation for querying the **Oracle database** and exporting the results as a **JSON array**. You need to implement the `toJSON()` function, which queries the `users`, `friends`, and `cities` tables to retrieve the necessary data. 
- **Note**: The `writeJSON()` function will take care of converting your output into a **JSON string** and store it in `output.json`.

### Main.java
- **Purpose**: This file serves as the main driver for running Part A. You can use it to execute your program. After running `Main.java`, an output file named `output.json` will be generated. 
- **Note**: Modify the `oracleUserName` and `password` static variables with your own Oracle credentials:
  ```java
  static String oracleUserName = "uniqname"; // replace with your uniqname
  static String password = "password"; // replace with your Oracle password
  ```

### Makefile
- **Purpose**:Once you have implemented GetData.java and modified Main.java , you can compile and run your program.
  ```bash
  $make complie
  $make run
  ```

### sample.json
This file contains the JSON data generated from running the instructor's implementation of the `toJSON()` function in `GetData.java`. While comparing your output (`output.json`) with `sample.json`, **do not use a simple `diff` command** because JSON arrays may come out in different orders between runs. 

However, both `output.json` and `sample.json` should contain the **same elements** within their JSON arrays. To properly compare the content of JSON files, you can use command-line JSON diff processors like:

- **jd** (available on GitHub and online)
- **deepdiff** 

These tools allow you to correctly compare the contents of the two files.

### Required JAR Files
The following jar packages are used to compile your code:

- **json_simple-1.1.jar**: For handling JSON data.
- **json-20151123.jar**: For JSON manipulation.
- **ojdbc6.jar**: JDBC driver for connecting to the Oracle database.

These jars are essential for compiling and running the project. **Do not modify** these files.

### Wrapping Up
- **Part A** and **Part B** are independent of each other. You can set up your MongoDB database for **Part B** using the `sample.json` file to test your MongoDB queries.
- The **Autograder testing** for Part B does not require a correct `output.json` from Part A, so you can focus on completing Part B after finishing Part A.
- If desired, you can submit `GetData.java` from **Part A** to the Autograder without completing Part B.

# Part B: MongoDB Queries
## Introduction to MongoDB
MongoDB is a document-oriented noSQL DBMS. Each document in MongoDB is one JSON object, with key-value pairs of data, just like how a tuple in SQL has fields of data. Each collection in MongoDB is one JSON array of multiple documents, just like how a table in SQL has multiple tuples. Refer to the following table for some high-level differences between SQL and MongoDB

| **SQL**                                           | **MongoDB**                                           |
|---------------------------------------------------|-------------------------------------------------------|
| **Tuple**                                         | **Document** (Represented as a JSON object)          |
| **Relation/Table**                                | **Collection** (Represented as a JSON array)         |
| `SELECT * FROM Users;`                            | `db.users.find();`                                    |
| `SELECT * FROM Users WHERE name = 'John' AND age = 50;` | `db.users.find({name: 'John', age: 50});`            |
| `SELECT user_id, addr FROM Users WHERE name = 'John';` | `db.users.find({name: 'John'}, {user_id: 1, addr: 1, _id: 0});` |
| **Output Example**                                | **Output Example**                                    |
| `{user_id: 1, addr: 1, _id: 0}`                   | `{user_id: 1, addr: 1, _id: 0}`                       |

### Import JSON to MongoDB

In this section of the project, the goal is to import data into a MongoDB database using either the `sample.json` or `output.json` file.

1. **Prepare your environment**:
   - Make sure you are in the folder where the `sample.json` (or `output.json`) and `Makefile` are located.

2. **Update the Makefile**:
   - Open the `Makefile` and update it with your MongoDB password.
   
3. **Import the Data**:
   - Depending on whether you want to use the provided sample data or your own generated data, run one of the following commands in the terminal:
   
   ```bash
   $ make setupsampledb  # to load the user collection using sample.json
   $ make setupmydb      # to load the user collection using output.json
   ```
   Refer to the Makefile for the details on the actual commands. Please do not modify the –collection users field. On success, you should
 have imported 800 user documents. As a reminder, sample.json is correct and given in the starter files. output.json is generated by your
 code from Part A.


# Queries
## Query 1: Townspeople
 In this query, we want to find all users whose hometown city is the specified city . The result is to be returned as a JavaScript array of user
 ids. The order of user ids does not matter.

## Query 2: Flatten Friends
 In Part A, we created a friends array for every user using JDBC. Each user (JSON object) has friends (JSON array) that contains all the
 user_id s representing friends of the current user who have a larger user_id . In this query, we want to restore the friendship information
 into a friend pair table format.

Create a collection called flat_users . Documents in the collection follow this schema:
```javascript
{"user_id": xxx, "friends": xxx}
```
 The query would produce 3 documents (JSON objects) and store them in the collection flat_users :
```javascript
{"user_id": 100, "friends": 120},
{"user_id": 100, "friends": 200},
{"user_id": 100, "friends": 300}
```
## Query 3: City Dwellers
 Create a collection named cities . Each document in the collection should contain two fields: a field called _id holding the city name,
 and a users field holding an array of user_id s who currently live in that city. The user_id s do not need to be sorted but should be
 distinct. For example, if users 10, 20 and 30 live in Bucklebury, the following document will be in the collection cities :
 ```javascript
 {"_id": "Bucklebury", "users": [10, 20, 30]}
```
## Query 4: Matchmaker
 The goal of this query is to find all user ID pairs (A, B) that meet the following criteria:
 - **User A** is male, and **User B** is female.
 - The difference between their **year of birth (YOB)** is less than a specified threshold, **year_diff**.
 - User A and User B are **not friends**.
 - Both users are from the same **hometown.city**.

 The query should return a JSON array of pairs, where each pair consists of two user IDs. The order of the user IDs does not matter.

## Query 5: Oldest Friends
 This query is designed to identify the oldest friend for each user who has friends. The age of friends is determined using the **year of birth (YOB)** field, and in case of a tie (i.e., two friends have the same YOB), the friend with the smallest **user_id** is considered the oldest.

### Key Requirements:
- The query will only consider **friends** whose **user_id** is greater than the **user_id** of the current user.
- The query must take into account all existing friendships, and if needed, recreate the required collections (like `flat_users` and `cities`) in subsequent queries.
  
### Expected Output:
 ```javascript
 {user_id1: user_idx,
 user_id2: user_idy,
 ...}
 ```

## Query 6: Average Friend Count
 This query calculates the average number of friends a user has in the `users` collection and returns a decimal number. It takes into account users with **0 friends**, treating the number of friends a user has as the count of users in their friend list. **Users with lower IDs** are excluded from the count, as they aren't present in the friend list of higher-ID users.

### Key Requirements:
 - The number of friends is determined by the size of each user's **friends list**.
 - Users who have **0 friends** should be included in the calculation.
 - The result should be a decimal, and **do not round** the result to an integer.


## Query 7: Birth Months using Aggregate
 In this query, the goal is to use the `aggregate` command to create a collection called **countbymonth**, which will provide information about the number of users born in each month.

### Key Requirements:
 - The collection **countbymonth** should include 12 records, one for each month (MOB ranging from 1 to 12).
 - Each record in the collection should contain two fields:
   - **MOB**: The month number, which should be a value between 1 and 12.
   - **borncount**: The number of users born in that specific month.
 - The records must be **sorted by ascending order of MOB** (i.e., from month 1 to month 12).
 - The **borncount** values should sum up to the total number of users in the original collection.
 - The function should **not return anything** but should create the collection.


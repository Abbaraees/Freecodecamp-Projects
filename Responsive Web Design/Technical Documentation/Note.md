* Introduction
A SQL injection attack consists of insertion or “injection” of a SQL query via the input data from 
the client to the application. A successful SQL injection exploit can read sensitive data from the 
database, modify database data (Insert/Update/Delete), execute administration operations on the 
database (such as shutdown the DBMS), recover the content of a given file present on the DBMS file 
system and in some cases issue commands to the operating system. SQL injection attacks are a type of 
injection attack, in which SQL commands are injected into data-plane input in order to affect the 
execution of predefined SQL commands.

* How SQL injection Works
The primary form of SQL injection consists of direct insertion of code into user-input variables that 
are concatenated with SQL commands and executed. A less direct attack injects malicious code into 
strings that are destined for storage in a table or as metadata. When the stored strings are subsequently 
concatenated into a dynamic SQL command, the malicious code is executed.

The injection process works by prematurely terminating a text string and appending a new command. 
Because the inserted command may have additional strings appended to it before it is executed, 
the malefactor terminates the injected string with a comment mark "--". Subsequent text is ignored at 
execution time.

* Threat Modeling
    SQL injection attacks allow attackers to spoof identity, tamper with existing data, cause repudiation issues such as voiding transactions or changing balances, allow the complete disclosure of all data on the system, destroy the data or make it otherwise unavailable, and become administrators of the database server.
    SQL Injection is very common with PHP and ASP applications due to the prevalence of older functional interfaces. Due to the nature of programmatic interfaces available, J2EE and ASP.NET applications are less likely to have easily exploited SQL injections.
    The severity of SQL Injection attacks is limited by the attacker’s skill and imagination, and to a lesser extent, defense in depth countermeasures, such as low privilege connections to the database server and so on. In general, consider SQL Injection a high impact severity.

* Examples
Example 1

In SQL: select id, firstname, lastname from authors

If one provided: Firstname: evil'ex and Lastname: Newman

the query string becomes:

select id, firstname, lastname from authors where firstname = 'evil'ex' and lastname ='newman'

which the database attempts to run as:

Incorrect syntax near il' as the database tried to execute evil.

Example 2
 Consider an application that lets users log in with a username and password. If a user submits the username wiener and the password bluecheese, the application checks the credentials by performing the following SQL query:
SELECT * FROM users WHERE username = 'wiener' AND password = 'bluecheese'

If the query returns the details of a user, then the login is successful. Otherwise, it is rejected.

Here, an attacker can log in as any user without a password simply by using the SQL comment sequence -- to remove the password check from the WHERE clause of the query. For example, submitting the username administrator'-- and a blank password results in the following query:
SELECT * FROM users WHERE username = 'administrator'--' AND password = ''

This query returns the user whose username is administrator and successfully logs the attacker in as that user. 

* How to detect SQL injection vulnerabilities

The majority of SQL injection vulnerabilities can be found quickly and reliably using Burp Suite's web vulnerability scanner.

SQL injection can be detected manually by using a systematic set of tests against every entry point in the application. This typically involves:

    Submitting the single quote character ' and looking for errors or other anomalies.
    Submitting some SQL-specific syntax that evaluates to the base (original) value of the entry point, and to a different value, and looking for systematic differences in the resulting application responses.
    Submitting Boolean conditions such as OR 1=1 and OR 1=2, and looking for differences in the application's responses.
    Submitting payloads designed to trigger time delays when executed within an SQL query, and looking for differences in the time taken to respond.
    Submitting OAST payloads designed to trigger an out-of-band network interaction when executed within an SQL query, and monitoring for any resulting interactions.

* How to prevent SQL injection

Most instances of SQL injection can be prevented by using parameterized queries (also known as prepared statements) instead of string concatenation within the query.

The following code is vulnerable to SQL injection because the user input is concatenated directly into the query:
String query = "SELECT * FROM products WHERE category = '"+ input + "'";
Statement statement = connection.createStatement();
ResultSet resultSet = statement.executeQuery(query);

This code can be easily rewritten in a way that prevents the user input from interfering with the query structure:
PreparedStatement statement = connection.prepareStatement("SELECT * FROM products WHERE category = ?");
statement.setString(1, input);
ResultSet resultSet = statement.executeQuery();
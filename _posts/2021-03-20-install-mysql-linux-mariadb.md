---
layout: post
title: "Install MySQL on Linux with MariaDB"
description: "Installing MySQL on Fedora Linux with MariaDB. Quick SQL tutorial."
author: tom
image: assets/images/1.jpg
tags: [analytics, mariadb, mysql, linux]
---

Installing MySQL on Fedora Linux using MariaDB. Since MySQL was sold to Oracle.
MariaDB is a fork and open source version of MySQL.

## Install MySQL on Fedora using MariaDB

    $ sudo dnf install mariadb mariadb-server mariadb-devel

## Start MariaDB

Run this to start the process:

    $ sudo systemctl start mariadb

Later if you try to connect to `MariaDB` and get this error:

    ERROR 2002 (HY000): Can't connect to local MySQL server through socket

This means that you didn't start the process. Or you restarted the machine and the process didn't start.

For reference:

* Stop: `sudo systemctl stop mariadb`
* Restart: `sudo systemctl restart mariadb`
* Status: `sudo systemctl status mariadb`
* Start at boot: `sudo systemctl enable mariadb`

This the output of checking status with `sudo systemctl status mariadb`:

    mariadb.service - MariaDB 10.5 database server
    Loaded: loaded (/usr/lib/systemd/system/mariadb.service; disabled; vendor preset: disabled)
    Active: active (running) since Wed 2021-05-05 09:24:50 EDT; 9min ago
        Docs: man:mysqld(8)
            https://mariadb.com/kb/en/library/systemd/

    Process: 1137071 ExecStartPre=/usr/libexec/mysql-check-socket (code=exited, status=0/SUCCESS)
    Process: 1137094 ExecStartPre=/usr/libexec/mysql-prepare-db-dir mariadb.service
    Process: 1137139 ExecStartPost=/usr/libexec/mysql-check-upgrade
    Main PID: 1137129 (mysqld)
        Status: "Taking your SQL requests now..."
        Tasks: 8 (limit: 9124)
    Memory: 124.4M
        CPU: 343ms
    CGroup: /system.slice/mariadb.service

## Config MariaDB

    $ sudo mysql_secure_installation

In order to log into MariaDB to secure it, we'll need the current password for the root user. If you've just installed MariaDB, and haven't set the root password yet, you should just press enter here.

    Enter current password for root (enter for none):

Setting the root password or using the unix_socket ensures that nobody can log into the MariaDB root user without the proper authorisation. You already have your root account protected, so you can safely answer 'n'.

    Switch to unix_socket authentication [Y/n] n
    ... skipping.

You already have your root account protected, so you can safely answer 'n'.

    Change the root password? [Y/n] n
    ... skipping.

By default, a MariaDB installation has an anonymous user, allowing anyone to log into MariaDB without having to have a user account created for them. This is intended only for testing, and to make the installation go a bit smoother. You should remove them before moving into a production environment.

    Remove anonymous users? [Y/n] y
    ... Success!

Normally, root should only be allowed to connect from 'localhost'. This ensures that someone cannot guess at the root password from the network.

    Disallow root login remotely? [Y/n] y
    ... Success!

By default, MariaDB comes with a database named 'test' that anyone can access. This is also intended only for testing, and should be removed before moving into a production environment.

    Remove test database and access to it? [Y/n] y
    Dropping test database...
    ... Success!
    Removing privileges on test database...
    ... Success!

Reloading the privilege tables will ensure that all changes made so far will take effect immediately.

    Reload privilege tables now? [Y/n] y
    ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB installation should now be secure.

Thanks for using MariaDB!


## Import a database schema

Login with your database user root using sudo:

    $ sudo mysql -uroot -p

Create a new database:

    mysql> create database db1;

Create a user with the syntax `create user [username] identified by [password]`:

    mysql> create user 'homer'@'localhost' identified by 'simpson';

Grant access:

    mysql> grant all on db1.* to 'homer'@'localhost';

Exit:

    mysql> exit

Import the schema

    $ mysql -u homer -p db1 < schema.sql

Import the data if this is in SQL script:

    $ mysql -u homer -p db1 < data.sql

Connect to the database:

    $ mysql -u homer -p db1

## Import a database schema if grant access is set on the schema script

I worked on a project where grant access was set on the `schema.sql` script.

    schema.sql
    CREATE USER IF NOT EXISTS homer@localhost IDENTIFIED BY `simpson`;
    DROP DATABASE IF EXISTS `db1`;
    SET default_storage_engine=InnoDB;
    SET NAMES utf8mb4 COLLATE utf8mb4_unicode_ci;

    CREATE DATABASE IF NOT EXISTS db1
        DEFAULT CHARACTER SET utf8mb4
        DEFAULT COLLATE utf8mb4_unicode_ci;
    USE db1;

    GRANT SELECT, INSERT, UPDATE, DELETE, FILE ON *.* TO 'homer'@'localhost';
    GRANT ALL PRIVILEGES ON `homer`.* TO 'homer'@'localhost';
    GRANT ALL PRIVILEGES ON `db1`.* TO 'homer'@'localhost';


This means that this didn't work, because the user was not previously created
or granted access:

    $ mysql -u homer -p db1 < schema.sql

I got this error:

    ERROR 1227 (42000) at line 2: Access denied; you need (at least one of) the
    CREATE USER privilege(s) for this operation

Login with your database user root using sudo:

    $ sudo mysql -uroot -p

Create a new database:

    mysql> create database db1;

Import like this:

    $ sudo mysql -uroot -p db1 < schema.sql

Since the `schema.sql` creates the user and grants them access. Then you can
run other scripts like this:

    $ mysql -u homer -p db1 < data.sql
    $ mysql -u homer -p db1
    Enter password: 

Output:

    Reading table information for completion of table and column names
    You can turn off this feature to get a quicker startup with -A

    Welcome to the MariaDB monitor.  Commands end with ; or \g.
    Your MariaDB connection id is 55
    Server version: 10.4.17-MariaDB MariaDB Server

    Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    MariaDB [db1]>

## Using the help command

The `help` command helps you with syntax. Use like this:

    MariaDB [db1]> help contents

This specific command shows a menu of these options:

    For more information, type 'help <item>', where <item> is one of the
    following
    categories:
    Account Management
    Administration
    Compound Statements
    Data Definition
    Data Manipulation
    Data Types
    Functions
    Functions and Modifiers for Use with GROUP BY
    Geographic Features
    Help Metadata
    Language Structure
    Optimization and Indexes
    Plugins
    Procedures
    Sequences
    Storage Engines
    Table Maintenance
    Transactions
    User-Defined Functions
    Utility

Here are a few `help` commands.

Run `help functions`:

    > help Functions
    You asked for help about help category: "Functions"
    For more information, type 'help <item>', where <item> is one of the following
    categories:
    Bit Functions
    Comparison Operators
    Control Flow Functions

This doesn't work `help 'Language Structure'`:

    > help 'Language Structure'
    Nothing found
    Please try to run 'help contents' for a list of all accessible topics

This works `help 'Data Types'`:

    > help 'Data Types'
    You asked for help about help category: "Data Types"
    For more information, type 'help <item>', where <item> is one of the following
    topics:
    AUTO_INCREMENT
    BIGINT
    BINARY
    BIT
    BLOB
    ...

Run `help 'create user'`:

    > help 'create user'
    Name: 'CREATE USER'
    Description:
    Syntax
    ------
    CREATE [OR REPLACE] USER [IF NOT EXISTS]
    user_specification [,user_specification ...]
    [REQUIRE {NONE | tls_option [[AND] tls_option ...] }]
    ...


## Enable Automcomplete on SQL prompt

It starts to get annoying when you have to type a long table name. Isn't there
an autocomplete like the Linux prompt?

There are three options:

* Type `rehash` in the SQL prompt: `MariaDB> rehash`
* Type `\#` in the SQL prompt: `MariaDB> \#`
* Start MySQL with this flag: `--auto-rehash`

You can also create a config file in your home directory called `.my.cnf`:

    [mysql]
    auto-rehash

## Foreign key constraint is incorrectly formed

I created another schema with a foreign key specified with a circular
reference:

    CREATE TABLE EMPLOYEE(
        Fname varchar(15) NOT NULL,
        Minit char,
        Lname varchar(15) NOT NULL,
        Ssn char(9) NOT NULL,
        Bdate date,
        Address varchar(30),
        Sex char,
        Salary decimal(10,2),
        Super_ssn char(9),
        Dno int NOT NULL DEFAULT 1,
        PRIMARY KEY(Ssn)
        );

    ALTER TABLE EMPLOYEE
        ADD CONSTRAINT fk_Employee_Super_ssn_Employee_Ssn FOREIGN
        KEY(Super_ssn) REFERENCES EMPLOYEE(Ssn) ON DELETE SET NULL ON UPDATE
        CASCADE,

However, when I imported the schema, I got this error:

    ERROR 1005 (HY000) at line 76: Can't create table `company`.`EMPLOYEE`
    (errno: 150 "Foreign key constraint is incorrectly formed")

This was fixed by adding this before the `ALTER`:

    SET FOREIGN_KEY_CHECKS=OFF;

And this after the `ALTER`:

    SET FOREIGN_KEY_CHECKS=ON;

## Cannot truncate a table referenced in a foreign key constraint

I have a SQL script to import the dataset into the database tables. It deletes
all the data with `TRUNCATE` such as:

    TRUNCATE TABLE EMPLOYEE;
    TRUNCATE TABLE DEPARTMENT;

However, when I tried running the script the output was an error:

    Cannot truncate a table referenced in a foreign key constraint

The solution was similar as above, set on/off `FOREIGN_KEY_CHECKS`:

    SET FOREIGN_KEY_CHECKS=OFF;

    TRUNCATE TABLE EMPLOYEE;
    TRUNCATE TABLE DEPARTMENT;

    SET FOREIGN_KEY_CHECKS=ON;

## MySQL basics

Show users in the database:

    $ sudo mysql -uroot -p
    MariaDB> show databases;

    Database
    --------
    db1
    information_schema
    mysql
    performance_schema

    MariaDB> select user from mysql.user;

    User
    ----
    homer
    mariadb.sys
    mysql
    root

Show tables in the `mysql` database:

    MariaDB> use mysql;
    MariaDB [mysql]> show tables;

<details>
    <summary>show tables output</summary>
    <pre>

    MariaDB [mysql]> show tables;
    +---------------------------+
    | Tables_in_mysql           |
    +---------------------------+
    | column_stats              |
    | columns_priv              |
    | db                        |
    | event                     |
    | func                      |
    | general_log               |
    | global_priv               |
    | gtid_slave_pos            |
    | help_category             |
    | help_keyword              |
    | help_relation             |
    | help_topic                |
    | index_stats               |
    | innodb_index_stats        |
    | innodb_table_stats        |
    | plugin                    |
    | proc                      |
    | procs_priv                |
    | proxies_priv              |
    | roles_mapping             |
    | servers                   |
    | slow_log                  |
    | table_stats               |
    | tables_priv               |
    | time_zone                 |
    | time_zone_leap_second     |
    | time_zone_name            |
    | time_zone_transition      |
    | time_zone_transition_type |
    | transaction_registry      |
    | user                      |
    +---------------------------+
    </pre>

</details>

Describe the `user` table in the `mysql` database:

    MariaDB [mysql]> desc user;

<details>
    <summary>desc user, some of the output</summary>
    <pre>

    +------------------------+------------+------+
    | Field                  | Type       | Null |
    +------------------------+------------+------+
    | Host                   | char(60)   | NO   |
    | User                   | char(80)   | NO   |
    | Password               | longtext   | YES  |
    | Select_priv            | varchar(1) | YES  |
    | Insert_priv            | varchar(1) | YES  |
    | Update_priv            | varchar(1) | YES  |
    | Delete_priv            | varchar(1) | YES  |
    | Create_priv            | varchar(1) | YES  |
    | Drop_priv              | varchar(1) | YES  |
    | Reload_priv            | varchar(1) | YES  |
    | Shutdown_priv          | varchar(1) | YES  | 
    | Process_priv           | varchar(1) | YES  | 
    | File_priv              | varchar(1) | YES  | 
    | Grant_priv             | varchar(1) | YES  | 
    | References_priv        | varchar(1) | YES  | 
    | Index_priv             | varchar(1) | YES  | 
    | Alter_priv             | varchar(1) | YES  | 
    | Show_db_priv           | varchar(1) | YES  | 
    | Super_priv             | varchar(1) | YES  | 
    | Create_tmp_table_priv  | varchar(1) | YES  | 
    | Lock_tables_priv       | varchar(1) | YES  | 
    | Execute_priv           | varchar(1) | YES  | 
    | Repl_slave_priv        | varchar(1) | YES  | 
    | Repl_client_priv       | varchar(1) | YES  | 
    | Create_view_priv       | varchar(1) | YES  | 
    | Show_view_priv         | varchar(1) | YES  | 
    | Create_routine_priv    | varchar(1) | YES  | 
    | Alter_routine_priv     | varchar(1) | YES  | 
    | Create_user_priv       | varchar(1) | YES  | 
    | Event_priv             | varchar(1) | YES  | 
    | Trigger_priv           | varchar(1) | YES  | 
    | Create_tablespace_priv | varchar(1) | YES  | 
    | Delete_history_priv    | varchar(1) | YES  | 
    | ssl_type               | varchar(9) | YES  | 
    </pre>

</details>

Show grants for a user:

    MariaDB [mysql]> show grants for 'homer'@'localhost';

    +---------------------------
    Grants for homer@localhost
    +---------------------------
    GRANT USAGE ON *.* TO `homer`@`localhost` IDENTIFIED BY PASSWORD '...
    GRANT ALL PRIVILEGES ON `db1`.* TO 'homer'@'localhost';

## Inserting NULL values

When the schema is described as this example:

    CREATE TABLE Store(
        Store_Number varchar(50) NOT NULL,
        Team_Size int(3) NULL DEFAULT NULL,
        primary key (Store_Number)
    );

And you insert a record when you don't have a value. In this case you don't know the value of `Team_Size`:

    INSERT INTO Store(Store_Number,Team_Size) VALUES('Store1',);

You might get this error:

    ERROR 1064 (42000): You have an error in your SQL syntax; check the manual
    that corresponds to your MariaDB server version for the right syntax
    to use near ')' at line 1

Since the schema for `Team_Size` is set to `NULL DEFAULT NULL`. Then insert the record using as value `DEFAULT` like this:

    INSERT INTO Store(Store_Number,Team_Size) VALUES('Store1',DEFAULT);

## Escaping strings with single quote

Given this schema;

    CREATE TABLE Holiday(
    `Date` date NOT NULL,
    Holiday_Name varchar(100) NOT NULL,
    primary key (`Date`,Holiday_Name)
    );

Inserting this with backticks:

    INSERT INTO Holiday(`Date`, Holiday_Name) VALUES('2011-01-01',`Veteran's Day`);

Results in this error:

    ERROR 1054 (42S22): Unknown column 'Veteran's Day' in 'field list'

Don't use backticks. Use single quotes and escape the single quote within the string with two single quotes:

    INSERT INTO Holiday(`Date`, Holiday_Name) VALUES('2011-01-01','Veteran''s Day');


Sources:
* [MariaDB on Fedora
    Developer](https://developer.fedoraproject.org/tech/database/mariadb/about.html)
* [MariaDB on Fedora wiki](https://fedoraproject.org/wiki/MariaDB)
* [Import mySQL database
    Digitalocean](https://www.digitalocean.com/community/tutorials/how-to-import-and-export-databases-in-mysql-or-mariadb)
* [mySQL docs](https://dev.mysql.com/doc/refman/8.0/en/creating-database.html)
* [MariaDB docs](https://mariadb.com/kb/en/help-command/)
* [Autocompletion in
    MySQL](https://stackoverflow.com/questions/8332338/autocompletion-in-the-mysql-command-line-client)
* [Foreign key incorrectly
    formed](https://stackoverflow.com/questions/8434518/mysql-foreign-key-constraint-is-incorrectly-formed-error)
* [Truncate a foreign key constrained
    table](https://stackoverflow.com/questions/5452760/how-to-truncate-a-foreign-key-constrained-table)
* [Escape quotes in MySQL](https://stackoverflow.com/a/9596819)

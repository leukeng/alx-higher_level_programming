0x0D. SQL - Introduction

Basic SQL statements: DDL and DML

SQL statements are divided into two major categories: data definition language (DDL) and data manipulation language (DML). Both of these categories contain far more statements than we can present here, and each of the statements is far more complex than we show in this introduction. If you want to master this material, we strongly recommend that you find a SQL reference for your own database software as a supplement to these pages.
Data definition language
DDL statements are used to build and modify the structure of your tables and other objects in the database. When you execute a DDL statement, it takes effect immediately.

The create table statement does exactly that:
        CREATE TABLE <table name> ( 
        <attribute name 1> <data type 1>,
        ...
        <attribute name n> <data type n>);
The data types that you will use most frequently are character strings, which might be called VARCHAR or CHAR for variable or fixed length strings; numeric types such as NUMBER or INTEGER, which will usually specify a precision; and DATE or related types. Data type syntax is variable from system to system; the only way to be sure is to consult the documentation for your own software.

The alter table statement may be used as you have seen to specify primary and foreign key constraints, as well as to make other modifications to the table structure. Key constraints may also be specified in the CREATE TABLE statement.
        ALTER TABLE <table name>
        ADD CONSTRAINT <constraint name> PRIMARY KEY (<attribute list>);
You get to specify the constraint name. Get used to following a convention of tablename_pk (for example, Customers_pk), so you can remember what you did later. The attribute list contains the one or more attributes that form this PK; if more than one, the names are separated by commas.

The foreign key constraint is a bit more complicated, since we have to specify both the FK attributes in this (child) table, and the PK attributes that they link to in the parent table.
        ALTER TABLE <table name>
        ADD CONSTRAINT <constraint name> FOREIGN KEY (<attribute list>)
        REFERENCES <parent table name> (<attribute list>);
Name the constraint in the form childtable_parenttable_fk (for example, Orders_Customers_fk). If there is more than one attribute in the FK, all of them must be included (with commas between) in both the FK attribute list and the REFERENCES (parent table) attribute list.

You need a separate foreign key definition for each relationship in which this table is the child.

If you totally mess things up and want to start over, you can always get rid of any object you’ve created with a drop statement. The syntax is different for tables and constraints.
        DROP TABLE <table name>;

        ALTER TABLE <table name>
        DROP CONSTRAINT <constraint name>;
This is where consistent constraint naming comes in handy, so you can just remember the PK or FK name rather than remembering the syntax for looking up the names in another table. The DROP TABLE statement gets rid of its own PK constraint, but won’t work until you separately drop any FK constraints (or child tables) that refer to this one. It also gets rid of all data that was contained in the table—and it doesn't even ask you if you really want to do this!

All of the information about objects in your schema is contained, not surprisingly, in a set of tables that is called the data dictionary. There are hundreds of these tables most database systems, but all of them will allow you to see information about your own tables, in many cases with a graphical interface. How you do this is entirely system-dependent.
Data manipulation language
DML statements are used to work with the data in tables. When you are connected to most multi-user databases (whether in a client program or by a connection from a Web page script), you are in effect working with a private copy of your tables that can’t be seen by anyone else until you are finished (or tell the system that you are finished). You have already seen the SELECT statement; it is considered to be part of DML even though it just retreives data rather than modifying it.

The insert statement is used, obviously, to add new rows to a table.
        INSERT INTO <table name>
        VALUES (<value 1>, ... <value n>);
The comma-delimited list of values must match the table structure exactly in the number of attributes and the data type of each attribute. Character type values are always enclosed in single quotes; number values are never in quotes; date values are often (but not always) in the format 'yyyy-mm-dd' (for example, '2006-11-30').

Yes, you will need a separate INSERT statement for every row.

The update statement is used to change values that are already in a table.
        UPDATE <table name>
        SET <attribute> = <expression>
        WHERE <condition>;
The update expression can be a constant, any computed value, or even the result of a SELECT statement that returns a single row and a single column. If the WHERE clause is omitted, then the specified attribute is set to the same value in every row of the table (which is usually not what you want to do). You can also set multiple attribute values at the same time with a comma-delimited list of attribute=expression pairs.

The delete statement does just that, for rows in a table.
        DELETE FROM <table name>
        WHERE <condition>;
If the WHERE clause is omitted, then every row of the table is deleted (which again is usually not what you want to do)—and again, you will not get a “do you really want to do this?” message.

If you are using a large multi-user system, you may need to make your DML changes visible to the rest of the users of the database. Although this might be done automatically when you log out, you could also just type:
        COMMIT;
If you’ve messed up your changes in this type of system, and want to restore your private copy of the database to the way it was before you started (this only works if you haven’t already typed COMMIT), just type:
        ROLLBACK;
Although single-user systems don’t support commit and rollback statements, they are used in large systems to control transactions, which are sequences of changes to the database. Transactions are frequently covered in more advanced courses.

Privileges
If you want anyone else to be able to view or manipulate the data in your tables, and if your system permits this, you will have to explicitly grant the appropriate privilege or privileges (select, insert, update, or delete) to them. This has to be done for each table. The most common case where you would use grants is for tables that you want to make available to scripts running on a Web server, for example:

BaseModel
=========

Base model for CodeIgniter, helps you with your database operations in the model. it auto guesses the table name from the model class name, saves you the hassle of soft deletes and more. BaseModel is also used by [BaseController](https://github.com/slax0rr/BaseModel).

The idea for the BaseModel came from Jamie Rumbelows [base model](https://github.com/jamierumbelow/codeigniter-base-model), with some additions and changes. At this point I would also like to thank [Marco Monteiro](https://github.com/mpmont) and [Sami Keinänen](https://github.com/skope) for their help.

If you run into issues or have questions/ideas, please submit a ticket here on [GitHub](https://github.com/slax0rr/BaseModel/issues).

This is still in development phase, but is available for public as early-beta. Please use with caution, I can not guarantee that something will not change along the way. It has only been tested with mySQL, and at the time will probably work only with mySQL.

Table of contents
=================
* [BaseModel](https://github.com/slax0rr/BaseModel/blob/develop/README.md#basemodel)
* [Table of contents](https://github.com/slax0rr/BaseModel/blob/develop/README.md#table-of-contents)
* [Install](https://github.com/slax0rr/BaseModel/blob/develop/README.md#install)
  * [Use the BaseModel](https://github.com/slax0rr/BaseModel/blob/develop/README.md#use-the-basemodel)
* [Properties](https://github.com/slax0rr/BaseModel/blob/develop/README.md#properties)
* [Table names](https://github.com/slax0rr/BaseModel/blob/develop/README.md#table-names)
  * [Guessing the table name](https://github.com/slax0rr/BaseModel/blob/develop/README.md#guessing-the-table-name)
* [Config constants](https://github.com/slax0rr/BaseModel/blob/develop/README.md#config-constants)
* [Database operations](https://github.com/slax0rr/BaseModel/blob/develop/README.md#database-operations)
  * [Inserting data](https://github.com/slax0rr/BaseModel/blob/develop/README.md#inserting-data)
  * [Getting data](https://github.com/slax0rr/BaseModel/blob/develop/README.md#getting-data)
  * [Updating data](https://github.com/slax0rr/BaseModel/blob/develop/README.md#updating-data)
  * [Deleting data](https://github.com/slax0rr/BaseModel/blob/develop/README.md#deleting-data)
* [SQL clauses](https://github.com/slax0rr/BaseModel/blob/develop/README.md#sql-clauses)
  * [GROUP BY](https://github.com/slax0rr/BaseModel/blob/develop/README.md#group-by)
  * [ORDER BY](https://github.com/slax0rr/BaseModel/blob/develop/README.md#order-by)
  * [LIMIT](https://github.com/slax0rr/BaseModel/blob/develop/README.md#limit)
* [Validation](https://github.com/slax0rr/BaseModel/blob/develop/README.md#validation)
  * [Run validation manually](https://github.com/slax0rr/BaseModel/blob/develop/README.md#run-validation-manually)
  * [Skipping validation](https://github.com/slax0rr/BaseModel/blob/develop/README.md#skipping-validation)
* [Queries on soft deleted rows](https://github.com/slax0rr/BaseModel/blob/develop/README.md#queries-on-soft-deleted-rows)
* [Results](https://github.com/slax0rr/BaseModel/blob/develop/README.md#results)
  * [Getting column data](https://github.com/slax0rr/BaseModel/blob/develop/README.md#getting-column-data)
  * [Number of rows](https://github.com/slax0rr/BaseModel/blob/develop/README.md#number-of-rows)
  * [Get all rows](https://github.com/slax0rr/BaseModel/blob/develop/README.md#get-all-rows)
  * [Traversing through rows](https://github.com/slax0rr/BaseModel/blob/develop/README.md#traversing-through-rows)
* [Errors](https://github.com/slax0rr/BaseModel/blob/develop/README.md#errors)
  * [Initialization of the Error class](https://github.com/slax0rr/BaseModel/blob/develop/README.md#initialization-of-the-error-class)
  * [Add an error](https://github.com/slax0rr/BaseModel/blob/develop/README.md#add-an-error)
  * [Has errors and count errors](https://github.com/slax0rr/BaseModel/blob/develop/README.md#has-errors-and-count-errors)
  * [Getting errors](https://github.com/slax0rr/BaseModel/blob/develop/README.md#getting-errors)
  * [Traversing through errors](https://github.com/slax0rr/BaseModel/blob/develop/README.md#traversing-through-errors)
* [ChangeLog](https://github.com/slax0rr/BaseModel/blob/develop/README.md#changelog)

Install
=======

The easiest way to install at the moment is to use [composer](https://getcomposer.org/), or by installing the [BaseController](https://github.com/slax0rr/BaseModel), version 0.2+, which has BaseModel listed as requirement.
Simply create composer.json file in your project root:
```
{
  "require": {
    "slaxweb/ci-basemodel": "0.3.*@dev"
  }
}
```

Then run **composer.phar install**. When finished, enable composer autoloader in *application/config/config.php* configuration file.

Use the BaseModel
-----------------

The BaseModel is meant to be extended by your models, so, instead of extending from *CI_Model*, extend from **\SlaxWeb\BaseModel\Model**:
```PHP
class Some_model extend \SlaxWeb\BaseModel\Model
```
With this, your BaseModel is ready to use.

Properties
==========

BaseModel provides you with the next set of properties you can set:
* **table** - The table of the model, although, BaseModel does auto-guess the table name from its own name.
* **tablePrefix** - The table prefix, if you use a special prefix, per model
* **primaryKey** - Defaults to "id", if you use something else as primary key, set it here
* **keyType** - Type of the key, Auto-Increment(default), UUID key, or a specific PHP or custom function you use to generate a primary key
* **keyFunc** - If you use a function to generate a primary key, assign it here, accepts type callable
* **keyFuncParams** - Parameters used by keyFunc function
* **softDelete** - Use soft delete, "hard" delete is default. Has 3 options, Soft delete status column, soft delete mark column, hard deletes
* **deleteCol** - If using soft delete mark column, define it here, default is "deleted"
* **statusCol** - If using soft delete status column, define the column here, default is "status"
* **deleteStatus** - Set the deleted status value for status column, default is "deleted"
* **rules** - Validation rules
* **where** - A custom where string
* **whereBinds** - If using a custom where string, and you want to bind your parameters into the where string, you can add those parameters to thir property as an array

At the moment BaseModel provides a single callback **beforeInit**, this callback is invoked before initialization of the BaseModel.

Table name
==========

There are 3 ways of defining a table name, first is to define a public property **table** in your model, passing the table name at construction time to the BaseModel *__construct* method, or just let BaseModel try and figure out your table name.

Guessing the table name
-----------------------

When guessing the table name, BaseModel takes the class name of your model, removes the *_model* or *_m* suffixes, pluralizes the remainder, and converts everything to lower case letters. So in example **User_model** becomes **users**.

Config constants
================

BaseModel provides a \SlaxWeb\BaseModal\Constants class for configuration of soft deletes and primary key types. To use in your controller, the best way is to *use* the class before declaration of your model.
```PHP
<?php
use \SlaxWeb\BaseModel\Constants as C;

class Test_model extends \SlaxWeb\BaseModel\model
{

}
```

And if you need to change soft delete settings or primary key type, simply use those constants:
* **DELETEHARD** - Hard delete
* **DELETESOFTMARK** - Use deleted column for marking of deleted rows
* **DELETESOFTSTATUS** - Use the status column for marking of deleted rows
* **PKEYAI** - Auto-incremented primary key
* **PKEYUUID** - Primary key generated in database by UUID()
* **PKEYFUNC** - Primary key generated by a PHP function or your custom function
* **PKEYNONE** - No primary key

Database oprations
==================

Inserting data
--------------

To insert data, the BaseModel provides a **insert** method which takes the data to be inserted as an array.
```PHP
$this->insert(array("column" => "value"));
```
The method will first try to validate data, if you have set the **rules** property and you have not set to skip the validation.

On error, the method will return an *Error* object, or true on success.

Getting data
------------

You can get data by the primary key, by your own where parameters, or just everything in there. For this, there are two methods, **get** and **getBy**. To retrieve a row by your primary key you call the **get** method with the primary key value as the input parameter.
```PHP
// retrieves row with primary key value 123
$this->get(123);
```
If you wish to get everything, just omit the primary key value in **get** method. To get data based on your own where statement you have two options, either set the BaseModel **where** property, or pass a "where" array to **getBy** method.
```PHP
$this->getBy(array("column" => "value"));
```
This method also provides a means to select only specific columns. You can pass an array of columns as the second parameter, or the columns as a string, as you would use them in a SELECT SQL statement.a

All get methods return the *Result* object.

Updating data
-------------

As well as getting data, updating also provides 3 ways, update by primary key, by your own where statement, or update everything in the table. Those two methods are **update** and **updateBy**. And they work pretty much the same as getting data, except you need to privde an array of data to be updated as the first parameter, and the primary key value or your where statement as the second parameter.
```PHP
$this->update(array("column" => "value"), 123);
```
This will update the column named "column" with the value where primary key value is 123.

To use your own where statement, you need to pass it as an array or a where string as you would use it in your SQL statement, to the **updateBy** method.
```PHP
$this->updateBy(array("column" => "value"), array("whereColumn" => "whereValue"));
```
Updating will first try to validate the data, if the rules are set and if you have not marked the validation to be skipped.

On error the methods return the *Error* object.

Deleting data
-------------

For deletion you once again have two methods, **delete** and **deleteBy**, and once again, you can delete by primary key value, your own where statement, or delete everything. If you are using deletion by status or deleted columns, this method will automatically make an update for you, and mark the row(s) as deleted. For usage examples, refer to (Getting data), because the usage is exactly the same, except the different method names.

SQL clauses
===========

BaseModel suppors some SQL clauses that you can use.

GROUP BY
--------

To add a group by clause to the next query, set it with the **groupBy** method. The input parameter must be an array containing the columns you wish to group by. It returns the object of the model so you can link your method calls.

ORDER BY
--------

To add an order by clause to the query set it with the **orderBy** method. It will be used only for the next query. First parameter must be an array, and must contain column names. The second parameter is the direction of order by, default is "ascending". The method returns the object of the model, so you can link together your method calls.

LIMIT
-----

To add a limit clause to the next query, set it with the **limit** method. It takes two integer parameters, the first one is the limit on how many rows you want to affect with your query, and the second, default int(0) is the offset, at which row to begin counting.

Validation
==========

BaseModel automatically validates your data when inserting or updating, as long as you provide it the validation rules in the **rules** proeprty. The rules have to be CodeIgniter Form Validation compliant.
```PHP
$this->rules = array(
    array(
        "field" =>  "fieldName",
        "label" =>  "Field label",
        "rules" =>  "required|max_length[100]"
    )
);
$this->insert(array("fieldName" => "fieldValue"));
```

Run validation manually
-----------------------

You can also run the validation manually, with the **validate** method. Once again, you need to have rules set in the **rules** property.
```PHP
$this->rules = array(
    array(
        "field" =>  "fieldName",
        "label" =>  "Field label",
        "rules" =>  "required|max_length[100]"
    )
);
$this->validate(array("fieldName" => "fieldValue"));
```

Skipping validation
-------------------

To skip the validation you either set the rules to an empty array, or prior to calling insert or update methods call the **skipValidation** method, which will skip the validation for the next query. **skipValidation** returns back the model object, so you can link your query after it.
```PHP
$this->skipValidation()->insert($data);
```

Queries on soft deleted rows
============================

If you wish to run update/get queries on the soft deleted rows, you need to call the **withDeleted** method before executing your query, or use a custom where string. The **withDeleted** will allow to include deleted rows only for the next query in your update or select queries. It also returns the model object, so you can link your queries.
```PHP
$this->withDeleted()->get();
```

Results
=======

The select query will return a *Result* object, from which you can then access data, and traverse through rows.

Getting column data
-------------------

To get the data, just access the Result object property, and use the column name as the property name.
```PHP
$result = $this->getBy(array("whereColumn" => "whereValue"), "column");
$result->column;
```

If the result is multi-row, the first row is used for data retrieval. If the column doesn't exist, *null* is returned.

Get as array
------------

To get all columns as array, simply call the **asArray** method, this will return the current row you're on as an array.
```PHP
$result->asArray();
```

Number of rows
--------------

To get the number of rows just call the **rowCount** method.

Get all rows
------------

To get all the rows at once, call **getResult** method.

Traversing through rows
-----------------------

To traverse through the rows, the Result class provides 3 methods:
* next - moves to the next row
* prev - moves to the previous row
* row - moves to the row you specify in the input parameter

All three methods return the object to it self, or false if the next, previous or specific rows don't exist.

Errors
======

Error class provides an easier mean to handle errors, and enables a way for you to assign error messages for specific errors easily.

Initialization of the Error class
---------------------------------

In order to successfully assign error messages, a language array must be passed to the Error object at construction time. BaseModel does this for you in some methods where the error occurs, if you need to do it your self in your own method, the best and easiest way is to supply it with the CodeIgniter language array ($this->lang->language).

Add an error
------------

To add an error you have to provide an error code, and as optional parameters you can provide an integer severity level, and an array of additional error data. When adding, Error class automatically sorts your errors based on the severity. As well as it searches for a respective error message in the provided language array. The key for the message has to be "error_your_error_code" all in lower case letters.

Has errors and count errors
---------------------------

To check if there are errors use the **hasErrors** method, and to check how many errors are there, use the **errorCount** method.
```PHP
$error->hasErrors();
$error->errorCount();
```

Getting errors
--------------

You can get all errors at once with the **getErrors** method, or the current error with **get** method. You can also get an error at specific index with **errorAt**, or an error which contains your code that you supply as the input parameter with the **error** method.

Traversing through errors
-------------------------

The same as the Result class, Error class provides a **prev** and **next** methods, that return false if there is no previous or next errors, or the object to it self for method linking, but it does not provide a method like **row**, except the **errorAt** which returns already the error at the provided index.

ChangeLog
=========

0.3.0 - development
-------------------

* Add Error class
* Add validation
* Add primary key types

0.2.5
-----
* Reset the where array between queries

0.2.4
-----
* Remove WHERE keyword from query when there is no WHERE statement

0.2.3
-----
* With deleted where statement was mistakingly removed

0.2.2
-----
* Add backtics to column names

0.2.1
-----

* Remove quotes on bound placeholders

0.2.0
-----

* Add insert method
* Bind parameters to update statement

0.1.0
-----

* Initial release

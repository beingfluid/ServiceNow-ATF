
# GlideRecord

- The GlideRecord class is the way to interact with the ServiceNow database from a script.

- GlideRecord interactions start with a database query.

- The generalized strategy is:

    1. Create a GlideRecord object for the table of interest.
    2. Build the query condition(s).
    3. Execute the query.
    4. Apply script logic to the records returned in the GlideRecord object.

```javascript
// 1. Create an object to store rows from a table
var myObj = new GlideRecord('table_name');

// 2. Build query
myObj.addQuery('field_name','operator','value');
myObj.addQuery('field_name','operator','value');

// 3. Execute query 
myObj.query();

// 4. Process returned records
while(myObj.next()){
  //Logic you want to execute.  
  //Use myObj.field_name to reference record fields
}
```

> There is a client-side GlideRecord API for global applications.
>
>The client-side GlideRecord API cannot be used in scoped applications.

## Build the Query Condition(s)

- Use the addQuery() method to add query conditions. 
- The addQuery() method is typically passed three arguments: field name, operator, and value.
- The addQuery operators are:

    1. **Numbers:** =, !=, >, >=, <, <=
    2. **Strings:** =, !=, STARTSWITH, ENDSWITH, CONTAINS, DOES NOT CONTAIN, IN, NOT IN, INSTANCEOF

- In some scripts you will see only two arguments: field name and value.

    - When the addQuery() method is used without an operator, the operation is assumed to be =.

- When there are multiple queries, each additional clause is treated as an AND.
- Queries with no query conditions return all records from a table.


- Always test queries on a sub-production instance prior to deploying them on a production instance.
- If a malformed query executes in runtime, all records from the table are returned.
- An incorrectly constructed encoded query, such as including an invalid field name, produces an invalid query.

    - When the invalid query is run, the invalid part of the query condition is dropped, and the results are based on the valid part of the query, which may return all records from the table.
    - Using an insert(), update(), deleteRecord(), or deleteMultiple() method on bad query results can result in data loss.

> You can set the `glide.invalid_query.returns_no_rows` system property to `true` to have queries with invalid encoded queries return no records.
> - In some cases, the query may still return records in API results even when `glide.invalid_query.returns_no_rows` is set to `true`.

## Iterating through Returned Records

- There are several strategies for iterating through returned records.
- The `next()` method and a `while` loop iterates through all returned records to process script logic:

```javascript
// iterate through all records in the GlideRecord and set the Priority field value to 4 (low priority).
// update the record in the database
while(myObj.next()){
  myObj.priority = 4;
  myObj.update(); 
}
```

- The `next()` method and an if processes only the first record returned.

```javascript
// Set the Priority field value to 4 (low priority) for the first record in the GlideRecord
// update the record in the database
if(myObj.next()){
  myObj.priority = 4;
  myObj.update(); 
}
```

- Use the `updateMultiple()` method to update all records in a GlideRecord.
- To ensure expected results with the `updateMultiple()` method, set field values with the the `setValue()` method rather than direct assignment.

```javascript
// When using updateMultiple(), use the setValue() method.  
// Using myObj.priority = 4 may return unexpected results.
myObj.setValue('priority',4);
myObj.updateMultiple();
```

## Counting Records in a GlideRecord

- The GlideRecord API has a method for counting the number of records returned by a query: `getRowCount()`.
- Do not use the `getRowCount()` method on a production instance as there could be a negative performance impact on the database.
- To determine the number of rows returned by a query on a production instance, use GlideAggregate.

```javascript
// If you need to know the row count for a query on a production instance do this
var count = new GlideAggregate('x_snc_needit_needit'); 
count.addAggregate('COUNT'); 
count.query(); 
var recs = 0; 
  if (count.next()){ 
    recs = count.getAggregate('COUNT');
  }
gs.info("Returned number of rows = " +recs);
```

```javascript
// Do not do this on a production instance. 
var myObj = new GlideRecord('x_snc_needit_needit');
myObj.query();
gs.info("Returned record count = " + myObj.getRowCount());
```

## Encoded Queries

- As already discussed, if there are multiple conditions in query, the conditions are ANDed.
- To use ORs or create technically complex queries, use encoded queries.
- The code for using an encoded query looks like this:

```javascript
var myObj = new GlideRecord("x_snc_needit_needit");
myObj.addEncodedQuery('<your_encoded_query>');
myObj.query();
while(myObj.next()){
  // Logic you want to execute for the GlideRecord records
}
```

- The trick to making this work is to know the encoded query syntax.
- The syntax is not documented so let ServiceNow build the encoded query for you.
    1. In the main ServiceNow browser window, use the All menu to open the list for the table of interest.
        - If there is no module to open the list, type `<table_name>.list` in the Filter field.
    2. Use the Filter to build the query condition.
    3. Click the Run button to execute the query.
    4. Right-click the breadcrumbs and select the Copy query menu item. 
        - Where you click in the breadcrumbs matters.
        - The copied query includes the condition you right-clicked on and all conditions to the left.
        - To copy the entire query, right-click the condition farthest to the right.
    5. Return to the script and paste the encoded query into the `addEncodedQuery()` method.

> Be sure to enclose the encoded query in "" or ''.

```javascript
var myObj = new GlideRecord("x_snc_needit_needit");
myObj.addEncodedQuery("u_when_neededBETWEENjavascript:gs.daysAgoStart(0)@javascript:gs.quartersAgoEnd(1)^active=true^state=14^ORstate=16");
  myObj.query();
  while(myObj.next()){
    // Insert logic you want to execute for the GlideRecord records
  }
```

## Retrieve values from records

- In most cases, don't use dot-walking to get values from a record.

- Dot-walking retrieves the entire object instead of the field value.
    - Retrieving the object uses more storage and might cause undesirable results when used in arrays or in Service Portal.

- Instead of retrieving the entire object, you can use one of the following methods to copy the field values:
    1. `getValue()`
    2. `getDisplayValue()`

- If dot-walking through a GlideElement object is necessary, use the toString() method to retrieve values.

    - For example, you might need the current caller's manager sys_id to set another reference field.
    - The following example shows how to get the string value instead of the entire object:

    ```javascript
    var mgr = current.caller_id.manager.toString();
    ```

## GlideRecordSecure

- It is a class inherited from GlideRecord that performs the same functions as GlideRecord and also enforces ACLs.

## GlideRecord API

- The GlideRecord API is used for database operations.

### GlideRecord(String tableName)

Creates an instance of the GlideRecord class for the specified table.

```javascript
var now_GR = new GlideRecord('incident');
```

### addActiveQuery()

- Adds a filter to return active records.
- **Returns:** Filter to return active records.

```javascript
var inc = new GlideRecord('incident');
inc.addActiveQuery();
inc.query();
```

### addInactiveQuery()

- Adds a filter to return inactive records.
- Inactive records have the active flag set to false.

```javascript
var inc = new GlideRecord('incident');
inc.addInactiveQuery();
inc.query();
```

### addNotNullQuery(String fieldName)

- Adds a filter to return records where the specified field is not `null`.

```javascript
var target = new GlideRecord('incident'); 
  target.addNotNullQuery('short_description');
  target.query();   // Issue the query to the database to get all records
  while (target.next()) {   
     // add code here to process the incident record
  }
  ```

### addNullQuery(String fieldName)

- Adds a filter to return records where the specified field is `null`.

```javascript
var target = new GlideRecord('incident'); 
  target.addNullQuery('short_description');
  target.query();   // Issue the query to the database to get all records
  while (target.next()) {   
     // add code here to process the incident record
  }
  ```

### addQuery(String name, Object operator, Object value)

- Provides the ability to build a request, which when executed, returns the rows from the specified table that match the request.
- If you're familiar with SQL, this method is similar to the "where" clause.
- You can make one or more addQuery() calls in a single query.
- For this method the queries are AND'ed.
- addQuery() is typically called with three parameters; table field, operator, and comparison value.
- It can be called with only two parameters, table field and comparison value, such as `myObj.addQuery('category','Hardware');`.

    - The operator in this case is assumed to be "equal to".

```javascript
var rec = new GlideRecord('incident');
rec.addQuery('active',true);
rec.addQuery('sys_created_on', ">", "2010-01-19 04:05:00");
rec.query();
while (rec.next()) { 
 rec.active = false;
 gs.print('Active incident ' + rec.number + ' closed');
 rec.update();
}
```

```javascript
var que = new GlideRecord('incident');
que.addQuery('number','IN','INC00001,INC00002');
que.query();
while(que.next()) {
 //do something....
}
```

#### If any of the query statements need to be OR'ed

- The `GlideQueryCondition` API provides additional AND or OR conditions that can be added to the current condition, allowing you to build complex queries.
- In the case of `addCondition()`, an implied **AND** is added.

##### addCondition(String name, String oper, Object value)

- Adds an AND condition to the current condition.

```javascript
var now_GR = new GlideRecord('incident');
var qc = now_GR.addQuery('category', 'Hardware');
qc.addCondition('category', 'Network');
now_GR.addQuery('number','INC0000003');
now_GR.next();
now_GR.number;
gs.info(now_GR.getEncodedQuery());
```

##### addOrCondition(String name, String oper, Object value)

- Appends a OR condition to an existing GlideQueryCondition.
- addOrCondition() works in conjunction with any of the addQuery()methods to OR the specified query parameters to the query previously constructed using addQuery().

```js
var now_GR = new GlideRecord('incident');
var qc = now_GR.addQuery('category', 'Hardware');
qc.addOrCondition('category', 'Network');
now_GR.addQuery('number','INC0000003');
now_GR.next();
now_GR.number;
gs.info(now_GR.getEncodedQuery());
```

- To group AND/OR statements such as `(state < 3 OR state > 5) AND (priority = 1 OR priority = 5)` use code similar to the following:

```js
var myObj = new GlideRecord('incident');
var q1 = myObj.addQuery('state', '<', 3);
q1.addOrCondition('state', '>', 5);
var q2 = myObj.addQuery('priority', 1);
q2.addOrCondition('priority', 5);
myObj.query();
```

- If there is a complicated set of AND and OR queries, a single encoded query containing all conditions simplifies the query creation.
- To simplify the query creation, create a query in a list view, right-click the query, and select Copy query.
- It creates a single encoded query string to return your result set.
- Use that string as a parameter in an addEncodedQuery() method call.


### addEncodedQuery(String query, Boolean enforceFieldACL)

- Adds an encoded query to other queries that may have been set.
- **enforceFieldACL**: Optional flag that indicates whether to enforce field ACL (Access Control List) rules.

```javascript
var queryString = "priority=1^ORpriority=2";
 var now_GR = new GlideRecord('incident');
 
 now_GR.addEncodedQuery(queryString);
 now_GR.query();
 while (now_GR.next()) {
 gs.addInfoMessage(now_GR.number);
 }
```

### applyEncodedQuery(String queryString)

- Sets the values of the specified encoded query terms and applies them to the current GlideRecord.

```js
function createAcl(table, role) {
  gs.print("Checking security on table " + table);
  var now_GR = new GlideRecord("sys_security_acl");
  now_GR.addQuery("name", table);
  now_GR.addQuery("operation", "read");
  now_GR.query();
  var encQuery = now_GR.getEncodedQuery();
  if (now_GR.next()) {
    // existing acl found so use it
    createAclRole(now_GR.sys_id.toString(), role);
    return;
  } else {
  now_GR.initialize();
  now_GR.applyEncodedQuery(encQuery);
  var acl = now_GR.insert();
    gs.print("Added read access control on " + table);
    createAclRole(acl, role);
  } 
}
```

### getEncodedQuery()

- Retrieves the query condition of the current result set as an encoded query string.

```js
 var now_GR = new GlideRecord("sys_security_acl");
  now_GR.addQuery("name", table);
  now_GR.addQuery("operation", "read");
  now_GR.query();
  var encQuery = now_GR.getEncodedQuery();
```

### addExtraField(String dotWalkedField)

- Queries one or more dot-walked fields from a form or script in a single request.
- The `addExtraField()` method allows you to query dot-walked fields in a single database request, rather than perform multiple queries per dot-walked element in a form or script (which requires multiple round trips to the database).
- Levels of field names are separated by dots (periods). 
- The format of dotWalkedField follows the left-to-right order of fields in a dotwalked form or script.

```javascript
var gliderecord = new GlideRecord("incident");
gliderecord.addQuery("number", "INC0041457");
gliderecord.addExtraField("cmdb_ci.location.contact.name");
gliderecord.query();
gliderecord.next();
gs.print(gliderecord.cmdb_ci.location.contact.name);
```

> **Note:** The `addExtraField()` method does not impact output results; the output is always the same regardless if you use this method in your script.

- Using `gs.print(gr.cmdb_ci.location.contact.name)` with addExtraField() is same as without using addExtraField().
- The `addExtraField()` method optimizes the querying of the dot-walked fields.


### addJoinQuery(String table)

- Adds a filter to return records based on a relationship in a related table.
- For example, find all the users that are in the database group (users via `sys_user_grmember` table). - Another example would be find all problems that have an assigned incident (problems via the `incident.problem_id` relationship).

- This is not a true database join; rather, addJoinQuery() adds a subquery.
- So, while the result set is limited based on the join, the only fields that you have access to are those on the base table (those which are in the table with which the GlideRecord was initialized).

```javascript
var prob = new GlideRecord('problem');
prob.addJoinQuery('incident');
prob.query();
```

- Above script find problems that have an incident attached.
    - This returns problems that have associated incidents.
    - However, it won't pull values from the incidents that are returned as a part of the query.

#### addJoinQuery(String table, String primaryField)

- **primaryField**: If other than `sys_id`, the primary field.

```javascript
var now_GR = new GlideRecord('problem'); 
now_GR.addJoinQuery('incident', 'opened_by'); 
now_GR.query();
```

- Above script find problems that have incidents using the open_by field at the join key instead of the sys_id.

#### addJoinQuery(String table, String primaryField, String joinTableField)

- **joinTableField**: If other than sys_id, the field that joins the tables

```javascript
var now_GR = new GlideRecord('problem'); 
now_GR.addJoinQuery('incident', 'opened_by', 'caller_id'); 
now_GR.query();
```

- Above script find problems that have incidents associated where the `incident` `caller_id` field value matches that of the `problem` `opened_by` field.

### addValue(String field, Number value)

- Provides atomic add and subtract operations on a specified number field at the database level for the current GlideRecord object.

- Typically, a GlideRecord object is written as one record in a database.
    - Individual field values are stored as defined. 
    - For code that adds a value to a GlideRecord field, it simply saves the field to the database with the new value, rather than atomically incrementing it.
    - For example, when the following code is executed, the value of the u_count field in the database is 2.

        ```js
        gs.print(now_now_GR.u_count); // "1" 
        now_GR.u_count += 1; 
        now_GR.update(); 
        now_GR.get(now_now_GR.sys_id); 
        gs.print(now_now_GR.u_count); // "2"   
        ```

    - If another user concurrently runs identical code, instead of the two operations each adding 1 to u_count, the net effect is that u_count only contains 2, with one operation's update actually being lost.

- Conversely, the addValue() method performs the addition/subtraction in the database when the record is updated as an atomic operation.
- Two operations running concurrently each properly update the field.

```js
gs.print(now_now_GR.u_count); // "1" 
now_GR.addValue("u_count", 1); 
now_GR.update(); 
now_GR.get(now_GR); // The record must be reloaded from the database to observe the result
gs.print(now_now_GR.u_count); // "3", if executed concurrently with another user 
```

- Like setValue(), addValue() changes only take effect in the database after a subsequent call to update() or insert().
- If insert() is called, the specified field is initialized with the value parameter passed into addValue().

> Note: If setValue() is called for the specified field prior to calling addValue(), the addValue() method is not processed and an error message is logged.

### applyTemplate(String template)

- Apply a template record from the Template table [sys_template] to the current record.
- If the specified template is not found, no action is taken.
- **template**: Name of a template from the Templates [sys_template] table.

```js
var rec1 = new GlideRecord("incident");
rec1.initialize();
rec1.applyTemplate("my_incident_template");
rec1.insert();
```

### autoSysFields(Boolean e)

- Enables or disables the update to the fields sys_updated_by, sys_updated_on, sys_mod_count, sys_created_by, and sys_created_on.
- If false disables updates to sys_updated_by, sys_updated_on, sys_mod_count, sys_created_by, and sys_created_on.
- This is often used for manually updating field values on a record while leaving historical information unchanged.

> Warning: Use caution if you use this method.
>
> - When you use this method the sys_mod_count field will not be incremented, and other sys_ fields will not be updated.
> - This can break functionality including, but not limited to, the Activity Formatter, History Sets, Notifications, and Metrics.

```js
var inc = new GlideRecord('incident');
 
// Change all Open(1) incidents to Active(2)
 
inc.addQuery('state', 1);
inc.query();
 
while (inc.next()) {
  inc.autoSysFields(false);  // Do not update sys_updated_by, sys_updated_on, sys_mod_count, sys_created_by, and sys_created_on
  inc.setWorkflow(false);    // Do not run any other business rules
  inc.setValue('state', 2);
  inc.update();
}
```

### canCreate()

- Determines if the access control rules (which includes the user's role) permit inserting new records in this table.

```js
var now_GR = new GlideRecord('benefit_plan');
return now_GR.canCreate();
```

### canDelete()

- Determines if the access control rules (which includes the user's role) permit deletion of records in this table.

### canRead()

- Determines if the Access Control Rules (ACLs) permit reading records in this table.
- This method evaluates all ACL types, such as user roles, scripted ACLs, ACLs with scripted conditions, and so on.

```js
var now_GR = new GlideRecord('benefit_plan');
return now_GR.canRead();
```

### canWrite()

- Determines if the access control rules (which includes the user's role) permit updates to records in this table.

```js
var now_GR = new GlideRecord('benefit_plan');
return now_GR.canWrite();
```

### changes()

- Determines whether any of the fields in the record have changed.

```js
var now_GR = new GlideRecord("incident");
now_GR.query();
now_GR.next();
if (now_GR.changes()) {
  gs.print("The incident record reported changes right after being read");
} else {
  gs.print("The incident record has not changed");
}
```

### deleteRecord()

- Deletes a single record.

```js
var rec = new GlideRecord('incident');
rec.addQuery('active',false);
rec.query();
while (rec.next()) { 
 gs.info('Inactive incident ' + rec.number + ' deleted');
 rec.deleteRecord();
}
```

### deleteMultiple()

- Deletes all records that satisfy the query.
- This method does not delete attachments.
-  When using deleteMultiple() to cascade delete, prior calls to setWorkflow() on the same GlideRecord object are ignored.
- Dot-walking is not supported for this method.
    - When using the deleteMultiple() function on referenced tables, all the records in the table are deleted.
- Do not use deleteMultiple() on tables with currency fields.
    - Always delete each record individually.
- Also, do not use this method with the chooseWindow() or setLimit() methods when working with large tables.
    - The setLimit() method does not limit the number of records that are deleted with deleteMultiple().
    - All records returned by the query are deleted regardless of setLimit().

```js
var now_GR = new GlideRecord('incident');
now_GR.addQuery('active','false');
now_GR.query();
now_GR.deleteMultiple();
```

### find(String columnName, String value)

- Returns true if any record has a matching value in the specified column.
- If found, it also moves to the first record that matches, essentially executing next() until the record is returned.

```js
var now_GR = new GlideRecord("incident");
now_GR.query();
var shortDescription = "Critical";
if (now_GR.find("short_description", shortDescription)) {
  gs.print("An incident with the specified field value was found");
  var recordID = now_GR.getValue("sys_id");
  gs.print("Found in the following record: " + recordID);
} else {
  gs.print("An incident with the specified field value was not found");
}
```

### get(Object name, Object value)

- Returns the specified record in the current GlideRecord object.
- This method accepts either one or two parameters.
    1. If only a single parameter is passed in, the method assumes that it is the sys_id of the desired record.
        - If not found, it then tries to match the value against the display value.
    2. If two parameters are passed in, the first is the name of the column within the GlideRecord to search.
        - The second is the value to search for.

- If multiple records are found, use next() to access the additional records.

```js
var grIncident = new GlideRecord('incident');
var returnValue = grIncident.get('99ebb4156fa831005be8883e6b3ee4b9');
gs.info(returnValue); // logs true or false
gs.info(grIncident.number); // logs Incident Number
```

```js
var grIncident = new GlideRecord('incident');
var returnValue = grIncident.get('caller_id.name','Sylivia Wayland');
gs.info(returnValue); // logs true or false
gs.info(grIncident.number); // logs Incident Number
```

### getAttribute(String fieldName)

- Returns the dictionary attributes on the specified field.

```js
var now_GR = new GlideRecord('sys_user');
  now_GR.query("user_name","admin");
  if (now_GR.next()) {
    gs.print("we got one");
    gs.print(now_GR.location.getAttribute("tree_picker"));
  }
```

### getClassDisplayValue()

- Returns the table's label.

```js
// Display the incident table label
var now_GR = new GlideRecord("incident");
var value = now_GR.getClassDisplayValue();
gs.info("The table label is " + value + ".");
```

### getDisplayValue(String name)

- Retrieves the display value for the current record or the display value of an attribute in a dynamic attribute store.

```js
var now_GR = new GlideRecord('incident');
now_GR.get('sys_id','<sys_id>');
gs.info(now_GR.getDisplayValue());
```


### getEscapedDisplayValue()

- Retrieves the field value for the display field of the current record and adds escape characters for use in Jelly scripts.

> For this method to work, a display value must have been set on the associated table.

```js
var escapeValue = now_GR.getEscapedDisplayValue();
gs.print("Escaped name: " + escapeValue);
```

### getFields()

- Retrieves a Java ArrayList of fields in the current record.

```js
// Get a single incident record
var grINC = new GlideRecord('incident');
grINC.query();
grINC.next();
gs.print('Using ' + grINC.getValue('number'));
gs.print('');
 
// getFields() returns a Java ArrayList
var fields = grINC.getFields();
```

### getLink(Boolean noStack)

- Retrieves the link for the current record.
- noStack: Flag that indicates whether to append the generated link to the end of the URL. 
    - **For example:** `&sysparm_stack=[tablename]_list.do? sysparm_query=active=true.`

### getRecordClassName()

- Retrieves the class (table) name for the current record.

```js
var classname = current.getRecordClassName();
```

### getRelatedLists()

- Retrieves a list of names and display values of related lists associated with the current GlideRecord.

```js
var now_GR = new GlideRecord('incident');
var c = now_GR.getRelatedLists().values().toArray();
var numElements = c.length;
for( var i = 0; i < numElements; ++i){
  gs.print(i+": "+c[i]);
}
```

### getRelatedTables()

- Retrieves a list of names and display values of tables that are referred to by the current record.

```js
var now_GR = new GlideRecord('incident');
var c = now_GR.getRelatedTables().values().toArray();
var numElements = c.length;
for( var i = 0; i < numElements; ++i){
  gs.print(i+": "+c[i]);
}
```

### getRowCount()

- Retrieves the number of rows (records) in the current GlideRecord object.

```js
var numberOfIncidents = new GlideRecord('incident');
numberOfIncidents.query();
gs.info("Records in incident table: " + numberOfIncidents.getRowCount());
```

### getTableName()

- Retrieves the table name associated with this GlideRecord.

```js
gs.log('Table: ' + current.getTableName()); 
```

### getUniqueValue()

- Gets the primary key of the record, which is usually the sys_id unless otherwise specified.

```js
var now_GR = new GlideRecord('kb_knowledge');
now_GR.query();
now_GR.next();
var uniqueid = now_GR.getUniqueValue();
gs.info(uniqueid);
```

### getValue(String fieldName)

- Retrieves the string value of a specified field or the string value of an attribute in a dynamic attribute store.
- Returns null if the field is empty or the field doesn't exist.
- Boolean values return as "0" and "1" string values instead of false and true.

### hasAttachments()

- Determines if the current GlideRecord has any attachments.

### hasNext()

- Determines if there are any more records in the GlideRecord.

### initialize()

- Creates an empty record within the current GlideRecord that is suitable for population before an insert.

```js
var now_GR = new GlideRecord('to_do');
now_GR.initialize(); 
now_GR.name = 'first to do item'; 
now_GR.description = 'learn about GlideRecord'; 
now_GR.insert();
```

### newRecord()

- Creates a GlideRecord, sets the default values for the fields, and assigns a unique ID to the record.

```js
var now_GR = new GlideRecord("sys_user");
now_GR.newRecord();
now_GR.setValue("user_name", "John Smith");
gs.print("Is this a new record: " + now_GR.isNewRecord());
```

### insert()

- Inserts a new record with the field values that have been set for the current record.

### instanceOf(String className)

- Checks a table for the type\class of table.

### insertWithReferences()

- Inserts a new record and also inserts or updates any related records with the provided information.

#### Examples

1. If a reference value is not specified (as below), then a new user record is created with the provided first_name and last_name, and the caller_id value is set to this newly created sys_user record. The result is a new sys_user record with the provided first_name and last_name and a new incident record with the provided short_description and caller_id.

```js
var inc = new GlideRecord('incident');
inc.initialize();
inc.short_description = 'New incident 1';
inc.caller_id.first_name = 'John';
inc.caller_id.last_name = 'Doe';
inc.insertWithReferences();
```

2. If a caller_id value is specified, then that caller_id is updated with the provided first_name and last_name. The result is a newly created incident record with values set for short_description and caller_id.

```js
var inc = new GlideRecord('incident');
inc.initialize();
inc.short_description = 'New incident 1';
inc.caller_id.setDisplayValue('David Loo');
inc.caller_id.first_name = 'John';
inc.caller_id.last_name = 'Doe';
inc.insertWithReferences();
```

### isNewRecord()

- Determines whether the current record has been inserted into the database.
- This method returns true only if the newRecord() method has been called.
- This method is useful for scripted ACL, and in the condition of UI actions, but should not be used in background scripts.

```js
var now_GR = new GlideRecord("sys_user");
now_GR.newRecord();
now_GR.setValue("user_name", "John Smith");
gs.print("Is this a new record: " + now_GR.isNewRecord());

var now_GR2 = new GlideRecord("sys_user");
now_GR2.addQuery("user_name", "Abel Tutor");
now_GR2.query();
now_GR2.next();
gs.print("Is this a new record: " + now_GR2.isNewRecord());
```

### isValid()

- Determines if the current GlideRecord table exists.

```js
var testTable = new GlideRecord('incident');
gs.print(testTable.isValid());
```

### isValidField(String columnName)

- Determines if the specified field is defined in the current GlideRecord table.

```js
si.isValidField('sys_class_name')
```

### next()

- Moves to the next record in the GlideRecord.
- Use this method to iterate through the records returned by a GlideRecord query.
- The `if(myObj.next())` construct only processes the first record returned.

```js
var rec = new GlideRecord('incident');
rec.query();
while (rec.next()) { 
  gs.print(rec.number + ' exists');
}
```

>  This method fails if there is a field in the table called "next". If that is the case, use the method _next().

### _next()

- Moves to the next record in the GlideRecord. 
- Provides the same functionality as next(), intended to be used in cases where the GlideRecord has a column named next.

```js
var rec = new GlideRecord('sys_template');
rec.query();
while (rec._next()) { 
  gs.print(rec.number + ' exists');
}
```

### orderBy(String fieldName)

- Specifies a field name, or path to an attribute within a dynamic attribute store, to use to order the query set.
- To order by multiple fields, call this method multiple times with different field values.

```js
var count = 0;
  var child = new GlideRecord('pm_project_task');
  child.addQuery('parent', project.sys_id);
  child.orderBy('order');
  child.orderBy('number');
  child.query();
  var len = child.getRowCount().toString().length;
  var seq = 0;
  while (child.next()) {
    count += UpdateProjectTaskWBS(child, 1, ++seq, len, '');
  }
  gs.addInfoMessage(count + ' Project Tasks updated');
```

### orderByDesc(String, fieldName)

- Specifies the field, or an attribute in a dynamic attribute store, to use to order the query set in descending order.

### query(String field, String value)

- Runs a query against the table based on the filters specified by query methods such as addQuery() and addEncodedQuery().
- This method fails if there is a field in the table called "query".
    - If that is the case, use the method _query().
- To run queries in a domain-separated instance, use the method queryNoDomain().


### setAbortAction(Boolean b)

- Sets a flag to indicate if the next database action (insert, update, delete) is to be aborted.

```js
current.setAbortAction(true);
```

### setLimit(Number limit)

- Sets the maximum number of records to return in the GlideRecord from a query.

```js
var now_GR = new GlideRecord('incident');
now_GR.orderByDesc('sys_created_on');
now_GR.setLimit(10);
now_GR.query();
```

### setNewGuid()

- Generates a new GUID and sets it as the unique ID (sys_id) for the current record.
- This function applies only to new records.
- You cannot change the GUID for an existing record.

```js
var task = new GlideRecord ('task');
var tsk_id = task.setNewGuid();
 
task.description = "Request: " + current.request.number;
task.description = task.description + "\n" + "Requested by: " + current.request.u_requested_by.name;
task.description = task.description + "\n" + "Requested for: " + current.request.u_requested_for.name;
task.description = task.description + "\n" + "Item: " + current.cat_item.name;
 
var now_GR = new GlideRecord ('task_rel_task');
//link the incident to the request
now_GR.parent = current.request;
now_GR.child = tsk_id;
now_GR.insert();
```

#### setNewGuidValue (String guid)

- Generates a new GUID and sets it as the unique ID for the current record, when inserting a new record.

```js
ci.setNewGuidValue(sys_id);
```

### setUseEngines(Boolean e)

- Disables or enables the running of any engines (approval rules / assignment rules).

```js
var now_GR = new GlideRecord("oauth_credential");
  var oauthClient = now_GR.addJoinQuery("oauth_entity", "peer", "sys_id");
  now_GR.addQuery("type", "access_token");
  now_GR.addQuery("expires", ">", nowDateTime());
  now_GR.addNullQuery("oauth_requestor_profile");
  oauthClient.addCondition("active", "true");
  oauthClient.addCondition("type", "client");
  now_GR.setUseEngines(false);
  now_GR.setWorkflow(false);
  now_GR.query();
  return now_GR.hasNext();
```

### setValue(String name, Object value)

- Sets the specified field, or an attribute in a dynamic attribute store, to a specified value.

- Normally a script would do a direct assignment, for example now_GR.category = value. 
    - However, if in a script the element name is a variable, then you can use now_GR.setValue(elementName, value). 
- When setting a value, ensure that the data type of the field matches the data type of the value you enter.

- If the value parameter is null, the record isn't updated, and an error isn't thrown.

- This method can't be used on journal fields.

### setWorkflow(Boolean e)

- Enables or disables the running of business rules that might normally be triggered by subsequent actions.
- If the e parameter is set to false, an insert/update will not be audited.
    - Auditing only happens when the parameter is set to true for a GlideRecord operation.

### update(Object reason)

- Updates the GlideRecord with any changes that have been made.
- If the record does not exist, it is inserted.
- **reason**: Optional. Reason for the update. The reason appears in the audit record.

### updateMultiple()

- Updates each GlideRecord in a stated query with a specified set of changes.

```js
var now_GR = new GlideRecord('incident');
now_GR.addQuery('active', true);
now_GR.setValue('state',  4);
now_GR.updateMultiple();
```

### updateWithReferences(Object reason)

- Updates a record and also inserts or updates any related records with the information provided.

If processing an incident where the Caller ID is set to reference sys_user record 'John Doe,' then the following code would update John Doe's user record. If processing an incident where there is no Caller ID specified, then the following code would create a new sys_user record with the provided information (first_name, last_name) and set the Caller ID value to the newly created sys_user record.

```js
var inc = new GlideRecord('incident');
inc.get(inc_sys_id);  // Looking up an existing incident record where 'inc_sys_id' represents the sys_id of a incident record
inc.caller_id.first_name = 'John';
inc.caller_id.last_name = 'Doe';
inc.updateWithReferences();
}
```

### setJournalEntry(String entry, String userName)

- Adds a journal entry and author as a work note or comment field.

```js
var now_GR = new GlideRecord("incident");

now_GR.addQuery("sys_id", "<sys_id_value>");
now_GR.query();

if(now_GR.next()){
  now_GR.work_notes.setJournalEntry("Content of the journal entry.", "abel.tuter");  
  now_GR.update();
}
```

### getJournalEntry(Number mostRecent)

- Returns either the most recent journal entry or all journal entries.
- **mostRecent**: 
    - If 1, returns the most recent entry.
    - If -1, returns all journal entries.

```js
var notes = current.work_notes.getJournalEntry(-1); 
```

## References:

1. [Server-side Scripting](https://developer.servicenow.com/dev.do#!/learn/learning-plans/xanadu/new_to_servicenow/app_store_learnv2_scripting_xanadu_gliderecord)
2. [GlideRecord](https://developer.servicenow.com/dev.do#!/reference/api/xanadu/server_legacy/c_GlideRecordAPI?navFilter=gliderecord)
3. [GlideRecord - Global](https://www.servicenow.com/docs/bundle/vancouver-api-reference/page/app-store/dev_portal/API_reference/GlideRecord/concept/c_GlideRecordAPI.html)
4. [GlideElement](https://developer.servicenow.com/dev.do#!/reference/api/xanadu/server_legacy/c_GlideElementAPI)
5. [GlideQueryCondition](https://developer.servicenow.com/dev.do#!/reference/api/xanadu/server_legacy/c_GlideQueryConditionAPI)

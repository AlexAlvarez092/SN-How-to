# Find duplicated records

> **⚠️ Disclaimer:** Please, always test the script in non-productive environments before running in a productive instance. Script was reviewed at the creation time but may break/get unexpected results due to instance versioning and SN API upgrades.

The following script can be used to find duplicated records, considering duplicated records as records in the same table that have a field with the same value

```js
var table = "";
var field = "";

(function(table, field) {
  var dupRecords = [];
  
  var gaDupCheck = new GlideAggregate(table);
  gaDupCheck.addAggregate('COUNT', field);
  gaDupCheck.addNotNullQuery(field);
  gaDupCheck.groupBy(field);
  gaDupCheck.addHaving('COUNT', '>', 1);
  gaDupCheck.query();
  
  while (gaDupCheck.next()) {
    dupRecords.push(gaDupCheck[field].toString());
  }
  
  gs.info(dupRecords.toString());
})(table, field);
```
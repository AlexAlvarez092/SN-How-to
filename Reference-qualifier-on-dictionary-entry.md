# Reference qualifier on dictionary entry

> ℹ️ This article is the result of the following question I posted in the community

> **⚠️ Disclaimer:** Please, always test the script in non-productive environments before running in a productive instance. Script was reviewed at the creation time but may break/get unexpected results due to instance versioning and SN API upgrades.

## Scenario

Imagine you have your custom table **My Table** which includes the following attributes among of others:

1. **Table** referencing `sys_db_object`
2. **Field** referencing `sys_dictionary`

Maybe you are wondering why someone needed that. Well, on my case, it was creating a configuration table for a custom API engine... there are some situations whether it would make sense.

Going back to the use case. The idea is to apply a reference qualifier to the field **Field** so it filters by the fields belonging to the selected table in the **Table** field.

The first idea would be just apply something like:

```js
javascript:"active=true^name="+current.u_table.name.getValue()
```

It would work as long as the table does not extend any other table, in such scenario, the above filter does not gather the fields belonging to the parent table.

## Solution

Depending whether your custom table is created in the global scope or in a scoped application, the filter may differ a little bit.

### Global scope

```js
javascript:"active=true^nameIN" + Array(new TableUtils(current.u_table.name.getValue()).getHierarchy()).toString().replace("[", "").replace("]", "");
```

Obviously, you need to replace `u_table` by the name of your custom **Table** field. The following script is equivalent:

```js
javascript:gs.include("j2js"); var tbl = new TableUtils(current.u_table.name.getValue()).getHierarchy(); "active=true^nameIN" + j2js(tbl);
```

### Scoped application

Surprisingly, same goal is more simple in a scoped application as you don't need to deal with JAVA objects.

```js
javascript:"active=true^nameIN" + new GlideTableHierarchy(current.u_table.name.toString()).getTables().toString();
```
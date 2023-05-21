# Find stuck events

> **⚠️ Disclaimer:** Please, always test the script in non-productive environments before running in a productive instance. Script was reviewed at the creation time but may break/get unexpected results due to instance versioning and SN API upgrades.

## Scenario

The following script determines and returns whether there are events stuck — meaning they were claimed by a node which, for whatever reason, is unavailable at the moment — thus, event won't be processed.

## Script

```js
(function () {
  var gaEvent = new GlideAggregate('sysevent');
  gaEvent.addAggregate('COUNT', 'claimed_by');
  gaEvent.addQuery('state', '!=', 'processed');
  gaEvent.addQuery('state', '!=', 'error');
  gaEvent.addQuery('state', '!=', 'transferred');
  gaEvent.addNotNullQuery('claimed_by');
  gaEvent.query();

  while (gaEvent.next()) {
    var node = gaEvent.getValue('claimed_by');
    var count = gaEvent.getAggregate('COUNT', 'claimed_by');

    var grNode = new GlideRecord('sys_cluster_state');

    grNode.addQuery('status', 'online');
    grNode.addQuery('system_id', node);
    grNode.query();

    grNode.hasNext()
      ? gs.log('No stuck events found')
      : gs.log('Unavailable node: ' + node + ' is claiming ' + count + ' events', 'stuck_event');
  }
})();
```
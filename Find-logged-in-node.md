# Find logged-in node

> ‼️ Please, always test the script in non-productive environments before running in a productive instance. Script was reviewed at the creation time but may break/get unexpected results due to instance versioning and SN API upgrades.

The following piece of code determines and returns the node which a specified user is logged into.

1. Replace variable username by the user ID
2. Execute as background script / fix script

```js
(function () {
	var username = 'admin'; //sys_user.user_name
	var userSession = new GlideRecord('v_user_session');
	userSession.addQuery('user', username);
	userSession.addActiveQuery();
	userSession.setLimit(1);
	userSession.query();
	
	if (!userSession.next()) {
		gs.log('User is not logged in');
		return;
	}
	
	var sessionID = userSession.getValue('session_id');
	var transactionLog = new GlideRecord('syslog_transaction');
	transactionLog.addQuery('session', sessionID);
	transactionLog.orderBy('sys_created_on');
	transactionLog.setLimit(1);
	transactionLog.query();

	if (!transactionLog.next()) {
		gs.log('Unknown error');
		return;
	}

	var systemId = transactionLog.getValue('system_id');
	systemId = systemId.split(':');
	var node = systemId[systemId.length - 1];
	gs.log(username + " is logged into node: " + node);
})();
```
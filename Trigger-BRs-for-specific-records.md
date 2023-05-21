# Trigger Business Rules for specific records

## Scenario 

This article explains different approaches to to trigger the execution of a business rule for a record without make any change to the record (it could be easily extended to multiple records).

## Case A: Business rules has no conditions and is triggered on update operation

This case is the most simple one, You just need to include the GlideRecord API method `.setForceUpdate()`.

`gr.update()` ****would do nothing when the record is not updated. Including `gr.setForceUpdate(true)` before forces the record to be updated therefore the business rule to be triggered*. (Assuming that the business rule runs on update operation).*

```js
var incGR = new GlideRecord('incident');
incGR.orderBy('sys_updated_on', 'desc');
incGR.setLimit(1);
incGR.query();
while (incGR.next()) {
	incGR.setForceUpdate(true);
	incGR.update();
}
```
## **Case B: Business rule has conditions or is not triggered on update operation**

In this scenario, the business rule would not run even including `gr.setForceUpdate(true)` because the conditions are not met. Likewise, if the business rule is not marked to run in update operations, the execution would not be triggered.

A simple (and dirty) solution could be copy / pase the script in a background script and execute it. If the business rule script has no logic and just calls a script include, this solutions could be acceptable in my opinion.

Another option is take advantage of the GlideScopedEvaluator API which allows you to evaluate scripts in a GlideRecord field (in out case, the GlideRecord is the business rule and the field is the script).

```js
(function() {
	var incGR = new GlideRecord('incident');
	incGR.orderBy('sys_updated_on', 'desc');
	incGR.setLimit(1);
	incGR.query();
	incGR.next();

	var br = new GlideRecord('sys_script');
	br.get('abc13f441b774110be17a9b4bd4bcb56');
	var ev = new GlideScopedEvaluator();

	try {
		var result = ev.evaluateScript(
			br, 
			'script', 
			{'current': incGR}
		);
		gs.info('Result: ' + result);
		inc.update(); //needed on onBefore BRs. Unnecessary on onAfter BRs
	} catch (e) {
		gs.error('Error in evaluating script: ' + e);
	};
})();
```

First and second steps are to obtain the GlideRecord of the business rule and the record(s) to be passed in. Next step, we force the execution of the script for the specific record thanks to the GlideScopedEvaluator object.

This script has to be executed also as a background script and might look unnecessary for one-time executions. But imagine that you may need to do this task from every once in a while and the business rule is susceptible to be eventually updated.. Saving the script as a fix script means you don't need to do anything the next time.
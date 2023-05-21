# GlideList - Remove options

> ‼️ This procedure implies DOM manipulation which is not considered a best practice. However, I didn’t find any other solution

## **Problem**

Glide list fields — *multiple choice list* — does not work properly with API methods `g_form.clearOptions()`, `g_form.addOption()`, and `g_form.removeOption()`, therefore it is not possible to add/remove choice values with client-side scripting.

## **Workaround**

*Example*

![Workaroung example screenshot](/images/glidelist-remove-options-1.png)

**⚠️ DOM manipulation**

![Workaroung client script screenshot](/images/glidelist-remove-options-2.png)

```js
function onChange(control, oldValue, newValue, isLoading, isTemplate) {
  // Remove option 'D'
  if (newValue == 'true') {
    var choiceBox = g_form.getElement('choice.u_test_table.u_multiple_choice_list');
    choiceBox.remove(choiceBox.length - 1);    
  }
}
```
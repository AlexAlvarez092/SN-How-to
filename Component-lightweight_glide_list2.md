# Component lightweight_glide_list2 

```html
<html>
    <!-- Without reference qualifier -->
    <div>
        <g2:lightweight_glide_list2 
            reference="sys_user" 
            id="incident_assigned_to"
            can_write="true" 
            start_locked="false" 
            control_name="incident_assigned_to" />
    </div>

    <!-- With reference qualifier -->
    <div>
        <j>
            <j:set 
              var="jvar_ref_qual_elements"
              value="active=true^department=CIO Office" />
            <g:macro_invoke 
                macro="lightweight_glide_list2" 
                reference="sys_user" 
                id="incident_assigned_to"
                list_data=""
                can_write="true"
                control_name="incident_assigned_to" />
        </j>
    </div>
</html>
```
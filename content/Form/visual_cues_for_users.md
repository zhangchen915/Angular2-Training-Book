# 用户的视觉提示

HTML5 provides `:invalid` and `:valid` pseudo-selectors for its input fields.

```
input[type="text"]:valid {
  border: 2px solid green;
}

input[type="text"]:invalid {
  border: 2px solid red;
}
```

Unfortunately, this system is rather unsophisticated and would require more manual effort in order to work with complex forms or user behavior.

Rather than writing extra code, and creating and enforcing your own CSS classes, to manage these behaviors, Angular 2 provides you with several classes, already accessible on your inputs.

```
/* field value is valid */
.ng-valid {}

/* field value is invalid */
.ng-invalid {}

/* field has not been clicked in, tapped on, or tabbed over */
.ng-untouched {}

/* field has been previously entered */
.ng-touched {}

/* field value is unchanged from the default value */
.ng-pristine {}

/* field value has been modified from the default */
.ng-dirty {}

```

Note the three pairs:

- valid / invalid
- untouched / touched
- pristine / dirty

These pairs can be used in many combinations in your CSS to change style based on the three separate flags they represent. Angular will toggle between the pairs on each input as the state of the input changes.
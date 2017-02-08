# Scope and styling

When you define a new Polymer element, every elements in the template tag would be part of the scope
of this new element. That means deeper customized elements would have their own scope and therefore
stylings defined in the style tag in the template tag would only apply on the element of the current
scope.
For instance,

```css
p {
  color: red;
}
```
will be translated to
```css
p.my-element {
  color: red;
}
```

If you want to apply a styling to every deeper elements of an element's template, in the style tag
you have to use the `::content` tag, e.g.



```css
::content p {
  color: red;
}
```

will be converted to
```css
p my-element {
  color: red;
}
```

*note :* in the method above, if you define `p` in the embedded element, this will overwrite the value of the current scope (or deeper scope if you use `::content`). That way it prevents modifying globally some stylings when deeper elements already define their style.

## Global styling

You can use the method above (using `::content`) to modify globally some styles. In fact, if you apply `::content` on the most higher element in the app hierarchy, it will modify all subsequent stylings (unless deeper element override the style as we saw). Or you can use the `is="custom-style"` key/value on a global `<style>` tag. E.g.


```html
<style is="custom-style">
  ::shadow p {
    color: green;
  }
</style>
```

(*note #1:* it's not recommanded to use `::shadow` because it's deprecated)
(*note #2:* also note, if customized element overwrite `p` in the example above, then `::shadow p` will have no effect, a solution could be to use a class on the paragraph you really want to apply a global effect, e.g. `::shadow p.my-style { ... }`)
# Danzerpress Layouts
What is the purpose of danzerpress layouts? I created this scss framework that is somewhat close to bootstrap. But instead of adding your naming schemes to html and being married to that you also now have the option to use it with sass. This is heavily reliant on sass maps so while it can be a little confusing it is incredibly powerful.

## Examples

Lets say you want 3 columns of items in the posts parent. You could attach the class `col-3` to the html class on each of those elements to get that effect:

```html
<div class="posts">
    <div class="items col-3">...</div>
    <div class="items col-3">...</div>
    <div class="items col-3">...</div>
</div>
```

This process can be cumbersome since the html is now set to need that class. What the sass framework allows is to set something like this. So it lets you html be a little looser and potentially more reusable.

```scss
.posts {
    @include row();
    .item {
        @include col($col-3);
    }
}
```

The `$col-3` is a variable that is set to something like this: 

```scss
$col-css: (
    'default': (
        padding: 0 15px,
        margin-bottom: 20px
    )
);

$col-3: (default: 1, md: 3, sm: 2, css: $col-css);
```

It looks a bit confusing but I'll try my best to explain what is going on here.

## Dynamic breakpoints with sass maps
So in the example above we can create dynamic breakpoints with maps. Lets take a deeper look at the `$col-3` variable.

```scss
$col-3: (default: 1, md: 3, sm: 2, css: $col-css);
```

`$col-3` is set to a scss map that will change the col number at different breakpoints that you can set. So `sm` will actually reference this souce of maps:

```scss
$xlg: 1400; 
$lg : 1300;
$md : 1024;
$sm : 768;
$xs : 480;

$mapped-breakpoints: (
    'xs' : $xs,
    'sm' : $sm,
    'md' : $md,
    'lg' : $lg,
    'xlg': $xlg
);
```

You can change this list but basically if you tried to set `smm: 1` it would fail because it's not in that `$mapped-breakpoints` list. But the `sm: 2` does exist so when it gets between `sm & md` the 3 columns will change to 2 columns. Following through a min-width mobile first approach.

I think one interesting thing here is that you can also pass css for any extra css you might want on different viewports. So as an example all columns by default have this `'default'` definition:

```scss
$col-css: (
    'default': (
        padding: 0 15px,
        margin-bottom: 20px
    )
);
```

But let's say some columns need to look different on larger screens well you can change that scss map to this:

```scss
$col-css: (
    'default': (
        padding: 0 15px,
        margin-bottom: 20px
    ),
    'lg': (
        padding: 0;
        margin: 0
    )
);
```

So basically in the scss it will loop through each of those and create the media queries for you for those specific breakpoints that are defined in `mapped-breakpoints`. So in this case the `'default'` is the default value with no media queries and `lg` is mapped to `1300` px so it will set a min-width of 1300px. It's a nice easy way of handling things rather than trying to use a mixin like `@breakpoint(1)` multiple times.

What it will look like compiled to css
```css
.item {
    padding: 0 15px;
    margin-bottom: 20px;
}

@media screen and (min-width: 1300px) {
    .item {
        padding: 0;
        margin: 0
    }
}
```
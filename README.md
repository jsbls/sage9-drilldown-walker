# Foundation 6 Drilldown Walker for Sage 9
A quick boilerplate for adding a Drilldown Menu Nav Walker to Sage 9, based on the code found at [WLCDesigns](https://wlcdesigns.com/2015/11/foundation-6-menu-walker-class-for-wordpress/).

_**Warning:** This only implements the Drilldown section on the Primary Navigation. That means no Top Bar, responsive toggles, registering additional menus or anything, but you should get the hang of it and extend it to your heart's desire. You can also get it working inside an [Off-Canvas Menu](http://foundation.zurb.com/sites/docs/off-canvas.html)._

## Creating a navigation.php file for our Walker Class
- Create a new file named `navigation.php` inside the `src` folder.
- Paste the following code:
```
<?php

class F6_Drill_Menu_Walker extends Walker_Nav_Menu
{
    /*
     * Add vertical menu class
     */

    function start_lvl( &$output, $depth = 0, $args = array() ) {
        $indent = str_repeat("\t", $depth);
        $output .= "\n$indent<ul class=\"vertical menu\">\n";
    }
}

function f6_drill_menu_fallback($args)
{
    /*
     * Instantiate new Page Walker class instead of applying a filter to the
     * "wp_page_menu" function in the event there are multiple active menus in theme.
     */

    $walker_page = new Walker_Page();
    $fallback = $walker_page->walk(get_pages(), 0);
    $fallback = str_replace("children", "children vertical menu", $fallback);
    echo '<ul class="vertical menu" data-drilldown="">'.$fallback.'</ul>';
}

?>
```

## Adding navigation.php to functions.php
- Open up `functions.php` on your theme's root folder and go to line 58.
- Change this line: ```['helpers', 'setup', 'filters', 'admin']);```
- To this: ```['helpers', 'setup', 'filters', 'navigation', 'admin']);```

## Fetching the Nav Menu
- Open the file where you want to display the menu (`header.blade.php` or wherever you want)
- Add the following code:
```
@php
  $mainNavArgs = [
    'container' => 'ul',
    'menu' => __('Primary Navigation', 'sage'),
    'menu_class' => 'vertical menu',
    'theme_location' => 'primary_navigation',
    'items_wrap'      => '<ul id="%1$s" class="%2$s" data-drilldown="">%3$s</ul>',
    //Recommend setting this to false, but if you need a fallback...
    'fallback_cb' => false,
    'walker' => new F6_Drill_Menu_Walker()
  ];
@endphp

{!! wp_nav_menu($mainNavArgs) !!}
```

And that's it, your brand new drilldown should appear, provided you already added the [correct markup](http://foundation.zurb.com/sites/docs/drilldown-menu.html). 

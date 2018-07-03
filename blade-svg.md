
SVG blade directive:

In setup.php:
  
```
/**
 * Create @svg() Blade directive
 */
sage('blade')->compiler()->directive('svg', function ($arguments) {
    // Accept multiple arguments
    list($path, $class) = array_pad(explode(',', trim($arguments, "() ")), 2, '');
    $path = trim($path, "' ");
    $class = trim($class, "' ");

    // Create the DOM document to remove the XML version element
    $svg = new \DOMDocument();
    $svg->load(svg_path($path));
    $svg->documentElement->setAttribute("class", $class);
    $output = $svg->saveXML($svg->documentElement);

    return $output;
});
```  

Working together with your helper function in helpers.php
  
```
/**
 * Return path of requested svg file
 * @param  string $file File name
 * @return string       file path
 */
function svg_path($file)
{
    return get_template_directory() . '/assets/svg/' . $file;
}
```

To be used in your views like this:
  
```
@svg('something.svg', 'your-custom-class')
```
Empty fields
============

This module provides a way to show empty fields that would otherwise be hidden.
It achieves this by appending new settings to existing display formatters. These
settings allow you to display a value using either:

* custom text, as defined by the field administrator;
* to show the results of a custom callback;
* or, to show the default empty value (if default is not empty itself).

This last option may be useful in case the field is not required and the
instance default value was added after content had been already created.

For example, you could have a list of checkboxes. If none are checked, normally
the entire field would be removed from the display. However, with this module
you could display something like "Status unknown", "Nothing selected" or "This
user failed their IQ test" when the field was empty.

Note that this can be achieved in a custom preprocess callback or
`hook_field_attach_view_alter()` if you know some PHP coding, but this module
makes it configurable in the user interface.

Installation
------------

1. Install this module using the official Backdrop CMS instructions at
<https://backdropcms.org/guide/modules>.

Configuration
-------------

The module can be configured on each content type's display formatter.

API
---

For specific use-cases, you can define a custom callback to generate dynamic
content.

First, implement `hook_empty_fields()`. This returns an array indexed by the
class name that implements the handler.

```php
/**
 * Implements hook_empty_fields().
 */
function HOOK_empty_fields() {
  $items = array(
    'CurrentTimeEmptyFieldText' => array(
      'title' => t('Display current time if empty'),
    ),
  );
  return $items;
}
```

Create a new concrete class that extends the abstract class EmptyFieldHandler.

```php
/**
 * @file
 * Contains the CurrentTimeEmptyFieldText plugin for EmptyFieldHandler.
 */

/**
 * Defines CurrentTimeEmptyFieldText class.
 */
class CurrentTimeEmptyFieldText extends EmptyFieldHandler {

  /**
   * Implementation of EmptyFieldText::react().
   */
  public function react($context) {
    return format_date(time());
  }

  /**
   * Implementation of EmptyFieldText:summaryText().
   */
  public function summaryText() {
    return t('Empty Text: current time');
  }

}
```

Register this class in your module:

```php
/**
 * Implements hook_autoload_info().
 */
function YOUR_MODULE_autoload_info() {
  return array(
    'CurrentTimeEmptyFieldText' => 'plugins/empty_fields_handler_current_time.inc',
  );
}
```

The context has the entity_type, entity, view_mode as well as the empty field
details, field and instance.

If the callback is empty or a zero-length string, the empty field will not be
rendered.

License
-------

This project is GPL v2 software. See the LICENSE.txt file in this directory
for complete text.

Maintainers
-----------

* [Herb v/d Dool](https://github.com/herbdool)

Credits
-------

Ported to Backdrop by [Herb v/d Dool](https://github.com/herbdool).

Drupal version currently maintained by:

* [Alan D.](https://www.drupal.org/u/alan-d)
* [rypit](https://www.drupal.org/u/rypit)
* [andypost](https://www.drupal.org/u/andypost)

---
title: "Palettes"
description: "Back end form layout for editing records."
weight: 4
aliases:
  - /reference/dca/palettes/
---


A palette is a group of form fields which are required to edit a record. A
palette typically does not include all columns of a table but only the ones that
belong to a particular module or content element. Palettes can change
dynamically depending on the user's permissions or type of element and certain
subparts of the form (called subpalettes) can be loaded interactively via Ajax.


## Main palettes

Your palettes need to be added to the `palette` key of your table's DCA. This
will be an associative array which can contain multiple palette definitions. In
the most basic form you will have at least a `default` palette:

```php
// contao/dca/tl_example.php
$GLOBALS['TL_DCA']['tl_example'] = [
    // …
    'palettes' => [
        'default' => '{title_legend},title,alias,addImage',
    ],
    // …
];
```


## Sub palettes

Sub palettes are defined in the `subpalettes` key of your table's DCA. It is an
associative array with the key being a field from the main palette and the value
being the sub palette definition with additional fields to be added. These
fields will be appended to the field of the main palette when that field is
active. Additionally, you have to add a `__selector__` field to the main palette
definition.

```php
// contao/dca/tl_example.php
$GLOBALS['TL_DCA']['tl_example'] = [
    // …
    'palettes' => [
        '__selector__' => ['addImage'],
        'default' => '{title_legend},title,alias,addImage',
    ],
    'subpalettes' => [
        'addImage' => 'singleSRC,size'
    ],
    // …
];
```

In the above example the fields `singleSRC` and `size` will be shown after the
`addImage` checkbox field, when `addImage` has been activated.

{{% notice tip %}}
When working with sub palettes you typically want to enable the `submitOnChange` `eval` option of your `__selector__` field. This will cause
the sub palette to immediately appear or disappear, when the value of the field changes.
{{% /notice %}}


## Sub palettes with selects

Instead of reacting to a checkbox, you can also define different sub palettes
depending on different value for a select input field:

```php
// contao/dca/tl_example.php
$GLOBALS['TL_DCA']['tl_example'] = [
    // …
    'palettes' => [
        '__selector__' => ['selectField'],
        'default' => '{title_legend},title,alias,selectField',
    ],
    'subpalettes' => [
        'selectField_value1' => 'field1,field2',
        'selectField_value2' => 'field3,field4',
    ],
    // …
];
```

In this case the sub palette key needs to be in the form of
`fieldName_fieldValue`. Depending on the field's value, the appropriate sub
palette will be inserted.


## Multiple main palettes

A defined `__selector__` can also switch between multiple main palettes. The
main palette's key needs to be the selector's value.

```php
// contao/dca/tl_example.php
$GLOBALS['TL_DCA']['tl_example'] = [
    // …
    'palettes' => [
        '__selector__' => ['type'],
        'default' => '{title_legend},type',
        'text' => '{title_legend},type,textField',
        'image' => '{title_legend},type,imageField',
    ],
    // …
];
```


## Defining groups

A palette is a string of field names which are concatenated with either a
semicolon (;) or a comma (,). Whereas the comma is just used to separate the
field names, the semicolon indicates the beginning of a new fieldset, which can
be expanded and collapsed. Typically, each group will also have a specific _legend_.

![palette example]({{% asset "images/dev/reference/palette_example.png" %}}?classes=shadow)

The above example is defined by the following code:

```php
{title_legend},headline,alias,author;{date_legend},date,time;{teaser_legend:hide},subheadline,teaser
```

The `title_legend` and `date_legend` placeholders will be replaced with the
corresponding labels from the "TL_LANG" array.

```php
// contao/languages/en/tl_news.php
$GLOBALS['TL_LANG']['tl_news']['title_legend'] = 'Title and author';
$GLOBALS['TL_LANG']['tl_news']['date_legend']  = 'Date and time';
```


## Arranging fields

The Contao back end uses a simple two-column grid system to arrange input fields
within their groups. You can apply the following CSS classes in the evaluation
section of the Data Container Array as `tl_class` (e.g. `'tl_class' => 'w50 wizard'`).

| `tl_class` | Description |
| --- | --- |
| `w25` | Set the field width to 25% and float it (`float:left`). {{< version-tag "5.1" >}} |
| `w33` | Set the field width to 33.33333333% and float it (`float:left`). {{< version-tag "5.1" >}} |
| `w50` | Set the field width to 50% and float it (`float:left`). |
| `w66` | Set the field width to 66.66666666% and float it (`float:left`). {{< version-tag "5.1" >}} |
| `w75` | Set the field width to 75% and float it (`float:left`). {{< version-tag "5.1" >}} |
| `clr` | Clear all floats (`clear:both`). Use this for any full length field coming after a `w50` field within a fieldset (see note below). |
| `wizard` | Shorten the input field so there is enough room for the wizard button (e.g. date picker fields). |
| `long` | Make the input field take the full available width. |
| `cbx` | Set the field minimum height to 46 pixels (used for single checkboxes; more space at bottom). |
| `m12` | Adds a 17 pixels top and bottom padding to the element (used for single checkboxes; more space at top and bottom). |
| `cbx m12` | A combination of cbx and m12 sets the minimum height of the field to 80 pixels (used for single checkboxes; more space at top and bottom). |

{{% notice note %}}
Due to the nature of floating elements in CSS you need to be careful about which fields of your palette are 50% width
and thus are floating and which are not. For example if a 50% (`w50`) field is immediately followed by a full length
field then you must add the `clr` class to the latter, otherwise the layout might break (e.g. the full length field 
floating in the wrong place or being overlayed by other fields).
{{% /notice %}}


## Manipulating palettes

Palette definitions are simple strings and thus can be modified in their Data
Container Array via string replacement for example. This can be a cumbersome
tasks, especially if you want to add or remove fields at specific positions or
groups. The [Contao Palette Manipulator][1] can help in such a case.

[1]: ../../../framework/dca/palettemanipulator/
[2]: ../reference/fields/

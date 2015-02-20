# collective.ttw

Happy hacking for Plone

## Origin

**Hackability is not a bug, it is a feature.**

See [Turning hackability into a use case](http://makina-corpus.com/blog/metier/2015/turning-hackability-into-a-use-case)

## Principle

The ZMI is ugly and old, we want something more fancy to hack Plone.

collective.ttw proposes to use massively the Plone theming editor to customize non themed-related elements.

## Features

### Regular Plone views

- Templates contained in the `/overrides` folder would be editable using the theming editor and would be plugged properly in `z3c.jbot`.

- A more user-friendly approach could be to enable content-to-content transformation in Diazo, so any part of a Plone page could be easily replaced by a custom view (see below).

### Custom views

We can create **views** through the theming editor (in `/custom` for instance), they would behave as actual Browser Views, and they would be built that way:
- templates: just basic ZPT templates
- Python: Python files containing functions.

For instance, we create a file named `myviews` containing:
```python
def menu():
    template = getTemplate('menu.pt')
    menus = ['Try it for free', 'Subscribe']
    return template(menus=menus)

def get_description(obj):
    return obj.Description()
```

Then we can get the menu resulting html by accessing `./@@ttw/myviews/menu`, and it can be used for instance in our Diazo `rules.xml` like this:
```xml
<replace 
    css:theme="#menu"
    href="./@@ttw/myviews/menu" />
```
In content-to-content mode:
```xml
<replace 
    css:content="#portal-siteactions"
    href="./@@ttw/myviews/menu" />
```
It could also be inserted as a portlet.

Regarding the `get_description` function, it could be called from a template like this:
```html
<div tal:content="python:context.unrestrictedTraverse('@@ttw/myviews').get_description(context.news)" />
```

## File-system / TTW round trip

Everything is managed in our Diazo theme, TTW defined items can be exported and then stored in an actual Plone module in the `/static` folder.

## Implementation

### Security

Python code will be compiled and run securely using `zope.untrustedpython.interpreter`, and will receive as parameter the current context and request.

---
tags: python django
---
I'm really tired of searching solutions everywhere.

This article is about to set timezone in django.

Here's the snippet of code to set showing format in django.
```python
DATE_FORMAT = 'Y-m-d'
TIME_FORMAT = 'H:i:s'
DATETIME_FORMAT = 'Y-m-d H:i:s'
```
Also, there's input format besides showing format.
```python
DATE_INPUT_FORMATS = 'Y-m-d'
TIME_INPUT_FORMATS = 'H:i:s'
DATETIME_INPUT_FORMATS = 'Y-m-d H:i:s'
```

Here are the relationship of data field and the format

| Format   | Model Field   |
|----------|---------------|
| DATE     | DateField     |
| TIME     | TimeField     |
| DATETIME | DateTimeField |

Still, we need some other modifications to assure the settings above works.

```python
USE_L10N = False
USE_TZ = False
```

That's done. Now the django will show and set date time as `yyyy-mm-dd hh:mm:ss` in your local timezone.

###### P.S. The date or time stores in django database always uses timezone UTC+0:00. All the settings only changes the way shows on the front-end.
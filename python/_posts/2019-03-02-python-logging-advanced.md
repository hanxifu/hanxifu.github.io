---
layout: post
author: Hanxi Fu
tags: [python]
---
# Python Logging Advanced

## Overview

We all need logging while running scripts automatically.

This article is about how to generate great format log and how to log over files and functions.

## Additional Logging Modules

- colorlog

## Configuration

Colorlog includes its own StreamHandler, so we have to comment these lines in order to customize our own logger.

Here are the lines, locates in `<python path>/Lib/site-packages/huey/consumer_options.py`

```python
if logger is None:
    logging.basicConfig(level=loglevel, format=logformat)
    logger = logging.getLogger()
else:
    logger.setLevel(loglevel)
```

## Start to write our own Logger

First of all, we should think about what features should this logger have. Ordered by the importance.

1. Automatically add start and end messages for some functions.
2. Show correct filename and funcName, even in decorators.
3. In great format, even for multi-line messages.
4. Colorful.

So let's start with this order.

### Automatically add start and end messages for some functions.

This should be implemented with decorator. We should also pass an argument `logger` in order to show the correct name instead of filename.

```python
# log_util.py

import logging
import functools


def get_logger(name: str, level=logging.INFO):
    logger = logging.getLogger(name)

    # Return the logger to avoid logging duplicate.
    if logger.handlers:
        return logger

    # Handle the not allowed parameter cases.
    if level not in [logging.DEBUG,
                     logging.INFO,
                     logging.WARNING,
                     logging.ERROR,
                     logging.CRITICAL]:
        level = logging.INFO

    logger.setLevel(level)
    fmt = '%(asctime)s %(filename)s %(funcName)s %(levelname)s %(message)s'
    fmt_date = '%Y-%m-%d %T'
    stream_handler = logging.StreamHandler()
    stream_handler.setFormatter(logging.Formatter(fmt, datefmt=fmt_date))
    logger.addHandler(stream_handler)

    return logger

```

### Show correct funcName and filename by using functools.wraps()
```python
# log_util.py

...

def task_log(logger):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            logger.info(f'Starting {func.__name__}.')
            func(*args, **kwargs)
            logger.info(f'Finished {func.__name__}.')

        return wrapper

    return decorator

```

```python
# a.py

from log_util import task_log, get_logger

logger = get_logger('a')


@task_log(logger)
def a():
    logger.info('a')

```

```python
# b.py

from log_util import task_log, get_logger

logger = get_logger('b')


@task_log(logger)
def b():
    logger.info('b')

```

if we run:
```python
>>> from a import a
>>> from b import b
>>> a()
>>> b()
```

The log will be:
```
2019-03-04 21:47:26 log_util.py wrapper INFO Starting a.
2019-03-04 21:47:26 a.py a INFO a
2019-03-04 21:47:26 log_util.py wrapper INFO Finished a.

2019-03-04 21:47:27 log_util.py wrapper INFO Starting b.
2019-03-04 21:47:27 b.py b INFO b
2019-03-04 21:47:27 log_util.py wrapper INFO Finished b.
```

### Same indent with multi-line log

What's next? It seems like all fine.
But when it goes to multi-line messages, what will it like?

Now we change a.py to:
```python

@task_log(logger)
def a():
    logger.info('line 1\n'
                'line 2\n')

```

Log will be:
```
2019-03-04 21:50:00 log_util.py wrapper INFO Starting a.
2019-03-04 21:50:00 a.py a INFO line 1
line 2
2019-03-04 21:50:00 log_util.py wrapper INFO Finished a.
```

That's not so pretty.

TO BE CONTINUED

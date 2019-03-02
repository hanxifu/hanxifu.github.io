---
layout: post
author: Hanxi Fu
tags: [python, django]
---
# Django Task Queue on Windows

## Overview

Recently I'm doing some research on the python task queue to work with django running on windows.

So here're some general task queue projects on Python.

- [celery](http://www.celeryproject.org/) - Based on Redis
- [python-rq](http://python-rq.org/) - Based on Redis
- [huey](https://github.com/coleifer/huey) - Based on Redis or Sqlite
- others

Redis is not supported on original Windows, so we do not consider it anymore.
That's why I choose huey + sqlite, (which is django included already.)

## Installation

I assume we have installed:

- python 3
- django 2.1

`pip install huey`

## Configuration

 - `settings.py`
```python
INSTALLED_APPS = [
    ...
    'huey.contrib.djhuey',
]
```
add 
# Setting up logging on the Specify 7 server

The Specify 7 server can be configured to log a great deal of
information. The logging is through the
[Django logging system](https://docs.djangoproject.com/en/dev/topics/logging/).

## Logging configuration

Logging configuration is specified in the
`specify7/specifyweb/settings/logging_settings.py` or
`specify7/specifyweb/settings/local_logging_settings.py`. The latter
takes precedent if it exists.

### Example configuration

Below is an example `local_logging_settings.py` that records events at
the *Debug* or higher level from Specify and the Django request
modules to a log file in `/home/specify/logs/`. 


```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': True,
    'formatters': {
        'standard': {
            'format': '[%(asctime)s] [%(levelname)s] [%(name)s:%(lineno)s] %(message)s',
            'datefmt': "%d/%b/%Y %H:%M:%S"
        },
    },
    'filters': {
        'require_debug_false': {
            '()': 'django.utils.log.RequireDebugFalse'
            }
     },
    'handlers': {
        'mail_admins': {
            'level': 'ERROR',
            'filters': ['require_debug_false'],
            'class': 'django.utils.log.AdminEmailHandler'
        },
        'console': {
            'level': 'DEBUG',
            'class': 'logging.StreamHandler',
        },
        'logfile': {
            'level': 'DEBUG',
            'class': 'logging.handlers.RotatingFileHandler',
            'filename': '/home/specify/logs/specify.log',
            'maxBytes': 1024*1024,
            'backupCount': 10,
            'formatter': 'standard',
        }
    },
    'loggers': {
        'specifyweb': {
            'handlers': ['logfile'],
            'level': 'DEBUG',
            'propagate': True,
        },
        'django.request': {
            'handlers': ['mail_admins', 'logfile'],
            'level': 'DEBUG',
            'propagate': True,
        },
    }
}
```

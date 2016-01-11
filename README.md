[![PyPI version](https://img.shields.io/pypi/v/django-pluggableappsettings.svg)](http://badge.fury.io/py/django-pluggableappsettings) [![Build Status](https://travis-ci.org/NB-Dev/django-pluggableappsettings.svg?branch=master)](https://travis-ci.org/NB-Dev/django-pluggableappsettings) [![Coverage Status](https://coveralls.io/repos/NB-Dev/django-pluggableappsettings/badge.svg?branch=master&service=github)](https://coveralls.io/github/NB-Dev/django-pluggableappsettings?branch=master) [![Downloads](https://img.shields.io/pypi/dm/django-pluggableappsettings.svg)](https://pypi.python.org/pypi/django-pluggableappsettings/) [![Supported Python versions](https://img.shields.io/pypi/pyversions/django-pluggableappsettings.svg)](https://pypi.python.org/pypi/django-pluggableappsettings/) [![License](https://img.shields.io/pypi/l/django-pluggableappsettings.svg)](https://pypi.python.org/pypi/django-pluggableappsettings/) [![Codacy Badge](https://api.codacy.com/project/badge/grade/79d4fa62bb77478392d9535067d010c6)](https://www.codacy.com/app/tim_11/django-pluggableappsettings)

#django-pluggableappsettings


This app provides a baseclass to easily realize AppSettings for any django project. The advantage of using an
AppSettings class lies in the possibility for the programmer to assign default values for settings if the setting is
not present in the main settings.py

##Requirements:

* Django >= 1.6

##Quick start

1. Install django-pluggableappsettings
    * From the pip repository: ```pip install django-pluggableappsettings```
    * or directly from github: ```pip install git+git://github.com/NB-Dev/django-pluggableappsettings.git``

2. Create your AppSettings class in any of your project's files. E.g. in 'app_settings.py'.

3. Define your settings by setting the class attributes as one of the provided settings types

	```
	from django_pluggableappsettings import AppSettings, Setting
	
	class MyAppSettings(AppSettings):
		MY_SETTING = Setting('DEFAULT_VALUE')
	```

4. Access the setting from anywhere:
	```
	from app_settings import MyAppSettings
	setting = MyAppSettings.MY_SETTING
	```

##Provided Setting Types

Different setting types are provided with the package:

###Setting(default_value, aliases)

The most basic setting that simply returns the value from the settings.py or, if that is not set, the default value.
If no default value is provided and the setting is not set in your settings.py, an ```AttributeError``` is thrown.
Also a list of aliases can be passed to allow for multiple names of one setting (e.g. for backwards compatibility)


###CalledOnceSetting(default_value, aliases)

Behaves as a Setting but checks whether the value is callable and calls it before returning.

**Attention:** The call is only performed on the first access of the setting and cached afterwards.
If you need a function that is called each time use the `CalledEachTimeSetting`

###CalledEachTimeSetting(default_value, aliases)

Behaves as a Setting but checks whether the value is callable. The callable is called each time when the setting is
accessed.


###ClassSetting(default_value, aliases)

Behaves as a Setting but accepts only Classes or dotted paths to classes as values. If the value is a dotted path, the
path is translated to a class before returning, so the returned value is always a class.

###IntSetting(default_value, aliases)

Accepts only values that are of type int or can be casted to type int

###FloatSetting(default_value, aliases)

Accepts only values of type float of values that can be casted to type float

###StringSetting(default_value, aliases)

Accepts only strings as value

###IterableSetting(default_value, aliases)

Makes sure that the value is an iterable

###TypedSetting(default_value, aliases)

A class that checks whether the given value is of a certain type and optionally allows casting the value to that type.
Used as a base class for all type checking classes and can be easily subclassed to allow checking of various
value types.

To create your own type checking setting simply subclass this type and set the class attributes `_setting_type`
and `_cast_value` according to your needs. The `_setting_type` attribute specifies the desired type while the
`_cast_value` attribute specifies whether the value should be casted to the `_setting_type`. A `_cast_value`
of `True` essentially results in a call of `value = _setting_type(value)`.

E.g. The `IntSetting` is defined as follows:
```
class IntSetting(TypedSetting):
    """
    An integer setting
    """
    _setting_type = int
    _cast_value = True
```

If you need more elaborate casting functions, you can overwrite the `cast_value(self, value)` function
of your type which should return the casted value.

##Accessing Values

You can access any setting by simply importing your AppSettings class and accessing the corresponding attribute. 

##Running the tests

The included tests can be run standalone by running the `tests/runtests.py` script. You need to have Django and
mock installed for them to run. If you also want to run coverage, you need to install it before running the tests


##CHANGELOG

###Development
- Changing structure of Setting class to being able to add repeatedly called functions as setting.
 
 **Warning**: This breaks compatibility of custom settings classes. To fix this, simply rename the ```get``` method of
 your custom classes to ``_get``
- Added a ```CalledEachTimeSetting``` that takes a callable that is called each time the setting's value is accessed
- Renamed the ```CallableSetting``` to ```CalledOnceSetting``` to make the differentiation to the ```CalledEachTimeSetting```
 clearer. The old name will stay as an alias for now.

###v.1.0.0
- Releasing first stable version

###v.0.2.3
- Added 'aliases' parameter to ```Setting``` to allow multiple names for one setting (e.g. for backwards compatibility)

###v.0.2.2
- Extended code to also work with Python 3

###v.0.2.1
- Added ```TypedSetting``` Setting type which allows for the setting to be typechecked
- Added ```IntSetting```, ```FloatSetting```, ```StringSetting``` and ``ÌterableSetting``` as subtypes of ```TypedSetting``

###v.0.2.0

- Added the changelog
- Redesign of settings to allow different types of settings that can now also provide type checking.
- Settings are now explicitly defined and no ```_DEFAULT_``` prefix is needed anymore
- Also no staticmethod decorator is needed anymore

## Maintainers
This Project is maintaned by [Northbridge Development Konrad & Schneider GbR](http://www.northbridge-development.de) Softwareentwicklung

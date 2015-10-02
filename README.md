[![PyPI version](https://img.shields.io/pypi/v/django-pluggableappsettings.svg)](http://badge.fury.io/py/django-pluggableappsettings) [![Build Status](https://travis-ci.org/NB-Dev/django-pluggableappsettings.svg?branch=master)](https://travis-ci.org/NB-Dev/django-pluggableappsettings) [![Coverage Status](https://coveralls.io/repos/NB-Dev/django-pluggableappsettings/badge.svg?branch=master&service=github)](https://coveralls.io/github/NB-Dev/django-pluggableappsettings?branch=master) [![Downloads](https://img.shields.io/pypi/dm/django-pluggableappsettings.svg)](https://pypi.python.org/pypi/django-pluggableappsettings/) [![Supported Python versions](https://img.shields.io/pypi/pyversions/django-pluggableappsettings.svg)](https://pypi.python.org/pypi/django-pluggableappsettings/) [![License](https://img.shields.io/pypi/l/django-pluggableappsettings.svg)](https://pypi.python.org/pypi/django-pluggableappsettings/)

#django-pluggableappsettings


This app provides a baseclass to easily realize AppSettings for any django project. The advantage of using an
AppSettings class lies in the possibility for the programmer to assign default values for settings if the setting is
not present in the main settings.py

**Attention:** The project is fairly new and not stable yet.

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

Three different setting types are provided with the package:

###Setting

The most basic setting that simply returns the value from the settings.py or, if that is not set, the default value.
If no default value is provided and the setting is not set in your settings.py, an ```AttributeError``` is thrown.

###CallableSetting

Behaves as a Setting but checks whether the value is callable and calls it if possible before returning.

**Attention:** As each setting is only loaded once and then stored in a cache, the call is only performed on the first
access of the setting.

###ClassSetting

Behaves as a Setting but accepts only Classes or dotted paths to classes as values. If the value is a dotted path, the
path is translated to a class before returning, so the returned value is always a class.

###IntSetting

Accepts only values that are of type int or can be casted to type int

###FloatSetting

Accepts only values of type float of values that can be casted to type float

###StringSetting

Accepts only strings as value

###IterableSetting

Makes sure that the value is an iterable

###TypedSetting

A class that checks whether the given value is of a certain type and optionally allows casting the value to that type.
Used as a base class for all type checking classes and can be easily subclassed to allow checking of various
value types.

To create your own type checking Setting simply subclass this type and set the class attributes ```_setting_type```
and ```_cast_value``` according to your needs. The ```_setting_type``` attribute specifies the desired type while the
```_cast_value``` attribute specifies whether the value should be casted to the ```_setting_type```. A ```_cast_value```
of ```True``` essentially results in a call of ```value = _setting_type(value)```.

E.g. The ```IntSetting``` is defined as follows:
```
class IntSetting(TypedSetting):
    """
    An integer setting
    """
    _setting_type = int
    _cast_value = True
```

If you need more elaborate casting functions, you can overwrite the ```cast_value(self, value)``` function
of your type which should return the casted value.

##Accessing Values

You can access any setting by simply importing your AppSettings class and accessing the corresponding attribute. 

##Running the tests

The included tests can be run standalone by running the ```tests/runtests.py``` script. You need to have Django and
mock installed for them to run. If you also want to run coverage, you need to install it before running the tests


##CHANGELOG

###v.0.2.1
- Added ```TypedSetting``` Setting type which allows for the setting to be typechecked
- Added ```IntSetting```, ```FloatSetting```, ```StringSetting``` and ``ÌterableSetting``` as subtypes of ```TypedSetting``

###v.0.2.0

- Added the changelog
- Redesign of settings to allow different types of settings that can now also provide type checking.
- Settings are now explicitly defined and no ```_DEFAULT_``` prefix is needed anymore
- Also no staticmethod decorator is needed anymore

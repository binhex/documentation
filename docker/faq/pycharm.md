# **PyCharm Docker FAQ**

**Q1.** I want to use a different version of Python compared to the included version. How can I do this?

**A1.** This can be achieved by using the included tool `pyenv`. This utility allows you to install multiple different versions of Python to a set path.

An example of installing Python v3.11.0 would be:

`pyenv install 3.11.0`

If you want to install to a specific location, you can set this via the env var `PYENV_ROOT`. For example, to install Python v3.11.0 to '/tmp':

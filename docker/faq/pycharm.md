# **PyCharm Docker FAQ**

**Q1.** I want to use a different version of python compared to the included version, how can i do this?

**A1.** This can be achieved by using the included tool `pyenv`, this utility allows you to install multiple different versions of python to a set path.

An example of installing python v3.11.0 would be:-

`pyenv install 3.11.0`

If you want to install to a specific location then you can set this via env var `PYENV_ROOT` e.g. to install python 3.11.0 to '/tmp':-

``` bash
export PYENV_ROOT='/tmp'
pyenv install 3.11.0
```

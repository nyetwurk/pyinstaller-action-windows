# PyInstaller-Action-Windows

Github Action for building executables with PyInstaller

To build your application, you need to specify where your source code is via the `path` argument, this defaults to `src`.

The source code directory should have your `.spec` file that PyInstaller generates. If you don't have one, you'll need to run PyInstaller once locally to generate it.
Also if you have another program `.spec` file you can set specific pyinstaller `.spec` file by `spec: <YOUR_SPEC_FILE_NAME>`

If the `src` folder has a `requirements.txt` file, the packages will be installed into the environment before PyInstaller runs. This can also be specified via the `requirements` argument.

If you wish to specify a package mirror, this is possibly via the `pypi_url` and/or the `pypi_index_url`, these defaults are:

- `pypi_url` = `https://pypi.python.org/`
- `pypi_index_url` = `https://pypi.python.org/simple`

> If you are using the default Python `gitignore` file, ensure to remove `.spec`.

> This action uses [Wine](https://www.winehq.org) to emulate windows inside Docker for packaging POSIX executables.

## CURRENT ISSUE: `ModuleNotFoundError pkg_resources.extern`
The pkg_resources hook for setuptools>=v70.0.0 is currently missing in pyinstaller 5.13, which is used in this Github Action.

To address this, a future version of this project will incorporate pyinstaller>=6.7 to resolve the issue. 
However, in the interim, it is necessary to manually include a hiddenimport to the `.spec` file.

Include `hiddenimports=['pkg_resources.extern'],` in your `.spec` file, something like this:
```
a = Analysis(

    hiddenimports=['pkg_resources.extern'],
)
```

For further assistance, please refer to the following issues:
- JackMcKew/pyinstaller-action-windows#51
- pyinstaller/pyinstaller#8554


## Example usage

There's an example repository where you can see this action in action: <https://github.com/JackMcKew/pyinstaller-action-windows-example>.

Include this in your `.github/workflows/main.yaml`:

```yaml
- name: PyInstaller Windows
  uses: JackMcKew/pyinstaller-action-windows@main
  with:
    path: src
```

## Full Example

Here is an entire workflow for:

- Packaging an application with PyInstaller
- Uploading the packaged executable as an artifact

``` yaml

name: Package Application with Pyinstaller

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v4

    - name: Package Application
      uses: JackMcKew/pyinstaller-action-windows@main
      with:
        path: src

    - uses: actions/upload-artifact@v4
      with:
        name: name-of-artifact
        path: src/dist/windows
```

## FAQ

If you get this error: `OSError: [WinError 123] Invalid name: '/tmp\\*'`, ensure your path is correctly configured, the default is `src`.

## Sources

A big thank you to all the contributors over at <https://github.com/cdrx/docker-pyinstaller>, this action is just a modified version of their docker container, thank you!

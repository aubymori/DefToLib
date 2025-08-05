# DefToLib
This is a script for MSVC/MSBuild that generates a .lib file from just a .def file, which is particularly useful
for importing undocumented functions.

## Background
Using just `lib.exe` for x64/ARM64 works fine because it assumes `__cdecl` which x64/ARM64 always use. It does not
work for x86, however. This is because DLL-exported functions in x86 will most of the time use `__stdcall`, which
`lib.exe` is not set up to handle. For no-name (ordinal) functions, you can bypass this by appending the `__stdcall`
decoration in the .def file. However, for named functions, this will result in the wrong name being imported by the
resulting binary.

## How to use
1. Put `DefToLib.ps1` in the `scripts` folder in the root of your solution.
2. Put the contents of `For your .vcxproj.xml`,
somewhere inside the `Project` root element in your .vcxproj file.
3. Inside your project, create a `defs` directory.
4. Any `.def` file inside that directory will automatically be compiled and linked to your project.
   Make sure to read the [Syntax](#syntax) section before making any .def files.

## Syntax
In the `EXPORTS` section of your .def file, *any* function you need to import needs to have a comment at
the end of the line with the format `;argsize=XX`, where `XX` is the size of the arguments visible in the
`__stdcall` decoration.

**Example**:
```
LIBRARY WINBRAND
EXPORTS
    BrandingLoadImage ;argsize=24
```
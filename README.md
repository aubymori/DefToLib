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
1. Clone this repository into your project. For example, `<ProjectRoot>\libs\DefToLib`.
2. In your .vcxproj file, add the following line after `<Import Project="$(VCTargetsPath)\Microsoft.Cpp.targets" />`:
   ```xml
   <Import Project="libs\DefToLib\DefToLib.targets" Condition="Exists('libs\DefToLib\DefToLib.targets')" />
   ```
   Adjust the path as necessary to match your project structure.
3. In your project's root directory, create a `defs` directory (`<ProjectRoot>\defs`).
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
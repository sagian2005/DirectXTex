![DirectX Logo](https://raw.githubusercontent.com/wiki/Microsoft/DirectXTex/X_jpg.jpg)

# DirectXTex texture processing library

http://go.microsoft.com/fwlink/?LinkId=248926

Copyright (c) Microsoft Corporation.

## July 9, 2025

This package contains DirectXTex, a shared source library for reading and writing ``.DDS`` files, and performing various texture content processing operations including resizing, format conversion, mip-map generation, block compression for Direct3D runtime texture resources, and height-map to normal-map conversion. This library makes use of the Windows Image Component (WIC) APIs. It also includes ``.TGA`` and ``.HDR`` readers and writers since these image file formats are commonly used for texture content processing pipelines, but are not currently supported by a built-in WIC codec.

This code is designed to build with Visual Studio 2019 (16.11), Visual Studio 2022, clang for Windows v12 or later, or MinGW 12.2. Use of the Windows 10 May 2020 Update SDK ([19041](https://walbourn.github.io/windows-10-may-2020-update-sdk/)) or later is required for Visual Studio. It can also be built for Windows Subsystem for Linux using GCC 11 or later.

These components are designed to work without requiring any content from the legacy DirectX SDK. For details, see [Where is the DirectX SDK?](https://aka.ms/dxsdk).

## Directory Layout

* ``DirectXTex\``

  * This contains the DirectXTex library. This includes a full-featured DDS reader and writer including legacy format conversions, a TGA reader and writer, a HDR reader and writer, a WIC-based bitmap reader and writer (BMP, JPEG, PNG, TIFF, and HD Photo), and various texture processing functions. This is intended primarily for tool usage.

> The majority of the header files here are intended for internal implementation of the library only (``BC.h``, ``BCDirectCompute.h``, ``DDS.h``, ``DirectXTexP.h``, etc.). Only ``DirectXTex.h`` and ``DirectXTex.inl`` are meant as the 'public' header for the library.

* ``Auxiliary\``

  * Contains optional source files for the DirectXTex library, such as adapter loading functions using the OpenEXR library, Xbox texture tiling extensions, etc.

* ``Common\``

  * Contains shared source headers used by the DirectXTex library and tools.

* ``Texconv\``

  * This DirectXTex sample is an implementation of the [texconv](https://github.com/Microsoft/DirectXTex/wiki/Texconv) command-line texture utility from the DirectX SDK utilizing DirectXTex rather than D3DX.

    It supports the same arguments as the _Texture Conversion Tool Extended_ (``texconvex.exe``) legacy DirectX SDK utility. The primary differences are the ``-10`` and ``-11`` arguments are not applicable and the filter names (``POINT``, ``LINEAR``, ``CUBIC``, ``FANT`` or ``BOX``, ``TRIANGLE``, ``*_DITHER``, ``*_DITHER_DIFFUSION``). This also includes support for the JPEG XR (HD Photo) bitmap format.

* ``Texassemble\``

  * This DirectXTex sample is a [command-line utility](https://github.com/Microsoft/DirectXTex/wiki/Texassemble) for creating cubemaps, volume maps, or texture arrays from a set of individual input image files.

* ``Texdiag\``

  * This DirectXTex sample is a [command-line utility](https://github.com/Microsoft/DirectXTex/wiki/Texdiag) for analyzing image contents, primarily for debugging purposes.

* ``DDSView\``

  * This DirectXTex sample is a simple Direct3D 11-based viewer for DDS files. For array textures or volume maps, the "<" and ">" keyboard keys will show different images contained in the DDS. The "1" through "0" keys can also be used to jump to a specific image index.

* ``DDSTextureLoader\``

  * This contains a streamlined version of the legacy DirectX SDK sample _DDSWithoutD3DX11_ texture loading code for a simple light-weight runtime DDS loader. There are versions for Direct3D 9, Direct3D 11, and Direct3D 12. This performs no runtime pixel data conversions. This is ideal for runtime usage, and supports the full complement of Direct3D texture  resources (1D, 2D, volume maps, cubemaps, mipmap levels, texture arrays, BC formats, etc.).

* ``ScreenGrab\``

  * This contains texture writing modules for Direct3D 9, Direct3D 11, and Direct3D 12 primarily intended for creating screenshots. The images are written as a DDS or as an image file format using WIC.

* ``WICTextureLoader\``

  * This contains a Direct3D 9, Direct3D 11 and Direct3D 12 2D texture loader that uses WIC to load a bitmap (BMP, JPEG, PNG, HD Photo, or other WIC supported file container), resize if needed based on the current feature level (or by explicit parameter), format convert to a DXGI_FORMAT if required, and then create a 2D texture. Note this does not support 1D textures, volume textures, cubemaps, or texture arrays. DDSTextureLoader is recommended for fully "precooked" textures for maximum performance and image quality, but this loader can be useful for creating simple 2D texture from standard image files at runtime.

> DDSTextureLoader11, ScreenGrab11, and WICTextureLoader11 are 'stand-alone' versions of the same modules provided in the [DirectX Tool Kit for DX11](https://github.com/Microsoft/DirectXTK)
> DDSTextureLoader12, ScreenGrab12, and WICTextureLoader12 are 'stand-alone' versions of the same modules provided in the [DirectX Tool Kit for DX12](https://github.com/Microsoft/DirectXTK12).

* ``build\``

  * Contains miscellaneous build files and scripts.

## Documentation

Documentation is available on the [GitHub wiki](https://github.com/Microsoft/DirectXTex/wiki).

## Notices

All content and source code for this package are subject to the terms of the [MIT License](https://github.com/microsoft/DirectXTex/blob/main/LICENSE).

For the latest version of DirectXTex, bug reports, etc. please visit the project site on [GitHub](https://github.com/microsoft/DirectXTex).

## Release Notes

FOR SECURITY ADVISORIES, see [GitHub](https://github.com/microsoft/DirectXTex/security/advisories).

For a full change history, see [CHANGELOG.md](https://github.com/microsoft/DirectXTex/blob/main/CHANGELOG.md).

* Starting with the March 2025 release, Windows 7 and Windows 8.0 support has been retired.

* Starting with the July 2022 release, the ``bool forceSRGB`` parameter for the **CreateTextureEx** and **CreateShaderResourceViewEx** functions is now a ``CREATETEX_FLAGS`` typed enum bitmask flag parameter. This may have a _breaking change_ impact to client code. Replace ``true`` with ``CREATETEX_FORCE_SRGB`` and ``false`` with ``CREATETEX_DEFAULT``.

* Starting with the June 2020 release, this library makes use of typed enum bitmask flags per the recommendation of the _C++ Standard_ section _17.5.2.1.3 Bitmask types_. This is consistent with Direct3D 12's use of the ``DEFINE_ENUM_FLAG_OPERATORS`` macro. This may have _breaking change_ impacts to client code:

  * You cannot pass the ``0`` literal as your flags value. Instead you must make use of the appropriate default enum value: ``CP_FLAGS_NONE``, ``DDS_FLAGS_NONE``, ``WIC_FLAGS_NONE``, ``TEX_FR_ROTATE0``, ``TEX_FILTER_DEFAULT``, ``TEX_FILTER_DEFAULT``, ``TEX_FILTER_DEFAULT``, ``CNMAP_DEFAULT``, or ``CNMAP_DEFAULT``.

  * Use the enum type instead of ``DWORD`` if building up flags values locally with bitmask operations. For example, ```DDS_FLAGS flags = DDS_FLAGS_NONE; if (...) flags |= DDS_FLAGS_EXPAND_LUMINANCE;```

  * In cases where some of the flags overlap, you can use the ``|`` to combine the relevant types: ``TEX_FILTER_FLAGS`` filter modes combine with ``WIC_FLAGS``, ``TEX_FILTER_FLAGS`` sRGB flags combine with ``TEX_PMALPHA_FLAGS`` or ``TEX_COMPRESS_FLAGS``. No other bitwise operators are defined. For example, ```WIC_FLAGS wicFlags = WIC_FLAGS_NONE | TEX_FILTER_CUBIC;```

* WICTextureLoader cannot load ``.TGA`` or ``.HDR`` files unless the system has a 3rd party WIC codec installed. You must use the DirectXTex library for TGA/HDR file format support without relying on an add-on WIC codec.

* The UWP projects and the Win10 classic desktop project include configurations for the ARM64 platform. Building these requires installing the ARM64 toolset.

* For ARM64/AArch64 development, the VS 2022 compiler is strongly recommended over the VS 2019 toolset. The Windows SDK (26100 or later) is not compatible with VS 2019 for Win32 on ARM64 development. _Note that the ARM32/AArch32 platform is [deprecated](https://learn.microsoft.com/windows/arm/arm32-to-arm64)_.

* When using clang/LLVM for the ARM64/AArch64 platform, the Windows 11 SDK ([22000](https://walbourn.github.io/windows-sdk-for-windows-11/)) or later is required.

* The ``CompileShaders.cmd`` script must have Windows-style (CRLF) line-endings. If it is changed to Linux-style (LF) line-endings, it can fail to build all the required shaders.

* As of the October 2024 release, the command-line tools also support GNU-style long options using ``--``. All existing switches continue to function, but some of the `-` options are now deprecated per this table:

|texassemble||texconv||texdiag||
|---|---|---|---|---|---|
|-tonemap|--tonemap|-badtails|--bad-tails|-badtails|--bad-tails|
|-bgcolor|--gif-bg-color|-fixbc4x4|--fix-bc-4x4|-ignoremips|--ignore-mips|
|-swizzle|--swizzle|-ignoremips|--ignore-mips|-permissive|--permissive|
|-stripmips|--strip-mips|-inverty|--invert-y|-targetx|--target-x|
|||-keepcoverage|--keep-coverage|-targety|--target-y|
|||-permissive|--permissive|||
|||-reconstructz|--reconstruct-z|||
|||-rotatecolor|--rotate-color|||
|||-singleproc|--single-proc|||
|||-swizzle|--swizzle|||
|||-tgazeroalpha|--tga-zero-alpha|||
|||-timing|--timing|||
|||-tonemap|--tonemap|||
|||-wiclossless|--wic-lossless|||
|||-wicmulti|--wic-multiframe|||
|||-x2bias|--x2-bias|||

## Support

For questions, consider using [Stack Overflow](https://stackoverflow.com/questions/tagged/directxtk) with the _directxtk_ tag, or the [DirectX Discord Server](https://discord.gg/directx) in the _dx12-developers_ or _dx9-dx11-developers_ channel.

For bug reports and feature requests, please use GitHub [issues](https://github.com/microsoft/DirectXTex/issues) for this project.

## Contributing

This project welcomes contributions and suggestions. Most contributions require you to agree to a Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions provided by the bot. You will only need to do this once across all repos using our CLA.

Tests for new features should also be submitted as a PR to the [Test Suite](https://github.com/walbourn/directxtextest/wiki) repository.

## Code of Conduct

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Trademarks

This project may contain trademarks or logos for projects, products, or services. Authorized use of Microsoft trademarks or logos is subject to and must follow [Microsoft's Trademark & Brand Guidelines](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general). Use of Microsoft trademarks or logos in modified versions of this project must not cause confusion or imply Microsoft sponsorship. Any use of third-party trademarks or logos are subject to those third-party's policies.

## Credits

The DirectXTex library is the work of Chuck Walbourn, with contributions from Matt Lee, Xin Huang, Craig Peeper, and the numerous other Microsoft engineers who developed the D3DX utility library over the years.

Thanks to Paul Penson for his help with the implementation of ``MemoryStreamOnBlob``.

Thanks to Andrew Farrier and Scott Matloff for their on-going help with code reviews.

Thanks to Park DongHa for their contribution of the JPEG/PNG auxiliary functions.

project('nanosvg', 'cpp', version: '1.0')

cpp = meson.get_compiler('cpp')
fs = import('fs')

nanosvg_pkg = dependency('nanosvg', required: false)

# `src/wx/images.cpp` includes `nanosvg/nanosvg.h` directly.
# When `libnanosvg-dev` isn't available on the target host, we vendor a copy
# into `third_party/nanosvg/` (network may be unavailable on the host).
#
# Note: NanoSVG is a single-header library; our code defines
# `NANOSVG_IMPLEMENTATION` in `images.cpp`, so we only need the header file.

if nanosvg_pkg.found()
    # System provides both headers; no extra include dirs needed.
    nanosvg_dep = nanosvg_pkg
else
    # find /usr/include/nanosvg/nanosvg.h
    if fs.exists('/usr/include/nanosvg/nanosvg.h')
        nanosvg_inc = []
    else
        # Provide include path such that `#include <nanosvg/nanosvg.h>`
        # and `#include <nanosvg/nanosvgrast.h>` both work.
        nanosvg_inc = [include_directories('.')]
    endif
    nanosvg_dep = declare_dependency(include_directories: nanosvg_inc)
endif

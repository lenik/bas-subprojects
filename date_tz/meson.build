project('date_tz', 'cpp', version: '1.0')

cpp = meson.get_compiler('cpp')

threads_dep = dependency('threads')

# Howard Hinnant date/time (libdate-tz)
# We vendor the needed headers and tz implementation to avoid relying on
# the target host having `libhowardhinnant-date-dev` installed.
date_tz_inc = include_directories('date/include')
date_tz_src = files('date/src/tz.cpp')
date_tz_lib_sys = cpp.find_library('date-tz', required: false)
date_exe_inc = []
if not date_tz_lib_sys.found()
    date_exe_inc = [date_tz_inc]
endif

# Prefer the system lib if present; otherwise build/link our vendored tz.cpp.
# Placed after `add_project_arguments()` to satisfy Meson's ordering rules.
if date_tz_lib_sys.found()
    date_dep = declare_dependency(dependencies: [threads_dep, date_tz_lib_sys])
else
    date_tz_lib = static_library(
        'date-tz',
        date_tz_src,
        include_directories: date_tz_inc,
    )
    date_dep = declare_dependency(
        dependencies: [threads_dep],
        link_with: date_tz_lib,
        include_directories: date_tz_inc,
    )
endif


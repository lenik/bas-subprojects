project('boost', 'cpp', version: '1.0')

cpp = meson.get_compiler('cpp')

# boost libraries
# use vendored boost libraries if boost-system <= 1.67

# Boost.System (error_code) used by vendored Boost.JSON and JsonForm
boost_system_dep = dependency('boost', modules: ['system'], required: true)
boost_locale_dep = dependency('boost', modules: ['locale'], required: true)

boost_json_vendored_inc = [
    include_directories('boost_assert/include'),
    include_directories('boost_core/include'),
    include_directories('boost_json/include'),
]
boost_json_vendored_lib = static_library(
    'boost_json',
    'boost_json/src/src.cpp',
    include_directories: boost_json_vendored_inc,
)
boost_json_vendored_dep = declare_dependency(
    include_directories: boost_json_vendored_inc,
    link_with: boost_json_vendored_lib,
    dependencies: [
        boost_system_dep,
    ],
)

if not boost_system_dep.found() or (boost_system_dep.version() < '1.68')
    boost_json_dep = boost_json_vendored_dep
else
    # Do not allow pkg-config fallback to this same subproject (recursive include).
    boost_json_dep = dependency(
        'boost',
        modules: [
            'assert',
            'core',
            'json',
        ],
        required: false,
        allow_fallback: false,
    )
    if not boost_json_dep.found()
        boost_json_dep = boost_json_vendored_dep
    endif
endif

project('wx_logic', 'cpp', version: '1.0')

cpp = meson.get_compiler('cpp')

# wxWidgets dependencies (must use a single GTK toolkit to avoid "GTK+ 2.x and GTK+ 3 in the same process" error)
wx_config = find_program('wx-config', required: true)

# Prefer gtk3u (and refuse to silently fall back to gtk2u).
wx_selected_config = run_command(
    wx_config,
    '--toolkit=gtk3',
    '--unicode=yes',
    '--selected-config',
    check: true,
    capture: true
).stdout().strip()
if not wx_selected_config.contains('gtk3')
    error(
        'Refusing gtk2u fallback: wx-config selected-config="@0@" (expected gtk3-unicode-3.0/3.1). Configure PATH/alternatives so meson uses gtk3 wxWidgets.'.format(
            wx_selected_config
        )
    )
endif

wx_gtk_prefix = 'wx_gtk3u'
wx_libs_out = run_command(
    wx_config,
    '--toolkit=gtk3',
    '--unicode=yes',
    '--libs',
    check: true,
    capture: true
).stdout()

# Fetch compilation flags from the same gtk3 wx-config.
wx_cxxflags_out = run_command(
    wx_config,
    '--toolkit=gtk3',
    '--unicode=yes',
    '--cxxflags',
    check: true,
    capture: true
).stdout()

# Detect wx minor version from the libraries exposed by wx-config.
wx_ver = '3.0'
if wx_libs_out.contains('-3.1')
    wx_ver = '3.1'
endif
# wxWidgets media (wxMediaCtrl) and extras - use same toolkit as core
# Media/webview optional on GTK2 (often not built for gtk2)
wx_media_lib = cpp.find_library(wx_gtk_prefix + '_media-' + wx_ver, required: true)
wx_richtext_lib = cpp.find_library(wx_gtk_prefix + '_richtext-' + wx_ver, required: true)
wx_stc_lib = cpp.find_library(wx_gtk_prefix + '_stc-' + wx_ver, required: true)
wx_webview_lib = cpp.find_library(wx_gtk_prefix + '_webview-' + wx_ver, required: true)
wx_extra_libs = [wx_media_lib, wx_richtext_lib, wx_stc_lib, wx_webview_lib]

# wx_dep = dependency('wxwidgets', modules : [
#     'core',
#     'media',
#     'richtext',
#     'stc',
#     'webview',
#     ], required : true)

if wx_ver == '3.0'
    # wx 3.0: media and webview are in separate libs; core setup has wxUSE_*=0, so enable for our build
    wx_defines = ['-DwxUSE_MEDIACTRL=1', '-DwxUSE_WEBVIEW=1']
else
    wx_defines = []
endif

# Use gtk3 flags for both compile and link, so we don't accidentally link
# gtk2u libs via Meson's `dependency('wxwidgets', ...)`.
wx_dep = declare_dependency(
    compile_args: wx_cxxflags_out.split() + wx_defines,
    link_args: wx_libs_out.split(),
    dependencies: wx_extra_libs,
)


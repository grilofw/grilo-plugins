#
# meson.build
#
# Author: Juan A. Suarez Romero <jasuarez@igalia.com>
#
# Copyright (C) 2016 Igalia S.L. All rights reserved.

project('grilo-plugins', 'c',
        version: '0.3.11',
        default_options: [
            'buildtype=debugoptimized',
            'c_std=gnu99',
            'warning_level=1'
        ],
        license: 'LGPL 2.1',
        meson_version: '>= 0.37.0')

plugin_version = meson.project_version()
version_array = plugin_version.split('.')
grl_major = version_array[0]
grl_minor = version_array[1]

prefix = get_option('prefix')
localedir = join_paths(prefix, get_option('localedir'))
libdir = get_option('libdir')
datadir = get_option('datadir')
pluginsdir =join_paths(libdir, 'grilo-@0@.@1@'.format(grl_major, grl_minor))

glib2_required = '2.44'
grilo_required = '0.3.8'
glib2_required_info = '>= @0@'.format(glib2_required)
grilo_required_info = '>= @0@'.format(grilo_required)

glib_dep = dependency('glib-2.0', version: glib2_required_info, required: true)
gmodule_dep = dependency('gmodule-2.0', version: glib2_required_info, required: true)
gobject_dep = dependency('gobject-2.0', version: glib2_required_info, required: true)
grilo_dep = dependency('grilo-0.3', version: grilo_required_info, required: true,
                       fallback: ['grilo', 'libgrl_dep'])

pkgconf = configuration_data()
pkgconf.set('VERSION', plugin_version)

pkgconfig_file = 'grilo-plugins-@0@.@1@'.format(grl_major, grl_minor)

configure_file(input: pkgconfig_file + '.pc.in',
               output: pkgconfig_file + '.pc',
               configuration: pkgconf,
               install_dir: '@0@/pkgconfig'.format(get_option('libdir')))

avahi_client_dep = dependency('avahi-client', required: false)
avahi_glib_dep = dependency('avahi-glib', required: false)
avahi_gobject_dep = dependency('avahi-gobject', required: false)
gio_dep = dependency('gio-2.0', required: false)
gio_unix_dep = dependency('gio-unix-2.0', required: false)
goa_dep = dependency('goa-1.0', version: '>= 3.17.91', required: false)
gom_dep = dependency('gom-1.0', version: '>= 0.3.2', required: false)
grilo_net_dep = dependency('grilo-net-0.3', version: '>= 0.3.0', required: false,
                           fallback: ['grilo', 'libgrlnet_dep'])
grilo_pls_dep = dependency('grilo-pls-0.3', version: '>= 0.3.0', required: false,
                           fallback: ['grilo', 'libgrlpls_dep'])
gstreamer_dep = dependency('gstreamer-1.0', required: false)
gthread_dep = dependency('gthread-2.0', required: false)
json_glib_dep = dependency('json-glib-1.0', required: false)
libarchive_dep = dependency('libarchive', required: false)
libdmapsharing_dep = dependency('libdmapsharing-3.0', version: '>= 2.9.12', required: false)
libgdata_dep = dependency('libgdata', version: '>= 0.9.1', required: false)
libmediaart_dep = dependency('libmediaart-2.0', required: false)
libsoup_dep = dependency('libsoup-2.4', required: false)
libxml_dep = dependency('libxml-2.0', required: false)
oauth_dep = dependency('oauth', required: false)
sqlite3_dep = dependency('sqlite3', required: false)
totem_plparser_dep = dependency('totem-plparser', version: '>= 3.4.1', required: false)
totem_plparser_mini_dep = dependency('totem-plparser-mini', version: '>= 3.4.1', required: false)
tracker_sparql_dep = dependency('tracker-sparql-2.0', version: '>= 2.3.0', required: false)

lua_dep = dependency('lua', version: '>= 5.3.0', required: false)
if not lua_dep.found()
    lua_dep = dependency('lua5.3', version: '>= 5.3.0', required: false)
    if not lua_dep.found()
        lua_dep = dependency('lua-5.3', version: '>= 5.3.0', required: false)
    endif
endif

gperf = find_program('gperf', required: false)
glib_compile_resources = find_program('glib-compile-resources', required: false)

must_deps = [glib_dep, gmodule_dep, gobject_dep, grilo_dep]

cdata = configuration_data()
cdata.set('GRL_MAJOR', grl_major)
cdata.set('GRL_MINOR', grl_minor)
cdata.set_quoted('GETTEXT_PACKAGE', meson.project_name())
cdata.set_quoted('LOCALEDIR', localedir)
cdata.set_quoted('VERSION', plugin_version)

# Defines for optional dependencies
cdata.set('GOA_ENABLED', goa_dep.found())
cdata.set('HAVE_TOTEM_PLPARSER_MINI', totem_plparser_mini_dep.found())

NAME = 0
REQ_DEPS = 1
OPT_DEPS = 2

plugins = [ # NAME, REQ_DEPS, OPT_DEPS
    ['bookmarks', [gio_dep, libxml_dep, gom_dep], []],
    ['chromaprint', [gstreamer_dep], []],
    ['dleyna', [gio_dep, gio_unix_dep, libsoup_dep], []],
    ['dmap', [libdmapsharing_dep, libxml_dep], []],
    ['filesystem', [grilo_pls_dep], []],
    ['flickr', [grilo_net_dep, libxml_dep, oauth_dep], [goa_dep]],
    ['freebox', [grilo_pls_dep, avahi_client_dep, avahi_glib_dep, avahi_gobject_dep], []],
    ['gravatar', [], []],
    ['jamendo', [grilo_net_dep, libxml_dep], []],
    ['local-metadata', [gio_dep, libmediaart_dep], []],
    ['lua-factory', [lua_dep, libarchive_dep, grilo_net_dep, json_glib_dep, libxml_dep], [goa_dep, totem_plparser_mini_dep]],
    ['magnatune', [sqlite3_dep, grilo_net_dep], []],
    ['metadata-store', [sqlite3_dep], []],
    ['opensubtitles', [gio_dep, libsoup_dep], []],
    ['optical-media', [totem_plparser_dep], []],
    ['podcasts', [grilo_net_dep, libxml_dep, sqlite3_dep, totem_plparser_dep], []],
    ['raitv', [grilo_net_dep, libxml_dep], []],
    ['shoutcast', [grilo_net_dep, libxml_dep], []],
    ['thetvdb', [grilo_net_dep, libxml_dep, libarchive_dep, gom_dep], []],
    ['tmdb', [json_glib_dep, libsoup_dep, grilo_net_dep], []],
    ['tracker', [tracker_sparql_dep], []],
    ['vimeo', [grilo_net_dep, libxml_dep, totem_plparser_dep], []],
    ['youtube', [grilo_net_dep, libxml_dep, libgdata_dep, totem_plparser_dep], []],
]

idx = 0
foreach p: plugins
    varname = p[NAME].underscorify()
    varname_enabled = varname + '_enabled'
    varname_idx = varname + '_idx'

    current_enabled = get_option('enable-' + p[NAME]) == 'no' ? false : true
    current_deps = p[REQ_DEPS]
    if current_enabled
        current_auto = get_option('enable-'+ p[NAME]) == 'auto'
        foreach d: current_deps
            if current_auto
                current_enabled = current_enabled and d.found()
            elif not d.found()
                error('Missing dependencies for @0@ plugin'.format(p[NAME]))
            endif
        endforeach
    endif
    set_variable(varname_enabled, current_enabled)
    set_variable(varname_idx, idx)
    if p[NAME] != 'dmap'
        cdata.set_quoted(varname.to_upper() + '_PLUGIN_ID', 'grl-' + p[NAME])
    else
        cdata.set_quoted('DPAP_PLUGIN_ID', 'grl-dpap')
        cdata.set_quoted('DAAP_PLUGIN_ID', 'grl-daap')
    endif
    idx = idx + 1
endforeach

# Special case: Lua Factory requires gperf program
if lua_factory_enabled
    if get_option('enable-lua-factory') == 'auto'
        lua_factory_enabled = lua_factory_enabled and gperf.found() and glib_compile_resources.found()
    elif not gperf.found() or not glib_compile_resources.found()
        error('Missing dependencies for lua-factory plugin')
    endif
endif

message('Plugins:')
foreach p: plugins
    name = p[NAME].underscorify() + '_enabled'
    message('   @0@ : @1@'.format(p[NAME], get_variable(name).to_string('yes', 'no')))
endforeach

gnome = import('gnome')

rootdir = include_directories('.')

subdir('src')
subdir('help')
subdir('po')
subdir('tests')

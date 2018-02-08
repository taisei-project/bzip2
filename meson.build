project('bzip2', 'c', version : '1.0.6')

bzip2_sources = [
  'blocksort.c',
  'huffman.c',
  'crctable.c',
  'randtable.c',
  'compress.c',
  'decompress.c',
  'bzlib.c'
]

libversion = meson.project_version()
soversion = '1'

bz2_c_args = ['-D_FILE_OFFSET_BITS=64']

cc = meson.get_compiler('c')

foreach arg : [
  # gcc/clang: bzip2.c:557: warning: variable nread set but not used
  '-Wno-unused-but-set-variable',
  # msvc: Ignore C4996 about "insecure functions"
  '/wd4996'
]
  if cc.has_argument(arg)
    bz2_c_args += [arg]
  endif
endforeach

bz2_incs = include_directories('.')

install_headers(['bzlib.h'])

libtype = get_option('lib_type')

if libtype == 'default_library'
  libtype = get_option('default_library')
endif

if libtype != 'static'
  libbz2_shared = shared_library('bz2', bzip2_sources,
    version : libversion,
    soversion : soversion,
    c_args : bz2_c_args,
    include_directories : bz2_incs,
    install : not meson.is_subproject(),
#    dependencies : mathlib,
#    vs_module_defs: vs_module_defs_dir + 'libbz2.def',
  )
  libbz2 = libbz2_shared
endif

if libtype != 'shared'
  libbz2_static = static_library('bz2', bzip2_sources,
    c_args : bz2_c_args,
    include_directories : bz2_incs,
    install : true,
#    dependencies : mathlib,
  )
  libbz2 = libbz2_static
endif

if libtype == 'both'
  if get_option('default_library') == 'static'
    libbz2 = libbz2_static
  else
    libbz2 = libbz2_shared
  endif
endif

bz2_dep = declare_dependency(link_with : libbz2, include_directories : bz2_incs)

if not meson.is_subproject()
  executable('bzip2', 'bzip2.c',
    install : true,
    dependencies : bz2_dep,
    c_args: bz2_c_args)

  executable('bunzip2', 'bzip2.c',
    install : true,
    dependencies : bz2_dep,
    c_args: bz2_c_args)

  executable('bzcat', 'bzip2.c',
    install : true,
    dependencies : bz2_dep,
    c_args: bz2_c_args)

  executable('bzip2recover', 'bzip2recover.c',
    install : true,
    c_args: bz2_c_args)

  install_man('bzdiff.1', 'bzgrep.1', 'bzip2.1', 'bzmore.1')
  install_data('bzgrep', 'bzmore', 'bzdiff', install_dir : 'bin')

  pkg_mod = import('pkgconfig')
  pkg_mod.generate(
    name : meson.project_name(),
    filebase : meson.project_name(),
    description : 'Lossless, block-sorting data compression',
    libraries : libbz2,
    version : meson.project_version(),
  )
endif

# FIXME: build docs

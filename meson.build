project(
  'modpath-v7',
  ['fortran'],
  version: '7.2.002',
  license: 'CC0',
  meson_version: '>= 1.3.1',
  default_options : [
    'b_vscrt=static_from_buildtype', # Link runtime libraries statically on Windows
    'buildtype=release',
])

fc = meson.get_compiler('fortran')
fc_id = fc.get_id()
message('Compiler ID:', fc_id)

compile_args = []
link_args = []
extra_deps = []

if fc_id == 'gcc'
  # GCC/gfortran specific flags
  compile_args += ['-cpp', '-ffree-line-length-none']
  # Link quadmath library on macOS (required for gfortran runtime)
  if host_machine.system() == 'darwin'
    quadmath_dep = fc.find_library('quadmath', required: true, static: true)
    extra_deps += [quadmath_dep]
  endif
elif fc_id == 'intel'
  # Intel ifx specific flags
  compile_args += ['-fpp', '-diag-disable=10441']
  link_args += ['-diag-disable=10441']
elif fc_id == 'intel-cl'
  # Intel ifort (classic) Windows specific flags
  compile_args += ['/fpp']
elif fc_id == 'intel-llvm-cl'
  # Intel ifx Windows specific flags
  compile_args += ['/fpp']
endif

add_project_arguments(fc.get_supported_arguments(compile_args), language: 'fortran')
add_project_link_arguments(fc.get_supported_arguments(link_args), language: 'fortran')

subdir('source')

# tests
# Note: modpath-v7 requires input files to run properly, so we just test that
# the executable was built and can start (it will exit when stdin is empty)
test('version',
     exe,
     should_fail: true,  # will fail due to missing input file
     workdir: meson.current_source_dir())

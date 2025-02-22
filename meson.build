project(
    'phosphor-state-manager',
    'cpp',
    default_options: [
        'warning_level=3',
        'werror=true',
        'cpp_std=c++20'
    ],
    meson_version: '>= 0.57.0',
    license: 'Apache-2.0',
    version: '0.1',
)

build_host_gpios = get_option('host-gpios')

conf = configuration_data()
conf.set_quoted(
    'HOST_BUSNAME', get_option('host-busname'))
conf.set_quoted(
    'HOST_OBJPATH', get_option('host-objpath'))
conf.set_quoted(
    'HYPERVISOR_BUSNAME', get_option('hypervisor-busname'))
conf.set_quoted(
    'HYPERVISOR_OBJPATH', get_option('hypervisor-objpath'))
conf.set_quoted(
    'CHASSIS_BUSNAME', get_option('chassis-busname'))
conf.set_quoted(
    'CHASSIS_OBJPATH', get_option('chassis-objpath'))
conf.set_quoted(
    'BMC_BUSNAME', get_option('bmc-busname'))
conf.set_quoted(
    'BMC_OBJPATH', get_option('bmc-objpath'))
conf.set_quoted(
    'HOST_STATE_PERSIST_PATH', get_option('host-state-persist-path'))
conf.set_quoted(
    'POH_COUNTER_PERSIST_PATH', get_option('poh-counter-persist-path'))
conf.set_quoted(
    'CHASSIS_STATE_CHANGE_PERSIST_PATH', get_option('chassis-state-change-persist-path'))
conf.set_quoted(
    'SCHEDULED_HOST_TRANSITION_PERSIST_PATH', get_option('scheduled-host-transition-persist-path'))
conf.set_quoted(
    'SCHEDULED_HOST_TRANSITION_BUSNAME', get_option('scheduled-host-transition-busname'))
conf.set(
    'BOOT_COUNT_MAX_ALLOWED', get_option('boot-count-max-allowed'))
conf.set(
    'CLASS_VERSION', get_option('class-version'))
conf.set_quoted(
    'SYSFS_SECURE_BOOT_PATH', get_option('sysfs-secure-boot-path'))
conf.set_quoted(
    'SYSFS_ABR_IMAGE_PATH', get_option('sysfs-abr-image-path'))
if build_host_gpios.enabled()
    conf.set_quoted(
        'HOST_GPIOS_BUSNAME', get_option('host-gpios-busname'))
    conf.set_quoted(
        'HOST_GPIOS_OBJPATH', get_option('host-gpios-objpath'))
endif

# globals shared across applications
conf.set_quoted(
    'BASE_FILE_DIR', '/run/openbmc/')

conf.set_quoted(
    'HOST_RUNNING_FILE', '/run/openbmc/host@%d-on')

conf.set_quoted(
    'CHASSIS_ON_FILE', '/run/openbmc/chassis@%d-on')

conf.set_quoted(
    'CHASSIS_LOST_POWER_FILE', '/run/openbmc/chassis@%d-lost-power')

configure_file(output: 'config.h', configuration: conf)

if(get_option('warm-reboot').enabled())
    add_project_arguments('-DENABLE_WARM_REBOOT',language:'cpp')
endif

sdbusplus = dependency('sdbusplus')
sdeventplus = dependency('sdeventplus')
phosphorlogging = dependency('phosphor-logging')
phosphordbusinterfaces = dependency('phosphor-dbus-interfaces')
libgpiod = dependency('libgpiod', version : '>=1.4.1')

cppfs = meson.get_compiler('cpp').find_library('stdc++fs')

executable('phosphor-host-state-manager',
            'host_state_manager.cpp',
            'host_state_manager_main.cpp',
            'settings.cpp',
            'host_check.cpp',
            'utils.cpp',
            dependencies: [
            sdbusplus, sdeventplus, phosphorlogging,
            phosphordbusinterfaces, cppfs, libgpiod
            ],
    implicit_include_directories: true,
    install: true
)

executable('phosphor-hypervisor-state-manager',
            'hypervisor_state_manager.cpp',
            'hypervisor_state_manager_main.cpp',
            'settings.cpp',
            dependencies: [
            sdbusplus, sdeventplus, phosphorlogging,
            phosphordbusinterfaces, cppfs
            ],
    implicit_include_directories: true,
    install: true
)

executable('phosphor-chassis-state-manager',
            'chassis_state_manager.cpp',
            'chassis_state_manager_main.cpp',
            'utils.cpp',
            dependencies: [
            sdbusplus, sdeventplus, phosphorlogging,
            phosphordbusinterfaces, cppfs, libgpiod
            ],
    implicit_include_directories: true,
    install: true
)

executable('phosphor-chassis-check-power-status',
            'chassis_check_power_status.cpp',
            'utils.cpp',
            dependencies: [
            sdbusplus, phosphorlogging,
            phosphordbusinterfaces, cppfs, libgpiod
            ],
    implicit_include_directories: true,
    install: true
)

executable('phosphor-bmc-state-manager',
            'bmc_state_manager.cpp',
            'bmc_state_manager_main.cpp',
            'utils.cpp',
            dependencies: [
            sdbusplus, sdeventplus, phosphorlogging,
            phosphordbusinterfaces, cppfs, libgpiod
            ],
    implicit_include_directories: true,
    install: true
)

executable('phosphor-discover-system-state',
            'discover_system_state.cpp',
            'settings.cpp',
            'utils.cpp',
            dependencies: [
            sdbusplus, phosphorlogging, libgpiod, cppfs
            ],
    implicit_include_directories: true,
    install: true
)

executable('phosphor-systemd-target-monitor',
            'systemd_service_parser.cpp',
            'systemd_target_monitor.cpp',
            'systemd_target_parser.cpp',
            'systemd_target_signal.cpp',
            'utils.cpp',
            dependencies: [
                libgpiod,
                phosphorlogging,
                sdbusplus,
                sdeventplus,
            ],
    implicit_include_directories: true,
    install: true
)

executable('phosphor-scheduled-host-transition',
            'scheduled_host_transition_main.cpp',
            'scheduled_host_transition.cpp',
            'utils.cpp',
            dependencies: [
            sdbusplus, sdeventplus, phosphorlogging, libgpiod
            ],
    implicit_include_directories: true,
    install: true
)

executable('phosphor-host-reset-recovery',
            'host_reset_recovery.cpp',
            dependencies: [
            sdbusplus, phosphorlogging
            ],
    implicit_include_directories: true,
    install: true
)

executable('phosphor-secure-boot-check',
            'secure_boot_check.cpp',
            'utils.cpp',
            dependencies: [
            sdbusplus, phosphorlogging, libgpiod
            ],
    implicit_include_directories: true,
    install: true
)

install_data('obmcutil',
        install_mode: 'rwxr-xr-x',
        install_dir: get_option('bindir')
)

install_data('scripts/host-reboot',
        install_mode: 'rwxr-xr-x',
        install_dir: get_option('libexecdir')
)

systemd = dependency('systemd')
systemd_system_unit_dir = systemd.get_variable(
    pkgconfig : 'systemdsystemunitdir')
subdir('service_files')
subdir('target_files')

if build_host_gpios.enabled()
    subdir('host_condition_gpio')
endif

datadir = join_paths(
    get_option('sysconfdir'),
    'phosphor-systemd-target-monitor'
)
subdir('data')

build_tests = get_option('tests')

# If test coverage of source files within the root directory are wanted,
# need to define and build the tests from here
if not build_tests.disabled()
gtest = dependency('gtest', main: true, disabler: true, required: build_tests)
gmock = dependency('gmock', disabler: true, required: build_tests)
  test(
      'test_systemd_parser',
      executable('test_systemd_parser',
          './test/systemd_parser.cpp',
          'systemd_target_parser.cpp',
          dependencies: [
              gtest,
          ],
          implicit_include_directories: true,
          include_directories: '../'
      )
  )

  test(
      'test_systemd_signal',
      executable('test_systemd_signal',
          './test/systemd_signal.cpp',
          'systemd_target_signal.cpp',
          'utils.cpp',
          dependencies: [
              gtest,
              libgpiod,
              phosphorlogging,
              sdbusplus,
              sdeventplus,
          ],
          implicit_include_directories: true,
          include_directories: '../'
      )
  )

  test(
      'test_scheduled_host_transition',
      executable('test_scheduled_host_transition',
          './test/test_scheduled_host_transition.cpp',
          'scheduled_host_transition.cpp',
          'utils.cpp',
          dependencies: [
              gtest, gmock, sdbusplus, sdeventplus, phosphorlogging, libgpiod
          ],
          implicit_include_directories: true,
          include_directories: '../'
      )
  )

  test(
      'test_hypervisor_state',
      executable('test_hypervisor_state',
          './test/hypervisor_state.cpp',
          'hypervisor_state_manager.cpp',
          dependencies: [
              gtest, sdbusplus, sdeventplus, phosphorlogging,
          ],
          implicit_include_directories: true,
          include_directories: '../'
      )
  )
endif

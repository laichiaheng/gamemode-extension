project('gamemode-extension',
	version: '5',
	meson_version: '>= 0.46.0',
	license: 'LGPL-2.1' )

# our identity
uuid = 'gamemode@christian.kellner.me'
gettext_domain = meson.project_name()

# imports
gnome = import('gnome')
i18n = import('i18n')

# dirs
srcdir = meson.source_root()
prefix = get_option('prefix')
datadir = get_option('datadir')

shelldir = join_paths(datadir, 'gnome-shell')
extensiondir = join_paths(shelldir, 'extensions')
schemadir = join_paths(datadir, 'glib-2.0', 'schemas')

installdir = join_paths(extensiondir, uuid)

# dependencies
js60 = find_program('js60', required: false)
git = find_program('git', required: false)

# optional git revision suffix
gitrev = ''
if git.found()
  gitres = run_command(git, ['--git-dir=@0@/.git'.format(srcdir),
                             'rev-parse',
                             '--short=7',
                             'HEAD'])
  if gitres.returncode() == 0
    gitrev = gitres.stdout().strip()
  endif
endif

full_version =  meson.project_version() + '-' + gitrev

# metdata.json configuration data
conf = configuration_data()
conf.set('UUID', uuid)
conf.set('VERSION', full_version)
conf.set('GETTEXT_DOMAIN', gettext_domain)
conf.set('URL', 'https://github.com/gicmo/gamemode-extension')

md = configure_file(input: 'metadata.json.in',
		    output: 'metadata.json',
		    configuration: conf)

# installation
sources = [
  'client.js',
  'extension.js',
  'prefs.js'
]

install_data(
  sources + [md],
  install_dir: installdir)

install_data(
  'data/settings.gschema.xml',
  rename: 'org.gnome.shell.extensions.gamemode.gschema.xml',
  install_dir: schemadir)

meson.add_install_script(
  'scripts/post-install.sh',
  join_paths(prefix, schemadir))

# tests
if (js60.found())
  foreach src : sources
    fullpath = join_paths(srcdir, src)
    test('Syntax check for ' + '@0@'.format(src),
	 js60,
	 args: ['-s', '-c', fullpath])
  endforeach
endif

#i18n
subdir('po')

run_target('fix-translations',
	   command: ['scripts/fix_translations.py',
		     join_paths(meson.source_root(), 'po')])

# all done. report.
msg = ['',
  'version:             @0@'.format(meson.project_version()),
  'git-rev:             @0@'.format(gitrev),
  'full-version:        @0@'.format(full_version),
  ''
  ]
message('\n  '.join(msg))

require 'rake/rdoctask'
require 'rake/testtask'
require 'rake/packagetask'
require 'rake/gempackagetask'

require 'rbconfig'
require 'fileutils'

$:.unshift 'lib'

require 'mapi/msg'

PKG_NAME = 'ruby-msg'
PKG_VERSION = Mapi::VERSION

task :default => [:test, :clean, :package]
task :package => [:rdoc, :set_sane_file_permissions]

# This builds the actual gem. For details of what all these options
# mean, and other ones you can add, check the documentation here:
#
#   http://rubygems.org/read/chapter/20
#
spec = Gem::Specification.new do |s|
  s.name        = PKG_NAME
  s.version     = PKG_VERSION
  s.summary     = %q{Ruby Msg library.}
  s.description = %q{A library for reading and converting Outlook msg and pst files (mapi message stores).}
  s.authors     = ["Charles Lowe", "Karrick McDermott"]
  s.email       = ["aquasync@gmail.com", "karrick@gmail.com"]
  s.homepage    = %q{http://code.google.com/p/ruby-msg}
  s.rubyforge_project = %q{ruby-msg}

  s.has_rdoc          = true
  s.extra_rdoc_files = ['README']
  s.rdoc_options += ['--main', 'README',
                     '--title', "#{PKG_NAME} documentation",
                     '--tab-width', '2']

  # Add any extra files to include in the gem
  s.files       = FileList['data/*.yaml', 'Rakefile', 'README', 'FIXES']
  s.files      += FileList['lib/**/*.rb', 'bin/*']
  s.executables = FileList["bin/**"].map { |f| File.basename(f) }
  s.test_files = Dir.glob('test/test_*.rb')

  # If you want to depend on other gems, add them here, along with any
  # relevant versions
  s.add_dependency 'ruby-ole', '>=1.2.8'
  s.add_dependency 'vpim', '>=0.360'

  # If your tests use any gems, include them here
  # s.add_development_dependency("mocha") # for example
end

# Test the project
Rake::TestTask.new(:test) do |t|
  t.test_files = FileList["test/test_*.rb"] - ['test/test_pst.rb']
  t.warning = false
  t.verbose = true
end

begin
  require 'rcov/rcovtask'
  # NOTE: this will not do anything until you add some tests
  desc "Create a cross-referenced code coverage report"
  Rcov::RcovTask.new do |t|
    t.test_files = FileList['test/test*.rb']
    t.ruby_opts << "-Ilib" # in order to use this rcov
    t.rcov_opts << "--xrefs"  # comment to disable cross-references
    t.rcov_opts << "--exclude /usr/local/lib/site_ruby"
    t.verbose = true
  end
rescue LoadError
  # Rcov not available
end

# Generate documentation
Rake::RDocTask.new do |t|
  t.rdoc_dir = 'doc'
  t.title    = "#{PKG_NAME} documentation"
  t.options += %w[--main README --line-numbers --inline-source --tab-width 2]
  t.rdoc_files.include 'lib/**/*.rb'
  t.rdoc_files.include 'README'
end

desc 'Clear out RDoc and generated packages'
task :clean => [:clobber_rdoc, :clobber_package]

#
# Sets sane file and directory permissions prior to packaging
# (necessary when developer's umask is restrictive)
#
task :set_sane_file_permissions do
  Dir.glob("**/**").each do |e|
    perms = (File.directory?(e) or e =~ /bin\//) ? 0755 : 0644
    FileUtils.chmod(perms, e)
  end
end

Rake::GemPackageTask.new(spec) do |p|
  p.gem_spec = spec
  p.need_tar = false #true
  p.need_zip = false
  p.package_dir = 'build'
end

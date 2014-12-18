# This file is a rake build file. The purpose of this file is to simplify
# setting up and using Awestruct. It's not required to use Awestruct, though it
# does save you time (hopefully). If you don't want to use rake, just ignore or
# delete this file.
#
# If you're just getting started, execute this command to install Awestruct and
# the libraries on which it depends:
#
#  rake setup
#
# The setup task installs the necessary libraries according to which Ruby
# environment you are using. If you want the libraries kept inside the project,
# execute this command instead:
#
#  rake setup[local]
#
# IMPORTANT: To install gems, you'll need development tools on your machine,
# which include a C compiler, the Ruby development libraries and some other
# development libraries as well.
#
# There are also tasks for running Awestruct. The build will auto-detect
# whether you are using Bundler and, if you are, wrap calls to awestruct in
# `bundle exec`.
#
# To run in Awestruct in development mode, execute:
#
#  rake
#
# To clean the generated site before you build, execute:
#
#  rake clean preview
#
# To deploy using the production profile, execute:
#
#  rake deploy
#
# To get a list of all tasks, execute:
#
#  rake -T
#
# Now you're Awestruct with rake!

$rakebin = "_bin#{File::SEPARATOR}forrake"
$awestruct_cmd = nil
task :default => :preview

task :build_deck do
  run_asciidoctor "-T ~/Code/others/asciidoctor/asciidoctor-backends/haml/deckjs presentation.adoc"
end

desc 'Build the presentation in reveal.js'
task :build_reveal do
  run_asciidoctor "-T ~/Code/others/asciidoctor/asciidoctor-reveal.js/templates/slim presentation.adoc"
end

desc 'Enable guard'
task :guard do
  system "bundle exec guard"
end

task :pdf do
  cmd = "bundle exec asciidoctor-pdf presentation.adoc"
  msg cmd
  system cmd or raise "ERROR: Running Asciidoctor PDF failed."
end

# Execute Asciidoctor
def run_asciidoctor(args)
  cmd = "bundle exec asciidoctor #{args}" 
  msg cmd
  system cmd or raise "ERROR: Running Asciidoctor failed."
end


desc 'Setup the environment for Asciidoctor and guard'
task :setup, [:env] => :init do |task, args|
  system "bundle install --binstubs=#{$rakebin} --path=.bundle"
  msg 'Run awestruct `rake`'
  # Don't execute any more tasks, need to reset env
  exit 0
end

desc 'Update the environment to run Asciidoctor and guard'
task :update => :init do
  system 'bundle update'
  # Don't execute any more tasks, need to reset env
  exit 0
end

desc 'Push local commits to origin/master'
task :push do
  system 'git push upstream master'
end

desc 'Clean out generated site and temporary files, using [all] will also delete local gem files' 
task :clean, :all do |task, args|
  require 'fileutils'
  dirs = ['.awestruct', '.sass-cache', '_site']
  if args[:all] == 'all'
    dirs << '_tmp'
    dirs << '.bundle'
    dirs << "#{$rakebin}"
  end
  dirs.each do |dir|
    FileUtils.remove_dir dir unless !File.directory? dir
  end
end

# Perform initialization steps, such as setting up the PATH
task :init do
  # Detect using gems local to project
  if File.exist? "#{$rakebin}"
    ENV['PATH'] = "#{$rakebin}#{File::PATH_SEPARATOR}#{ENV['PATH']}"
    ENV['GEM_HOME'] = '.bundle'
  end
end

# A cross-platform means of finding an executable in the $PATH.
# Respects $PATHEXT, which lists valid file extensions for executables on Windows
#
#  which 'awestruct'
#  => /usr/bin/awestruct
def which(cmd, opts = {})
  unless $awestruct_cmd.nil? || opts[:clear_cache]
    return $awestruct_cmd
  end

  $awestruct_cmd = nil
  exts = ENV['PATHEXT'] ? ENV['PATHEXT'].split(';') : ['']
  ENV['PATH'].split(File::PATH_SEPARATOR).each do |path|
    exts.each do |ext|
      candidate = File.join path, "#{cmd}#{ext}"
      if File.executable? candidate
        $awestruct_cmd = candidate
        return $awestruct_cmd
      end
    end
  end
  return $awestruct_cmd
end

# Print a message to STDOUT
def msg(text, level = :info)
  case level
  when :warn
    puts "\e[31m#{text}\e[0m"
  else
    puts "\e[33m#{text}\e[0m"
  end
end


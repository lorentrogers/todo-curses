require 'fileutils'
require 'bundler'
require 'rake/clean'
require 'rake/testtask'
require 'rubocop/rake_task'
require 'cucumber'
require 'cucumber/rake/task'
require 'rdoc/task'

gem 'rdoc' # we need the installed RDoc gem, not the system one

include Rake::DSL

def dump_load_path
  puts $LOAD_PATH.join("\n")
  found = nil
  $LOAD_PATH.each do |path|
    if File.exists?(File.join(path,"rspec"))
      puts "Found rspec in #{path}"
      if File.exists?(File.join(path,"rspec","core"))
        puts "Found core"
        if File.exists?(File.join(path,"rspec","core","rake_task"))
          puts "Found rake_task"
          found = path
        else
          puts "!! no rake_task"
        end
      else
        puts "!!! no core"
      end
    end
  end
  if found.nil?
    puts "Didn't find rspec/core/rake_task anywhere"
  else
    puts "Found in #{path}"
  end
end

# Creates a sample file for testing in /tmp.
def reset_test_file(todo_file='test/todo.txt')
  FileUtils.rm_rf '/tmp/todo-curses'
  FileUtils.mkdir '/tmp/todo-curses'
  FileUtils.cp todo_file, '/tmp/todo-curses/'
end

Bundler::GemHelper.install_tasks

Rake::TestTask.new do |t|
  t.pattern = 'test/*.rb'
end

CUKE_RESULTS = 'results.html'
CLEAN << CUKE_RESULTS
Cucumber::Rake::Task.new(:features) do |t|
  t.cucumber_opts = "features --format html -o #{CUKE_RESULTS} --format pretty --no-source -x"
  t.fork = false
end

Rake::RDocTask.new do |rd|
  rd.main = "README.rdoc"
  rd.rdoc_files.include("README.rdoc","lib/**/*.rb","bin/**/*")
end

task :default => [:test,:features]

# Copies the roadmap file to /tmp and opens the app for it.
desc 'roadmap'
task :roadmap do
  reset_test_file('project-management/todo.txt')
  sh 'bundle exec bin/todo-curses /tmp/todo-curses/todo.txt'
end

# Reset the testing todo.txt file, if you have one.
desc 'reset'
task :reset do
  reset_test_file
end

# Easy way to run the app for dev
desc 'run'
task :run do
  reset_test_file unless File.file?('/tmp/todo-curses/todo.txt')
  sh 'bundle exec bin/todo-curses /tmp/todo-curses/todo.txt'
end

# Easy way to rubocop the project
desc 'Lint Ruby'
RuboCop::RakeTask.new(:rubocop) do |t|
  t.patterns = ['bin/**/*.rb', 'lib/**/*.rb']
end

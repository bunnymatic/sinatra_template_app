# Add RVM's lib directory to the load path.
$:.unshift(File.expand_path('./lib', ENV['rvm_path']))
# Load RVM's capistrano plugin.
require 'bundler/capistrano'
require 'rvm'
require 'rvm/capistrano'

set :rvm_ruby_string, '1.9.2-p180@sinatra_template_app'
set :rvm_type, :root
load 'deploy' if respond_to?(:namespace)

####### VARIABLES #######
set :application, "sinatra_template_app"
set :user, "sinatra_template_app_user"
set :use_sudo, false

set :scm, :git
set :repository,  "ssh://git.mygitrepo.com/git/sinatra_template_app.git"
set :deploy_via, :remote_cache
set :deploy_to, "/home/#{user}/webapp"

set :deploy_server, 'myserver.com
role :app, deploy_server
role :web, deploy_server
role :db, deploy_server, :primary => true

set :runner, user
set :admin_runner, user

namespace :deploy do
  desc "Deploy and start #{application} : #{deploy_server}:#{deploy_to}"
  task :start, :roles => [:web, :app] do
    run "cd #{deploy_to}/current && nohup bundle exec thin -C thin/production_config.yml -R config.ru start"
  end
 
  desc "Stop #{application} : #{deploy_server}:#{deploy_to}"
  task :stop, :roles => [:web, :app] do
    run "cd #{deploy_to}/current && nohup bundle exec thin -C thin/production_config.yml -R config.ru stop"
  end
 
  desc "Stop then start #{application} : #{deploy_server}:#{deploy_to}"
  task :restart, :roles => [:web, :app] do
    deploy.stop
    deploy.start
  end
 
  # This will make sure that Capistrano doesn't try to run rake:migrate (this is not a Rails project!)
  task :cold do
    deploy.update
    deploy.start
  end
end

####### Apache commands ####
namespace :apache do
  [:stop, :start, :restart, :reload].each do |action|
    desc "#{action.to_s.capitalize} Apache"
    task action, :roles => :web do
      invoke_command "sudo -u www /etc/init.d/httpd #{action.to_s}", :via => run_method
    end
  end
end

after "deploy:start", "apache:reload"

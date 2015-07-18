require 'html/proofer'

task :default => [:test]

task :test => [:generate_site, :html_proofer]

task :generate_site do
  sh "bundle exec jekyll build"
end

task :html_proofer do
  HTML::Proofer.new("./_site").run
end

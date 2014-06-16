desc "Generate jekyll site"
task :gen do
  raise "### Site Dir(_site/) not found!" unless File.directory?("./_site")

  puts "### Clear _site/"
  system "cd ./_site && git rm -q -r -f ./"

  puts "## Generating Site with Jekyll"
  system "jekyll build --lsi"

  puts "### Git commit"
  system "cd ./_site && git add . && git diff HEAD; git commit -a"
end

desc "Deploy to github"
task :deploy do
  raise "### Site git dir(_site/.git/) not found!" unless File.directory?("./_site/.git")

  puts "### Git push"
  system "cd _site && git push"
end

desc "Generate website and deploy"
task :gen_deploy => [:gen, :deploy] do
end

desc "Preview"
task :preview do
  system "jekyll serve --lsi"
end

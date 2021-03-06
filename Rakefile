
desc "Update configurations to support publishing to root or sub directory"
task :set_root_dir, :dir do |t, args|
  puts ">>> !! Please provide a directory, eg. rake config_dir[publishing/subdirectory]" unless args.dir
  if args.dir
    if args.dir == "/"
      dir = ""
    else
      dir = "/" + args.dir.sub(/(\/*)(.+)/, "\\2").sub(/^\/$/, '');
    end

    sass_file = '_scss/partials/_variables.scss'
    sass_config = IO.read(sass_file)
    sass_config.sub!(/^\$siteBase:(.+)$/, "$siteBase:\'#{dir}\';")
    File.open(sass_file, 'w') do |f|
      f.write sass_config
    end

    compass_config = IO.read('config.rb')
    compass_config.sub!(/http_path(\s*)=(\s*)(["'])[\w\-\/]*["']/, "http_path\\1=\\2\\3#{dir}/\\3")
    compass_config.sub!(/http_images_path(\s*)=(\s*)(["'])[\w\-\/]*["']/, "http_images_path\\1=\\2\\3#{dir}/images\\3")
    compass_config.sub!(/http_fonts_path(\s*)=(\s*)(["'])[\w\-\/]*["']/, "http_fonts_path\\1=\\2\\3#{dir}/fonts\\3")
    compass_config.sub!(/css_dir(\s*)=(\s*)(["'])[\w\-\/]*["']/, "css_dir\\1=\\2\\3#{dir}/css\\3")
    File.open('config.rb', 'w') do |f|
      f.write compass_config
    end

    jekyll_config = IO.read('_config.yml')
    jekyll_config.sub!(/^root:.*$/, "root: /#{dir.sub(/^\//, '')}")
    File.open('_config.yml', 'w') do |f|
      f.write jekyll_config
    end
    puts "## Site's root directory is now '/#{dir.sub(/^\//, '')}' ##"
  end
end


desc "deploy public directory to github pages"
multitask :push do
  puts "## Deploying branch to Github Pages "
  puts "## Pulling any updates from Github Pages "

  system "git pull"
  system "git add -A"
  puts "\n## Committing: Site updated at #{Time.now.utc}"
  message = "Site updated at #{Time.now.utc}"
  system "git commit -m \"#{message}\""
  puts "\n## Pushing generated website"
  deploy_branch = "gh-pages"
  system "git push origin #{deploy_branch}"
  puts "\n## Github Pages deploy complete"
end


desc "Deploy"
task :deploy do
  # system "rake 'set_root_dir[/rpa/]'"
  system "compass compile --css-dir css"
  system "rake push"
  system "rake 'set_root_dir[/]'"
end

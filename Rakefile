FACTORIO_VERSION = File.read("VERSION").chomp

task default: %w[tag]

desc "Update Version"
task :update_version do
  dockerfile = File.read('Dockerfile')
  File.open('Dockerfile', 'w') {|of|
    of.write dockerfile.gsub('TARGET_VERSION', FACTORIO_VERSION)
  }
  puts "Wrote dockerfile for version #{FACTORIO_VERSION}"
end

desc "Build clairaoswald/factorio:latest"
task :build => [:update_version] do
  system("docker build -t clairaoswald/factorio:latest .")
end

task :console => [:build] do
  system("docker run -it --rm clairaoswald/factorio:latest bash")
end

desc "Tag clairaoswald/factorio:latest with current factorio headless version"
task :tag => :build do
  system("git add .")
  system("git commit -m #{FACTORIO_VERSION}")
  system("git tag #{FACTORIO_VERSION}")
  puts "Wrote git tag"
  system("docker tag clairaoswald/factorio:latest clairaoswald/factorio:#{FACTORIO_VERSION}")
  puts "Wrote docker tag"
end

desc "Push to docker hub and github"
task :publish do
  system("git push origin master --tags")
  system("docker push clairaoswald/factorio:#{FACTORIO_VERSION}")
  system("docker push clairaoswald/factorio:latest")
end

desc "Seed and run the server, no persistence, remove container on exit"
task :quick_server => :build do
  system("docker run -it --rm -e SEED_SERVER=true --name factorio -net bridge -p 34197:34197/udp clairaoswald/factorio:latest /opt/factorio/bin/x64/factorio --start-server savegame")
end

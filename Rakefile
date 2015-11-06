require 'fileutils'

remote = "teapot@72.14.189.19"
dest = "#{remote}:/srv/www/download.rethinkdb.com"
remote_path = "#{dest}/public_html"

desc 'Update the nginx configuration'
task :update_nginx do
    nginx_conf = "nginx.conf"
    sh "scp -P 440 #{nginx_conf} #{dest}"
    sh "ssh #{remote} -t -p 440 'sudo service nginx restart'"
end

desc 'Use rsync to upload files to the download server.'
task :publish, [:force] do |t, args|
    if args.force == "force"
      puts "Publishing to #{remote_path}."
      pretend = ''
    else
      puts "Not publishing to #{remote_path} (Dry-run, use publish[force] to do the actual upload)."
      pretend = "--dry-run"
    end

    src = 'download.rethinkdb.com'
    sh "rsync --progress --recursive --delete --compress --human-readable --rsh='ssh -p 440' --itemize-changes --delay-updates --copy-links #{pretend} #{src}/ #{remote_path}"

    if args.force == "force"
      puts "Published to #{remote_path}."
    else
      puts "Not published to #{remote_path} (Dry-run, use publish[force] to do the actual upload)."
    end
end

def env_version
    version = ENV['VERSION']
    fail "missing VERSION" unless version
    return version
end

def cpe(src, dst, srcname, dstname = '')
    dstname = srcname if dstname.empty?
    FileUtils::Verbose.copy_entry "#{src}/#{srcname}", "#{dst}/#{dstname}"
end

desc 'add a new version to the apt repo'
task :apt do
    version = env_version
    sh "./apt-repo/add-version #{version}"
    sh "./apt-repo/build-repo"
end

desc 'copy all files except for deb packages over to the directory to be uploaded'
task :copy do
    version = env_version
    src = "version/#{version}"
    dst = "download.rethinkdb.com"
    cpe "#{src}/dist", "#{dst}/dist", "rethinkdb-#{version}.tgz"
    cpe "#{src}/osx", "#{dst}/osx", "rethinkdb.dmg", "rethinkdb-#{version}.dmg"
    [[6, 'i686'], [6, 'x86_64'], [7, 'x86_64']].each do |pair|
        ver, arch = pair
        cpe "#{src}/centos#{ver}", "#{dst}/centos/#{ver}/#{arch}", "rethinkdb-#{version.sub('-','_')}.#{arch}.rpm"
        sh "git annex add #{dst}/centos/#{ver}/#{arch}"
        sh "git annex unlock #{dst}/centos/#{ver}/#{arch}/repodata"
        sh "createrepo #{dst}/centos/#{ver}/#{arch}"
    end
    sh "git annex add #{dst}"
end

desc "copy files and build repos for a specific release"
task :prepare => [:apt, :copy] do
    version = env_version
    sh "git commit -m 'Prepared files for #{version}'"
    puts "Before publishing these new files"
    puts "please copy them to the backup using 'git annex copy --to'"
end

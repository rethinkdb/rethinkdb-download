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
task :publish do
    if ENV['force']
      puts "Publishing to #{remote_path}."
      pretend = ''
    else
      puts "Not publishing to #{remote_path} (Dry-run, use force=yes to do the actual upload)."
      pretend = "--dry-run"
    end

    src = 'download.rethinkdb.com'
    sh "rsync --progress --recursive --delete --compress --human-readable --rsh='ssh -p 440' --itemize-changes --delay-updates --copy-links #{pretend} #{src}/ #{remote_path}"

    if ENV['force']
      puts "Published to #{remote_path}."
    else
      puts "Not published to #{remote_path} (Dry-run, use force=yes to do the actual upload)."
    end
end

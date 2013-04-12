remote = "72.14.189.19"

desc 'Update the nginx configuration'
task :update_nginx do
    dest = "#{remote}:/srv/www/download.rethinkdb.com/"
    nginx_conf = "nginx.conf"
    sh "scp -P 440 #{nginx_conf} #{dest}"
    sh "ssh #{host} -t -p 440 'sudo service nginx restart'"
end

desc 'Use rsync to deploy binaries to the download server.'
task :release do
    src = '/srv/rethinkdb-download-mirror'
    dest = "#{remote}:/srv/www/download.rethinkdb.com/public_html"
    command = "rsync -Przvh --delete -e \'ssh -p 440\' #{src}/ #{dest}"
    sh "ssh teapot@dr-doom -t '#{command}'"
    puts 'Synced dr-doom mirror with Linode.'
end

0 Nginx native
1 PHP CLI 8.3
2 /public
3 SSL
4 Append mysql and php to files //maybe elastic already?
5 Restart services
5 Installed elastic and added config
7 Disabled Redis pass in Runcloud
8 Database setup
9 Install Shopware
10 Added SW Files
11 Restarted all services redis, elastic etc 
12 Checks
No sh needed





Step 1:
Deploy New Web App

Step 2: Public Path: /public

Step3:
Create Database User -> Create Database with this user
Database with collation: utf8mb4_unicode_ci

Step 3:
Create "Empty Web App" with "NGINX + Apache2 Hybrid"

FPM Config
Process Manager = Dynamic
pm.start_servers = 20
pm.min_spare_servers = 10
pm.max_spare_servers = 30
pm.max_children = 80
pm.max_requests = 500

PHP Settings:
disable_functions: 
getmyuid,passthru,leak,listen,diskfreespace,tmpfile,link,shell_exec,dl,exec,system,highlight_file,source,show_source,fpassthru,virtual,posix_ctermid,posix_getcwd,posix_getegid,posix_geteuid,posix_getgid,posix_getgrgid,posix_getgrnam,posix_getgroups,posix_getlogin,posix_getpgid,posix_getpgrp,posix_getpid,posix_getppid,posix_getpwuid,posix_getrlimit,posix_getsid,posix_getuid,posix_isatty,posix_kill,posix_mkfifo,posix_setegid,posix_seteuid,posix_setgid,posix_setpgid,posix_setsid,posix_setuid,posix_times,posix_ttyname,posix_uname,escapeshellcmd,ini_alter,popen,pcntl_exec,socket_accept,socket_bind,socket_clear_error,socket_close,socket_connect,symlink,posix_geteuid,ini_alter,socket_listen,socket_create_listen,socket_read,socket_create_pair,stream_socket_server


max_execution_time = 300
max_input_time = 300
max_input_vars = 10000
memory_limit = 1024
upload_max_filesize = 256
post_max_size = 256
session.gc_maxlifetime = 1440

Step 4:
Server Settings -> PHP-CLI Version -> PHP 8.3

Step 5: Link Database with Web App in Settings of it

root@nue01:/etc/php-extra# service php83rc-fpm restart
root@nue01:~# su - runcloud
runcloud@nue01:~$ cd webapps/shopware/
runcloud@nue01:~/webapps/shopware$ rm -rf * .*
runcloud@nue01:~/webapps/shopware$ php /usr/sbin/composer create-project shopware/production .
runcloud@nue01:~/webapps/cyber$ composer require symfony/redis-messenger

cd /tmp 
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.15.1-amd64.deb
sudo dpkg -i elasticsearch-8.15.1-amd64.deb
sudo apt-get install -f
sudo systemctl enable elasticsearch
sudo systemctl start elasticsearch
sudo systemctl status elasticsearch
##config
sudo systemctl restart elasticsearch



Disable password reddis in runcloud

Install Shopware www

Supervisor:
/RunCloud/Packages/php83rc/bin/php /home/runcloud/webapps/shopware/bin/console messenger:consume async low_priority scheduler_shopware --time-limit=600 --memory-limit=1024M --sleep=1
/RunCloud/Packages/php83rc/bin/php /home/runcloud/webapps/shopware/bin/console messenger:consume default --time-limit=600 --memory-limit=1024M --sleep=1
/RunCloud/Packages/php83rc/bin/php /home/runcloud/webapps/shopware/bin/console messenger:consume failed --time-limit=600 --memory-limit=1024M --sleep=1
/RunCloud/Packages/php83rc/bin/php /home/runcloud/webapps/shopware/bin/console scheduled-task:run --time-limit=600 --memory-limit=512M

/home/runcloud/webapps/shopware

Additional Config:
startsecs=1
stopwaitsecs=10

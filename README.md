#### The below python code use to pull the data from website (stackflow.py).

	import subprocess
	import mechanize
	import urlparse
	from bs4 import BeautifulSoup
	import urllib2 
	import cookielib
	cj = cookielib.CookieJar()
	br = mechanize.Browser()
	br.set_cookiejar(cj)
	br.open("https://******/login")

	br.select_form(nr=0)
	br.form['name'] = 'username'
	br.form['password'] = 'password'
	br.submit()
	#print =br.response().read()
	with open('contrat.txt') as f:
		for line in f:

			response = br.open(urlparse.urljoin('https://targetpage', line))
			r = response.read()
			text_file = open('output.txt', 'w')
			text_file.write(r)
			text_file.close()
			test = (subprocess.check_output("cat output.txt  | grep string1", shell=True))
			urls = re.findall(r'href=[\'"]?([^\'" >]+)', test) # we are finding all links in test
			con =  ', '.join(urls).split("/")[4] #4th link on page
			response1 = br.open(urlparse.urljoin('https://another url here', con)) #concatenation
			w =  response1.read()
			text_file = open('output1.txt', 'w')
			text_file.write(w)
			text_file.close()
			test1 = (subprocess.check_output("cat output1.txt | grep -w -A1 -e emp_name -e code_name -e address -e id | grep -v -e '<th>' -e 'label' -e 'input'", shell=True))
			print line
			print test1



#### The below code will align the report, 

	python stackflow.py > 02_06.txt

	cat 02_06.txt  | sed 's/<\/td>//g' | sed 's/<td>//g' | sed 's/^[ \t]*//;s/[ \t]*$//' | grep -v -e ^- -e ^$ | grep -v -B1 ^econ | grep -v  ^- | sed "s/$/,/g"| awk 'ORS=NR%5?FS:RS'

	1)sed 's/<\/td>//g' - will remove the <\/td> tag
	2)sed 's/<td>//g' - will remove the <td> tag
	3)sed 's/^[ \t]*//;s/[ \t]*$//' - replaces leading whitespace &  trailing whitespace
	4)grep -v -e ^- -e ^$ - Remove line start with - and  blanks lines
	5)grep -v -B1 ^econ - remove one line before the matching pattern.
	6)grep -v  ^- - Remove line start with -
	7)sed "s/$/,/g" - Add comma at end of every lines.
	8)awk 'ORS=NR%5?FS:RS' - Concatenate 5 words per line.


#### The below python script is use to auto login on jump server from local system and form tunnel with storage box without any addition package on any servers
##### Install paramiko module on local machine.
##### Run the below script from local machine.

	import paramiko
	from sshtunnel import SSHTunnelForwarder
	with SSHTunnelForwarder(
	   ('servername', 22),		#jump server address
	    ssh_username='ajay.prajapati',
	    ssh_pkey=paramiko.RSAKey.from_private_key_file("/root/.ssh/id_rsa"),
	    #ssh_private_key_password="*******",
	   remote_bind_address=("*.*.*.*", 22), #storage box ip address 
	   local_bind_address=('127.0.0.1', 10023)
	) as tunnel:
	   client = paramiko.SSHClient()
		#client.load_system_host_keys()
	   client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
	   client.connect(hostname='127.0.0.1', username="root", password="****", port=10023, look_for_keys=False)
		# do some operations with client session
	   stdin, stdout, stderr = (client.exec_command('./datapull.sh')) # it will run the datapull.sh which is on storage box.
	   print stdout.readlines()
	   print stderr.readlines()
	   client.close()
	   
#### The command used to upload the file on wesite- This example to upload file on confluence.
	cat confluence.py import subprocess subprocess.check_output("curl -v -S -X POST -H 'X-Atlassian-Token: no-check' -F 	file='@filename.txt' 'https:********* username=******&os_password=*********'", shell=True)`

#### The below command will print the MAC and IP address for provided interface - it ran on Centos/Redhat - 7
	ip addr show enp0s8 | grep -w  -e  inet -e ether | awk '{ print $2; }' | sed 's/\/.*$//'
	
#### Output-
		**:00:27:**:d7:**
		10.0.0.**

#### Upgrade the python from 2.7.5 to 3.5.2 - 
	Ensure you have installed below:
	a) yum groupinstall "Development tools"
	b) yum install openssl-devel
	c) yum install zlib-devel bzip2-devel sqlite sqlite-devel openssl-devel

	1) wget https://www.python.org/ftp/python/3.5.2/Python-3.5.2.tgz
	2) tar -xvzf Python-3.5.2.tgz
	3) cd Python-3.5.2
	4) ./configure --prefix=/usr/local
	5) make altinstall
	6) alias python=/usr/bin/python3 and enter the same entry in  ~/.bashrc file to make it permanent
	
	File - .bashrc is below

	# User specific aliases and functions

	alias python=/usr/local/bin/python3.5
	alias rm='rm -i'
	alias cp='cp -i'
	alias mv='mv -i'

	# Source global definitions
	if [ -f /etc/bashrc ]; then
        . /etc/bashrc
	fi
	
	7) logout from current bash and relogin
	
	8) verify with below command:
	[root@main3 ~]# python --version
	Python 3.5.2

#### How to find package name by using command:
	[root@****]# rpm -qf $(which top)
	output - procps-ng-3.3.10-10.el7.x86_64

#### For loop example 
		for i in `cat serverlist.txt | awk '{ print $2 }'` ; do ssh@$i; done
	bash: ssh@10.0.0.1: command not found...
	bash: ssh@10.0.0.2: command not found...


		]# for i in `seq 1 10` ; do echo file$i; done
		file1
		file2
		file3
		file4
		file5
		file6
		file7
		file8
		file9
		file10

#### To check port is open or not on remote/local system without nmap
		]# echo > /dev/tcp/`hostname`/80 && echo "PORT OPEN"
		PORT OPEN
		
		]# echo > /dev/tcp/`hostname`/81 && echo "PORT OPEN"
		-bash: connect: Connection refused
		-bash: /dev/tcp/localhost.localdomain/81: Connection refused
		
		for i in `seq 1 80` ; do echo > /dev/tcp/`hostname`/$i && echo "PORT OPEN $i";  done

		
#### While infinte loop example
	#!/bin/bash
	while :
	do
		echo "Press [CTRL+C] to stop.."
		sleep 1
	done
-----------------------------
	#!/bin/bash
	x=1
	while [ $x -le 5 ]
	do
	  echo "Welcome $x times"
	  x=$(( $x + 1 ))
	done
-----------------------------

	#!/bin/bash
	# ---------------------------------------------------------------------------
	# capture CTRL+C, CTRL+Z and quit singles using the trap
	trap '' SIGINT
	trap ''  SIGQUIT
	trap '' SIGTSTP

	# display message and pause 
	pause(){
		local m="$@"
		echo "$m"
		read -p "Press [Enter] key to continue..." key
	}

	# set an 
	while :
	do
		# show menu
		clear
		echo "---------------------------------"
		echo "	     M A I N - M E N U"
		echo "---------------------------------"
		echo "1. Show current date/time"
		echo "2. Show what users are doing"
		echo "3. Show top memory & cpu eating process"
		echo "4. Show network stats"
		echo "5. Exit"
		echo "---------------------------------"
		read -r -p "Enter your choice [1-5] : " c
		# take action
		case $c in
			1) pause "$(date)";;
			2) w| less;;
			3) echo '*** Top 10 Memory eating process:'; ps -auxf | sort -nr -k 4 | head -10; 
			   echo; echo '*** Top 10 CPU eating process:';ps -auxf | sort -nr -k 3 | head -10; 
			   echo;  pause;;
			4) netstat -s | less;;
			5) break;;
			*) Pause "Select between 1 to 5 only"
		esac
	done
-------------------------------------------------------------------
		printf "\033[0;31m Hello \033[0m\n" -> red
		printf "\033[0;32m Hello \033[0m\n" -> green
		printf "\033[0;33m Hello \033[0m\n" -> yellow
		
-------------------------------------------------------------------

		#netstat -tunap |awk 'NR > 2{print $4}' |sort -rn |cut -f 1 -d : |uniq -c
		
		NR is used to ignore lines
		to sort in uniq order, r=to sort in reverse order, n= numerical sort 
		cut -f -> f=is like column, 1 means 1st column, d=acts as delimiter
		uniq -c - prefix lines by the number of occurrences
-----------------------------------------------------------------------------

		#awk '!/^$/{print $1}'  -> To ignore blank lines with awk command
		
		similarly we can ignore lines which are starting with # in a file 
		eg: cat ls_files.txt |awk '!/^#/{print $1}'

-----------------------------------------------------------------------------

		#awk -F: '{print $2; system("bash -c hostname")}'
		We use system() flag to run a command in awk
		
---------------------------------------------------------------------------
		To grep a word in a line and use in awk
		
		#ifconfig enp0s3 | awk -F : '/inet /'
        	#inet 10.0.2.*  netmask 255.255.255.0  broadcast 10.0.2.255
		#ifconfig enp0s3 | awk '/inet / {print $2}'
-----------------------------------------------------------------------------	
		copy the list of serevers in /tmp/serverlist file and then execute below command
		# for i in `cat /tmp/serverlist`; do bash your_script $i; done
------------------------------------------------------------------------------

		#!/bin/bash
		
		if [ -z $1 ] 2> /dev/null; # Zero-length
		then
        		printf "\033[0;33m ******* Please Enter Server name on which 'test.sh' is to be executed ******* \033[0m\n";
        		exit 0;
		else
       		 	yum install -y expect
        		scp -o BatchMode=yes -o ServerAliveInterval=5 -o StrictHostKeyChecking=no /tmp/test.sh user@$1:/tmp
			exit 0;
		fi
------------------------------------------------------------------------
		#!/bin/bash
		echo '*******' |passwd --stdin root # set the root password
		/usr/bin/systemctl stop firewalld
		/usr/bin/systemctl disable firewalld
		echo "nameserver 8.8.8.8" > /etc/resolv.conf
		
		###
		#export LOC=MUM
		#export LOC=BAN
		export LOC=TAN
		
		if [ ${LOC = "MUM" ]; then
			export NTP_SER=*.*.*.*
			export DNS_SER=*.*.*.*
		
		elif [ ${LOC = "BAN" ]; then
			export NTP_SER=*.*.*.*
			export DNS_SER=*.*.*.*
		
		elif [ ${LOC = "TAN" ]; then
			export NTP_SER=*.*.*.*
			export DNS_SER=*.*.*.*
		fi
		
		/bin/mv /etc/ntp.conf /etc/ntp.conf.bak.`date '+%Y%m%d%H%M%S'`
		
		/bin/cat <<EOF > /etc/ntp.conf
		tinker panic 0
		restrict default nomodify notrap noquery
		restrict 127.0.0.1
		restrict ::1
		server ${NTP_SER} iburst 
		restrict ${NTP_SER} mask 255.255.255.255 nomodify notrap noquery
		driftfile /var/lib/ntp/drift
		EOF
		/etc/init.d/ntpd restart

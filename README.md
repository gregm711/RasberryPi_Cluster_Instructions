# RasberryPi_Cluster_Instructions
Setup instructions for how to create a Beowulf cluster of rasberry Pis 

Credit goes to tinkernut


Tools:
2 < # raspberry Pis
16 <  Gb sd cards
Ethernet cords
router
Perseverence


Steps:

1: Format Sd cards

2: Install Raspian  here :https://www.raspberrypi.org/downloads/     DO NOT INSTALL NOOBS

3: Burn Raspian onto one SD card

4: Boot Up pi, run sudo rasp-config and change the following:

  ~ Expand file system
  ~ match internationalization options to change keyboard if nessesary
  ~ Click advanced options and change the following
        ~ Set hostname to Pi01
        ~ split memory to 16mb 
        ~ enable SSH

5: sudo reboot

6: hostname -I                Write down IP address of pi!  Ex: 127.0.0.1     

7: From normal computer terminal:    ssh pi@127.0.0.1    Note that 127.0.0.1 is not your pis IP, just an example I picked

NOTE After SSH'ing into pi, all furth commands will be entered on normal computer

8: sudo apt-get update

9:mkdir mpich2

10:cd ~/mpich2

11:wget http://www.mpich.org/static/downloads/3.1/mpich-3.1.tar.gz

12:tar xfz mpich-3.1.tar.gz

13:sudo mkdir /home/rpimpi/

14:sudo mkdir /home/rpimpi/mpi-install

15:mkdir /home/pi/mpi-build

16:cd /home/pi/mpi-build

17:sudo apt-get install gfortran

18:sudo /home/pi/mpich2/mpich-3.1/configure -prefix=/home/rpimpi/mpi-install

19:sudo make

20:sudo make install

21:nano .bashrc

22:PATH=$PATH:/home/rpimpi/mpi-install/bin

23:sudo reboot

24:mpiexec -n 1 hostname         ** should return Pi01

25:sudo aptitude install python-dev

26:wget https://mpi4py.googlecode.com/files/mpi4py-1.3.1.tar.gz

27:tar -zxf mpi4py-1.3.1

28:cd mpi4py-1.3.1

29:python setup.py build

30:python setup.py install

31:export PYTHONPATH=/home/pi/mpi4py-1.3.1

32:mpiexec -n 5 python demo/helloworld.py

33: sudo apt-get install nmap

34: Ifconfig

35: sudo nmap -sn IPADDRESS ex: sudo nmap -sn 127.0.0.1

36:mpiexec -n 1 hostname

37:mkdir mpi_test

38: cd mpi_test

39: nano machinefile
		~ IPADDRESS1
		~ IPADDRESS2
		....
		
40:mpiexec -f machinefile -n 4 hostname

41: Add ssh keys to all pis

PI01

Ssh-keygen

Cd ~

Cd .ssh

Cp id_rsa.pub pi01

Ssh pi@IPADDRESSPi02

Setup Pi02:

Ssh-keygen

Cd .ssh

Cp id_rsa.pub pi02

Scp IPADDRESPi01:/home/pi/.ssh/pi01 .

Cat pi01 >> authorized_keys

Exit

Setup Pi03

Ssh-keygen

Cd .ssh

Cp id_rsa.pub pi03

Scp IPADDRESSPi01:/home/pi/.ssh/pi01 .

Cat pi01 >> authorized_keys

Exit

Scp 192.168.1.3:/home/pi/.ssh/pi02

Cat pi02 >> authorized_keys

YOU MADE IT!!!!  Now just make a python script, copy it to the same directory on alll pis and wtach it fly

EX:

nano helloWorld.py
	print 'hello world'
	
scp -r helloWorld.py IPADDRESSPI02:home/pi

scp -r helloWorld.py IPADDRESSPI03:home/pi

mpiexec -f machinefile -n 1 python helloWorld.py

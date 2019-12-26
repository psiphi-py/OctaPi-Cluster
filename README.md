# OctaPi-Cluster
Raspberry Pi 8 Node cluster build based on University of South Hampton method: https://www.southampton.ac.uk/~sjc/raspberrypi/

**ψ-π Cluster V.1.0**

**Components:** 

* 8 x raspberry-pi 3B+
* 8 x 4+ GB micro SD cards
* 1 x 8 port lan switch
* 8 x 5V 2.5A power supply
* 8 x network cables
* 1 x screen
* 1 x HDMI cable 
*1 x mouse
*1 x keyboard
* internet connection for setup

**optional**
* 8 x cluster cases
* SD card adapter

**Software Used:**

* MPICH
* Fortran (for MPICH installation)
* MPI4PY


**Setup:**

Note: we only install all software on 1 SD card, as we will copy the SD card for all others.

Install the latest raspbian OS on the SD card, and follow automated instructions, however, leave the hostname and password to default. Once logged in, ensure the raspberry is connected to the internet, then in the terminal type:

*sudo apt-get update*

Which will supply the system’s downloadable archive with upgraded packages (if available). Next, fortran needs to be installed, to aid with the first MPI (MPICH) installation. Once again, in the terminal, type:

 *sudo apt-get install gfortran*

Now, we need to make directories for MPICH installation. We do this via the terminal prompt:

*mkdir /home/pi/mpich3*

Note, we add “3” to “mpich” as the current version of MPICH as of the time of writing this version 3, adjust to higher numbers if necessary. Move the working directory within the newly created ‘mpich3’ directory via terminal input:

*cd mpich3*

Once in this directory, download the latest version of MPICH with the terminal command (currently, 3.3 is the latest, adjust if necessary, all within one line command):

*wget http://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz*

Unpack the downloaded file through command:

*tar xfz mpich-3.3.tar.gz*

Next, we make a build directory with two commands:

*sudo mkdir /home/rpimpi*
*sudo mkdir /home/rpimpi/mpich3-install*

Then build an additional file for the software, and move into the created directory:

*sudo mkdir /home/pi/mpich_build*
*cd /home/pi/mpich_build*

Configure the MPICH to a directory (Note, the following is within 1 line command):

*sudo /home/pi/mpich3/mpich-3.3/configure prefix=/home/rpimpi/mpich3-install*

Once the building process is complete, the files need to be made in the directory:

*sudo make*

Then, the files need to be installed:

*sudo make install*

For easier access to the MPI executable, a path needs to be created:

*sudo nano /home/pi/.profile*

Move to the opened text’s open bottom space and type:

*#Adding MPI to PATH*
*PATH=”$PATH:/home/rpimpi/mpich3-install/bin”*

Then close and save by pressing: ‘ctr’+’x’, then ‘y’, then ‘enter’. A restart of the system is needed. Once restarted, make a directory for the connected node’s IPs, and move into that directory:

*mkdir mpi_testing*
*cd mpi_testing*

The file containing the IPs needs to be made (find this pi’s IP by typing ‘ifconfig’ in the terminal and copying the eth IP) else, if already known:

*sudo nano machinefile*

And paste the pi’s IP into the document and save it. This MPICH software, however, isn’t ideal for a python environment. Now we install MPI4PY:

*sudo apt-get install python3-mpi4py*

Now, make a directory for MPI4PY, and move into that directory:

*mkdir /home/pi/mpi4py*
*cd mpi4py*

Download mpi4py into directory (all 1 line terminal command, where the version is a variable again):

*wget https://bitbucket.org/mpi4py/mpi4py/downloads/mpi4py-3.0.tar.gz*

Once again, unpack the downloaded file:

*tar xfz mpi4py-3.0.tar.gz*

The SD card may now be replicated using the SD card reader and the raspberry pi’s SD clong software. 7 Clones need to be made, and fitted into a pi before starting up each one. Access each pi’s machinefile.txt in the mpi_testing directory to change their IPs to their own (using ‘ifconfig’ in terminal for ‘eth IP’ reference). Assign to each pi a hostname cronologically, such as header_node, node02 etc. In header node, paste all the other node’s IPs into the machinefile as well. Connect all the network cables to the lan switch and power it up.

In the header_node terminal, make a ssh key:

*ssh-keygen -t rsa -C “raspberrypi@raspberrypi”*

And save it in deafault directory(press ‘enter again’ and leave the passphrase empty). This key needs to be shared with each node:

*cat ~/.ssh/id_rsa.pub | ssh pi@X “mkdir.ssh;cat >> .ssh//authorized_keys”*

Where X denotes the node’s IP. This process needs to be repeated for each node from the header_node terminal.


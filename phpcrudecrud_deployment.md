## PHP Crude CRUD App Deployment Guide

### Section #1: Purpose
The purpose of the PHP Crude CRUD App is to allow the user to access the a database of employees through a web browser. 

### Section #2: Steps to Deploy the Application
To deploy the application, you must install a LAMP (Linux, Apache, MySQL, PHP) stack by following the general steps below.
1. Configure and launch a virtual machine (explained in detail in **Sections 3 and 4**)
2. Install Ubuntu Server 20.04 (**Section 5**)
3. Install Apache2 and PHP (**Section 6**)
4. Install MySQL (**Section 7**)
5. Configure the app to connect to the MySQL database (**Section 8**)
6. Deploy the app (**Section 9**)
7. Test the app (**Section 10**)

### Section #3: Virtual Machine Configuration
This tutorial will assume that you are using a type-two hypervisor such as Oracle VM VirtualBox to configure and launch the virtual machine (VM). To ensure that the virtual machine has enough computing resources for it to handle all requests smoothly, it should have the following minimum specifications:
* 1 CPU Core
* 2GB RAM
* 40GB Hard Drive
* Ubuntu Server 20.04

### Section #4: Launching the Virtual Machine
To launch the virtual machine, follow the steps below. This tutorial assumes that you are using Oracle VM VirtualBox to create the VM and that you have already successfully downloaded and installed it.
1. In a web browser, download the Ubuntu Server 20.04 ISO image onto the host machine from the [Ubuntu Server downloads page](https://ubuntu.com/download/server).
1. In VirtualBox, press the "New" icon (a blue circle with spikes around it) at the top and start the VM installer.
1.  On the first page of the installer, give the VM a name, choose the folder you want the VM to be in, and select the ISO image file that you downloaded earlier. Check the "Skip Unattended Installation" box.
1. On the next two pages, allocate the computing resources specified in **Section 3** to the virtual machine
1. On the "Summary" page, confirm that the specifications were set to the desired values and, if so, click the "Finish" button at the bottom.
1. Once the VM is finished being made, you will see it listed on the left side of VirtualBox and already selected. Before you can launch it, you must set up the networking for it.
1. Press on the "Tools" button (symbolized by clip art of three hand tools) in the top left corner of VirtualBox.
1. Press on the "Create" button (symbolized by a green plus sign over clip art of a network adapter card) to the right of the "tools" button. This will create a virtual host-only networking card so that the host (your computer) can connect to its VMs.
1. If asked, allow VirtualBox to make changes to your computer.
1. You should see a new virtual network adapter, titled "VirtualBox Host-Only Ethernet Adapter", appear under the "Create" button.
1. Check that the settings for the virtual network adapter you just created are properly configured. Make sure that the adapter is selected (highlighted in blue) and click on the "Adapter" tab at the bottom of the VirtualBox window and make sure that the "Configure Adapter Manually" box is checked.
1. Click on the "DHCP Server" tab next to the "Adapter" tab. Make sure that the "Enable Server" box is checked.
1. Click on the VM you made earlier in this section on the left-hand side of the VirtualBox window and press the "Settings" icon at the top.
1. Press on the "Network" tab on the left and click the "Adapter 2" tab on the top. Check the "Enable Network Adapter" box. Under the "Attached to:" dropdown, press select the "Host-only Adapter" option, and under the "Name:" dropdown, select the host-only network adapter that you created earlier in this section. Press OK on the bottom of the settings window.

### Section #5: Install Ubuntu Server 20.04
1. Launch the virtual machine that you created in **Section 4** by making sure that it's selected on the left side of the VirtualBox window and clicking the "Start" button (symbolized by a green arrow pointing right) at the top.
1. Traverse through the Linux setup prompts by using the UP, DOWN, and ENTER keys. Each step below represents a page of the setup wizard.
    1. Select English for the language.
    1. Select "Continue without updating".
    1. Edit the keyboard settings to match yours and then select Done.
    1. If you configured the VM correctly, there should be two entries on this page (one named "enp0s3" and one named "enp0s8"). Make sure that both of these have IP addresses under them. If not, you may have configured the virtual machine incorrectly.
    1. Select Done.
    1. Select Done.
    1. Select Done.
    1. Select Done.
    1. Select Continue.
    1. Configure your server. Make sure to remember the username and password that you set so you can access your virtual machine later. Select DONE when you are finished.
    1. Press ENTER on the "Install Open SSH server" checkbox. Select Done.
    1. Select Done.
    1. Wait for the installation to complete. When it is done, select Reboot.
1. Once the system has rebooted, press ENTER to unmount the installation media. Then, log in with the username and password you set when you created the VM.
1. Once you're logged into the VM, check that the networking was configured correctly by entering `ip addr` into the command line. You should see both the "enp0s3" and "enp0s8" addresses. Write down the "enp0s8" address, it's the one that you will use to view the application in a web browser later.

### Section #6: Installing Apache2 and PHP
To turn the VM into a web server, you must install Apache2 and PHP. Do so by following the steps below.
1. Navigate to the home directory by entering `cd ~` into the command line.
2. To update the package manager so that it installs the latest packages, enter `sudo apt update` into the command line.
1. Enter `sudo apt install apache2` into the command line to install apache2.
1. To make sure that you installed Apache2 correctly, visit a web browser and enter `http://<ip address of your server>` into the address bar. If the server is working correctly, the browser should display the default Apache page.
1. To install PHP, enter `sudo apt install php libapache2-mod-php php-cli php-mysql php-pgsql` into the command line.
1. Check to make sure it worked by entering `php -v`. If PHP is correctly installed, this command should show the version that is installed.

### Section #7: Installing MySQL
This section will go over installing the MariaDB distribution of MySQL.
1. Naviagate to the home directory by entering `cd ~` into the command line.
1. Enter the following commands (without the '$') into the command line to install and verify MariaDB.
```
$ sudo apt upgrade
$ sudo apt update
$ sudo apt install mariadb-server
$ sudo systemctl status mariadb
```
The last command you entered should show that the status of mariadb is "active (running)". Press CTRL + C to get back to the Linux command line. 
1. Enter `sudo systemctl restart mariadb` into the command line to restart the server.
1. Enter `sudo mysql_secure_installation` into the command line to secure MariaDB. 
    1. Do not change the MySQL root user
    1. Remove anonymous access
    1. Remove the test table
    1. Do not allow root to access the database remotely.
1. Enter `sudo mysql` to log into MySQL.
1. You need to create a user that can access the database. To do so, enter `<someusername>@'localhost' identified by '<somepassword>';`. Replace what's in the <> brackets with the username and password for the user.
1. To give this user privileges, enter `grant all privileges on *.* to '<someusername>'@'localhost';`.
1. To make the changes take affect, flush the privilege table by entering `flush privileges;`.
1. Exit the database by entering `quit;`.
1. Test that you can get into the database by entering `mysql -u <your new user> -p` on the Linux command line and seeing if you were able to get in.
1. Now you need to create data for the application to read. 
    1. First, you need to create the "employees" table by entering the command `CREATE TABLE employees (PersonID int, LastName varchar(255), FirstName varchar(255), Address varchar(255), City varchar(255));`.
    1. Then, you need to insert employees into the database by entering this command for each employee: `INSERT INTO employees VALUES (<employee id>, <employee last name>, <employee first name>, <employee address>, <employee city>);`
    1. Exit MySQL by entering `quit;` into the command line.

### Section #8: Configuring PHP to Interact with MySQL
This section will go over downloading the PHP Crude CRUD App and configuring it so that it can be deployed correctly. 
1. Naviagate to the home directory by entering `cd ~` into the command line.
1. Clone the PHP Crude CRUD App from GitHub by entering `git clone https://github.com/axbjos/phpcrudecrudapp.git`.
1. Navigate to the app's directory by entering `cd phpcrudecrudapp`.
1. Now you must edit the "credentials.php" file so that the application will be able to access the database.
    1. Enter `nano credentials.php` into the command line. This will open the "credentials.php" file in a text editor.
    1. Change the `$username = "phpuser1";` line to `$username = "<username of the MySQL user you created earlier in the tutorial>";`.
    1. Change the `password = "abc123";` line to `$password = "<password of the MySQL user you created earlier in the tutorial>";`.
    1. Exit the text editor by pressing CTRL + O, ENTER, and then CTRL + X.

### Section #9: Deploying the PHP Crude CRUD App
Now you are ready to deploy the app so that it can be accessed by a web browser.
1. Make sure that you are in the `phpcrudecrudapp` directory by entering `cd ~/phpcrudecrudapp` into the command line.
1. Enter `sudo cp * /var/www/html/` into the command line. This will copy the `phpcrudecrudapp` directory into the `/var/www/html/` directory, which is where the apache web server reads from.
1. Enter `http://<ip address of your server>/phpcrudecrudapp` into the address bar of your web browser. If everything worked, you should see the main page of the app.

### Section #10: Testing the App
1. To test the app, go to the web browser you are accessing it on and click on "Add Employee Record" on the home page.
1. Use the user interface to enter in the details for a new employee and press the "Submit" button.
1. Go back to the home page and click on "Search Employee Records". Enter the last name of the employee you entered in the previous step. If the employee was successfully entered, they'll show up when you press the "Submit" button.
1. Go back to the home page and click "Delete Employee Record". Enter the employee number for the employee that you just entered and click on the "Submit" button.
1. Go back to the home page one last time and click on "Search Employee Records". Enter the last name of the employee you deleted. If their name does not show up, the delete function was successful.
6. Congratulations, you successfully deployed the PHP Crude CRUD App!
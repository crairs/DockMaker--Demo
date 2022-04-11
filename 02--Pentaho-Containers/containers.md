## <font color='red'>Pentaho 9.3 - Containers</font>  

The following pre-requisites configure the Pentaho Server and Data Integration 9.3.

Prerequisites for DockMaker:
* Docker
* A Docker Hub user account - pull required database images
* CURL command on host
* Java 8 or 11
* Pentaho EE license - PENTAHO_INSTALLED_LICENSE_PATH=/data/licenses/.installedLicenses.xml

---

<em>Pentaho EE License</em> 
 
Set the PENTAHO_INSTALLED_LICENSE_PATH variable so that when you start Pentaho, the licenses can install.  

<font color='red'>If you do not set the variables, Pentaho will not start correctly.</font>

<font color='teal'>This section is for reference only. The license is located at: /data/license</font>

``open a terminal window and log in as root:``
```
sudo -i
```
``edit /etc/environment file:``
```
cd /etc/environment
nano  /etc/environment
```
``add the following:``
```
export PENTAHO_INSTALLED_LICENSE_PATH=/data/licenses/.installedLicenses.xml
```
Note: You will need to log out and back in to set the variable. 

``verify that the PENTAHO_INSTALLED_LICENSE_PATH variable is set:``
```
env | grep PENTAHO_INSTALLED_LICENSE_PATH
```

---

<em>Install DockMaker</em>  

DockMaker is a command line tool used to create containers for Pentaho products:
* Pentaho Server
* Carte server
* Pentaho Data Integration

<font color='teal'>DockMaker has been downloaded and copied to ~/dock-maker-9.3.0.0</font>

Install the package by completing these steps.

``run the install script:``
```
cd dock-maker-9.3.0.0
./install.sh
```
Note: a console window will appear.
``accept license:``

``edit the installation path to:``
```
/home/pentaho/dock-maker-9.3.0.0
```
Note: The Installation Progress window appears. Progress bars indicate the status of the installation. When the installation progress is complete, click Quit to exit the Unpack Wizard.

---

<em>DockMaker</em>

DockMaker is a command line tool for building and deploying Pentaho Containers. 
DockMaker directories: 

* <font color='teal'>artifactCache:</font> This folder serves as the default storage location for any artifacts that are downloaded or required to setup the image.  The location of this folder can be changed in the DockMaker.properties file.
* <font color='teal'>containers:</font> This has various files and templates that will be tapped when running the command line tool.
* <font color='teal'>generatedFiles:</font> This folder is created when the command line tool is executed.  It contains all the file necessary to create a docker image and use docker compose to bring up the containers.
* <font color='teal'>lib:</font> Dependent libraries.

To build Pentaho EE 9.3.0.0-427 container:
the following commands will display the required commands but not execute:

``build Pentaho Server EE 9.3.0.0:``
```
cd dock-maker-9.3.0.0
./DockMaker.sh -V 9.3.0.0/427/ee -A paz,pdd,pir -U --EULA_ACCEPT=true
```
Note: if you wish to automate the build add the flag: -X


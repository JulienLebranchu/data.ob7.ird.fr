# Marine Institute Data Catalogue: GeoNetwork

This repository contains the custom files for the [GeoNetwork](https://geonetwork-opensource.org/) implementation of the
Marine Institute's Data Cataologue.

---

# How to Build the MI Data Catalogue geonetwork application

The external web portal for the MI Data Catalogue is based on GeoNetwork 3 with MI customizations:
These include:

- UI Changes to include an MI look & feel.
- Custom search facets
- Additional plugins: SensorML, SeaDataNet CDI, SeaDataNet CSR. For more information on building plugins for geonetwork refer to: <https://github.com/metadata101/sensorML>

## Prerequisites
The following tools are required to be installed to setup a development environment for GeoNetwork:

For details on the latest supported versions, please refer to: GeoNetwork Software Development Guide <https://github.com/geonetwork/core-geonetwork/tree/master/software_development>

- **Java**: Developing with GeoNetwork requires Java Development Kit (JDK) 1.8 or greater.
- **Maven** 3.1.0+ GeoNetwork uses Maven to manage the build process and the dependencies.
    Install Maven from following the Maven Install Guide: <https://maven.apache.org/install.html>. &nbsp;
    Manually set the following: Set the JAVA_HOME path (e.g. C:\Program Files\Java\jdk1.8.0_171​) &nbsp;
    Add the Maven bin location to your 'path' environment variable. (e.g. C:\Program Files\apache-maven-3.5.4\bin​)
- **Git**: GeoNetwork source code is stored and versioned in a Git repository on Github. Install Git for Windows. <https://git-scm.com/downloads>
- **Ant**: GeoNetwork uses Ant to build the installer. Install from Ant Install Guide​: <https://ant.apache.org/manual/install.html> &nbsp;
    Manually set the Ant bin loaction to you 'path' environment variable. (e.g. C:\Program Files\apache-ant-1.10.4\bin)
- **Sphinx** - To create the GeoNetwork documentation in a nice format Sphinx is used.

## Cloning the MI Data Catalogue Repository (this repo)
Ensure you have cloned the MI Data Catalogue Repository (this repo). This ensures you have the latest build scripts and MI specific file changes before starting the build.

Steps to clone MI Data Catalogue repository:

- Launch a new Git CMD Window and naviagte to (or create) a folder for your git local repositories.
- Run: `git clone --recurse-submodules https://github.com/IrishMarineInstitute/data.marine.ie.git data-catalogue`


## Detailed Build Steps:

**IMPORTANT: Any files included in this github repository must be included in the 'mi_geonetwork_build' maven config script, otherwise files will not be picked up by the build. Ensure your files are included in the build script before starting a build.**

Run the following:
- Launch a new Git CMD Window and naviagte to (or create) a folder for your git local repositories.

Run the commands:
- `git clone --recurse-submodules https://github.com/geonetwork/core-geonetwork.git geonetwork`
- `cd geonetwork`
- `git checkout tags/3.4.2 -b build`
- `git status`

Run a clean build by running the command:
- `mvn clean install -DskipTests`

The build should be sucessfull.
Note: Maven default location: C:\Users\<user>\.m2\repository. Output WAR is stored in: \geonetwork\web\target

Pull in sub-modules for Plugins:

- navigate to 'geonetwork\schemas' directory.

Run commands:
- `git submodule add https://github.com/metadata101/sensorML.git sensorML`
- `git submodule add https://github.com/metadata101/iso19139.sdn-csr.git`
- `git submodule add https://github.com/metadata101/iso19139.sdn-cdi.git`

Navigate to the data-catalogue repository and run the MI script: mi_geonetwork_build.xml. This is an ant build script and will copy all required MI content into the build branch of the geonetwork repository (created earlier)

- `cd data-catalogue`
- `ant -buildfile mi_geonetwork_build.xml`

The build (copy files) should complete successfully.

The final step is to navigate back to the 'geonetwork' repository and re-run the Maven build. This will build a new version of the geonetwork application with MI specific changes applied.

- `mvn clean install -DskipTests`

The build should be sucessfull. The final build output is an output WAR file stored in: \geonetwork\web\target.

## geonetwork h2 Database:
The default loctaion of the geonetwork h2 databse is: `\Apache Software Foundation\Tomcat 9.0`
The following files are created:

`gn.h2.db`
`gn.lock.db`
`gn.trace.db`
`gn.trace.db.old`

To deploy a fully clean version, the above files should be deleted and a new data harvest performed.


## Deployment
- Take WAR file made from the steps described here.
- Deploy in **WebApps** folder of an installed Tomcat instance - Stop and Start Tomcat. (Memory of 2GB needed - Java 8 and Tomcat 8 recommended by devs (9 works too though).
- Log-in to the GeoNetwork instance (initially default as `Admin/Admin`): `/geonetwork/srv/eng/catalog.signin`
- Under Admin Console:
	- Change the password: `/geonetwork/srv/eng/admin.console#/organization`
	- Change the catalogue name, organisation, host (e.g. [data.marine.ie]), preferred protocol and save changes: `/geonetwork/srv/eng/admin.console#/settings`
	- Run harvest: Create directory harvest (name it), run from the folder where the XML data has been saved to, 
		- Group: `Sample Group`
		- User: `Admin`
		- Directory: `Local file location`
		- Uncheck `keep catalog record even if deleted at source`
		- Type of Record: `Metadata`
		- Ensure all is clicked
- Go to Tools and click `rebuild index`.

- **Alternatively**, use the *dockerfile* included in this repo.

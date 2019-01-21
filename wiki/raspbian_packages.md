# Raspbian Packages commands help.

### Install and uninstall package
Raspbian like Debian uses a package management tool to install software, every application, 
every component – everything – is built into a package,
and then that package is installed onto your system (either by the Installer, or by you).

This package management tool is the Apt, the documentation can be found [here](https://wiki.debian.org/Apt).
#### Update package database
Before install a package you should update the apt database. Use ```sudo apt-get update```.

#### To Install a package
Use ```sudo apt-get install <package_name>```

#### To upgrade installed packages
From time to time is useful to run the following command ```sudo apt-get upgrade``` to upgrade the installed packages. 
If we want to only update a specific then use this: ```sudo apt-get upgrade <package_name>```

#### To Uninstall a package
```sudo apt-get remove <package_name>``` - This will uninstall a package but will leave the configuration files.

```sudo apt-get purge <package_name>``` - This will uninstall a package and will also remove the configuration files.

 To clear out the local repository of retrieved package files do: ```sudo apt-get clean```. 
 To clear only package files that can no longer be downloaded, and are largely useless do: ```sudo apt-get autoclean```.
 To remove packages that were automatically installed to satisfy dependencies for some package and that are no more needed do: ```sudo apt-get autoremove```.
 
#### Search for a package
Search of a package: ```dpkg -l <package_name>```.
Search of a package and show installed location: ```dpkg -S <package_name>```.

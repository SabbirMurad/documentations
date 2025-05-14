## Installing Build Essentials / Development Tools

The build-essentials package is a reference for all the packages needed to compile a Debian package. It generally includes the **GCC/g++** compilers and libraries and some other utilities. So if you need to install C/C++ compiler, you just need to install build-essential package on your machine. And the build-essential is a meta package that installs many other packages, like G++, GCC, dpkg-dev, make, etc.

Execute the following command to install build-essential package:

```sh
apt install build-essential -y
```

After the installation process is completed, you can confirm your installation by checking for GCC version with the following command:

```sh
gcc --version
```
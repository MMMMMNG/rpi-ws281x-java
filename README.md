# rpi-ws281x-java
rpi281x wrapper for Java using SWIG

## TL;DR
1. Set RaspberryPi ssh username and password in gradle.properties
2. run `./gradlew installSwigOnPi`
3. run `./gradlew buildNativeOnPi`
4. run `./gradlew publishToMavenLocal`

### To build on a raspberry pi

Run `src/scripts/createNativeLib.sh` to generate the SWIG java code and generate the libws2811.so native library (For a tutorial on how to install SWIG, see "Install SWIG on RaspberryPi" below).

Run `./gradlew assemble` to compile the java code and create a jar containing the compile class files and the native .so file.

### To build from another machine

Set the appropriate username, host and password (or path to private key) in `gradle.properties`.

Then, run `./gradlew buildNativeOnPi`.  Note that your RaspberryPi needs to have ssh installed as gradle will use *its* ssh client.

Alternatively, you can also use the script `build-native-on-remote-pi.sh` with bash or a bash-compatible shell (like babun, cygwin, or git-bash on windows.).


This will copy the project to the pi, and run the script in the previous section, and copy the .so library back to the dev machine. After that, run `.\gradlew assemble` to compile the java code and create a jar containing the compile class files and the native .so file.  The easiest way to use the jar would be to publish it to a maven repository, or your local .m2 repository using `./gradlew publishToMavenLocal` and use maven coordinates in your maven or gradle project. 

#### Install SWIG on RaspberryPi

You can try `./gradlew installSwigOnPi` to automatically execute all the steps detailed below on your pi.

**NOTE:**
The default-jdk-headless which this task will try to install works only on pis with ARMv7+ architecture (check with the command `cat /proc/cpuinfo`).
The script will try to detect this and install the zulu jdk as an alternative, but this has only been tested on a raspberrypi Zero so far.
On my RasPi Zero this task took 30min the majority of which was spent building SWIG.
On the RasPi 3 B it took 8 mins.

**So proceed with caution!**

You can read more in the [pi4j docs](https://pi4j.com/documentation/java-installation/).

These commands worked for me:
1. Install [prereqs](https://github.com/swig/swig/wiki/Getting-Started#linux---ubuntu)
```shell
sudo apt-get install build-essential libpcre2-dev libpcre3-dev
```
2. Install default jdk
```shell
sudo apt install default-jdk-headless
```

3. Download swig tarball and unzip
```shell
wget http://prdownloads.sourceforge.net/swig/swig-4.1.1.tar.gz
tar -xzvf swig-4.1.1.tar.gz
```
4. enter directory and start building
```shell
cd swig-4.1.1
./configure && make
```
5. Execute an example as a check
```shell
cd Examples/java/class && make check
```
6. Install SWIG
```shell
cd ../../../
sudo make install
swig -version
```
```
SWIG Version 4.1.1

Compiled with g++ [x86_64-pc-linux-gnu]

Configured options: +pcre

Please see https://www.swig.org for reporting bugs and further information
```

For reference, the [SWIG DOCS](https://www.swig.org/Doc4.1/SWIGDocumentation.html#Preface_unix_installation).

Attribution
-----------
* SWIG generation and native lib compiling based on scripts at https://github.com/limpygnome/build-tv
* build-native-on-remote-pi script based on script at https://github.com/Cacodaimon/rpi_ws281x4j
* Loading JNI native .so file code based on code at https://github.com/mattjlewis/diozero/blob/master/diozero-ws281x-java

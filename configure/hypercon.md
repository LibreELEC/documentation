# HyperCon

HyperCon allows you to configure Hyperion on a remote LibreELEC device from a Windows, macOS or Linux desktop GUI application. Hypercon is a Java application so you must have a Java runtime environment (JRE) installed to run it. Java v1.7 or higher is required.

The latest version is here [http://releases.libreelec.tv/hypercon-LE.jar](http://releases.libreelec.tv/hypercon-LE.jar)

## Linux

On Ubuntu/Debian systems

```
sudo apt-get install openjdk-7-jre
wget http://releases.libreelec.tv/hypercon-LE.jar
```

On Fedora/CentOS systems

```
yum install java-1.7.0-openjdk*.
wget http://releases.libreelec.tv/hypercon-LE.jar
```

To run Hypercon:

```
java -jar ./hypercon-LE.jar
```

## Windows

Download and install the correct 32-bit or 64-bit Java Runtime for your Windows OS version from the official Java download site [https://www.java.com/](https://www.java.com). Open the .jar file as you would a regular .exe file and it should launch using Java (JRE). If not, right click and select “Open with” then “Java” listed in the list. 


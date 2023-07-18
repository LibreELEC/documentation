# Hypercon

## <mark style="color:red;">Not working anymore since LE10</mark>

<mark style="color:red;">Starting with LE10 Hyperion no longer works due to changes in the video pipeline. Consider hardware/external grabbers.</mark>

HyperCon is a graphical application which allows you to configure [Hyperion](https://github.com/LibreELEC/documentation/tree/bb394d22013fdc5f491418b7367a92b73ca7c45a/hyperion.md) (an ambient lighting system) running on a remote LibreELEC device, from a Windows, macOS or Linux desktop. Hypercon is a Java application, and so requires you to have a working Java runtime environment (JRE) installed to run it. Java v1.7 or higher is required.

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

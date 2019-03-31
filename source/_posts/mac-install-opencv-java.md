---
title: Mac安装OpenCV for Java
date: 2019.3.31
---

Install OpenCV 3.x under macOS
The quickest way to obtain OpenCV under macOS is to use Homebrew. After installing Homebrew, you have to check whether the XCode Command Line Tools are already installed on your system.

To do so, open the Terminal and execute: `xcode-select --install` If macOS asks for installing such tools, proceed with the download and installation. Otherwise, continue with the OpenCV installation.

As a prerequisite, check that Apache Ant is installed. Otherwise, install it with Homebrew: `brew install ant`. Ant should be available at `/usr/local/bin/ant`.

To install OpenCV (with Java support) through Homebrew, you need to edit the opencv formula in Homebrew, to add support for Java: `brew edit opencv` In the text editor that will open, change the line: `-DBUILD_opencv_java=OFF` in `-DBUILD_opencv_java=ON` then, after saving the file, you can effectively install OpenCV: `brew install --build-from-source opencv`

After the installation of OpenCV, the needed jar file and the dylib library will be located at `/usr/local/Cellar/opencv/3.x.x/share/OpenCV/java/`, e.g., `/usr/local/Cellar/opencv/3.3.1/share/OpenCV/java/`.

Please, notice that this method doesn’t work if you update OpenCV from a previous version: you need to uninstall OpenCV and install it again.

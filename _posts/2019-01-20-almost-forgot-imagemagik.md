---
layout: post
title:  "Almost forgot ImageMagik"
date:   2019-01-20 12:05:00 -0500
tags: ["Ruby on Rails", "Linux"]
---

Rails 5.2 now has ActiveStorage which is amazing. However if you want to use some of the built in image processing tools, you have to have ImageMagick installed on all computers running your site (development laptops, production servers, etc.).

Welp, I deployed this site and almost forgot to install ImageMagik. Since I'm here, I thought I'd quickly write down the steps as a future reference:

**System:** Ubuntu 18.04 <br />
**Version:** ImageMagick 7.x

## Install steps:
```
cd
sudo apt-get install build-essential checkinstall &amp;&amp; sudo apt-get build-dep imagemagick -y
sudo wget http://www.imagemagick.org/download/ImageMagick.tar.gz
sudo tar xzvf ImageMagick.tar.gz
cd ImageMagick-7*
sudo ./configure
sudo make clean
sudo make
sudo checkinstall
sudo ldconfig /usr/local/lib
```

## Check if everything worked:
```
magick -version
```

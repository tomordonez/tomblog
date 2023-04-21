---
layout: post
title: "Install OpenCV in Linux"
description: "Step by step installing OpenCV in Linux"
author: tom
image: assets/images/1.jpg
tags: [linux, opencv, computer vision]
---

Step by step installing OpenCV in Linux.

Installing OpenCV with conda or pip installs successfully. But importing the modules and using some methods run a lot of errors. Also tried installing on Linux Fedora like this. But it wouldn't find the module when importing it in Python. Also couldn't find the installation directory to add it to the path.

    $ sudo dnf install python-opencv --setopt=install_weak_deps=False

Install in Linux Fedora from source as shown in the official OpenCV docs [here](https://docs.opencv.org/4.5.3/dd/dd5/tutorial_py_setup_in_fedora.html).

    $ sudo dnf install cmake
    $ sudo dnf install python-devel numpy
    $ sudo dnf install gcc gcc-c++
    $ sudo dnf install gtk2-devel
    $ sudo dnf install libdc1394-devel
    $ sudo dnf install ffmpeg-devel

Note that the official docs has this:

    $ yum install gstreamer-plugins-base-devel

But this shows an error on Fedora 34:

    No match for argument: gstreamer-plugins-base-devel

The Fedora docs [here](https://docs.fedoraproject.org/en-US/quick-docs/assembly_installing-plugins-for-playing-movies-and-music/) show that the package is called `gstreamer1`

    $ sudo dnf install gstreamer1-plugins-base-devel

Install optional dependencies

    $ sudo dnf install libpng-devel
    $ sudo dnf install libjpeg-turbo-devel
    $ sudo dnf install jasper-devel
    $ sudo dnf install openexr-devel
    $ sudo dnf install libtiff-devel
    $ sudo dnf install libwebp-devel

Install TBB (Threading Building Blocks) for parallelization, Eigen for math operations, and Doxygen for documentation

    $ sudo dnf install tbb-devel
    $ sudo dnf install eigen3-devel
    $ sudo dnf install doxygen

Download OpenCV, configure and install

    $ git clone https://github.com/opencv/opencv.git
    $ cd opencv
    $ mkdir build
    $ cd build
    $ cmake -D WITH_TBB=ON -D WITH_EIGEN=ON ..
    $ cmake -D BUILD_DOCS=ON -D BUILD_TESTS=OFF -D BUILD_PERF_TESTS=OFF -D BUILD_EXAMPLES=OFF ..
    $ cmake -D WITH_OPENCL=OFF -D BUILD_opencv_gpu=OFF -D BUILD_opencv_gpuarithm=OFF -D BUILD_opencv_gpubgsegm=OFF -D BUILD_opencv_gpucodec=OFF -D BUILD_opencv_gpufeatures2d=OFF -D BUILD_opencv_gpufilters=OFF -D BUILD_opencv_gpuimgproc=OFF -D BUILD_opencv_gpulegacy=OFF -D BUILD_opencv_gpuoptflow=OFF -D BUILD_opencv_gpustereo=OFF -D BUILD_opencv_gpuwarping=OFF ..
    $ cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local ..


Check the resulting configuration. Then install

    $ make
    $ su
    $ make install

Check if the files are installed in `/usr/local/`. In my case my directory is the following:

    /usr/local/lib/python3.9/site-packages/

In `~/.bashrc` add this directory to the Python path

    export PYTHONPATH=$PYTHONPATH:/usr/local/lib/python3.9/site-packages

Exit the terminal and open again.

Build the documentation with `make doxygen`.

    $ cd opencv/build/
    $ make doxygen

Open the documentation in the browser.

    $ xdg-open doc/doxygen/html/index.html

Go to the directory where you want to start the project.

	$ cd
	$ cd some_directory

Create a conda environment:

    $ conda update conda
    $ conda create --name cv
    $ conda activate cv
    (cv)$

Test the install. Open Python. Check the numpy version:

    >>> import numpy
    >>> numpy.__version__
    '1.20.1'

Check that OpenCV can be imported and version:

    >>> import cv2 as cv
    >>> cv.__version__
    '4.5.3-dev'

Test these methods to see if OpenCV is working correctly.

Copy an image to your current directory. Let's say the image is `awesome_image.png`

    (cv)$ python

Import libraries:

    >>> import numpy as np
    >>> import cv2 as cv

Read an image:

    >>> img = cv.imread("awesome_image.png")
    >>> cv.namedWindow("Image", cv.WINDOW_NORMAL)
    >>> cv.imshow("Image", img)
    >>> cv.waitKey(0)

Then the image will load. Press `ESC` to get back control of the Terminal.

    >>> cv.imwrite("output.jpg", img)
    True

It creates a copy of the file saved as `output.jpg`. The conversion changes the sizes.

    $ ls -lh
    25K awesome_image.png
    65K output.jpg
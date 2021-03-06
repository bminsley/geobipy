Welcome to GeoBIPy
~~~~~~~~~~~~~~~~~~~
Geophysical Bayesian Inference in Python

This package uses a Bayesian formulation and Markov chain Monte Carlo sampling methods to derive posterior distributions of subsurface and measured data properties. The current implementation is applied to time and frequency domain electro-magnetic data. Application outside of these data types is well within scope.

Currently there are two types of data that we have implemented; frequency domain electromagnetic data, and time domain electromagnetic data. The package comes with a frequency domain forward modeller, but it does not come with a time domain forward modeller.  See the section `Installing the time domain forward modeller`_ for more information.

.. contents:: Table of Contents

Getting started
=================
First things first, install a Python 3.5+ distribution.  This is the minimum version that we have tested with.

This package has a few requirements depending on what you wish to do with it.

If you require a serial version of the code, see `Installing a serial version of GeoBIPy`_.

If you require an parallel implementation, you will need to install an MPI library, and Python's mpi4py module. See `Installing MPI and mpi4py`_.

If you require parallel file reading and writing, you will also need to install an MPI enabled HDF5 library, as well as Python's h5py wrapper to that library. It is important to read the notes below on installing h5py on top of a parallel HDF library.  The traditional "pip install h5py" will not work correctly. See `Installing parallel HDF5 and h5py`_ to do this correctly.

If you need to install the parallel IO version of the code, we would recommend that you start with a clean install of Python. This makes it easier to determine whether you have installed and linked the correct version of the parallel HDF5 library.

Installing GeoBIPy
==================

There are two versions when installing GeoBIPy, a serial version, and a parallel version. Since GeoBIPy uses a Fortran backend for forward modelling frequency domain data, you will need to have a Fortran compiler installed. Make sure that the compiler can handle derived data types since I make use of object oriented programming in Fortran.

Installing a serial version of GeoBIPy
::::::::::::::::::::::::::::::::::::::
This is the easiest installation and provides access to a serial implementation of the code.

Simply clone the git repository, navigate to the package folder that contains the setup.py file, and type "pip install -e ."

You should then be able to import modules from geobipy.  For this type of installation mpi will not need to be installed, and the serial version of h5py will suffice i.e. the standard "pip install h5py" is fine.  h5py will automatically be installed during the install of GeoBIPy since it is a dependency.

**Side note:**  Let's say you ran a production run on a parallel machine with MPI and parallel HDF capabilities. You generated all the results, copied them back to your local machine, and wish to make plots and images.  You will only need to install the serial version of the code on your local machine to do this.

Installing a parallel version of GeoBIPy
::::::::::::::::::::::::::::::::::::::::
Installing the parallel version of the code is a little trickier due to the dependencies necessary between the OpenMPI and/or HDF libraries, and how Python's mpi4py and h5py wrap around those.


Installing MPI and mpi4py
-------------------------
To run this code in parallel you will need both an MPI library and the python wrapper, mpi4py.  You must install MPI first before mpi4py.

MPI
+++

If you are installing GeoBIPy on a parallel machine, I would think that you have access to prebuilt MPI libraries.  If you are on a local laptop, you will need to install one. This package has been tested with OpenMPI version 1.10.2. Be careful if you want to use a newer version as mpi4py may not communicate with it correctly (at the time of this writing, OpenMPI v2 was having issues).


mpi4py
++++++

At this point, if you have an mpi4py module already installed, please remove it (you can check with "pip list"). If you started with a clean installation you should not have to worry about this. To test whether a new install of mpi4py will see the mpi library you have, just type "which mpicc".  The path that you see should point to the implementation that you want mpi4py to link to.  Make sure you are about to install mpi4py to the correct python installation.  If you type 'which python' it should return the path to the correct python distribution.  If you are using environments, make sure you have activated the correct one.

Next, use "pip install mpi4py --no-cache-dir".  This last option is very important, without it, pip might install its own MPI library called MPICH2. I would try to avoid this because if you need to install the HDF5 library you will need know which directories to link to (see `Installing parallel HDF5 and h5py`_).

At the end of the day,  h5py needs to communicate with both the correct HDF5 library and mpi4py, and both of those need to communicate with the same MPI library.

Installing parallel HDF5 and h5py
---------------------------------
If a parallel HDF5 library is not available, you will need to install one. First make sure you follow `Installing MPI and mpi4py`_ so that an MPI library is available to you. You must install a HDF5 library first before h5py.

HDF5
++++
When you install HDF5, make sure that the correct MPI library can be seen by typing "which mpicc".  When you configure the HDF5 library, be sure to use the --enable-parallel option.

h5py
++++
Once the HDF5 library is installed you will need to clone the `h5py repository`_

.. _`h5py repository`: https://github.com/h5py/h5py

Make sure you are about to install h5py to the correct python installation.  If you type 'which python' it should return the path to the correct python installation.

Next, copy the following code into a file called install.sh in the h5py folder and run it.  You will need to edit 3 entries.

- In H5PY_PATH change the path to the location where you want h5py installed.
- In HDF5_PATH change the path to the location of the installed parallel HDF5 library (i.e. the directory above /lib/)
- Check that 'which mpicc' returns the correct version.

.. code:: bash

    #!/bin/bash
    export HDF5_PATH=<Your path to HDF5>
    python setup.py clean --all
    python setup.py configure -r --hdf5-version=<Your version of HDF5> --mpi --hdf5=$HDF5_PATH
    export gcc=gcc
    CC=mpicc HDF5_DIR=$HDF5_PATH python setup.py build
    python setup.py install


Using GeoBIPy on Yeti
:::::::::::::::::::::::::::
There is no need to install GeoBIPy on Yeti.  Simply type "module load python/geobipy" for the serial version of the code, mainly used for plotting results, or "module load python/pGeobipy" for a parallel enabled version.


Installing the time domain forward modeller
:::::::::::::::::::::::::::::::::::::::::::
Ross Brodie at Geoscience Australia has written a great forward modeller, gatdaem1D,  in C++ with a python interface.  You can obtain that code here at the `GA repository`_

.. _`GA repository`: https://github.com/GeoscienceAustralia/ga-aem

However, for use with GeoBIPy, use `this fork of gataem1D`_ if there are open pull requests at the original repository.

.. _`this fork of gataem1D`: https://github.com/leonfoks/ga-aem

Go ahead and "git clone" that repository.

These instructions only describe how to install Ross' forward modeller, but it is part of a larger code base for inversion. If you wish to install his entire package, please follow his instructions.

Prerequisites
-------------

To compile his forward modeller, you will need a c++ compiler, and `FFTW`_

.. _`FFTW`: http://www.fftw.org/

On a Mac, installing these two items is easy if you use a package manager such as `homebrew`_

.. _`homebrew`: https://brew.sh/

If you use brew, simply do the following

.. code:: bash

   brew install gcc
   brew install fftw

If you do not have brew, or use a package manager, you can install fftw from source instead.

Download fftw-3.3.7.tar.gz from the `FFTW downloads`_ .

.. _`FFTW downloads`: http://www.fftw.org/download.html

Untar the folder and install fftw using the following.

.. code:: bash

  tar -zxvf fftw-3.3.7.tar.gz
  cd fftw-3.3.7
  mkdir build
  cd build
  ../configure --prefix=path-to-install-to/fftw-3.3.7 --enable-threads
  make
  make install

where, path-to-install-to is the location where you want fftw to be installed.


Compile the gatdaem1d shared library
------------------------------------
Next, within the gatdaem1d folder, navigate to the makefiles folder and modify the top part of the file "gatdaem1d_python.make" to the following

.. code:: bash

  SHELL = /bin/sh
  .SUFFIXES:
  .SUFFIXES: .cpp .o
  cxx = g++
  cxxflags = -std=c++11 -O3 -Wall -fPIC
  FFTW_DIR = path-to-fftw

  ldflags    += -shared
  bindir     = ../python/gatdaem1d

  srcdir     = ../src
  objdir     = ./obj
  includes   = -I$(srcdir) -I$(FFTW_DIR)/include
  libs       = -L$(FFTW_DIR)/lib -lfftw3
  library    = $(bindir)/gatdaem1d.so

You can find out where brew installed fftw by typing

.. code:: bash

  brew info fftw

Which may return something like "/usr/local/Cellar/fftw/3.3.5"

In this case, path-to-fftw is "/usr/local/Cellar/fftw/3.3.5"

If you installed fftw from source, then path-to-fftw is that install path.

Next, type the following to compile the gatdaem1d c++ code.

.. code:: bash

  make -f gatdaem1d_python.make

Installing the Python Bindings
------------------------------

Finally, to install the python wrapper to gatdaem1d, navigate to the python folder of the gatdaem1d repository.
Type,

.. code:: bash

  pip install .

You should now have access to the time domain forward modeller within geobipy.

Documentation
=============

Publication
:::::::::::
The code and its processes have been documented in multiple ways.  First we have the publication associated with this software release, the citation is below, and presents the application of this package to frequency and time domain electro-magnetic inversion.

Source code HTML pages
::::::::::::::::::::::
For developers and users of the code, the code itself has been thouroughly documented. The `source code docs can be found here`_

.. _`source code docs can be found here`: https://usgs.github.io/geobipy/

However you can generate the docs locally as well. To do this, you will first need to install sphinx via "pip install sphinx".

Next, head to the documentation folder in this repository and type "make html".  Sphinx generates linux based and windows based make files so this should be a cross-platform procedure.

The html pages will be generated under "build/html", so simply open the "index.html" file to view and navigate the code.

Jupyter notebooks to illustrate the classes
:::::::::::::::::::::::::::::::::::::::::::
For more practical, hands-on documentation, we have also provided jupyter notebooks under the documentation/notebooks folder.  These notebooks illustrate how to use each class in the package.

You will need to install jupyter via "pip install jupyter".

You can then edit and run the notebooks by navigating to the notebooks folder, and typing "jupyter notebook". This will open up a new browser window, and you can play in there.

Running GeoBIPy
===============
There are two methods of running GeoBIPy from the command line once it is installed.
For the serial version the following can be used

.. code:: bash

  geobipySerial <userParameterFile> <Output Folder>
  
For a parallel installed version use the following, (replace the MPI redirect with whatever is suitable for your machine)

.. code:: bash

    mpirun geobipyParallel <userParameterFile> <Output Folder>
  
In both cases, <Output Folder> specifies where the HDF5 files will be written, while the <userParameterFile> is a python script that contains the customizable parameters for GeoBIPy.
Below is an example scipt that you can use for reference.

.. highlight:: python
.. code-block:: python

    from geobipy.src.inversion._userParameters import _userParameters
    
    # General information about specifying parameters.
    # The following list of parameters can be given either a single value or a list of values
    # of length equal to the number of systems in the data. If one value is specified and there
    # are multiple systems, that value is used for all of them.
    # self.initialRelativeError
    # self.minimumRelativeError
    # self.maximumRelativeError
    # self.initialAdditiveError
    # self.minimumAdditiveError
    # self.maximumAdditiveError
    # self.relativeErrorProposalVariance
    # self.additiveErrorProposalVariance
    
    # -------------------------------------------------------
    # Define whether this parameter file uses time domain or frequency domain data!
    timeDomain = False
    # -------------------------------------------------------
    
    # -------------------------------------------------------
    # General file structure information.
    # -------------------------------------------------------
    # Specify the folder to the data
    dataDirectory = "..//Data"
    # Data File Name. If there are multiple, encompass them with [ ].
    dataFilename = dataDirectory + "//DataFile.txt"
    # dataFilename = [dataDirectory + "//DataFile1.txt", dataDirectory + "//DataFile2.txt"]
    # System File Name. If there are multiple, encompass them with [ ].
    systemFilename = dataDirectory + "//SystemFile.stm"
    # systemFilename = [dataDirectory + "//SystemFile1.stm", dataDirectory + "//SystemFile2.stm"]


    class userParameters(_userParameters):
        """ User Interface Parameters for GeoBIPy """
        def __init__(self, DataPoint):
            """ File for the user to specify inpust to GeoBIPy. """

            ## Maximum number of Markov Chains per data point.
            self.nMarkovChains = 100000
        
            # -------------------------------------------------------
            # General GeoBIPy options.
            # -------------------------------------------------------
            # Interactively plot a single data point as it progresses
            self.plot = True
            # How often to update the plot. (lower is generally slower)
            self.plotEvery = 5000
            # Save a PNG of the final results for each data point.
            self.savePNG = False
            # Save the results of the McMC inversion to HDF5 files. (Generally always True)
            self.save = True
            # Set the display limits [min, max] for the parameter posterior (hitmap)
            self.parameterDisplayLimits = [0.001, 100000]
            
            # -------------------------------------------------------
            # Turning on or off different solvable parameters.
            # -------------------------------------------------------
            # Parameter Priors
            # solveParameter will prevent parameters from exploding very large or very small numbers.
            # solveGradient prevents large changes in parameters value from occurring.
            # If both of these are active, the recovered earth models generally contain
            # less layers due to an implicit constraint.
            # If you feel that your recovered models are too conservative, try turning one of these off.
            # It is highly recommended to have at least one of these options turned on!
            # Use a prior on the parameter magnitude.
            self.solveParameter = False
            # Use the Prior on the difference in log parameter diff(log(X))
            self.solveGradient = True
        
            # Use the prior on the relative data errors
            self.solveRelativeError = True
            # Use the prior on the additive data errors
            self.solveAdditiveError = True
            # Use the prior on the data elevation
            self.solveElevation = True
            # Use the prior on the calibration parameters for the data
            self.solveCalibration = False
        
            # -------------------------------------------------------
            # Prior Details
            # -------------------------------------------------------
        
            # Earth model prior details
            # -------------------------
            # Maximum number of layers in the 1D model
            self.maximumNumberofLayers = 30
            # Minimum layer depth in metres
            self.minimumDepth = 1.0
            # Maximum layer depth in metres
            self.maximumDepth = 150.0
            # Minimum layer thickness. 
            # If minimumThickness = None, it will be autocalculated.
            self.minimumThickness = None
        
            # Limit the parameter? Takes the limits as three standard deviations away from the mean. (Computed during initialization)
            self.LimitPar = True
        
            # Data prior details
            # ------------------
            # The data priors are imposed on three different aspects of the data.  
            # The relative and additive error and the elevation of the data point.
            # Data uncertainty priors are used if solveRelativeError or solveAdditiveError are True.
            # If the data file contains columns of the estimated standard deviations, they are used as the initial values 
            # when starting an McMC inversion. If the file does not contain these estimates, then the initial
            # values are used below as sqrt((relative * data)^2 + (additive)^2).
        
            # Assign an initial percentage relative Error
            # If the file contains no standard deviations, this will be used 
            # to assign the initial data uncertainties.
            self.initialRelativeError = 0.05
            ## Relative Error Prior Details
            # Minimum Relative Error
            self.minimumRelativeError = 0.001
            # Maximum Relative Error
            self.maximumRelativeError = 0.5
            
            # Assign an initial additivr error level.
            # If the file contains no standard deviations, this will be used 
            # to assign the initial data uncertainties.
            self.initialAdditiveError = 5.0
            # Additive Error Prior Details
            # Minimum Additive Error
            self.minimumAdditiveError = 3.0
            # Maximum Relative Error
            self.maximumAdditiveError = 20.0
        
            # Elevation range allowed (m), either side of measured height
            self.maximumElevationChange = 1.0
        
            # -------------------------------------------------------
            # Proposal details
            # -------------------------------------------------------
        
            # Data proposal details
            # ---------------------
            # Logical to determine whether to use the Steepest Descent or Stochastic Newton step direction
            # The Stochastic Newton approach utilizes information contained in the data themselves
            # to guide the model proposal step. This makes the McMC chain more efficient at choosing the next
            # model. If this is turned on (and generally it should be) you will notice
            # less variance in the parameter posterior once the inversion finishes.
            self.stochasticNewton = True
            # The relative, additive, and elevation proposal variances are assigned to 
            # normal distributions with a mean equal to its value in the current model (of the Markov chain)
            # These variances are used when we randomly choose a new value for that given variable.
            # Proposal variance for the relative error
            self.relativeErrorProposalVariance = 2.5e-7
            # Proposal variance for the additive error
            self.additiveErrorProposalVariance = 1.0e-4
            # Proposal variance of the elevation
            self.elevationProposalVariance = 0.01
        
            # Earth model proposal details
            # ----------------------------
            # Evolution Probabilities for earth model manipulation during the Markov chain.
            # These four values are internally scaled such that their sum is 1.0.
            # Probability that a layer is inserted into the model.
            self.pBirth = 1.0/6.0
            # Probablitiy that a layer is removed from the model.
            self.pDeath = 1.0/6.0
            # Probability that an interface in the model is perturbed.
            self.pPerturb = 1.0/6.0
            # Probability of no change occuring to the layers of the model.
            self.pNochange = 0.5
        
            # -------------------------------------------------------
            # Typically Defaulted parameters
            # -------------------------------------------------------
            # Standard Deviation of log(rho) = log(1 + factor)
            # Default is 10.0
            self.factor = None
            # Standard Deviation for the difference in layer resistivity
            # Default is 1.5
            self.gradientStd = None
            # Initial scaling factor for proposal covariance
            self.covScaling = None
            # Scaling factor for data misfit
            self.multiplier = None
            # Clipping Ratio for interface contrasts
            self.clipRatio = None
        
            # Display the resistivity?
            self.reciprocateParameters = True
            
        
            # Don't change these.
            self.dataDirectory = dataDirectory
            self.dataFilename = dataFilename
            self.systemFilename = systemFilename
        
            self.verbose = False
        
            _userParameters.__init__(self, DataPoint)

    # Don't change this.
    if (timeDomain):
    dataInit = 'TdemData()'
    else:
    dataInit = 'FdemData()'

=================
Quick Start Guide
=================
This guide provides information on how to get Motr ready and test it. After following this guide, if you would like to actually run a motr cluster, please follow the `cortx-hare quick start guide <https://github.com/Seagate/cortx-hare/blob/main/README.md>`_.

*************
Prerequisites
*************
The prerequisite that is necessary to install the Motr component is mentioned below.

- CentOS 7 on x86_64 or ARM64 (AArch64) platform.

- **Ansible** is needed::

    sudo yum install epel-release # Install EPEL yum repo
    sudo yum install ansible

Get the Sources
===============
Clone Motr::

    git clone --recursive https://github.com/Seagate/cortx-motr.git

Build
=====

1. Build and install the necessary dependencies::

    cd cortx-motr
    sudo scripts/install-build-deps

2. If using lnet as the transport, check the Lustre network interface configuration::

    sudo vi /etc/modprobe.d/lnet.conf

   Use ``ip a`` command to get a list of network interfaces.
   Then modify ``lnet.conf`` to use one of the listed network interfaces.
   After this run::

    sudo modprobe lnet
    sudo lctl list_nids

   Make sure that libfabric package is not installed.
   Please refer the following document for un-installation of libfabric package.
   https://seagate-systems.atlassian.net/wiki/spaces/PUB/pages/711230113/Libfabric+setup+and+using+libfabric+with+motr#Uninstalling-libfabric-package ::

    fi_info --version
    bash: fi_info: command not found

3. If using libfabric as the transport, check the Libfabric network interface configuration::

    sudo vi /etc/libfab.conf

   Use ``ip a`` command to get a list of network interfaces.
   Then modify ``libfab.conf`` to use one of the listed network interfaces.
   Verify the libfab.conf file contents::

    cat /etc/libfab.conf
    networks=tcp(eth1)

   Please refer the below document for installation.
   https://seagate-systems.atlassian.net/wiki/spaces/PUB/pages/711230113/Libfabric+setup+and+using+libfabric+with+motr

   Verify that libfabric package is installed::

    fi_info --version
    fi_info: 1.11.2
    libfabric: 1.11.2
    libfabric api: 1.11

4. To build Motr, run::

    scripts/m0 make

   Note: use ``scripts/m0 rebuild`` command to re-build Motr.
 
RPMs Generation
===============

To build RPMs, run::

    make rpms

The generated RPMs will be placed at ``$HOME/rpmbuild/RPMS/$(arch)/`` directory.

Running Tests
=============

Unit Test
---------
- To run unit tests, use this command::

    sudo scripts/m0 run-ut

  Note: running Time (approximate) - 20 to 30 minutes

- To list all available unit tests::

    sudo scripts/m0 run-ut -l

- To run some specific unit test(s)::

    sudo scripts/m0 run-ut -t libm0-ut,be-ut

Kernel Space Unit Test
----------------------
- To run kernel space unit tests, use this command::

    sudo scripts/m0 run-kut

System Tests
------------
- To list all available system tests, run the following command::

    sudo scripts/m0 run-st -l

- To run Motr sanity test, use the following command::

    sudo scripts/m0 run-st 52motr-singlenode-sanity

- To run all system tests::

    sudo scripts/m0 run-st

  Note: it might take several hours to finish.
  
Unit Benchmark
--------------
- To run unit benchmarks, use the following command::

    sudo scripts/m0 run-ub

  Running Time (approximate) - 60 to 70 minutes

- To list all available unit benchmarks::

    sudo scripts/m0 run-ub -l

- To run some specific unit benchmark(s), e.g. "ad-ub"::

    sudo scripts/m0 run-ub -t ad-ub

Troubleshooting
===============
- If pip fails to install a package while installing build dependencies,
  try installing packages using pip installer.
  run the following commands if package is ipaddress::

    sudo pip install ipaddress
    sudo scripts/install-build-deps

- If an installation failure occurs due to the dependency of ``pip3`` ,
  run the following commands::

    sudo yum install -y python36-setuptools
    sudo easy_install-3.6 pip

- If an installation failure occurs due to ``ply`` dependency,
  run the following command::

    pip3 install ply

- If ``lctl list_nids`` does not render an output, do the following:

  1. Create the ``lnet.conf`` file, if it does not exist. And make sure
     the interface name is specified correctly there::

       $ cat /etc/modprobe.d/lnet.conf
       options lnet networks=tcp(eth1) config_on_load=1

     Check the network interfaces in your system with ``ip a`` command.

  2. Restart the ``lnet`` service, and check LNet NIDs::

       sudo systemctl restart lnet
       sudo lctl list_nids

- For other errors, please check our `FAQs <https://github.com/Seagate/cortx/blob/master/doc/Build-Installation-FAQ.md>`_.

- After following this guide, if you would like to actually run a motr cluster, please follow the `cortx-hare quick start guide <https://github.com/Seagate/cortx-hare/blob/main/README.md>`_.

Build the documentation
=======================

To create Motr documentation files, make sure you first install ``latex`` and ``ghostscript``::

    sudo yum install doxygen
    sudo yum install texlive-pdftex texlive-latex-bin texlive-texconfig* texlive-latex* texlive-metafont* texlive-cmap* texlive-ec texlive-fncychap* texlive-pdftex-def texlive-fancyhdr* texlive-titlesec* texlive-multirow texlive-framed* texlive-wrapfig* texlive-parskip* texlive-caption texlive-ifluatex* texlive-collection-fontsrecommended texlive-collection-latexrecommended
    sudo yum install ghostscript


Then in Motr folder run::

    make doc

The files will be generated at doc/html/ folder.


Tested by:

- December 01, 2021: Naga Kishore Kommuri (nagakishore.kommuri@seagate.com) CentOS Linux release 7.9.2009 verified with git (#43a75c54d15b23532d883b6065a201b5d6a7f385)

- September 20, 2021: Yixuan Li (yixuan.li@seagate.com) in Red Hat Enterprise Linux Server release 7.7 (Maipo) (#5aac28633a149d2c7e6f8d4c502d80dabf7ebb7e)

- Sep 20, 2021: Liana Valdes Rodriguez (liana.valdes@seagate.com / lvald108@fiu.edu) tested in CentOS 7.8.2003 x86_64 using CORTX-2.0.0-77 tag on main branch  

- September 15, 2021: Jugal Patil (jugal.patil@seagate.com) tested using CentOS Linux release 7.9.2009 and 7.8.2003 verified with git tag CORTX-2.0.0-77 (#7d4d09cc9fd32ec7690c94298136b372069f3ce3) on main branch

- Sep 6, 2021: Rose Wambui (rose.wambui@seagate.com) in CentOS 7.8.2003 on a Mac running VirtualBox 6.1.

- June 21, 2021: Daniar Kurniawan (daniar@uchicago.edu) in CentOS 7.9.2003 on a Chameleon node (type=compute_skylake).

- May 23, 2021: Bo Wei (bo.b.wei@seagate.com) in CentOS 7.9.2009 on a Windows laptop running VirtualBox 6.1.

- May 2, 2021: Christina Ku (christina.ku@seagate.com) in Red Hat Enterprise Linux Server release 7.7 (Maipo)

- Apr 16, 2021: Jalen Kan (jalen.j.kan@seagate.com) in CentOS 7.9.2009 on a windows laptop running VMware Workstation Pro 16

- Mar 12, 2021: Yanqing Fu (yanqing.f.fu@seagate.com) in Red Hat Enterprise Linux Server release 7.7 (Maipo)

- Jan 27, 2021: Patrick Hession (patrick.hession@seagate.com) in CentOS 7.8.2003 on a Windows laptop running VMWare Workstation Pro 16

- Jan 20, 2021: Mayur Gupta (mayur.gupta@seagate.com) on a Windows laptop running VMware Workstation Pro 16.

- Dec 1, 2020: Huang Hua (hua.huang@seagate.com) in CentOS 7.7.1908

- Nov 25, 2020: Philippe Daniel (CEA) 

- Oct 11, 2020: Saumya Sunder (saumya.sunder@seagate.com) on a Windows laptop running VMWare Workstation Pro 16

- Oct 02, 2020: Venkataraman Padmanabhan (venkataraman.padmanabhan@seagate.com) on a Windows laptop running VMWare Workstation Pro 16

- Aug 09, 2020: Venkataraman Padmanabhan (venkataraman.padmanabhan@seagate.com) on a Windows laptop running VMWare Workstation Pro 16

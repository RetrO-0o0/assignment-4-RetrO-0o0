<<<<<<< HEAD
# assignment-4-RetrO-0o0
=======
# Overview

This repository contains assignment starter code for buildroot based assignments for the course Advanced Embedded Software Design, ECEN 5713

It also contains instructions related to modifying your buildroot project to use with supported hardware platforms.  See [this wiki page](https://github.com/cu-ecen-5013/buildroot-assignments-base/wiki/Supported-Hardware) for details.
>>>>>>> buildroot-assignments-base/master

As I said before I can't use self-runners to validate my assignments. It is because of increasing internet restrictions on Iran.
And Github runners don't work neither because of the high workload of this assignment.
But I've created a series of logs showing that all steps of assignment 4 are correctly implemented.
I even added these logs into the README.txt file to be easier to access.

Contents:
	1) created needed files for br2_external_tree and their contents
	2) created clean.sh in the root directory of the repository
	3) running QEMU and loging in to the machine
	4) presense of the writer, finder.sh, finder-test.sh and writer.sh in /usr/bin of the virtual machine
	5) running finder-test.sh from /root directory and it's resutls and the logs in /var/log/messages
	6) connecting to the machine via ssh and its' logs in /var/log/messages


##########
### 1
##########
➜  Embedded_Linux_Assignment_4 git:(main) ls base_external/

total 20K
-rw-rw-r-- 1 retr0 retr0   76 Sep  9 19:25 Config.in
drwxrwxr-x 2 retr0 retr0 4.0K Sep 11 16:25 configs
-rw-rw-r-- 1 retr0 retr0   65 Sep  9 12:40 external.desc
-rw-rw-r-- 1 retr0 retr0   77 Sep 12 16:58 external.mk
drwxrwxr-x 3 retr0 retr0 4.0K Sep  7 16:59 package


➜  Embedded_Linux_Assignment_4 git:(main) cat base_external/Config.in base_external/external.desc base_external/external.mk

source "$BR2_EXTERNAL_project_base_PATH/package/aesd-assignments/Config.in"

name: project_base
desc: Base external tree for AESD assignments

include $(sort $(wildcard $(BR2_EXTERNAL_project_base_PATH)/package/*/*.mk))


➜  Embedded_Linux_Assignment_4 git:(main) cat base_external/package/aesd-assignments/aesd-assignments.mk base_external/package/aesd-assignments/Config.in

##############################################################
#
# AESD-ASSIGNMENTS
#
##############################################################

#TODO: Fill up the contents below in order to reference your assignment 3 git contents
AESD_ASSIGNMENTS_VERSION = 293d4284f7beb5134719f6456954ba114b686d8d
# Note: Be sure to reference the *ssh* repository URL here (not https) to work properly
# with ssh keys and the automated build/test system.
# Your site should start with git@github.com:
AESD_ASSIGNMENTS_SITE = git@github.com:RetrO-0o0/assignments-RetrO-0o0.git
AESD_ASSIGNMENTS_SITE_METHOD = git
AESD_ASSIGNMENTS_GIT_SUBMODULES = YES

define AESD_ASSIGNMENTS_BUILD_CMDS
	$(MAKE) $(TARGET_CONFIGURE_OPTS) -C $(@D)/finder-app all
endef

# TODO add your writer, finder and finder-test utilities/scripts to the installation steps below
define AESD_ASSIGNMENTS_INSTALL_TARGET_CMDS
	$(INSTALL) -d 0755 $(@D)/conf/ $(TARGET_DIR)/etc/finder-app/conf/

	$(INSTALL) -m 0755 $(@D)/conf/* $(TARGET_DIR)/etc/finder-app/conf/
	$(INSTALL) -m 0755 $(@D)/assignment-autotest/test/assignment4/* $(TARGET_DIR)/bin

	$(INSTALL) -m 0755 $(@D)/finder-app/finder-test.sh $(TARGET_DIR)/usr/bin
	$(INSTALL) -m 0755 $(@D)/finder-app/finder.sh $(TARGET_DIR)/usr/bin
	$(INSTALL) -m 0755 $(@D)/finder-app/writer.sh $(TARGET_DIR)/usr/bin
	$(INSTALL) -m 0755 $(@D)/finder-app/writer $(TARGET_DIR)/usr/bin
endef

$(eval $(generic-package))

config BR2_PACKAGE_AESD_ASSIGNMENTS
	bool "aesd-assignments"
	help
	  Includes the writer executable, finder.sh and tester.sh in the kernel image.
	  Used to understand buildroot external trees and package builds.

#########
### 2
#########
➜  Embedded_Linux_Assignment_4 git:(main) ✗ cat clean.sh 
#!/bin/bash

set -e

cd "$(dirname "$0")"

cd "./buildroot"

make distclean

exit 0

##########
### 3 
##########
Welcome to Buildroot
buildroot login: root
Password: 
# whoami
root
# pwd
/root

##########
### 4
##########
# ls /etc/finder-app/conf/
assignment.txt  username.txt
# cat /etc/finder-app/conf/assignment.txt /etc/finder-app/conf/username.txt 
assignment4
RetrO-0o0

# ls /usr/bin | grep finder
finder-test.sh
finder.sh

# ls /usr/bin | grep writer
writer
writer.sh

##########
### 5 
##########
# finder-test.sh 
Using default value AELD_IS_FUN for string to write
Using default value 10 for number of files to write
Writing 10 files containing string AELD_IS_FUN to /tmp/aeld-data
/tmp/aeld-data created
The number of files are 10 and the number of matching lines are 10
success


# tail -n 10 /var/log/messages 
Sep 13 10:13:14 buildroot user.debug writer[129]: Closing the file
Sep 13 10:13:14 buildroot user.debug writer[130]: Openning the file "/tmp/aeld-data/RetrO-0o08.txt"
Sep 13 10:13:14 buildroot user.debug writer[130]: Writing "AELD_IS_FUN" to "/tmp/aeld-data/RetrO-0o08.txt"
Sep 13 10:13:14 buildroot user.debug writer[130]: Closing the file
Sep 13 10:13:14 buildroot user.debug writer[131]: Openning the file "/tmp/aeld-data/RetrO-0o09.txt"
Sep 13 10:13:14 buildroot user.debug writer[131]: Writing "AELD_IS_FUN" to "/tmp/aeld-data/RetrO-0o09.txt"
Sep 13 10:13:14 buildroot user.debug writer[131]: Closing the file
Sep 13 10:13:14 buildroot user.debug writer[132]: Openning the file "/tmp/aeld-data/RetrO-0o010.txt"
Sep 13 10:13:14 buildroot user.debug writer[132]: Writing "AELD_IS_FUN" to "/tmp/aeld-data/RetrO-0o010.txt"
Sep 13 10:13:14 buildroot user.debug writer[132]: Closing the file

##########
### 6
##########
➜  Embedded_Linux_Assignment_4 git:(main) ✗ ssh -p 10022 root@localhost 
The authenticity of host '[localhost]:10022 ([127.0.0.1]:10022)' can't be established.
ED25519 key fingerprint is: SHA256:rDR7n4E6XH7fhdsOqK0Q8U+ad9jB7qDQPIGmN4k+8M8
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:8: [hashed name]
Are you sure you want to continue connecting (yes/no/[fingerprint])? y
Please type 'yes', 'no' or the fingerprint: yes
Warning: Permanently added '[localhost]:10022' (ED25519) to the list of known hosts.
root@localhost's password: 
# whoami
root
# pwd
/root

Sep 13 10:24:47 buildroot authpriv.notice dropbear[156]: Password auth succeeded for 'root' from 10.0.2.2:59008
Sep 13 10:26:33 buildroot authpriv.info dropbear[156]: Exit (root) from <10.0.2.2:59008>: Disconnect received

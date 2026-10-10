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
I even added these logs into the README.md file to be easier to access.

# Embedded Linux Assignment 4

This document describes the implementation and verification of Assignment 4, including:

1. Creation and configuration of the Buildroot external tree
2. Creation of the repository cleanup script
3. Running the Buildroot image with QEMU
4. Verifying the required utilities inside the virtual machine
5. Running `finder-test.sh` and checking the generated system logs
6. Connecting to the virtual machine through SSH and verifying the SSH logs

---

## 1. Buildroot External Tree

The required Buildroot external tree was created under the `base_external/` directory.

### Directory Structure
```text
base_external/
├── Config.in
├── configs/
├── external.desc
├── external.mk
└── package/
└── aesd-assignments/
├── Config.in
└── aesd-assignments.mk

### `base_external/Config.in`

make
source "$BR2_EXTERNAL_project_base_PATH/package/aesd-assignments/Config.in"

### `base_external/external.desc`

text
name: project_base
desc: Base external tree for AESD assignments

### `base_external/external.mk`

make
include $(sort $(wildcard $(BR2_EXTERNAL_project_base_PATH)/package/*/*.mk))

### `base_external/package/aesd-assignments/aesd-assignments.mk`

make
################################################################################
#
# AESD-ASSIGNMENTS
#
################################################################################

AESD_ASSIGNMENTS_VERSION = 293d4284f7beb5134719f6456954ba114b686d8d

# The SSH repository URL is used so that the repository can be accessed
# correctly by the automated build and test system.
AESD_ASSIGNMENTS_SITE = git@github.com:RetrO-0o0/assignments-RetrO-0o0.git
AESD_ASSIGNMENTS_SITE_METHOD = git
AESD_ASSIGNMENTS_GIT_SUBMODULES = YES

define AESD_ASSIGNMENTS_BUILD_CMDS
$(MAKE) $(TARGET_CONFIGURE_OPTS) -C $(@D)/finder-app all
endef

define AESD_ASSIGNMENTS_INSTALL_TARGET_CMDS
$(INSTALL) -d -m 0755 $(TARGET_DIR)/etc/finder-app/conf/

$(INSTALL) -m 0755 $(@D)/conf/* \
	$(TARGET_DIR)/etc/finder-app/conf/

$(INSTALL) -m 0755 $(@D)/assignment-autotest/test/assignment4/* \
	$(TARGET_DIR)/bin

$(INSTALL) -m 0755 $(@D)/finder-app/finder-test.sh \
	$(TARGET_DIR)/usr/bin

$(INSTALL) -m 0755 $(@D)/finder-app/finder.sh \
	$(TARGET_DIR)/usr/bin

$(INSTALL) -m 0755 $(@D)/finder-app/writer.sh \
	$(TARGET_DIR)/usr/bin

$(INSTALL) -m 0755 $(@D)/finder-app/writer \
	$(TARGET_DIR)/usr/bin
endef

$(eval $(generic-package))

The package uses the following Git repository and commit:

text
Repository:
git@github.com:RetrO-0o0/assignments-RetrO-0o0.git

Commit:
293d4284f7beb5134719f6456954ba114b686d8d

### `base_external/package/aesd-assignments/Config.in`

make
config BR2_PACKAGE_AESD_ASSIGNMENTS
bool "aesd-assignments"
help
Includes the writer executable, finder.sh, and finder-test.sh
in the target image. This package is used to demonstrate
Buildroot external trees and package builds.

---

## 2. Repository Cleanup Script

A `clean.sh` script was created in the root directory of the repository. This script removes the Buildroot build output by running `make distclean`.

### `clean.sh`

bash
#!/bin/bash

set -e

cd "$(dirname "$0")"

cd "./buildroot"

make distclean

exit 0

The script first changes the working directory to the repository location and then enters the `buildroot/` directory before executing the cleanup command.

---

## 3. Running QEMU and Logging In

The Buildroot image was successfully started using QEMU. The virtual machine displayed the Buildroot login prompt.

The system was accessed using the `root` user:

text
Welcome to Buildroot
buildroot login: root
Password:

The current user and working directory were verified inside the virtual machine:

sh
# whoami
root

# pwd
/root

Output:

text
root
/root

---

## 4. Verifying the Required Files Inside the Virtual Machine

The configuration files were installed under `/etc/finder-app/conf/`.

### Configuration Files

sh
# ls /etc/finder-app/conf/
assignment.txt
username.txt

The contents of the files were verified as follows:

sh
# cat /etc/finder-app/conf/assignment.txt
assignment4

# cat /etc/finder-app/conf/username.txt
RetrO-0o0

The required finder utilities were installed under `/usr/bin/`:

sh
# ls /usr/bin | grep finder
finder-test.sh
finder.sh

The writer utilities were also present:

sh
# ls /usr/bin | grep writer
writer
writer.sh

Therefore, the following required files were successfully included in the target image:

text
/usr/bin/finder-test.sh
/usr/bin/finder.sh
/usr/bin/writer
/usr/bin/writer.sh

---

## 5. Running `finder-test.sh`

The test script was executed from the `/root` directory:

sh
# pwd
/root

# finder-test.sh

The script used its default values and created ten files under `/tmp/aeld-data`:

text
Using default value AELD_IS_FUN for string to write
Using default value 10 for number of files to write
Writing 10 files containing string AELD_IS_FUN to /tmp/aeld-data
/tmp/aeld-data created
The number of files are 10 and the number of matching lines are 10
success

The test completed successfully because:

- Ten files were created.
- Each file contained the string `AELD_IS_FUN`.
- Ten matching lines were found.
- The final test result was `success`.

### Writer Logs

The `writer` application uses syslog to write messages to `/var/log/messages`. The following entries confirm that the files were opened, written, and closed successfully:

text
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

These log messages verify that the `writer` executable performed the expected file operations.

---

## 6. Connecting to the Virtual Machine Through SSH

The virtual machine was accessed through SSH using port `10022`:

sh
$ ssh -p 10022 root@localhost

During the first connection, SSH displayed the host authenticity confirmation:

text
The authenticity of host '[localhost]:10022 ([127.0.0.1]:10022)' can't be established.
ED25519 key fingerprint is:
SHA256:rDR7n4E6XH7fhdsOqK0Q8U+ad9jB7qDQPIGmN4k+8M8

Are you sure you want to continue connecting (yes/no/[fingerprint])?
yes

Warning: Permanently added '[localhost]:10022' (ED25519) to the list of known hosts.
root@localhost's password:

After logging in through SSH, the user and working directory were verified:

sh
# whoami
root

# pwd
/root

Output:

text
root
/root

### SSH Authentication Logs

The SSH connection was handled by Dropbear. The following entries were recorded in `/var/log/messages`:

text
Sep 13 10:24:47 buildroot authpriv.notice dropbear[156]: Password auth succeeded for 'root' from 10.0.2.2:59008
Sep 13 10:26:33 buildroot authpriv.info dropbear[156]: Exit (root) from <10.0.2.2:59008>: Disconnect received

These messages confirm that:

1. Password authentication for the `root` user succeeded.
2. The SSH client connected from `10.0.2.2`.
3. The SSH session was closed normally after the client disconnected.

---

## Verification Summary

| Item | Result |
|---|---|
| Buildroot external tree | Created successfully |
| `aesd-assignments` package | Configured and included |
| Git repository and commit | Configured correctly |
| `clean.sh` | Created in the repository root |
| QEMU virtual machine | Started successfully |
| Root login through the console | Successful |
| Configuration files | Present under `/etc/finder-app/conf/` |
| `finder.sh` | Present under `/usr/bin/` |
| `finder-test.sh` | Present under `/usr/bin/` |
| `writer` | Present under `/usr/bin/` |
| `writer.sh` | Present under `/usr/bin/` |
| `finder-test.sh` execution | Successful |
| Generated files | 10 |
| Matching lines | 10 |
| SSH connection | Successful |
| SSH authentication logs | Present in `/var/log/messages` |
| Writer logs | Present in `/var/log/messages` |

## Conclusion

The Assignment 4 Buildroot external tree was successfully created and integrated into the target image. The required applications and scripts were installed in the virtual machine, `finder-test.sh` completed successfully, and both the writer activity and SSH authentication events were correctly recorded in `/var/log/messages`.
`

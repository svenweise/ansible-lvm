ansible-lvm
=======================

A role for managing LVM

Special about this role is that the VolumeGroups and Devicepaths are autodiscovered.
So you should be fine if you only give the mountpoints and sizes at the host_vars/* files or playbook.
Only if you add disks you need to set the VolumeGroup to where the new disks should be addet.


Actions
-------

- Installs lvm2 (and cryptsetup if needet)
- Make a partiton on all disks who doesn't already have a partition
- Make a cryptsetup ontop of created partitions
- Adds partitions to VolumeGroup
- Unmounts and Removes unwanted Montpoints and their LogicalVolumes
- Creates/Resizes LogicalVolumes based on Mountpoints given as variables
- Creates/Resizes Filesystems based on Mountpoints given as variables

Requirements
------------

None

Role Variables
--------------

Below are all the variables used by the role, together an explanation, and their defaults.

# If set to false, or all the partitioningwork is already done (eg. run this role twice in a row),
# don't forget to fill addet_partitions manually so we have partitions we can add to the VG.
partitioning: true

# If cryptdisk is set to true don't forget to set cryptdisk_pw to your passphrase.
cryptdisk: false
cryptdisk_pw:

# If addisk is true we resize the LVM-Group with the disks/partitions we have at addet_partitions: []
addisk: true

# If resizelvm is true we resize the LVM-Volumes
resizelvm: true

# Remove unwanted mounts and LVM-Volumes. Set these at your host_vars/* files
# Attention: the basename of the mountpoint and the name of the logical volume has to be the SAME!
#remove_mounts: []

# Diskalignment
disk_align_offset: "2048s"

# Default FileSystem
fs: ext4

# Default LVM-VolumeGroup
lvm_group: vg01

# Set these to the LV-Group to where the disks should be addet.
# NEED to be set (at group_vars/host_vars/playbook)
#lvgrp:

# LVM Mounts. Set these at your host_vars/* files
# Attention: the basename of the mountpoint will be the name and tag of the logical volume!
# Must-parameter: mount and size
# Optional-parameter: vg, fs and opts (for mount) - Only use them if you need and fill them. Dont leave them empty (eg.: "vg: ''")!
# NEED to be set (at group_vars/host_vars/playbook)
#lv_mounts:
#  - {vg: '', mount: '', size: '', fs: '', opts: ''}

# Swapsize
#swapsize: '8192m' 

# Needet so these variables/lists aren't "undefined" and provoke the "undefined error" when they are automagically filled
# DO NOT FILL THEM! - Unless you know what you are doing. ;-)
# eg. fill addet_partitions if you are running with "partitioning: false" and have already partitions to add
addet_disks: []
addet_partitions: []
addet_crypted_partitions: []


Example Playbook
----------------

---
- hosts: all
  become: true
  roles:
    - ansible-lvm

Example variables file
----------------------

host_vars/hostname.yml
---
cryptdisk: true
cryptdisk_pw: SecretCryptDiskPassword
lv_mounts:
  - {mount: '/data', size: '1T'}
  - {mount: '/opt', size: '500G', opts: 'defaults,acl,nodev,nosuid'}
  - {mount: '/home', size: '100M', opts: 'defaults,acl,nodev,nosuid,noexec', vg: 'vg00', fs: 'xfs'}
swapsize: '8192m'

license
-------

BSD

Author Information
------------------

This role was created in 2016 by [Sven Weise](https://github.com/svenweise)

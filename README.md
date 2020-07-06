# Ansible-ubuntu-partitioning

An Ansible playbook to set up RAID, filesystems and encrypted partitions on a soon-to-be-installed live system.

# Motivation

Each time I have to set up a new system on a personal machine which I want to encrypt I have to fight against most of the Linux distribution installers not being able to allow a fully customized RAID + encrypted setup.

This forces you to log in on a live image, customize the partitions to suit your needs and then resume the installer.

I always forget the small details to set up LUKS devices, RAID arrays and things like that, so I've decided to create this playbook so that I never have to do that again.

# Features

The features of this playbook include:

- Define which disk devices will be configured.
- Create and configure partitions for those disks.
- Add the created partitions to any kind of software RAID array with `mdadm`.
- Encrypt any partition with LUKS with either passphrases or key files.
- Idempotency.

There's also an extra playbook (`delete.yml`) which can be used to delete any configuration made with the `create.yml` playbook, which can be handy in tests.

# How to use this repository

As stated before, the main goal of this playbook is to provision the partitions, arrays and encrypted volumes on a live Linux box.

With this stated, the main use case requires you to:

1. Boot on your machine with a live OS.
2. Select the "_Try system_" instead of "_Install system_".
3. On your live terminal, install `git` and `ansible`.
4. Clone this repository and `cd` into the resulting directory.
5. Edit the `vars/main.yml` according to your needs. Check the comments for clarification.
6. Create the specified keyfiles on your `vars/main.yml` file on the `keyfiles/` directory.
7. Run the playbook with `ansible-playbook -vv create.yml`.

At this point your filesystem will be partitioned, encrypted and the LUKS devices will be ready to be used on your installer so you should start it.

Be advised that, some installers (such as the one in Ubuntu) might not properly recognize arrays and or LUKS devices and will require you to run the installer with some extra flags to avoid installing grub (in the case of Ubuntu you'll need to pass the `--no-bootloader` flag to the Ubiquity installer).

After you install the system you'll still need to set up a few things:

- Copy your key files (if you've generated them) to their new destinations.
- Set up a `/etc/crypttab` file on your new root filesystem.
- Install GRUB and set up so that it can read encrypted partitions (`GRUB_ENABLE_CRYPTODISK=y`). Remember that GRUB can only read LUKS version 1 devices.

# What to do if something went wrong?

Run the `delete.yml` playbook to revert your changes, check the errors on the output (which will most likely point to some error on the `vars/main.yml` file) and run the `create.yml` playbook again.

If the error persists and you need support feel free to open an issue.

# ClickHouse RPM packages installation from packagecloud.io

------

## Table of Contents

  * [Introduction](#introduction)
  * [Manual](#manual)
    * [Install required packages](#install-required-packages)
    * [Create required files](#create-required-files)
      * [EL6 repo file](#el6-repo-file)
      * [EL7 repo file](#el7-repo-file)
    * [Update cache](#update-cache)
    * [Install packages](#install-packages)
  * [Script-based](#script-based)
    * [Install script](#install-script)
    * [Install packages](#install-packages)
  * [Conclusion](#conclusion)

------


## Introduction

All instructions in this manual were tested on Centos 6.9 and CentOS 7.3. 
In order to install ClickHouse RPM packages from [packagecloud.io](#https://packagecloud.io) repository we need to register it with our `yum`. 
So `yum` will know that additional packages can be installed from this repository. In general, repositories are listed in `/etc/yum.repos.d` folder, so we need to add packagecloud.io repository description in there.
This can be done either [manually](#manual) or via [script](#script-based) provided by packagecloud.io In any case, as a result, we'll have ClickHouse packages available for installation via `yum`

## Manual
Let's add packagecloud.io repo manually

### Install required packages
We'll need two packages installed beforehands:
  * pygpgme - helps handling gpg-signatures
  * yum-utils - contains tools for handling source RPMs

```bash
sudo yum install pygpgme yum-utils
```

### Create required files
Now let's create `yum`'s repository configuration file: `/etc/yum.repos.d/altinity_clickhouse.repo` Depending on what CentOS version you are running you may need files for EL 6 or 7 version

#### EL6 repo file

For EL6 copy+paste into `/etc/yum.repos.d/altinity_clickhouse.repo` 
```bash
[altinity_clickhouse]
name=altinity_clickhouse
baseurl=https://packagecloud.io/altinity/clickhouse/el/6/$basearch
repo_gpgcheck=1
gpgcheck=0
enabled=1
gpgkey=https://packagecloud.io/altinity/clickhouse/gpgkey
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300

[altinity_clickhouse-source]
name=altinity_clickhouse-source
baseurl=https://packagecloud.io/altinity/clickhouse/el/6/SRPMS
repo_gpgcheck=1
gpgcheck=0
enabled=1
gpgkey=https://packagecloud.io/altinity/clickhouse/gpgkey
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
```

#### EL7 repo file

For EL7 copy+paste into `/etc/yum.repos.d/altinity_clickhouse.repo` 
```bash
[altinity_clickhouse]
name=altinity_clickhouse
baseurl=https://packagecloud.io/altinity/clickhouse/el/7/$basearch
repo_gpgcheck=1
gpgcheck=0
enabled=1
gpgkey=https://packagecloud.io/altinity/clickhouse/gpgkey
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300

[altinity_clickhouse-source]
name=altinity_clickhouse-source
baseurl=https://packagecloud.io/altinity/clickhouse/el/7/SRPMS
repo_gpgcheck=1
gpgcheck=0
enabled=1
gpgkey=https://packagecloud.io/altinity/clickhouse/gpgkey
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
```

### Update cache

After repo files created, let's update `yum`'s cache with packges from newly added `altinity_clickhouse` repo
```bash
sudo yum -q makecache -y --disablerepo='*' --enablerepo='altinity_clickhouse'
```

### Install packages

## Script-based
However, packagecloud.io provides nice and user-friendly way to add repos with their script. All looks like some kind of magic.

### Install script
Ensure `curl` is installed on the system
```bash
sudo yum install curl
```

Let's run installation shell-script, provided by packagecloud.io
```bash
curl -s https://packagecloud.io/install/repositories/altinity/clickhouse/script.rpm.sh | sudo bash
```

### Install packages

## Conclusion
Now we have ClickHouse RPM packages available for easy installation.



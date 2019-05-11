# ClickHouse RPM packages installation from Altinity's [repo][pack_alt_repo] located on [packagecloud.io](https://packagecloud.io)

------

## Table of Contents

  * [What is this](#what-is-this)
  * [Introduction](#introduction)
  * [Script-based installation](#script-based-installation)
    * [Install dependencies](#install-dependencies)
    * [Install packages after script](#install-packages-after-script)
  * [Manual installation](#manual-installation)
    * [Install required packages](#install-required-packages)
    * [Create required files](#create-required-files)
      * [EL6 repo file](#el6-repo-file)
      * [EL7 repo file](#el7-repo-file)
    * [Update cache](#update-cache)
    * [Install packages manually](#install-packages-manually)
  * [Conclusion](#conclusion)

------

## What is this
This is a detailed explanation on how to install ready-to-use ClickHouse RPMs from Altinity's [repo][pack_alt_repo] located on [packagecloud.io][pack_alt_repo].
This is **not** an instructions on how to build your own hand-made RPMs.
However, if you need to build your own RPMs, there is a [detailed explanation][alt_rpm_builder_repo] on how to build ClickHouse RPMs from sources with the help of Altinity's [RPM builder][alt_rpm_builder_repo]

## Introduction

### Supported OSes
All instructions in this manual were tested on Centos 6.10, CentOS 7.5 and Amazon Linux 2.

**IMPORTANT for Amazon Linux users** 
Amazon Linux is being detected as CentOS 6, while RPMs built for CentOS 7 are the best choice. So we need to explicitly install CentOS 7 RPMs
More details further in the doc.

### Introduction

In order to install ClickHouse RPM packages from Altinity's [repository][pack_alt_repo], we need to register it (repo) with our `yum`, making `yum` aware of additional packages installable from [external source][pack_alt_repo].

In general, repositories are listed in `/etc/yum.repos.d` folder, so we need to add Altinity's [repository][pack_alt_repo] description in there.

This can be done either [manually](#manual-installation) or via [script](#script-based-installation), provided by [packagecloud.io][pack_alt_repo]. In any case, as a result, we'll have ClickHouse packages available for installation via `yum`.

**IMPORTANT for Amazon Linux users** 
Amazon Linux is being detected as CentOS 6 by the script, so we need to explicitly instruct it to use CentOS 7 repo.
  * In case of [manual installation](#manual-installation), just use [EL7 repo file](#el7-repo-file). It is compatible with Amazon Linux
  * In case of [script-based installation](#script-based-installation), script provided by packagecloud should be explicitly instructed to use CentOS 7 repo, instead of CentOS 6 repo, which is being used by default for Amazon Linux. More details further in the doc.

Let's start with script-based installation, since this approach looks like more user-friendly.

## Script-based installation
For our convenience, [packagecloud.io][pack_alt_repo] provides nice and user-friendly way to add repos with their [script][pack_rpm_script]. We'll need to download and run **packagecloud**'s [bash script][pack_rpm_script], which will do all required steps.

### Install dependencies
Ensure `curl` is installed on the system
```bash
sudo yum install -y curl
```

Let's download and run installation [shell-script][pack_rpm_script], provided by [packagecloud.io][pack_rpm_script]

**for CentOS 6 and 7**
```bash
curl -s https://packagecloud.io/install/repositories/altinity/clickhouse/script.rpm.sh | sudo bash
```

**for Amazon Linux**
```bash
curl -s https://packagecloud.io/install/repositories/altinity/clickhouse/script.rpm.sh | sudo os=centos dist=7 bash
```
pay attention to `os=centos dist=7` explicitly specified.

At this point we have RPM packages ready to install.

### Install packages after script

First of all, ensure we have ClickHouse packages available for installation

```bash
sudo yum list 'clickhouse*'
```

ClickHouse packages should be listed as available, something like this:
``` bash
Available Packages
clickhouse-client.x86_64                18.1.0-1.el7             Altinity_clickhouse
clickhouse-common-static.x86_64         18.1.0-1.el7             Altinity_clickhouse
clickhouse-compressor.x86_64            1.1.54336-3.el7          Altinity_clickhouse
clickhouse-debuginfo.x86_64             18.1.0-1.el7             Altinity_clickhouse
clickhouse-mysql.noarch                 0.0.20180319-1           Altinity_clickhouse
clickhouse-server.x86_64                18.1.0-1.el7             Altinity_clickhouse
clickhouse-server-common.x86_64         18.1.0-1.el7             Altinity_clickhouse
clickhouse-test.x86_64                  18.1.0-1.el7             Altinity_clickhouse
```

There are multiple packages available (new versions and old tools as well), some of them are deprecated already, so there is no need to install all available RPMs.

Now let's install ClickHouse main parts - server and client applications.
```bash
sudo yum install -y clickhouse-server clickhouse-client
```

and verify it is listed as installed
```bash
 sudo yum list installed 'clickhouse*'
```

ClickHouse packages should be listed as installed, something like this:
```bash
Installed Packages
clickhouse-client.x86_64                 18.1.0-1.el7           @Altinity_clickhouse
clickhouse-common-static.x86_64          18.1.0-1.el7           @Altinity_clickhouse
clickhouse-server.x86_64                 18.1.0-1.el7           @Altinity_clickhouse
clickhouse-server-common.x86_64          18.1.0-1.el7           @Altinity_clickhouse
```

Ensure ClickHouse server is running
```bash
sudo /etc/init.d/clickhouse-server restart
```

And connect to it with `clickhouse-client`
```bash
clickhouse-client
```

ClickHouse server should respond, something like this:
```bash
ClickHouse client version 18.1.0.
Connecting to localhost:9000 as user default.
Connected to ClickHouse server version 18.1.54396.

:)

```

Well, all looks fine and ClickHouse installed from **RPM** packages!

We are all done!

## Manual installation

Let's add Altinity's [repo][pack_alt_repo] manually

### Install required packages
We'll need two packages installed beforehands:
  * `pygpgme` - helps handling gpg-signatures
  * `yum-utils` - contains tools for handling source RPMs

```bash
sudo yum install -y pygpgme yum-utils
```

### Create required files
Now let's create `yum`'s repository configuration file: `/etc/yum.repos.d/altinity_clickhouse.repo` Depending on what CentOS version you are running you may need files for EL 6 or 7 version

#### EL6 repo file

For EL6 (**do NOT use with Amazon Linux**) copy+paste into `/etc/yum.repos.d/altinity_clickhouse.repo` 
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

For EL7 **and Amazon Linux** copy+paste into `/etc/yum.repos.d/altinity_clickhouse.repo` 
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

### Install packages manually
Packages can be installed the same way as in section [Install packages after script](#install-packages-after-script)


## Conclusion
Now we have ClickHouse **RPM** packages available for easy installation.

[pack_alt_repo]: https://packagecloud.io/Altinity/clickhouse
[pack_rpm_script]: https://packagecloud.io/Altinity/clickhouse/install#bash-rpm
[alt_rpm_builder_repo]: https://github.com/Altinity/clickhouse-rpm


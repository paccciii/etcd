# Here I explain how to install etcd, take backup and restore it
# Installing and Using etcdctl

This document provides detailed steps to install etcdctl, take a backup, and restore the backup of etcd in a Kubernetes environment.

# Installation Steps
Prerequisites

1. Ensure you have curl installed on your system.
2. Verify you have sufficient privileges to copy files to /usr/local/bin.

# 1. Install etcdctl

I have worked with with all the versions of etcd and found etcdctl version 2.32 is stable enough to work with all.

These are the steps we are going to do in these commands.  
Set the etcd version
Define the download URLs
Clean up any previous downloads
Download and extract etcd binaries
Verify the installation



    ETCD_VER=v3.2.32
    GOOGLE_URL=https://storage.googleapis.com/etcd
    GITHUB_URL=https://github.com/etcd-io/etcd/releases/download
    DOWNLOAD_URL=${GITHUB_URL}

    rm -f /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz
    rm -rf /tmp/etcd-download-test && mkdir -p /tmp/etcd-download-test

    curl -L ${DOWNLOAD_URL}/${ETCD_VER}/etcd-${ETCD_VER}-linux-amd64.tar.gz -o /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz
    tar xzvf /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz -C /tmp/etcd-download-test --strip-components=1
    rm -f /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz

    /tmp/etcd-download-test/etcd --version
    ETCDCTL_API=3 /tmp/etcd-download-test/etcdctl version

Move binaries to /usr/local/bin:

    sudo cp /tmp/etcd-download-test/etcdctl /usr/local/bin/
    sudo cp /tmp/etcd-download-test/etcd /usr/local/bin/
    sudo cp /tmp/etcd-download-test/etcdutl /usr/local/bin/

Set appropriate permissions:
    sudo chmod 777 /usr/local/bin/etcd /usr/local/bin/etcdctl 

# 2.Taking a Backup

To take a backup of your etcd cluster, use the following command:

    sudo ETCDCTL_API=3 etcdctl snapshot save /path/to/backup.db \
    --cert=/etc/kubernetes/pki/etcd/server.crt \
    --cacert=/etc/kubernetes/pki/etcd/ca.crt 
    --key=/etc/kubernetes/pki/etcd/server.key 
    --endpoints=https://<ipaddr>:2379

 # 3.Restoring a Backup

To restore an etcd backup, follow these steps:

    sudo ETCDCTL_API=3 etcdctl snapshot restore /path/to/backup.db \
    --data-dir=/var/lib/etcd \
    --cert=/etc/kubernetes/pki/etcd/server.crt \
    --cacert=/etc/kubernetes/pki/etcd/ca.crt \
    --key=/etc/kubernetes/pki/etcd/server.key \
    --endpoints=https://<ipaddr>:2379

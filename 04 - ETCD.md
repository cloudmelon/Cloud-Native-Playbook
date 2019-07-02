# Part 1 : Shortcuts

### 1. Backup etcd :

 Set environment variable ETCDCTL_API=3 to use v3 API or ETCDCTL_API=2 to use v2 API. If you're using v3 version you can use the following: 

  ETCDCTL_API=3 etcdctl --endpoints http://127.0.0.1:4001 snapshot save snapshotdb --cert-file /xxx --key-file /... --ca-file
    

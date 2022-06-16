# Production Notes

## MongoDB dbPath

The files in the dbPath directory must correspond to the configured storage engine. mongod will not start if dbPath contains data files created by a storage engine other than the one specified by --storageEngine.

mongod must possess read and write permissions for the specified dbPath.

## Hardware Considerations

Starting in MongoDB 3.4, the default WiredTiger internal cache size is the larger of either:

50% of (RAM - 1 GB), or 256 MB.

For example, on a system with a total of 4GB of RAM the WiredTiger cache will use 1.5GB of RAM (0.5 * (4 GB - 1 GB) = 1.5 GB). Conversely, a system with a total of 1.25 GB of RAM will allocate 256 MB to the WiredTiger cache because that is more than half of the total RAM minus one gigabyte (0.5 * (1.25 GB - 1 GB) = 128 MB < 256 MB).

To adjust the size of the WiredTiger internal cache, see storage.wiredTiger.engineConfig.cacheSizeGB and --wiredTigerCacheSizeGB. Avoid increasing the WiredTiger internal cache size above its default value.

## MongoDB and NUMA Hardware

1、Disable zone reclaim with one of the following commands:

    echo 0 | sudo tee /proc/sys/vm/zone_reclaim_mode
    sudo sysctl -w vm.zone_reclaim_mode=0

2、Ensure that mongod and mongos are started by numactl. 

    sudo cp /lib/systemd/system/mongod.service /etc/systemd/system/

Edit the /etc/systemd/system/mongod.service file, and update the ExecStart statement to begin with:

    /usr/bin/numactl --interleave=all

If your existing ExecStart statement reads:

    ExecStart=/usr/bin/mongod --config /etc/mongod.conf

Update that statement to read:

    ExecStart=/usr/bin/numactl --interleave=all /usr/bin/mongod --config /etc/mongod.conf

3、Apply the change to systemd:

    sudo systemctl daemon-reload

4、Restart any running mongod instances:

    sudo systemctl stop mongod
    sudo systemctl start mongod

## MongoDB on Linux

1、With the WiredTiger storage engine, using XFS is strongly recommended for data bearing nodes 
2、For best results, use the latest update available for this system-provided version

    sudo yum update glibc       or
    sudo apt-get install libc6

3、set vm.swappiness to 1 or 0

    cat /proc/sys/vm/swappiness

Edit the /etc/sysctl.conf file and add the following line:

    vm.swappiness = 1

Run the following command to apply the setting:

    sudo sysctl -p

## Recommended Configuration

For the WiredTiger storage engines, consider the following recommendations:

- Turn off atime for the storage volume containing the database files.
- Adjust the ulimit settings for your platform according to the recommendations in the ulimit reference. Low ulimit values will negatively affect MongoDB when under heavy use and can lead to failed connections to MongoDB processes and loss of service.
- Disable Transparent Huge Pages. MongoDB performs better with normal (4096 bytes) virtual memory pages. See Transparent Huge Pages Settings.
- Disable NUMA in your BIOS. If that is not possible, see MongoDB on NUMA Hardware.
- Configure SELinux for MongoDB if you are not using the default MongoDB directory paths or ports.See: Configure SELinux for MongoDB and Configure SELinux for MongoDB Enterprise for the required configuration.
- Set the readahead setting between 8 and 32 regardless of storage media type (spinning disk, SSD, etc.).Higher readahead commonly benefits sequential I/O operations. Since MongoDB disk access patterns are generally random, using higher readahead settings provides limited benefit or potential performance degradation. As such, for optimal MongoDB performance, set readahead between 8 and 32, unless testing shows a measurable, repeatable, and reliable benefit in a higher readahead value.

view tcp_keepalive_time:

    sysctl net.ipv4.tcp_keepalive_time
    cat /proc/sys/net/ipv4/tcp_keepalive_time

change:

    sudo sysctl -w net.ipv4.tcp_keepalive_time=<value>
    echo <value> | sudo tee /proc/sys/net/ipv4/tcp_keepalive_time

To persist the setting, add the following line to /etc/sysctl.conf, supplying a <value> in seconds, and reboot the machine:

    net.ipv4.tcp_keepalive_time = <value>

# install centos

https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-red-hat/#std-label-install-rhel-configure-selinux

按照文档安装时，到 Configure SELinux 这一步，在执行命令时

    checkmodule -M -m -o mongodb_cgroup_memory.mod mongodb_cgroup_memory.te
    semodule_package -o mongodb_cgroup_memory.pp -m mongodb_cgroup_memory.mod   <-----
    sudo semodule -i mongodb_cgroup_memory.pp

提示错误。

解决办法是安装 policycoreutils-python 包 就能够执行了。

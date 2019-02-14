---
layout: post
title: "Python Fabric 101"
permalink: /python-fabric-101/
categories: python,fabric,devops
date: "2019-02-14 13:54:21 +0800"
---

* TOC
{:toc}
python fabric的简单介绍, 详细请参考[官网](http://www.fabric.org)

## python fabric是神码

1. python fabric是一个纯python的library.

   简单易学，只需要基本的python基础，半个小时基本就学会.

   fabric脚本其实就是python脚本, 函数调用即python函数调用.

2. python fabric是一个ssh的library.
  
   通过fabric, 我们可以:
    * **ssh登录远程目标机器** (指定ssh用户名和密码，自动登录).
    * **远程目标机器上执行各种命令**，包括sudo命令.
      * 可以在远程机器上执行各种Linux基本命令.

        ~~~python
        // mkdir on remote server.
        run('mkdir /tmp/blabla')
        ~~~

      * 可以上传文件至远程目标机器

        ```python
        // upload files in local_path to remote server, under remote_path
        put(local_path=, remote_path=, mirror_local_mode=True/False )
        ```

      * fabric可以修改远端目标机器上的文件

        ```python
        // append your ssh public key to remote server $HOME/.ssh/authorized_keys
        append(remote_authorized_keys, key)
        ```

      * **并行在N个远程目标机器运行命令**, 批量+并行
      * **在本地机器上执行各种命令**.

3. python fabric的常见应用场景
    * 脚本化在N个远程机器上 **安装部署环境**

## Examples

1. 在远端机器上创建文件夹

    ```python
    def create_dir(parent="/tmp", path="xifeng"):
        """
        mkdir on remote server(s)
        """
        full_path = "%s/%s" % (parent, path)

        #delete first then create
        run ("echo rm %s" % full_path)
        run("rm -rf %s 2>/dev/null" % (full_path))

        run ("echo mkdir %s" % full_path)
        run("mkdir -p %s " % (full_path))
    ```

    fab调用如下:

    ```python
    //使用缺省参数列表
    fab -H host1,host2 --user=root --password=paassword --parallel --pool-size=8 create_dir

    //传递参数列表
    fab -H host1,host2 --user=root --password=password --parallel --pool-size=8 create_dir:path="/tmp"
    ```

2. 上传ssh证书至远程机器

    ```python
    def push_public_key(key_file='~/.ssh/id_rsa.pub'):
        """
        push ssh public key to remote server(s).
        """
        remote_authorized_keys = "/home/%s/.ssh/authorized_keys" % env.svc_user

        def _read_key_file(key_file):
            key_file = os.path.expanduser(key_file)
            with open(key_file) as f:
                return f.read()

            key = _read_key_file(key_file)

            with settings(warn_only=True):
                # check if the public exists on remote server.
                ret = run("grep -q '%s' '%s'" %  (key, remote_authorized_keys))
                if ret.return_code == 0:
                    pass
                else:
                    append(remote_authorized_keys, key)
                    run("chmod 600 %s" % remote_authorized_keys)
    ```

## fab常用的命令行参数

详细参见fab -h

1. print list of possible commands and exit

    ```bash
    -l, --list print list of possible commands and exit
    ```

2. comma-separated list of hosts to operate on

    ```bash
    -H HOSTS, --hosts=HOSTS
    ```

3. username to user when connecting to remote hosts

    ```bash
    -u USER, --user=USER
    ```

4. passwor for use with authentication and/or sudo

    ```bash
    -p PASSWORD, --password=PASSWORD
    ```

## fabfile常用的基础函数

1. local: 在本地执行命令
2. run: 在远端机器执行命令
3. sudo: 用sudo在远端机器执行命令
4. cd: 切换至某文件夹
5. put: 上传文件至远端机器
6. env/settings: 设置环境变量

## python fabric的安装

1. yum install -y fabric
    > fabric.noarch : A simple Pythonic remote deployment tool

## fabric VS ansible

1. fabric简单到不能再简单了
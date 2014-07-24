---
layout: post
title:  "openstack与docker结合"
---

一、前提

最近在学习docker[docker][docker]，在单机上运行docker工作的很好。偶然有一天想到docker能够跟openstack结合在一起运行，把docker当做一种nova里面的driver，就像nova和kvm那样工作。

二、前期准备

上网搜了一下，还真有人在做这个事情。在openstack havana版本中，已经可以把docker当做nova的一个driver了。具体的操作步骤如这篇[文章][dockerwiki]所描述的。

但是在新版本的openstack中，由于docker没有经过全部的测试，所以docker-driver已经被移出放到[stackforge][stackforge_docker]下，等未来版本稳定了再移回openstack中去。

最新的nova-docker的版本，已经支持docker与ovs的结合，这样大大扩展了网络方面的功能。

三、坑

资料收集完成之后，开始实际的操作。这其中遇到了不少坑，写下来留作记录吧。

1. 安装nova-docker

  安装nova-docker很容易，直接下载源代码安装即可。但是，如果想让网络部分运行ovs，一定要修改`/etc/nova/rootwrap.d/compute.filters`文件，在文件的最后部分添加

  ```bash
  ln: CommandFilter, ln, root
  ```


2. 镜像

  剩下的所有的坑，都是跟镜像有关。所以我们可以先看一下docker的镜像与openstack的镜像的区别。

  docker的镜像是一层一层的，即每一次commit之后都会产生一个新的layer，运行的时候把这些所有的layer一起mount就得到一个完整的环境。

  docker的每一个repository，不是一个镜像，是一组镜像的集合。在这中间，可以记录很多tag，每一个tag所代表的就是一个镜像。如下图所示：

  ![镜像结构图]({{ site.url }}/assets/image.jpg)

  在一个repository中，可以存放很多image，打不同的tag。

  让openstack支持docker的镜像还是比较容易的，安装[docker-registry][docker-registry],使用glance的store，就可以完成向glance存储和请求镜像的要求。



3. 生成snapshot

  开始的时候，用nova生成镜像，每次都是生成public的，让人很是不解。后来经过分析发现，这个问题是两个地方导致的。

  首先，是docker的bug。我用的docker是0.8.1版本。

  其次，docker-registry中，没有查询所有的image，只查询自己tenant的image。


四、结论

openstack（或者说是virtual machine)的镜像的理念与docker的理念是不相同的，就好比是svn和git的区别。我们可以想办法让docker兼容glance，但是这样就减弱了docker镜像的灵活性及魅力。

在其他方面，比如资源隔离等方面，openstack与docker可以很好的兼容。

但是由于镜像的问题，我觉得现阶段除非对openstack进行改造，包括nova和glance，否则openstack和docker的结合是不合适的。


[docker]:https://www.docker.
[dockerwiki]:https://wiki.openstack.org/wiki/Docker
[stackforge_docker]:https://github.com/stackforge/nova-docker
[docker-registry]:https://github.com/dotcloud/docker-registry
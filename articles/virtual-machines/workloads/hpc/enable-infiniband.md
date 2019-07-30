---
title: SR-IOV を使用して InifinBand を有効にする - Azure Virtual Machines | Microsoft Docs
description: SR-IOV を使用して InfiniBand を有効にする方法について説明します。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 2e28627359f339a3bf818a15d6a5c8e456fb554a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797526"
---
# <a name="enable-infiniband-with-sr-iov"></a>SR-IOV を使用して InfiniBand を有効にする

InfiniBand (IB) を使用してカスタム VM イメージを構成する最も簡単で推奨される方法は、InfiniBandDriverLinux または InfiniBandDriverWindows VM 拡張機能をデプロイに追加することです。
[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) および [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances) でこれらの VM 拡張機能を使用する方法について説明します

SR-IOV 対応の VM (現在は HB および HC シリーズ) 上で InfiniBand を手動で構成するには、次の手順を実行します。 これらの手順は RHEL/CentOS 専用です。 Ubuntu (16.04 および 18.04)、および SLES (12 SP4 および 15) の場合、受信トレイ ドライバーは適切に機能します。

## <a name="manually-install-ofed"></a>OFED を手動でインストールする

[Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26) から ConnectX-5 用の最新の MLNX_OFED ドライバーをインストールします。

RHEL/CentOS の場合 (7.6 の場合の例は次のとおり):

```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

Windows の場合、[Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34) から ConnectX-5 用の WinOF-2 ドライバーをダウンロードしてインストールします。

## <a name="enable-ipoib"></a>IPoIB を有効にする

```bash
sudo sed -i 's/LOAD_EIPOIB=no/LOAD_EIPOIB=yes/g' /etc/infiniband/openib.conf
sudo /etc/init.d/openibd restart
if [ $? -eq 1 ]
then
  sudo modprobe -rv  ib_isert rpcrdma ib_srpt
  sudo /etc/init.d/openibd restart
fi
```

## <a name="assign-an-ip-address"></a>IP アドレスを割り当てる

以下のいずれかを使用して、ib0 インターフェイスに IP アドレスを割り当てます。

- (ルートとして) ib0 インターフェイスに IP アドレスを手動で割り当てます。

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

または

- WALinuxAgent を使用して IP アドレスを割り当て、それを永続化します。

    ```bash
    yum install -y epel-release
    yum install -y python-pip
    python -m pip install --upgrade pip setuptools wheel
    wget "https://github.com/Azure/WALinuxAgent/archive/release-2.2.36.zip"
    unzip release-2.2.36.zip
    cd WALinuxAgent*
    python setup.py install --register-service --force
    sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
    sed -i -e 's/# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
    systemctl restart waagent
    ```

## <a name="next-steps"></a>次の手順

Azure での [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) の詳細を確認する。

---
title: "Linux VM の DHCPv6 の設定 | Microsoft Docs"
description: "Linux VM の DHCPv6 の構成方法について説明します。"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
keywords: "ipv6, azure load balancer, デュアル スタック, パブリック IP, ネイティブ ipv6, モバイル, iot"
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 5c591e7f1838c86ca74caea9dd3a5e8f874fd8a7
ms.lasthandoff: 03/29/2017

---

# <a name="configuring-dhcpv6-for-linux-vms"></a>Linux VM の DHCPv6 の設定

Azure Marketplace の Linux 仮想マシン イメージの一部には、既定で構成された DHCPv6 がありません。 IPv6 をサポートするには、使用している Linux OS のディストリビューションに合わせて DHCPv6 を構成する必要があります。 DHCPv6 の構成方法は Linux のディストリビューションによって異なります。ディストリビューションによって使用するパッケージが異なるためです。

> [!NOTE]
> Azure Marketplace の最近の SUSE Linux と CoreOS のイメージは DHCPv6 に対応するように事前構成されています。 これらのイメージを使用するのに追加の変更は必要ありません。

このドキュメントでは、DHCPv6 を有効にしてお使いの Linux 仮想マシンが IPv6 アドレスを取得できるようにする方法について説明します。

> [!WARNING]
> ネットワーク構成ファイルの編集が適切に行われないと、VM へのネットワーク アクセスが失われる恐れがあります。 構成の変更は、運用環境以外のシステムでテストすることをお勧めします。 この記事の手順は、Azure Marketplace 内の最新バージョンの Linux イメージでテストされています。 詳細な手順については、Linux のバージョン別のマニュアルを参照してください。

## <a name="ubuntu"></a>Ubuntu

1. `/etc/dhcp/dhclient6.conf` ファイルを編集し、次の行を追加します。

        timeout 10;

2. eth0 インターフェースのネットワーク構成を、次のように編集します。

   * **Ubuntu 12.04 と 14.04`/etc/network/interfaces.d/eth0.cfg` で、** ファイルを編集する
   * **Ubuntu 16.04`/etc/network/interfaces.d/50-cloud-init.cfg` で、** ファイルを編集する

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. IPv6 アドレスを次のように更新します。

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. `/etc/dhcp/dhclient6.conf` ファイルを編集し、次の行を追加します。

        timeout 10;

2. `/etc/network/interfaces` ファイルを編集し、次の構成を追加します。

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. IPv6 アドレスを次のように更新します。

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel--centos--oracle-linux"></a>RHEL/CentOS/Oracle Linux

1. `/etc/sysconfig/network` ファイルを編集し、次のパラメーターを追加します。

        NETWORKING_IPV6=yes

2. `/etc/sysconfig/network-scripts/ifcfg-eth0` ファイルを編集し、次の 2 つのパラメーターを追加します。

        IPV6INIT=yes
        DHCPV6C=yes

3. IPv6 アドレスを次のように更新します。

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11--opensuse-13"></a>SLES 11 と openSUSE 13

Azure の最近の SLES と openSUSE のイメージは、DHCPv6 に対応するように事前構成されています。 これらのイメージを使用するのに追加の変更は必要ありません。 前のバージョンまたはカスタマイズされた SUSE イメージを基盤とする VM がある場合は、次の手順に従います。

1. 必要に応じて、 `dhcp-client` パッケージをインストールします。

    ```bash
    sudo zypper install dhcp-client
    ```

2. `/etc/sysconfig/network/ifcfg-eth0` ファイルを編集し、次のパラメーターを追加します。

        DHCLIENT6_MODE='managed'

3. IPv6 アドレスを次のように更新します。

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 と openSUSE Leap

Azure の最近の SLES と openSUSE のイメージは、DHCPv6 に対応するように事前構成されています。 これらのイメージを使用するのに追加の変更は必要ありません。 前のバージョンまたはカスタマイズされた SUSE イメージを基盤とする VM がある場合は、次の手順に従います。

1. `/etc/sysconfig/network/ifcfg-eth0` ファイルを編集し、このパラメーターを

        #BOOTPROTO='dhcp4'

    次の値に置き換えます。

        BOOTPROTO='dhcp'

2. 次のパラメーターを `/etc/sysconfig/network/ifcfg-eth0`に追加します。

        DHCLIENT6_MODE='managed'

3. IPv6 アドレスを次のように更新します。

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Azure の最近の CoreOS のイメージは、DHCPv6 に対応するように事前構成されています。 これらのイメージを使用するのに追加の変更は必要ありません。 前のバージョンまたはカスタマイズされた CoreOS イメージを基盤とする VM がある場合は、次の手順に従います。

1. `/etc/systemd/network/10_dhcp.network`

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. IPv6 アドレスを次のように更新します。

    ```bash
    sudo systemctl restart systemd-networkd
    ```


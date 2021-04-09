---
title: VM のネットワーク スループットの最適化 | Microsoft Docs
description: Microsoft Azure の Windows VM および Linux VM (Ubuntu、CentOS、Red Hat などの主要ディストリビューションを含む) のネットワーク スループットを最適化します。
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2020
ms.author: steveesp
ms.openlocfilehash: bb9235f4d1190bf7f71ddc007f09c9666c353234
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98216803"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Azure 仮想マシンのネットワーク スループットの最適化

Azure 仮想マシン (VM) には既定のネットワーク設定がありますが、ネットワーク スループットを向上させるために最適化できます。 この記事では、Microsoft Azure の Windows VM および Linux VM (Ubuntu、CentOS、Red Hat などの主要ディストリビューションを含む) のネットワーク スループットを最適化する方法について説明します。

## <a name="windows-vm"></a>Windows VM

Windows VM で[高速ネットワーク](create-vm-accelerated-networking-powershell.md)がサポートされている場合、その機能を有効にすると、スループットにとって最適な構成になります。 他のすべての Windows VM では、Receive Side Scaling (RSS) を使うと、RSS を使わない VM より高い最大スループットを実現できます。 Windows VM では、RSS が既定で無効になっている場合があります。 RSS が有効かどうかを判断し、現在無効になっている場合は有効にするには、次の手順を実行します。

1. ネットワーク アダプターに対して RSS が有効になっているかどうかを、`Get-NetAdapterRss` PowerShell コマンドで確認します。 `Get-NetAdapterRss` からの次の出力例では、RSS は有効になっていません。

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. RSS を有効にするには、次のコマンドを入力します。

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    上記のコマンドの出力はありません。 このコマンドにより、NIC 設定が変更され、約 1 分間接続が一時的に切断されます。 接続の切断中は、再接続中を示すダイアログ ボックスが表示されます。 通常、3 回目の試行後に接続が復元されます。
3. 再度 `Get-NetAdapterRss` コマンドを入力して、VM で RSS が有効になっていることを確認します。 成功した場合は、次のような出力が返されます。

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Linux VM

Azure Linux VM では、RSS は既定で常に有効になっています。 2017 年 10 月以降にリリースされた Linux カーネルには、Linux VM がより高いネットワーク スループットを実現できる新しいネットワーク最適化オプションが含まれています。

### <a name="ubuntu-for-new-deployments"></a>新規デプロイ用の Ubuntu

Ubuntu Azure カーネルは、Azure でのネットワーク パフォーマンスに対して非常に効果的に最適化されています。 最新の最適化を得るには、まず以下のように、サポートされている最新バージョンの 18.04-LTS をインストールします。

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "18.04-LTS",
"Version": "latest"
```

作成が完了したら、次のコマンドを入力して、最新の更新プログラムを入手します。 次の手順は、Ubuntu Azure カーネルを現在実行している VM にも使用できます。

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

次の省略可能なコマンド セットは、既に Azure カーネルがあるが、エラーで更新に失敗している既存の Ubuntu 展開に役立つ場合があります。

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>既存の VM 用の Ubuntu Azure カーネル アップグレード

Azure Linux カーネルにアップグレードすることで、スループットのパフォーマンスが大幅に向上する可能性があります。 このカーネルがあるかどうかを確認するには、カーネルのバージョンを調べてください。 例と同じかそれより新しいものにしてください。

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

VM に Azure カーネルがない場合は、通常、バージョン番号は "4.4" で始まります。 VM に Azure カーネルがない場合は、ルートとして次のコマンドを実行します。

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

最新の最適化を得るには、次のパラメーターを指定して、サポートされている最新バージョンを使用して VM を作成することをお勧めします。

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.7",
"Version": "latest"
```

新規および既存の VM は、最新の Linux Integration Services (LIS) のインストールによりメリットを得られます。 スループットの最適化は LIS の 4.2.2-2 以降に含まれていますが、新しいバージョンほどさらなる向上が含まれています。 次のコマンドを入力して、最新の LIS をインストールします。

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

最適化を得るには、次のパラメーターを指定して、サポートされている最新バージョンを使用して VM を作成することをお勧めします。

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

新規および既存の VM は、最新の Linux Integration Services (LIS) のインストールによりメリットを得られます。 スループットの最適化は、LIS の 4.2 以降に含まれています。 次のコマンドを実行して、LIS をダウンロードしてインストールします。

```bash
wget https://aka.ms/lis
tar xvf lis
cd LISISO
sudo ./install.sh #or upgrade.sh if prior LIS was previously installed
```

Linux Integration Services Version 4.2 for Hyper-V の詳細については、[ダウンロード ページ](https://www.microsoft.com/download/details.aspx?id=55106)を参照してください。

## <a name="next-steps"></a>次のステップ
* [近接配置グループ](../virtual-machines/co-location.md)を使用して VM を互いに近い場所にデプロイし、待ち時間を短縮する
* 自分のシナリオで [Azure VM をテストする帯域幅/スループット](virtual-network-bandwidth-testing.md)の最適化された結果を確認します。
* [仮想マシンに帯域幅が割り当てられる方法](virtual-machine-network-throughput.md)に関するページを参照してください
* 「[Azure Virtual Network についてよく寄せられる質問 (FAQ)](virtual-networks-faq.md)」を参照してください。
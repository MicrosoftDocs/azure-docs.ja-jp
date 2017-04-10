---
title: "VM のネットワーク スループットの最適化 | Microsoft Docs"
description: "Azure 仮想マシンのネットワーク スループットを最適化する方法について説明します。"
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2017
ms.author: steveesp
translationtype: Human Translation
ms.sourcegitcommit: 50be31e179bf52e009596fbc68339dfb5a1aa1e4
ms.openlocfilehash: d53b1cae9845be32bd053ef196203ea83df06b10
ms.lasthandoff: 02/15/2017


---

# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Azure 仮想マシンのネットワーク スループットの最適化

Azure 仮想マシン (VM) には既定のネットワーク設定がありますが、ネットワーク スループットを向上させるために最適化できます。 この記事では、Microsoft Azure の Windows VM および Linux VM (Ubuntu、CentOS、Red Hat などの主要ディストリビューションを含む) のネットワーク スループットを最適化する方法について説明します。

## <a name="windows-vm"></a>Windows VM

Receive Side Scaling (RSS) を使用する VM は、RSS を使用しない VM よりも高い最大スループットを実現できます。 Windows VM では、RSS が既定で無効になっている場合があります。 次の手順を実行して、RSS が有効かどうかを確認し、無効になっている場合は有効にします。

1. `Get-NetAdapterRss` PowerShell コマンドを入力して、ネットワーク アダプターに対して RSS が有効になっているかどうかを確認します。 `Get-NetAdapterRss` からの次の出力例では、RSS は有効になっていません。

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. 次のコマンドを入力して、RSS を有効にします。

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    上記のコマンドの出力はありません。 このコマンドにより、NIC 設定が変更され、約&1; 分間接続が一時的に切断されます。 接続の切断中は、再接続中を示すダイアログ ボックスが表示されます。 通常、3 回目の試行後に接続が復元されます。
3. 再度 `Get-NetAdapterRss` コマンドを入力して、VM で RSS が有効になっていることを確認します。 成功した場合は、次のような出力が返されます。

    ```powershell
    Name                    :Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : True
    ```

## <a name="linux-vm"></a>Linux VM

Azure Linux VM では、RSS は既定で常に有効になっています。 2017 年 1 月以降にリリースされた Linux カーネルには、Linux VM がより高いネットワーク スループットを実現できる新しいネットワーク最適化オプションが含まれています。

### <a name="ubuntu"></a>Ubuntu

最適化を行うには、最初に、サポートされている最新バージョンに更新します。2017 年 1 月時点で次のバージョンが該当します。
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
更新が完了したら、次のコマンドを入力して、最新のカーネルを入手します。

```bash
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
```

省略可能なコマンド:

`apt-get -y dist-upgrade`

### <a name="centos"></a>CentOS

最適化を行うには、最初に、サポートされている最新バージョンに更新します。2017 年 1 月時点で次のバージョンが該当します。
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.3",
"Version": "latest"
```
更新が完了したら、最新の Linux Integration Services (LIS) をインストールします。
スループットの最適化は、LIS の 4.1.3 以降に含まれています。 次のコマンドを入力して、LIS をインストールします。

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

最適化を行うには、最初に、サポートされている最新バージョンに更新します。2017 年 1 月時点で次のバージョンが該当します。

"Publisher": "RedHat" "Offer": "RHEL" "Sku": "7.3" "Version": "7.3.20161104"

更新が完了したら、最新の Linux Integration Services (LIS) をインストールします。
スループットの最適化は、LIS の 4.1.3 以降に含まれています。 次のコマンドを実行して、LIS をダウンロードしてインストールします。

```bash
mkdir lis4.1.3
cd lis4.1.3
wget https://download.microsoft.com/download/7/6/B/76BE7A6E-E39F-436C-9353-F4B44EF966E9/lis-rpms-4.1.3.tar.gz
tar xvzf lis-rpms-4.1.3.tar.gz
cd LISISO
install.sh #or upgrade.sh if previous LIS was previously installed
```
 
Linux Integration Services Version 4.1 for Hyper-V の詳細については、[ダウンロード ページ](https://www.microsoft.com/download/details.aspx?id=51612)を参照してください。


---
title: "Linux VM のデバイス名が Azure で変更される | Microsoft Docs"
description: "デバイス名が変更される理由を説明し、この問題の解決策を提供します。"
services: virtual-machines-linux
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: 
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 07/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 789f4580901a22dc3aaae9599c7205c76f268403
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="troubleshooting-linux-vm-device-names-are-changed"></a>トラブルシューティング: Linux VM のデバイス名が変更される

この記事では、Linux 仮想マシン (VM) を再起動した後、またはディスクに再接続した後に、デバイス名が変更される理由について説明します。 また、この問題の解決策も提供します。

## <a name="symptom"></a>症状

Microsoft Azure で Linux VM を実行する場合に、次の問題が発生する可能性があります。

- 再起動後、VM が起動に失敗する。

- データ ディスクが切断されて再接続された場合は、ディスクのデバイス名が変更されます。

- デバイス名を使用してディスクを参照するアプリケーションやスクリプトは失敗します。 ディスクのデバイス名が変更されたことがわかります。

## <a name="cause"></a>原因

Linux のデバイス パスは、再起動前後の一貫性が保証されていません。 デバイス名はメジャー番号 (文字) とマイナー番号で構成されています。  Linux ストレージ デバイス ドライバーは新しいデバイスを検出すると、メジャー デバイス番号とマイナー デバイス番号を利用可能な範囲からデバイスに割り当てます。 デバイスが削除されると、後で再利用できるようにデバイス番号が解放されます。

この問題は、SCSI サブシステムでスケジュールされた Linux のデバイス スキャンが非同期で行われるために発生します。 再起動前後で、最終的なデバイス パス名が異なる場合があります。 

## <a name="solution"></a>解決策

この問題を解決するには、永続的な名前付けを使用します。 永続的な名前付けを行うには 4 つの方法があります。それは、ファイル システム ラベル、UUID、ID、パスのいずれかを利用する方法です。 Azure Linux VM には、ファイル システム ラベルと UUID を使用する方法をお勧めします。 

また、ほとんどのディストリビューションでは、**nofail** または **nobootwait** fstab オプションが提供されています。 このオプションにより、起動時にディスクのマウントに失敗しても、システムを起動できます。 これらのパラメーターの詳細については、ディストリビューションのドキュメントをご覧ください。 データ ディスクの追加時に UUID を使用するように Linux VM を構成する方法の詳細については、[Linux VM に接続して新しいディスクをマウントする](add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk)に関するページをご覧ください。 

Azure Linux エージェントが VM にインストールされている場合は、**/dev/disk/azure** にシンボリック リンクのセットを構築するために Udev ルールが使用されます。 アプリケーションとスクリプトはこれらの Udev ルールを使用することで、ディスクが VM に接続されていることや、ディスクの種類、ディスクの LUN などを識別できます。

## <a name="more-information"></a>詳細情報

### <a name="identify-disk-luns"></a>ディスク LUN の識別

アプリケーションは、LUN を使用して、接続されているすべてのディスクと、作成中のシンボリック リンクを特定できます。 Azure Linux エージェントには現在、以下のような、LUN からデバイスへのシンボリック リンクを設定する Udev ルールが備わっています。

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3                                    
                                 

lsscsi ツールや類似のツールを使用して、以下のように Linux ゲストから LUN 情報を取得することもできます。

       $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

このゲスト LUN の情報を Azure サブスクリプション メタデータと共に使用して、パーティション データが保存されている VHD の Azure ストレージ内のロケーションを識別できます。 たとえば、以下のように az cli を使用します。

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks                                        
    [                                                                                                                                                                  
    {                                                                                                                                                                  
    "caching": "None",                                                                                                                                              
      "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 1023,                                                                                                                                             
      "image": null,                                                                                                                                                   
    "lun": 0,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-114353",                                                                                                                    
    "vhd": {                                                                                                                                                          
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"                                                       
    }                                                                                                                                                              
    },                                                                                                                                                                
    {                                                                                                                                                                   
    "caching": "None",                                                                                                                                               
    "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 512,                                                                                                                                              
    "image": null,                                                                                                                                                   
    "lun": 1,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-121516",                                                                                                                    
    "vhd": {                                                                                                                                                           
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"                                                       
      }                                                                                                                                                             
      }                                                                                                                                                             
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>blkid を使用したファイルシステム UUID の検出

スクリプトまたはアプリケーションは blkid の出力や類似の情報ソースを読み取り、使用する **/dev** にシンボリック リンクを構築できます。 この出力には、VM に接続されたすべてのディスクの UUID と、関連するデバイス ファイルが示されています。

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

waagent udev ルールにより、**/dev/disk/azure** にシンボリック リンクのセットが構築されます。


    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1


アプリケーションは、この情報を使用して、ブート ディスク デバイスとリソース (一時) ディスクを識別できます。 Azure では、アプリケーションは **/dev/disk/azure/root-part1** または **/dev/disk/azure-resource-part1** を参照してこれらのパーティションを検出する必要があります。

その他のパーティションが blkid リストにある場合、それらはデータ ディスク上に存在します。 アプリケーションはこれらのパーティションの UUID を維持し、以下のようなパスを使用して、実行時にデバイス名を検出できます。

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1

    
### <a name="get-the-latest-azure-storage-rules"></a>最新の Azure Storage ルールを取得する

最新の Azure Storage ルールを取得するには、次のコマンドを実行します。

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block


詳細については、次の記事を参照してください。

- [Ubuntu: UUID の使用](https://help.ubuntu.com/community/UsingUUID)

- [Red Hat: 永続的な名前付け](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)

- [Linux: UUID ができること](https://www.linux.com/news/what-uuids-can-do-you)

- [Udev: 最新の Linux システムでのデバイス管理の概要](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)



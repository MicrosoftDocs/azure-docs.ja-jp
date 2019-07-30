---
title: Azure 上の Linux VM デバイス名の変更のトラブルシューティング | Microsoft Docs
description: Linux VM デバイス名が変更される理由と、問題の解決方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 0350b6bdc990ed6c2de60e3e98c3768b18d0d636
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710419"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Linux VM デバイス名の変更トラブルシューティング

この記事では、Linux VM を再起動した後、またはデータ ディスクに再接続した後に、デバイス名が変更される理由について説明します。 記事では、この問題の解決方法も示します。

## <a name="symptoms"></a>現象
Microsoft Azure で Linux VM を実行する場合に、次の問題が発生する可能性があります。

- 再起動後、VM が起動に失敗する。
- データ ディスクが切断されて再接続された場合は、ディスクのデバイス名が変更されます。
- デバイス名を使用してディスクを参照するアプリケーションやスクリプトは、デバイス名が変更されているため失敗します。

## <a name="cause"></a>原因

Linux のデバイス パスは、再起動前後の一貫性が保証されていません。 デバイス名はメジャー番号 (文字) とマイナー番号で構成されています。 Linux ストレージ デバイス ドライバーは新しいデバイスを検出すると、メジャー番号とマイナー番号を利用可能な範囲からデバイスに割り当てます。 デバイスが削除されると、再利用できるようにデバイス番号が解放されます。

この問題は、Linux のデバイス スキャンが非同期で行われるように SCSI サブシステムでスケジュールされているために発生します。 その結果、デバイスのパス名は再起動の前後で変わることがあります。

## <a name="solution"></a>解決策

この問題を解決するには、永続的な名前付けを使用します。 永続的な名前付けを行うには 4 つの方法があります。それは、ファイル システム ラベル、UUID、ID、パスのいずれかを利用する方法です。 Azure Linux VM には、ファイル システム ラベルまたは UUID の使用をお勧めします。

ほとんどのディストリビューションでは、`fstab` **nofail** または **nobootwait** パラメーターが提供されています。 これらのパラメーターにより、起動時にディスクのマウントに失敗しても、システムを起動できます。 これらのパラメーターについて詳しくは、ディストリビューションのドキュメントをご覧ください。 データ ディスクの追加時に UUID を使用するように Linux VM を構成する方法について詳しくは、[Linux VM に接続した新しいディスクのマウント](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk)に関するページをご覧ください。

Azure Linux エージェントが VM にインストールされている場合、エージェントは /dev/disk/azure パスにシンボリック リンクのセットを構築するために Udev ルールを使用します。 アプリケーションとスクリプトは、VM にアタッチされているディスクと、ディスクの種類およびディスク LUN を識別するために Udev ルールを使用します。

VM が起動せず、VM に SSH 接続できないように fstab を既に編集している場合、[VM シリアル コンソール](./serial-console-linux.md)を使用して[シングル ユーザー モード](./serial-console-grub-single-user-mode.md)に入り、fstab を編集できます。

### <a name="identify-disk-luns"></a>ディスク LUN の識別

アプリケーションは、LUN を使用して、接続されているすべてのディスクを見つけ、シンボリック リンクを作成します。 Azure Linux エージェントには、LUN からデバイスへのシンボリック リンクを設定する Udev ルールが備わっています。

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

`lsscsi` または同様のツールを使用して、Linux ゲスト アカウントから LUN 情報が取得されます。

      $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

ゲスト LUN 情報は、パーティション データを含む Azure Storage の VHD を特定するために Azure サブスクリプション メタデータで使用されます。 たとえば、`az` CLI を使用できます。

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

アプリケーションやスクリプトは `blkid` の出力や類似の情報ソースを読み取り、/dev パスにシンボリック リンクを構築します。 出力は、VM に関連付けられているすべてのディスクおよびその関連デバイス ファイルの UUID を示しています。

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Azure Linux エージェント Udev ルールは、/dev/disk/azure パスにシンボリック リンクのセットを構築します。

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

アプリケーションでは、リンクを使用して、ブート ディスク デバイスとリソース (短期) ディスクを識別します。 Azure では、アプリケーションが /dev/disk/azure/root-part1 または /dev/disk/azure-resource-part1 パスを参照して、これらのパーティションを検出する必要があります。

`blkid` リストにあるその他のパーティションは、データ ディスク上に存在します。 アプリケーションはこれらのパーティションの UUID を維持し、実行時にパスを使用してデバイス名を検出します。

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1


### <a name="get-the-latest-azure-storage-rules"></a>最新の Azure Storage ルールを取得する

最新の Azure Storage ルールを取得するには、次のコマンドを実行します。

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>関連項目

詳細については、次の記事を参照してください。

- [Ubuntu:Using UUID (UUID の使用)](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat:永続的な命名](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux:What UUIDs can do for you (UUID ができること)](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev:Introduction to device management in a modern Linux system (最新の Linux システムでのデバイス管理の概要)](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)


---
title: Azure CLI を使用して Linux VM にデータ ディスクを追加する | Microsoft Docs
description: Azure CLI を使用して Linux VM に永続データ ディスクを追加する方法について説明します
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 06/13/2018
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.subservice: disks
ms.openlocfilehash: 1c8d4d2b26b356c524523d73d53fd641eef5f3cb
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465830"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Linux VM へのディスクの追加
この記事では、メンテナンスやサイズ変更により VM が再プロビジョニングされる場合でもデータを保持できるように、永続ディスクを VM に接続する方法について説明します。


## <a name="attach-a-new-disk-to-a-vm"></a>新しいディスクの VM への接続

VM に新しい空のデータ ディスクを追加する場合は、`--new` パラメーターを指定して [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest) コマンドを使用します。 VM が可用性ゾーン内にある場合は、VM と同じゾーンで、ディスクが自動的に作成されます。 詳細については、[可用性ゾーンの概要](../../availability-zones/az-overview.md)に関するページをご覧ください。 次の例では、*myDataDisk* という名前で、サイズが 50 GB のディスクを作成します。

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>既存のディスクの接続

既存のディスクを接続するには、ディスク ID を探し、[az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest) コマンドに渡します。 次の例では、*myResourceGroup* 内の *myDataDisk* という名前のディスクにクエリを実行し、それを *myVM* という名前の VM に接続します。

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Linux VM を接続して新しいディスクをマウントする

Linux VM から使用できるように新しいディスクのパーティション分割、フォーマット、マウントを行うには、SSH で VM に接続します。 詳細については、[Azure 上の Linux における SSH の使用方法](mac-create-ssh-keys.md)に関するページをご覧ください。 次の例では、パブリック DNS エントリ *mypublicdns.westus.cloudapp.azure.com* を持つ VM に、ユーザー名 *azureuser* で接続します。

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

VM に接続された時点で、ディスクを接続する準備ができました。 まず、`dmesg` を使用してディスクを探します (新しいディスクの検出に使用する方法は異なる場合があります)。 次の例では、*SCSI* ディスクでのフィルター処理に dmesg を使用します。

```bash
dmesg | grep SCSI
```

出力は次の例のようになります。

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

> [!NOTE]
> ご使用のディストリビューションで利用できる最新バージョンの fdisk または parted を使用することをお勧めします。

ここでは、*sdc* が対象のディスクです。 `parted` を使用してディスクをパーティション分割します。ディスクのサイズが 2 テビバイト (TiB) 以上の場合は、GPT パーティション分割を使用する必要があります。2 TiB 未満の場合は、MBR または GPT のパーティション分割を使用することができます。 MBR パーティション分割を使用している場合、`fdisk` を使用できます。 それをパーティション 1 上のプライマリ ディスクにして、それ以外は既定値をそのまま使用します。 次の例では、`fdisk` プロセスが */dev/sdc* 上で開始されます。

```bash
sudo fdisk /dev/sdc
```

`n` コマンドを使用して新しいパーティションを追加します。 この例では、プライマリ パーティションのために `p` も選択し、残りの既定値はそのまま使用します。 出力は次の例のようになります。

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

`p` を入力してパーティション テーブルを出力し、次に `w` を使用してテーブルをディスクに書き込んで終了します。 出力は次の例のようになります。

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```
次のコマンドを使用してカーネルを更新します。
```
partprobe 
```

次に、`mkfs` コマンドを使用してパーティションにファイル システムを書き込みます。 ファイル システムの種類とデバイス名を指定します。 次の例では、上記の手順で作成された */dev/sdc1* パーティション上に *ext4* ファイル システムを作成します。

```bash
sudo mkfs -t ext4 /dev/sdc1
```

出力は次の例のようになります。

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

次に、`mkdir` を使用して、ファイル システムをマウントするディレクトリを作成します。 次の例では、 */datadrive* にディレクトリを作成します。

```bash
sudo mkdir /datadrive
```

`mount` を使用して、ファイル システムをマウントします。 次の例では、 */dev/sdc1* パーティションを */datadrive* マウント ポイントにマウントします。

```bash
sudo mount /dev/sdc1 /datadrive
```

再起動後にドライブを自動的に再マウントするために、そのドライブを */etc/fstab* ファイルに追加する必要があります。 ドライブを参照する際に、デバイス名 ( */dev/sdc1* など) だけでなく、UUID (汎用一意識別子) を */etc/fstab* で使用することもお勧めします。 UUID を使用すると、OS が起動中にディスク エラーを検出した場合に、間違ったディスクが特定の場所にマウントされるのを防ぐことができます。 その後、残りのデータ ディスクは、その同じデバイス ID に割り当てられます。 新しいドライブの UUID を確認するには、`blkid` ユーティリティを使用します。

```bash
sudo blkid
```

出力は次の例のようになります。

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> **/etc/fstab** ファイルを不適切に編集すると、システムが起動できなくなる可能性があります。 編集方法がはっきりわからない場合は、このファイルを適切に編集する方法について、ディストリビューションのドキュメントを参照してください。 編集する前に、/etc/fstab ファイルのバックアップを作成することもお勧めします。

次に、テキスト エディターで次のように */etc/fstab* ファイルを開きます。

```bash
sudo vi /etc/fstab
```

この例では、前の手順で作成した */dev/sdc1* デバイスに対して UUID 値を使用し、マウント ポイントとして */datadrive* を使用します。 次の行を */etc/fstab* ファイルの末尾に追加します。

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> この後、fstab を編集せずにデータ ディスクを削除すると VM は起動できません。 ほとんどのディストリビューションでは、*nofail* または *nobootwait* fstab オプションが提供されています。 これにより起動時にディスクのマウントが失敗しても、システムを起動できます。 これらのパラメーターの詳細については、使用しているディストリビューションのドキュメントを参照してください。
>
> *nofail* オプションを使用すると、ファイル システムが壊れているか、ブート時にディスクが存在しない場合でも VM が起動されるようになります。 このオプションを指定しない場合、「[Cannot SSH to Linux VM due to FSTAB errors (FSTAB エラーが原因で Linux VM に SSH 接続できない)](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)」で説明されているような動作が発生します。
>
> fstab の変更が原因で起動エラーが発生した場合は、VM へのコンソール アクセスに Azure VM シリアル コンソールを使用できます。 詳細については、[シリアル コンソールのドキュメント](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)を参照してください。

### <a name="trimunmap-support-for-linux-in-azure"></a>Azure における Linux の TRIM/UNMAP サポート
一部の Linux カーネルでは、ディスク上の未使用ブロックを破棄するために TRIM/UNMAP 操作がサポートされます。 この機能は主に、Standard Storage で、削除されたページが無効になり、破棄できるようになったことを Azure に通知するときに役立ちます。また、この機能により、サイズの大きいファイルを作成して削除する場合のコストを削減できます。

Linux VM で TRIM のサポートを有効にする方法は 2 通りあります。 通常どおり、ご使用のディストリビューションで推奨される方法をお問い合わせください。

* 次のように、 */etc/fstab* で `discard` マウント オプションを使用します。

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* 場合によっては、`discard` オプションがパフォーマンスに影響する可能性があります。 または、 `fstrim` コマンドを手動でコマンド ラインから実行するか、crontab に追加して定期的に実行することができます。

    **Ubuntu**

    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>トラブルシューティング

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>次の手順

* [Linux マシンのパフォーマンスの最適化](optimization.md) に関する推奨事項を読んで、Linux VM が正しく構成されていることを確認します。
* ディスクを追加してストレージ容量を拡張し、 [RAID を構成](configure-raid.md) してパフォーマンスを強化します。

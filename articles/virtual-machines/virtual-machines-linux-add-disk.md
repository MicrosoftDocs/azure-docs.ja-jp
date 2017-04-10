---
title: "Azure CLI を使用して Linux VM にディスクを追加する | Microsoft Docs"
description: "Azure CLI 1.0 および 2.0 を使用して Linux VM に永続ディスクを追加する方法について説明します。"
keywords: "Linux 仮想マシン,リソース ディスクの追加"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 3005a066-7a84-4dc5-bdaa-574c75e6e411
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.date: 02/02/2017
ms.author: rasquill
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f520ed9cc1a3a7063d5b3ddacf7f0c8174e75a36
ms.openlocfilehash: 77cbdaaea0eec5265ef005f66dd5efdd8e237022
ms.lasthandoff: 02/21/2017

---
# <a name="add-a-disk-to-a-linux-vm"></a>Linux VM へのディスクの追加
この記事では、メンテナンスやサイズ変更により VM が再プロビジョニングされる場合でもデータを保持できるように、永続ディスクを VM に接続する方法について説明します。 

## <a name="quick-commands"></a>クイック コマンド
次の例では、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM に `50` GB のディスクを接続します。

管理ディスクを使用する場合:

```azurecli
az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk \
  --new --size-gb 50
```

非管理対象ディスクを使用する場合:

```azurecli
az vm unmanaged-disk attach -g myResourceGroup -n myUnmanagedDisk --vm-name myVM \
  --new --size-gb 50
```

## <a name="attach-a-managed-disk"></a>管理ディスクの接続

管理ディスクを使用すると、Azure Storage アカウントについて心配することなく、VM およびそれらのディスクに注力できます。 同じ Azure リソース グループを使用して、管理ディスクを迅速に作成して VM に接続したり、任意の数のディスクを作成して VM に接続したりできます。


### <a name="attach-a-new-disk-to-a-vm"></a>新しいディスクの VM への接続

VM に新しいディスクが必要な場合は、`az vm disk attach` コマンドを使用できます。

```azurecli
az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk \
  --new --size-gb 50
```

### <a name="attach-an-existing-disk"></a>既存のディスクの接続 

多くの場合は、既に作成されているディスクを接続します。 まずディスク ID を探し、`az vm disk attach` コマンドに渡します。 次の例では、`az disk create -g myResourceGroup -n myDataDisk --size-gb 50` で作成されたディスクを使用します。

```azurecli
# find the disk id
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)
az vm disk attach -g myResourceGroup --vm-name myVM --disk $diskId
```

出力は次のようになります (どのコマンドにも `-o table` オプションを使用して、出力の形式を設定できます)。

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Empty",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 50,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/rasquill-script/providers/Microsoft.Compute/disks/myDataDisk",
  "location": "westus",
  "name": "myDataDisk",
  "osType": null,
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-02T23:35:47.708082+00:00",
  "type": "Microsoft.Compute/disks"
}
```


## <a name="attach-an-unmanaged-disk"></a>非管理対象ディスクの接続

VM と同じストレージ アカウントにディスクを作成しても問題ない場合は、新しいディスクの接続は簡単です。 「`azure vm disk attach-new`」と入力して、VM 用に新しい GB ディスクを作成し、接続します。 ストレージ アカウントを明示的に特定しない場合、作成するディスクは、OS ディスクと同じストレージ アカウントに配置されます。 次の例では、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM に `50` GB のディスクを接続します。

```azurecli
az vm unmanaged-disk attach -g myResourceGroup -n myUnmanagedDisk --vm-name myVM \
  --new --size-gb 50
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Linux VM を接続して新しいディスクをマウントする
> [!NOTE]
> このトピックでは、ユーザー名とパスワードを使用して VM に接続します。 公開キーおよび秘密キーのペアを使用して VM と通信する方法については、[Azure 上の Linux における SSH の使用方法](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。 
> 
> 

Linux VM から使用できるように新しいディスクのパーティション分割、フォーマット、マウントを行うには、SSH で Azure VM に接続する必要があります。 **ssh** を使用した接続に慣れていない場合は、`ssh <username>@<FQDNofAzureVM> -p <the ssh port>` 形式のコマンドを使用します。コマンドは次のようになります。

```bash
ssh ops@mypublicdns.westus.cloudapp.azure.com -p 22
```

出力

```bash
The authenticity of host 'mypublicdns.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
ops@mypublicdns.westus.cloudapp.azure.com's password:
Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Fri May 22 21:02:32 UTC 2015

System load: 0.37              Memory usage: 2%   Processes:       207
Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

Graph this data and manage this system at:
  https://landscape.canonical.com/

Get cloud support with Ubuntu Advantage Cloud Guest:
  http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ops@myVM:~$
```

これで VM に接続されたので、ディスクを接続する準備ができました。  まず、 `dmesg | grep SCSI` を使用してディスクを探します (新しいディスクの検出に使用する方法は異なる場合があります)。 この場合、次のようになります。

```bash
dmesg | grep SCSI
```

出力

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

このトピックで必要なのは `sdc` ディスクです。 使用しているディスクを `sdc` と想定して、`sudo fdisk /dev/sdc` でディスクをパーティション分割します。それをパーティション 1 上のプライマリ ディスクにして、それ以外は既定値をそのまま使用します。

```bash
sudo fdisk /dev/sdc
```

出力

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

プロンプトで「 `p` 」と入力して、パーティションを作成します。

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

**mkfs** コマンドを使用し、ファイル システムの種類とデバイス名を指定して、パーティションにファイル システムを作成します。 このトピックでは、前の内容から `ext4` と `/dev/sdc1` を使用しています。

```bash
sudo mkfs -t ext4 /dev/sdc1
```

出力

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

次に、 `mkdir`を使用して、ファイル システムをマウントするディレクトリを作成します。

```bash
sudo mkdir /datadrive
```

`mount`を使用して、ディレクトリをマウントします。

```bash
sudo mount /dev/sdc1 /datadrive
```

これで、データ ディスクを `/datadrive`として使用する準備ができました。

```bash
ls
```

出力

```bash
bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var
```

再起動後にドライブを自動的に再マウントするために、そのドライブを /etc/fstab ファイルに追加する必要があります。 また、ドライブを参照する際に、デバイス名 ( `/dev/sdc1`など) だけでなく、UUID (汎用一意識別子) を /etc/fstab で使用することもお勧めします。 UUID を使用すると、OS が起動中にディスク エラーを検出した場合に、間違ったディスクが特定の場所にマウントされるのを防ぐことができます。 その後、残りのデータ ディスクは、その同じデバイス ID に割り当てられます。 新しいドライブの UUID を確認するには、 **blkid** ユーティリティを使用します。

```bash
sudo -i blkid
```

出力は、次のようになります。

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> **/etc/fstab** ファイルを不適切に編集すると、システムが起動できなくなる可能性があります。 編集方法がはっきりわからない場合は、このファイルを適切に編集する方法について、ディストリビューションのドキュメントを参照してください。 編集する前に、/etc/fstab ファイルのバックアップを作成することもお勧めします。
> 
> 

次に、テキスト エディターで **/etc/fstab** ファイルを開きます。

```bash
sudo vi /etc/fstab
```

この例では、前の手順で作成した新しい **/dev/sdc1** デバイスに対して UUID 値を使用し、マウント ポイントとして **/datadrive** を使用します。 次の行を **/etc/fstab** ファイルの末尾に追加します。

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> この後、fstab を編集せずにデータ ディスクを削除すると VM は起動できません。 ディストリビューションのほとんどに `nofail` または `nobootwait` fstab オプションが用意されています。 これにより起動時にディスクのマウントが失敗しても、システムを起動できます。 これらのパラメーターの詳細については、使用しているディストリビューションのドキュメントを参照してください。
> 
> **nofail** オプションを使用すると、ファイル システムが壊れているか、ブート時にディスクが存在しない場合でも VM が起動されるようになります。 このオプションを指定しない場合、「[Cannot SSH to Linux VM due to FSTAB errors (FSTAB エラーが原因で Linux VM に SSH 接続できない)](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)」で説明されているような動作が発生します。

### <a name="trimunmap-support-for-linux-in-azure"></a>Azure における Linux の TRIM/UNMAP サポート
一部の Linux カーネルでは、ディスク上の未使用ブロックを破棄するために TRIM/UNMAP 操作がサポートされます。 これは主に、Standard Storage で、削除されたページが無効になり、破棄できるようになったことを Azure に通知するときに役立ちます。 これによって、サイズの大きいファイルを作成して削除する場合のコストを節約できます。

Linux VM で TRIM のサポートを有効にする方法は&2; 通りあります。 通常どおり、ご使用のディストリビューションで推奨される方法をお問い合わせください。

* 次のように、`/etc/fstab` で `discard` マウント オプションを使用します。

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
[!INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>次のステップ
* 新しいディスクは、 [fstab](http://en.wikipedia.org/wiki/Fstab) ファイルにその情報を書き込まない限り、再起動しても VM で使用できないことに注意してください。
* [Linux マシンのパフォーマンスの最適化](virtual-machines-linux-optimization.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) に関する推奨事項を読んで、Linux VM が正しく構成されていることを確認します。
* ディスクを追加してストレージ容量を拡張し、 [RAID を構成](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) してパフォーマンスを強化します。



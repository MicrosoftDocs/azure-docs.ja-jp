---
title: データ ディスクを Linux VM に接続する | Microsoft Docs
description: ポータルを利用し、新しいデータ ディスクまたは既存のデータ ディスクを Linux VM に接続します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: a96c7bd3c461c70f3bdf5e3e12181dbc37008512
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092338"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>ポータルを利用し、データ ディスクを Linux VM に接続する 
この記事では、Azure ポータルを使用して新しいディスクと既存のディスクの両方を Linux 仮想マシンに接続する方法について示します。 [Azure Portal で Windows VM にデータ ディスクを接続する](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)こともできます。 

VM にディスクを接続する前に、次のヒントを確認してください。

* 仮想マシンのサイズによって、接続できるデータ ディスク数は変わります。 詳細については、「 [仮想マシンのサイズ](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。
* 仮想マシンに接続されているディスクは、実際には Azure に保存されている .vhd ファイルです。 詳しくは、「[マネージド ディスクの概要](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。
* ディスクをアタッチした後、[Linux VM に接続して新しいディスクをマウントする](#connect-to-the-linux-vm-to-mount-the-new-disk)必要があります。


## <a name="find-the-virtual-machine"></a>仮想マシンの検索
1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. 左側のメニューで **[仮想マシン]** をクリックします。
3. 一覧から仮想マシンを選択します。
4. [仮想マシン] ページにアクセスし、 **[要点]** にある **[ディスク]** をクリックします。
   
    ![ディスク設定を開く](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>新しいディスクの接続

1. **[ディスク]** ウィンドウで、 **[+ データ ディスクの追加]** をクリックします。
2. **[名前]** のドロップダウン メニューをクリックして、 **[ディスクの作成]** を選択します。

    ![Azure マネージド ディスクの作成](./media/attach-disk-portal/create-new-md.png)

3. マネージド ディスクの名前を入力します。 既定の設定を確認し、必要に応じて更新して、 **[作成]** をクリックします。
   
   ![ディスク設定を確認する](./media/attach-disk-portal/create-new-md-settings.png)

4. **[保存]** をクリックしてマネージド ディスクを作成し、VM の構成を更新します。

   ![新しい Azure Managed Disk の保存](./media/attach-disk-portal/confirm-create-new-md.png)

5. Azure でディスクが作成され、仮想マシンに接続されると、仮想マシンのディスク設定の **[データ ディスク]** に新しいディスクが表示されます。 マネージド ディスクは最上位リソースであるため、リソース グループのルートに表示されます。

   ![リソース グループの Azure Managed Disk](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>既存のディスクの接続
1. **[ディスク]** ウィンドウで、 **[+ データ ディスクの追加]** をクリックします。
2. **[名前]** のドロップダウン メニューをクリックして、Azure サブスクリプションにアクセスできる既存のマネージド ディスク一覧を確認します。 接続するマネージド ディスクを選択します。

   ![既存の Azure Managed Disk の接続](./media/attach-disk-portal/select-existing-md.png)

3. **[保存]** をクリックして既存のマネージド ディスクを接続し、VM の構成を更新します。
   
   ![Azure Managed Disk の更新の保存](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Azure で仮想マシンにディスクが接続されると、仮想マシンのディスク設定の **[データ ディスク]** にそのディスクが表示されます。

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

ここでは、*sdc* が対象のディスクです。 

### <a name="partition-a-new-disk"></a>新しいディスクのパーティション分割
データを含む既存のディスクを使用する場合、ディスクのマウントをスキップします。 新規ディスクをアタッチする場合、ディスクをパーティション分割する必要があります。

> [!NOTE]
> ご使用のディストリビューションで利用できる最新バージョンの fdisk または parted を使用することをお勧めします。

`fdisk` を使用してディスクをパーティション分割します。 ディスクのサイズが 2 テビバイト (TiB) 以上であり GPT パーティション分割を使用する必要がある場合、`parted` を使用して GPT パーティション分割を実行できます。 ディスクのサイズが 2TiB 未満の場合、MBR または GPT どちらかのパーティション分割を使用できます。 それをパーティション 1 上のプライマリ ディスクにして、それ以外は既定値をそのまま使用します。 次の例では、`fdisk` プロセスが */dev/sdc* 上で開始されます。

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
### <a name="mount-the-disk"></a>ディスクのマウント
`mkdir` を使用して、ファイル システムをマウントするディレクトリを作成します。 次の例では、 */datadrive* にディレクトリを作成します。

```bash
sudo mkdir /datadrive
```

`mount` を使用して、ファイル システムをマウントします。 次の例では、 */dev/sdc1* パーティションを */datadrive* マウント ポイントにマウントします。

```bash
sudo mount /dev/sdc1 /datadrive
```

再起動後にドライブを自動的に再マウントするために、そのドライブを */etc/fstab* ファイルに追加する必要があります。 ドライブを参照する際に、デバイス名 ( */dev/sdc1* など) だけでなく、UUID (汎用一意識別子) を */etc/fstab* で使用することもお勧めします。 UUID を使用すると、OS が起動中にディスク エラーを検出した場合に、間違ったディスクが特定の場所にマウントされるのを防ぐことができます。 その後、残りのデータ ディスクは、その同じデバイス ID に割り当てられます。 新しいドライブの UUID を確認するには、`blkid` ユーティリティを使用します。

```bash
sudo -i blkid
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

## <a name="next-steps"></a>次の手順
Azure CLI を利用し、[データ ディスクを接続する](add-disk.md)こともできます。

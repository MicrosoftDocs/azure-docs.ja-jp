---
title: データ ディスクを Linux VM に接続する
description: ポータルを利用し、新しいデータ ディスクまたは既存のデータ ディスクを Linux VM に接続します。
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/28/2020
ms.author: cynthn
ms.subservice: disks
ms.collection: linux
ms.openlocfilehash: a6ef2cd62fc14958e8505a03e69e4b20cf6ce49f
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556827"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>ポータルを利用し、データ ディスクを Linux VM に接続する 
この記事では、Azure ポータルを使用して新しいディスクと既存のディスクの両方を Linux 仮想マシンに接続する方法について示します。 [Azure Portal で Windows VM にデータ ディスクを接続する](../windows/attach-managed-disk-portal.md)こともできます。 

VM にディスクを接続する前に、次のヒントを確認してください。

* 仮想マシンのサイズによって、接続できるデータ ディスク数は変わります。 詳細については、「 [仮想マシンのサイズ](../sizes.md)」を参照してください。
* 仮想マシンに接続されているディスクは、実際には Azure に保存されている .vhd ファイルです。 詳しくは、「[マネージド ディスクの概要](../managed-disks-overview.md)」を参照してください。
* ディスクをアタッチした後、[Linux VM に接続して新しいディスクをマウントする](#connect-to-the-linux-vm-to-mount-the-new-disk)必要があります。


## <a name="find-the-virtual-machine"></a>仮想マシンの検索
1. [Azure portal](https://portal.azure.com/) にアクセスして VM を検索します。 **[仮想マシン]** を検索して選択します。
2. 一覧から VM を選択します。
3. **[仮想マシン]** ページの **[設定]** で、 **[ディスク]** を選択します。


## <a name="attach-a-new-disk"></a>新しいディスクの接続

1. **[ディスク]** ウィンドウの **[データ ディスク]** で、 **[新しいディスクを作成し接続する]** を選択します。

1. マネージド ディスクの名前を入力します。 既定の設定を確認し、必要に応じて、 **[ストレージの種類]** 、 **[サイズ (GiB)]** 、 **[暗号化]** 、 **[ホスト キャッシュ]** を更新します。
   
   :::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="ディスク設定を確認する":::


1. 完了したら、ページの上部にある **[保存]** を選択してマネージド ディスクを作成し、VM 構成を更新します。


## <a name="attach-an-existing-disk"></a>既存のディスクの接続
1. **[ディスク]** ウィンドウの **[データ ディスク]** で、 **[既存のディスクの接続]** を選択ます。
1. **[ディスク名]** のドロップダウン メニューをクリックし、使用可能なマネージド ディスクのリストからディスクを選択します。 

1. **[保存]** をクリックして既存のマネージド ディスクを接続し、VM の構成を更新します。
   

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Linux VM を接続して新しいディスクをマウントする
Linux VM から使用できるように新しいディスクのパーティション分割、フォーマット、マウントを行うには、SSH で VM に接続します。 詳細については、[Azure 上の Linux における SSH の使用方法](mac-create-ssh-keys.md)に関するページをご覧ください。 次の例では、パブリック IP アドレス *10.123.123.25* と、ユーザー名 *azureuser* を指定して、VM に接続します。 

```bash
ssh azureuser@10.123.123.25
```

## <a name="find-the-disk"></a>ディスクの特定

ご利用の VM に接続したら、ディスクを特定する必要があります。 この例では、`lsblk` を使用してディスクを一覧表示します。 

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

出力は次の例のようになります。

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
```

この例において、追加したディスクは `sdc` です。 これは LUN 0 で、4 GB です。

より複雑な例として、複数のデータ ディスクがポータルでどのように表示されるかを次に示します。

:::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="ディスク設定を確認する":::

この画像には、次の 3 つのデータ ディスクがあることがわかります: LUN 0 の 4 GB、LUN 1 の 16 GB、LUN 2 の 32 G。

`lsblk` を使用すると、それは次のようになります:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
sdd     3:0:0:1      16G
sde     3:0:0:2      32G
```

`lsblk` の出力から、LUN 0 の 4 GB ディスクは `sdc` であり、LUN 1 の 16 GB ディスクは `sdd` であり、LUN 2 の 32 G ディスクは `sde` であることがわかります。

### <a name="partition-a-new-disk"></a>新しいディスクのパーティション分割

データを含む既存のディスクを使用する場合、ディスクのマウントをスキップします。 新規ディスクをアタッチする場合、ディスクをパーティション分割する必要があります。

`parted` ユーティリティを使用すれば、データ ディスクのパーティション分割とフォーマットを行うことができます。

> [!NOTE]
> ご利用のディストリビューションで入手可能な最新バージョンの `parted` を使用することをお勧めします。
> ディスク サイズが 2 テビバイト (TiB) 以上の場合は、GPT パーティション分割を使用する必要があります。 ディスク サイズが 2 TiB 未満の場合は、MBR または GPT のどちらのパーティション分割でも使用できます。  


次の例では、`/dev/sdc` にある `parted` を使用します。これは、通常、ほとんどの VM 上で最初のデータ ディスクが置かれる場所です。 `sdc` を、ご利用のディスクに適したオプションに置き換えます。 また、[XFS](https://xfs.wiki.kernel.org/) ファイル システムを使用して、その書式設定も行います。

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

[`partprobe`](https://linux.die.net/man/8/partprobe) ユーティリティを使用して、カーネルが新しいパーティションとファイルシステムを認識できるようにしています。 `partprobe` を使用しないと、blkid または lslbk コマンドで、新しいファイルシステムの UUID が返されない可能性があります。

### <a name="mount-the-disk"></a>ディスクのマウント

`mkdir` を使用して、ファイル システムをマウントするディレクトリを作成します。 次の例では、 `/datadrive` にディレクトリを作成します。

```bash
sudo mkdir /datadrive
```

`mount` を使用して、ファイル システムをマウントします。 次の例では、 */dev/sdc1* パーティションを `/datadrive` マウント ポイントにマウントします。

```bash
sudo mount /dev/sdc1 /datadrive
```

再起動後にドライブを自動的に再マウントするために、そのドライブを */etc/fstab* ファイルに追加する必要があります。 ドライブを参照する場合に、デバイス名 ( */dev/sdc1* など) だけでなく、UUID (汎用一意識別子) を */etc/fstab* で使用することもお勧めします。 UUID を使用すると、OS が起動中にディスク エラーを検出した場合に、間違ったディスクが特定の場所にマウントされるのを防ぐことができます。 その後、残りのデータ ディスクは、その同じデバイス ID に割り当てられます。 新しいドライブの UUID を確認するには、`blkid` ユーティリティを使用します。

```bash
sudo blkid
```

出力は次の例のようになります。

```bash
/dev/sda1: LABEL="cloudimg-rootfs" UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4" PARTUUID="1a1b1c1d-11aa-1234-1a1a1a1a1a1a"
/dev/sda15: LABEL="UEFI" UUID="BCD7-96A6" TYPE="vfat" PARTUUID="1e1g1cg1h-11aa-1234-1u1u1a1a1u1u"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4" TYPE="ext4" PARTUUID="1a2b3c4d-01"
/dev/sda14: PARTUUID="2e2g2cg2h-11aa-1234-1u1u1a1a1u1u"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs" PARTLABEL="xfspart" PARTUUID="c1c2c3c4-1234-cdef-asdf3456ghjk"
```

> [!NOTE]
> **/etc/fstab** ファイルを不適切に編集すると、システムが起動できなくなる可能性があります。 編集方法がはっきりわからない場合は、このファイルを適切に編集する方法について、ディストリビューションのドキュメントを参照してください。 編集する前に、/etc/fstab ファイルのバックアップを作成することもお勧めします。

次に、テキスト エディターで次のように */etc/fstab* ファイルを開きます。

```bash
sudo nano /etc/fstab
```

この例では、前の手順で作成した `/dev/sdc1` デバイスに対して UUID 値を使用し、マウント ポイントとして `/datadrive` を使用します。 次の行を `/etc/fstab` ファイルの末尾に追加します。

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,nofail   1   2
```

nano エディターを使用したので、ファイルの編集が完了したら、`Ctrl+O` を使用してファイルを書き込み、`Ctrl+X` を使用してエディターを終了してください。

> [!NOTE]
> この後、fstab を編集せずにデータ ディスクを削除すると VM は起動できません。 ほとんどのディストリビューションでは、*nofail* または *nobootwait* fstab オプションが提供されています。 これにより起動時にディスクのマウントが失敗しても、システムを起動できます。 これらのパラメーターの詳細については、使用しているディストリビューションのドキュメントを参照してください。
> 
> *nofail* オプションを使用すると、ファイル システムが壊れているか、ブート時にディスクが存在しない場合でも VM が起動されるようになります。 このオプションを指定しない場合、「[Cannot SSH to Linux VM due to FSTAB errors (FSTAB エラーが原因で Linux VM に SSH 接続できない)](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting)」で説明されているような動作が発生します。


## <a name="verify-the-disk"></a>ディスクを確認する

`lsblk` を再度使用して、ディスクとマウントポイントを表示できるようになりました。

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

出力は次のようになります。

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
└─sdc1                4G /datadrive
```

`sdc` が `/datadrive` にマウントされていることがわかります。

### <a name="trimunmap-support-for-linux-in-azure"></a>Azure における Linux の TRIM/UNMAP サポート

一部の Linux カーネルでは、ディスク上の未使用ブロックを破棄するために TRIM/UNMAP 操作がサポートされます。 この機能は主に、Standard Storage で、削除されたページが無効になり、破棄できるようになったことを Azure に通知するときに役立ちます。また、この機能により、サイズの大きいファイルを作成して削除する場合のコストを削減できます。

Linux VM で TRIM のサポートを有効にする方法は 2 通りあります。 通常どおり、ご使用のディストリビューションで推奨される方法をお問い合わせください。

* 次のように、 */etc/fstab* で `discard` マウント オプションを使用します。

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,discard   1   2
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

## <a name="next-steps"></a>次のステップ

詳細およびディスクの問題のトラブルシューティングについては、「[Linux VM デバイス名の変更のトラブルシューティング](../troubleshooting/troubleshoot-device-names-problems.md)」を参照してください。

Azure CLI を利用し、[データ ディスクを接続する](add-disk.md)こともできます。

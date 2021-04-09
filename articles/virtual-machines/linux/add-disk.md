---
title: Azure CLI を使用して Linux VM にデータ ディスクを追加する
description: Azure CLI を使用して Linux VM に永続データ ディスクを追加する方法について説明します
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/20/2020
ms.author: cynthn
ms.openlocfilehash: adf6198cf12011c77fcf3f93d4b595ea433ddefd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580387"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Linux VM へのディスクの追加

この記事では、メンテナンスやサイズ変更により VM が再プロビジョニングされる場合でもデータを保持できるように、永続ディスクを VM に接続する方法について説明します。


## <a name="attach-a-new-disk-to-a-vm"></a>新しいディスクの VM への接続

VM に新しい空のデータ ディスクを追加する場合は、`--new` パラメーターを指定して [az vm disk attach](/cli/azure/vm/disk) コマンドを使用します。 VM が可用性ゾーン内にある場合は、VM と同じゾーンで、ディスクが自動的に作成されます。 詳細については、[可用性ゾーンの概要](../../availability-zones/az-overview.md)に関するページをご覧ください。 次の例では、*myDataDisk* という名前で、サイズが 50 GB のディスクを作成します。

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>既存のディスクの接続

既存のディスクを接続するには、ディスク ID を探し、[az vm disk attach](/cli/azure/vm/disk) コマンドに渡します。 次の例では、*myResourceGroup* 内の *myDataDisk* という名前のディスクにクエリを実行し、それを *myVM* という名前の VM に接続します。

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="format-and-mount-the-disk"></a>ディスクのフォーマットとマウント

Linux VM から使用できるように新しいディスクのパーティション分割、フォーマット、マウントを行うには、SSH で VM に接続します。 詳細については、[Azure 上の Linux における SSH の使用方法](mac-create-ssh-keys.md)に関するページをご覧ください。 次の例では、パブリック IP アドレス *10.123.123.25* と、ユーザー名 *azureuser* を指定して、VM に接続します。

```bash
ssh azureuser@10.123.123.25
```

### <a name="find-the-disk"></a>ディスクの特定

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
sdc     3:0:0:0      50G
```

ここでは、`sdc` は 50 G であるため、これが目的のディスクです。 サイズだけではどのディスクかわからない場合は、ポータルの [VM] ページにアクセスし、 **[ディスク]** を選択して、 **[データ ディスク]** でディスクの LUN 番号を確認します。 


### <a name="format-the-disk"></a>ディスクのフォーマット

`parted` を使用してディスクをフォーマットします。ディスクのサイズが 2 テビバイト (TiB) 以上の場合は、GPT パーティション分割を使用する必要があります。2 TiB 未満の場合は、MBR または GPT のパーティション分割を使用することができます。 

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

次に、`mkdir` を使用して、ファイル システムをマウントするディレクトリを作成します。 次の例では、 `/datadrive` にディレクトリを作成します。

```bash
sudo mkdir /datadrive
```

`mount` を使用して、ファイル システムをマウントします。 次の例では、 `/dev/sdc1` パーティションを `/datadrive` マウント ポイントにマウントします。

```bash
sudo mount /dev/sdc1 /datadrive
```

### <a name="persist-the-mount"></a>マウントの永続化

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

この例では nano エディターを使用しているので、ファイルの編集が完了したら、`Ctrl+O` を使用してファイルを書き込み、`Ctrl+X` を使用してエディターを終了してください。

> [!NOTE]
> この後、fstab を編集せずにデータ ディスクを削除すると VM は起動できません。 ほとんどのディストリビューションでは、*nofail* または *nobootwait* fstab オプションが提供されています。 これにより起動時にディスクのマウントが失敗しても、システムを起動できます。 これらのパラメーターの詳細については、使用しているディストリビューションのドキュメントを参照してください。
>
> *nofail* オプションを使用すると、ファイル システムが壊れているか、ブート時にディスクが存在しない場合でも VM が起動されるようになります。 このオプションを指定しない場合、「[Cannot SSH to Linux VM due to FSTAB errors (FSTAB エラーが原因で Linux VM に SSH 接続できない)](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting)」で説明されているような動作が発生します。
>
> fstab の変更が原因で起動エラーが発生した場合は、VM へのコンソール アクセスに Azure VM シリアル コンソールを使用できます。 詳細については、[シリアル コンソールのドキュメント](/troubleshoot/azure/virtual-machines/serial-console-linux)を参照してください。

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

## <a name="troubleshooting"></a>トラブルシューティング

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>次のステップ

* [Linux マシンのパフォーマンスの最適化](/previous-versions/azure/virtual-machines/linux/optimization) に関する推奨事項を読んで、Linux VM が正しく構成されていることを確認します。
* ディスクを追加してストレージ容量を拡張し、 [RAID を構成](/previous-versions/azure/virtual-machines/linux/configure-raid) してパフォーマンスを強化します。
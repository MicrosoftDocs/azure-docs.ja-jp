---
title: chroot を使用して、LVM (論理ボリューム マネージャー) が使用されている場所に Linux VM を復旧する - Azure VM
description: LVM を使用した Linux VM の復旧。
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: Linux chroot LVM
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/24/2019
ms.author: vilibert
ms.openlocfilehash: 03e6f51d2ab7138675f7d79c04faa2e4dffec60c
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87825686"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Azure シリアル コンソールにアクセスできず、ディスク レイアウトが LVM (論理ボリューム マネージャー) を使用している場合の Linux VM のトラブルシューティング

このトラブルシューティング ガイドは、Linux VM が起動せず、ssh が使用できず、基になるファイル システム レイアウトが LVM (論理ボリューム マネージャー) を使用して構成されているシナリオで役立ちます。

## <a name="take-snapshot-of-the-failing-vm"></a>失敗した VM のスナップショットを作成する

影響を受けている VM のスナップショットを作成します。 

このスナップショットは、**復旧** VM にアタッチされます。 **スナップショット**の作成方法については、[こちら](../linux/snapshot-copy-managed-disk.md#use-azure-portal)の手順に従ってください。

## <a name="create-a-rescue-vm"></a>復旧 VM を作成する
通常、オペレーティング システムのバージョンが同一であるか類似している復旧 VM を使用することをお勧めします。 **リージョン**と**リソース グループ**は、影響を受けている VM と同じものを使用します

## <a name="connect-to-the-rescue-vm"></a>復旧 VM に接続する
SSH を使用して、**復旧** VM に接続します。 以下を使用して権限を昇格し、スーパー ユーザーになります

`sudo su -`

## <a name="attach-the-disk"></a>ディスクをアタッチする
前に作成したスナップショットから作成された**復旧** VM にディスクをアタッチします。

Azure portal -> **復旧** VM の選択 -> **[ディスク]** 

![ディスクの作成](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

フィールドに入力します。 新しいディスクに名前を割り当て、スナップショット、影響を受けている VM、復旧 VM と同じリソース グループを選択します。

**[ソースの種類]** は **[スナップショット]** です。
**[ソースのスナップショット]** は、先ほど作成した**スナップショット**の名前です。

![ディスク 2 を作成する](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

アタッチされるディスクのマウント ポイントを作成します。

`mkdir /rescue`

**fdisk -l** コマンドを実行して、スナップショット ディスクがアタッチされていることを確認し、使用可能なすべてのデバイスとパーティションを一覧表示します

`fdisk -l`

ほとんどのシナリオでは、アタッチされたスナップショット ディスクは **/dev/sdc** として表示され、 **/dev/sdc1** と **/dev/sdc2** の 2 つのパーティションが表示されます

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

**\*** はブート パーティションであることを示しています。両方のパーティションをマウントする必要があります。

**lsblk** コマンドを実行して、影響を受けている VM の LVM を確認します

`lsblk`

![lsblk を実行する](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


影響を受けている VM の LVM が表示されているか確認します。
表示されていない場合は、以下のコマンドを使用してそれらを有効にし、**lsblk** を再実行します。
続行する前に、アタッチされたディスクの LVM が表示されていることを確認してください。

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

/ (ルート) パーティションを含む論理ボリュームをマウントするパスを見つけます。 このパスには /etc/default/grub などの構成ファイルがあります

この例では、前の **lsblk** コマンドの出力を利用しているため、**rootvg-rootlv** はマウントすべき正しい**ルート** LV になっており、次のコマンドで使用できます。

次のコマンドの出力には、**ルート** LV のマウント先のパスが表示されます

`pvdisplay -m | grep -i rootlv`

![Rootlv](./media/chroot-logical-volume-manager/locate-rootlv.png)

次に、このデバイスを /rescue ディレクトリにマウントします

`mount /dev/rootvg/rootlv /rescue`

**ブート フラグ**が設定されているパーティションを /rescue/boot にマウントします

`
mount /dev/sdc1 /rescue/boot
`

**lsblk** コマンドを使用して、アタッチされたディスクのファイル システムが正しくマウントされていることを確認します

![lsblk を実行する](./media/chroot-logical-volume-manager/lsblk-output-1.png)

または、**df -Th** コマンドを使用します

![Df](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>chroot アクセスを取得する

**chroot** アクセスを取得すると、さまざまな修正を実行できます。これには、Linux ディストリビューションで微妙なバリエーションが存在します。

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

エラーが発生した場合 (例:

**chroot: failed to run command ‘/bin/bash’:No such file or directory**

)、**usr** 論理ボリュームのマウントを試行します

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> **chroot** 環境でコマンドを実行する場合、それらのコマンドが、ローカルの**復旧** VM に対してではなく、アタッチされている OS ディスクに対して実行されることに注意してください。 

コマンドは、ソフトウェアのインストール、削除、更新に使用できます。 エラーを修正するために、VM のトラブルシューティングを行います。


lsblk コマンドを実行します。/rescue は / で、/rescue/boot は /boot になっています ![Chrooted](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>修正を実行する

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>例 1 - 別のカーネルから起動するように VM を構成する

以前のカーネルから VM を強制的に起動するのは、現在インストールされているカーネルが破損している可能性があったり、アップグレードが正常に終了しなかったりした場合などによくあるシナリオです。


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*チュートリアル*

**grep** コマンドを実行すると、**grub.cfg** が認識しているカーネルが一覧表示されます。
![カーネル](./media/chroot-logical-volume-manager/kernels.png)

**grub2-editenv list** を実行すると、次回の起動時に読み込まれるカーネルが表示されます ![既定のカーネル](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2-set-default** は、別のカーネルに変更するために使用します ![Grub2 設定](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2-editenv list** を実行すると、次回の起動時に読み込まれるカーネルが表示されます ![新しいカーネル](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig** を実行すると、必要なバージョンを使用して grub.cfg が再構築されます ![Grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>例 2 - パッケージをアップグレードする

カーネルのアップグレードに失敗すると、VM が起動不可能になる場合があります。
パッケージの削除や再インストールを実行できるようにするには、すべての論理ボリュームをマウントします

**lvs** コマンドを実行して、それぞれの VM のマウントに使用できる **LV** を確認します。VM には移行されたものや他のクラウド プロバイダーに由来するものがあり、構成が異なります。

**chroot** 環境を終了し、必要な **LV** をマウントします

![詳細設定](./media/chroot-logical-volume-manager/advanced.png)

以下を実行して、**chroot** 環境にもう一度アクセスします

`chroot /rescue`

すべての LV がマウントされたパーティションとして表示されるはずです

![詳細設定](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

インストールされている**カーネル**をクエリします

![詳細設定](./media/chroot-logical-volume-manager/rpm-kernel.png)

必要に応じて**カーネル**を削除またはアップグレードします
![詳細](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>例 3 - シリアル コンソールを有効にする
Azure シリアル コンソールにアクセスできない場合は、Linux VM の GRUB 構成パラメーターを確認して修正します。 詳細については、[このドキュメント](./serial-console-grub-proactive-configuration.md)を参照してください

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>例 4 - 問題のある LVM スワップ ボリュームを使用したカーネルの読み込み

VM が完全に起動せず、**dracut** プロンプトにドロップすることがあります。
エラーの詳細については、Azure シリアル コンソールを使用するか、Azure portal -> [ブート診断] -> [シリアル ログ] を参照してください。


次のようなエラーが表示されることがあります。

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

この例では grub.cfg が **rd.lvm.lv=VG/SwapVol** という名前の LV を読み込むように構成されており、VM がこれを見つけられません。 この行は、LV SwapVol を参照しているカーネルがどのように読み込まれているかを示しています

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 問題のある LV を/etc/default/grub 構成から削除し、grub2.cfg をリビルドします


## <a name="exit-chroot-and-swap-the-os-disk"></a>chroot を終了し、OS ディスクをスワップします

問題が解決したら、復旧 VM からディスクをマウント解除してデタッチし、影響を受けた VM OS ディスクとスワップできるようにします。

```
exit
cd /
umount /rescue/proc/
umount /rescue/sys/
umount /rescue/dev/pts
umount /rescue/dev/
umount /rescue/boot
umount /rescue
```

復旧 VM からディスクをデタッチし、ディスク スワップを実行します。

ポータルの **[ディスク]** から VM を選択し、 **[デタッチ]** を選択します
![ディスクをデタッチする](./media/chroot-logical-volume-manager/detach-disk.png) 

変更を保存します ![デタッチを保存する](./media/chroot-logical-volume-manager/save-detach.png) 

ディスクが使用可能になり、影響を受けた VM の元の OS ディスクとスワップできるようになります。

Azure portal で、障害が発生している VM に移動し、 **[ディスク]**  ->  **[OS ディスクのスワップ]** の順に選択します
![ディスクのスワップ](./media/chroot-logical-volume-manager/swap-disk.png) 

フィールドに入力します。 **[ディスクの選択]** は、前の手順でデタッチしたスナップショット ディスクを指定します。 影響を受けた VM の VM 名も指定したら、 **[OK]** を選択します

![新しい OS ディスク](./media/chroot-logical-volume-manager/new-osdisk.png) 

VM が実行中で、ディスク スワップにより VM がシャットダウンされる場合は、ディスク スワップ操作が完了してから VM を再起動します。


## <a name="next-steps"></a>次のステップ
項目ごとに詳しい情報を確認できます。

 [Azure シリアル コンソール]( ./serial-console-linux.md)

[シングル ユーザー モード](./serial-console-grub-single-user-mode.md)
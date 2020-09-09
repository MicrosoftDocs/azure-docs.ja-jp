---
title: Linux 復旧 VM での chroot 環境
description: この記事では、Linux の復旧仮想マシン (VM) で chroot 環境のトラブルシューティングを行う方法について説明します。
mservices: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.date: 05/05/2020
ms.author: kaib
ms.openlocfilehash: d6605aa77cfa746f60fc3b23e120267a2614f7b1
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82868272"
---
# <a name="chroot-environment-in-a-linux-rescue-vm"></a>Linux 復旧 VM での chroot 環境

この記事では、Linux の復旧仮想マシン (VM) で chroot 環境のトラブルシューティングを行う方法について説明します。

## <a name="ubuntu-16x--ubuntu-18x"></a>Ubuntu 16.x と Ubuntu 18.x

1. 影響を受ける VM を停止するか、割り当てを解除します。
1. マネージド ディスクを使用して、同じリソース グループ (RSG) と場所に、同じ OS バージョンの復旧 VM イメージを作成します。
1. Azure portal を使用して、影響を受ける仮想マシンの OS ディスクのスナップショットを取得します。
1. OS ディスクのスナップショットからディスクを作成し、それを復旧 VM にアタッチします。
1. ディスクが作成されたら、復旧 VM で chroot 環境のトラブルシューティングを行います。

   1. 次のコマンドを使用し、ルート ユーザーとして VM にアクセスします。

      `#sudo su -`

   1. `dmesg` を使用してディスクを探します (別の方法を使用して新しいディスクを検出してもかまいません)。 次の例では、**SCSI** ディスクでのフィルター処理に **dmesg** を使用します。

      `dmesg | grep SCSI`

      出力は次の例のようになります。 この例では、**sdc** ディスクが必要です。

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 次のコマンドを使用して、chroot 環境にアクセスします。

      ```
      #mkdir /rescue
      #mount /dev/sdc1 /rescue
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. chroot 環境のトラブルシューティングを行います。

   1. 次のコマンドを使用して、chroot 環境を終了します。

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue
      ```

      > [!NOTE]
      > `unable to unmount /rescue` というエラーが発生する場合は、-l オプションを umount コマンドに追加します。
      >
      > 例: `umount -l /rescue`

1. 復旧 VM からディスクをデタッチし、元の VM でディスク スワップを実行します。
1. 元の VM を起動し、接続を確認します。

## <a name="rhelcentosoracle-6x--oracle-8x--rhelcentos-7x-with-raw-partitions"></a>RHEL/Centos/Oracle 6.x および Oracle 8.x、RHEL/Centos 7.x と未フォーマット パーティション

1. 影響を受ける VM を停止するか、割り当てを解除します。
1. マネージド ディスクを使用して、同じリソース グループ (RSG) と場所に、同じ OS バージョンの復旧 VM イメージを作成します。
1. Azure portal を使用して、影響を受ける仮想マシンの OS ディスクのスナップショットを取得します。
1. OS ディスクのスナップショットからディスクを作成し、それを復旧 VM にアタッチします。
1. ディスクが作成されたら、復旧 VM で chroot 環境のトラブルシューティングを行います。

   1. 次のコマンドを使用し、ルート ユーザーとして VM にアクセスします。

      `#sudo su -`

   1. `dmesg` を使用してディスクを探します (別の方法を使用して新しいディスクを検出してもかまいません)。 次の例では、**SCSI** ディスクでのフィルター処理に **dmesg** を使用します。

      `dmesg | grep SCSI`

      出力は次の例のようになります。 この例では、**sdc** ディスクが必要です。

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 次のコマンドを使用して、chroot 環境にアクセスします。

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc2 /rescue
      #mount -o nouuid /dev/sdc1 /rescue/boot/
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. chroot 環境のトラブルシューティングを行います。

   1. 次のコマンドを使用して、chroot 環境を終了します。

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue
      ```

      > [!NOTE]
      > `unable to unmount /rescue` というエラーが発生する場合は、-l オプションを umount コマンドに追加します。
      >
      > 例: `umount -l /rescue`

1. 復旧 VM からディスクをデタッチし、元の VM でディスク スワップを実行します。
1. 元の VM を起動し、接続を確認します。

## <a name="rhelcentos-7x-with-lvm"></a>RHEL/Centos 7.x と LVM

   > [!NOTE]
   > 元の VM の OS ディスクに論理ボリューム マネージャー (LVM) が含まれている場合は、OS ディスク上の未フォーマット パーティションが含まれるイメージを使用して、復旧 VM を作成します。

1. 影響を受ける VM を停止するか、割り当てを解除します。
1. マネージド ディスクを使用して、同じリソース グループ (RSG) と場所に、同じ OS バージョンの復旧 VM イメージを作成します。
1. Azure portal を使用して、影響を受ける仮想マシンの OS ディスクのスナップショットを取得します。
1. OS ディスクのスナップショットからディスクを作成し、それを復旧 VM にアタッチします。
1. ディスクが作成されたら、復旧 VM で chroot 環境のトラブルシューティングを行います。

   1. 次のコマンドを使用し、ルート ユーザーとして VM にアクセスします。

      `#sudo su -`

   1. `dmesg` を使用してディスクを探します (別の方法を使用して新しいディスクを検出してもかまいません)。 次の例では、**SCSI** ディスクでのフィルター処理に **dmesg** を使用します。

      `dmesg | grep SCSI`

      出力は次の例のようになります。 この例では、**sdc** ディスクが必要です。

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 次のコマンドを使用して、論理ボリューム グループをアクティブ化します。

      ```
      #vgscan --mknodes
      #vgchange -ay
      #lvscan
      ```

   1. `lsblk` コマンドを使用して、LVM 名を取得します。

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      ├─sdc3              8:3    0    2M  0 part
      ├─sdc4              8:4    0   63G  0 part
        ├─rootvg-tmplv  253:0    0    2G  0 lvm  
        ├─rootvg-usrlv  253:1    0   10G  0 lvm  
        ├─rootvg-optlv  253:2    0    2G  0 lvm  
        ├─rootvg-homelv 253:3    0    1G  0 lvm  
        ├─rootvg-varlv  253:4    0    8G  0 lvm  
        └─rootvg-rootlv 253:5    0    2G  0 lvm
      ```

   1. 次のコマンドを使用して、chroot 環境にアクセスします。

      ```
      #mkdir /rescue
      #mount /dev/mapper/rootvg-rootlv /rescue
      #mount /dev/mapper/rootvg-varlv /rescue/var
      #mount /dev/mapper/rootvg-homelv /rescue/home
      #mount /dev/mapper/rootvg-usrlv /rescue/usr
      #mount /dev/mapper/rootvg-tmplv /rescue/tmp
      #mount /dev/mapper/rootvg-optlv /rescue/opt
      #mount /dev/sdc2 /rescue/boot/
      #mount /dev/sdc1 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. chroot 環境のトラブルシューティングを行います。

   1. 次のコマンドを使用して、chroot 環境を終了します。

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue/home
      #umount /rescue/var
      #umount /rescue/usr
      #umount /rescue/tmp
      #umount /rescue/opt
      #umount /rescue
      ```

      > [!NOTE]
      > `unable to unmount /rescue` というエラーが発生する場合は、-l オプションを umount コマンドに追加します。
      >
      > 例: `umount -l /rescue`

1. 復旧 VM からディスクをデタッチし、元の VM でディスク スワップを実行します。
1. 元の VM を起動し、接続を確認します。

## <a name="rhel-8x-with-lvm"></a>RHEL 8.x と LVM

   > [!NOTE]
   > 元の VM の OS ディスクに論理ボリューム マネージャー (LVM) が含まれている場合は、OS ディスク上の未フォーマット パーティションが含まれるイメージを使用して、復旧 VM を作成します。

1. 影響を受ける VM を停止するか、割り当てを解除します。
1. マネージド ディスクを使用して、同じリソース グループ (RSG) と場所に、同じ OS バージョンの復旧 VM イメージを作成します。
1. Azure portal を使用して、影響を受ける仮想マシンの OS ディスクのスナップショットを取得します。
1. OS ディスクのスナップショットからディスクを作成し、それを復旧 VM にアタッチします。
1. ディスクが作成されたら、復旧 VM で chroot 環境のトラブルシューティングを行います。

   1. 次のコマンドを使用し、ルート ユーザーとして VM にアクセスします。

      `#sudo su -`

   1. `dmesg` を使用してディスクを探します (別の方法を使用して新しいディスクを検出してもかまいません)。 次の例では、**SCSI** ディスクでのフィルター処理に **dmesg** を使用します。

      `dmesg | grep SCSI`

      出力は次の例のようになります。 この例では、**sdc** ディスクが必要です。

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 次のコマンドを使用して、論理ボリューム グループをアクティブ化します。

      ```
      #vgscan --mknodes
      #vgchange -ay
      #lvscan
      ```

   1. `lsblk` コマンドを使用して、LVM 名を取得します。

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      │ ├─rootvg-tmplv  253:0    0    2G  0 lvm  
      │ ├─rootvg-usrlv  253:1    0   10G  0 lvm  
      │ ├─rootvg-homelv 253:2    0    1G  0 lvm  
      │ ├─rootvg-varlv  253:3    0    8G  0 lvm  
      │ └─rootvg-rootlv 253:4    0    2G  0 lvm  
      ├─sdc14             8:14   0    4M  0 part
      └─sdc15             8:15   0  495M  0 part
      ```

   1. 次のコマンドを使用して、chroot 環境にアクセスします。

      ```
      #mkdir /rescue
      #mount /dev/mapper/rootvg-rootlv /rescue
      #mount /dev/mapper/rootvg-varlv /rescue/var
      #mount /dev/mapper/rootvg-homelv /rescue/home
      #mount /dev/mapper/rootvg-usrlv /rescue/usr
      #mount /dev/mapper/rootvg-tmplv /rescue/tmp
      #mount /dev/sdc1 /rescue/boot/
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. chroot 環境のトラブルシューティングを行います。

   1. 次のコマンドを使用して、chroot 環境を終了します。

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue/home
      #umount /rescue/var
      #umount /rescue/usr
      #umount /rescue/tmp
      #umount /rescue
      ```

      > [!NOTE]
      > `unable to unmount /rescue` というエラーが発生する場合は、-l オプションを umount コマンドに追加します。
      >
      > 例: `umount -l /rescue`

1. 復旧 VM からディスクをデタッチし、元の VM でディスク スワップを実行します。
1. 元の VM を起動し、接続を確認します。

## <a name="oracle-7x"></a>Oracle 7.x

1. 影響を受ける VM を停止するか、割り当てを解除します。
1. マネージド ディスクを使用して、同じリソース グループ (RSG) と場所に、同じ OS バージョンの復旧 VM イメージを作成します。
1. Azure portal を使用して、影響を受ける仮想マシンの OS ディスクのスナップショットを取得します。
1. OS ディスクのスナップショットからディスクを作成し、それを復旧 VM にアタッチします。
1. ディスクが作成されたら、復旧 VM で chroot 環境のトラブルシューティングを行います。

   1. 次のコマンドを使用し、ルート ユーザーとして VM にアクセスします。

      `#sudo su -`

   1. `dmesg` を使用してディスクを探します (別の方法を使用して新しいディスクを検出してもかまいません)。 次の例では、**SCSI** ディスクでのフィルター処理に **dmesg** を使用します。

      `dmesg | grep SCSI`

      出力は次の例のようになります。 この例では、**sdc** ディスクが必要です。

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 次のコマンドを使用して、chroot 環境にアクセスします。

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc2 /rescue
      #mount -o nouuid /dev/sdc1 /rescue/boot/
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      ##chroot /rescue
      ```

   1. chroot 環境のトラブルシューティングを行います。

   1. 次のコマンドを使用して、chroot 環境を終了します。

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue
      ```

      > [!NOTE]
      > `unable to unmount /rescue` というエラーが発生する場合は、-l オプションを umount コマンドに追加します。
      >
      > 例: `umount -l /rescue`

1. 復旧 VM からディスクをデタッチし、元の VM でディスク スワップを実行します。
1. 元の VM を起動し、接続を確認します。

## <a name="suse-sles-12-sp4-suse-sles-12-sp4-for-sap---suse-sles-15-sp1-suse-sles-15-sp1-for-sap"></a>SUSE-SLES 12 SP4、SUSE-SLES 12 SP4 For SAP、SUSE-SLES 15 SP1、SUSE-SLES 15 SP1 For SAP

1. 影響を受ける VM を停止するか、割り当てを解除します。
1. マネージド ディスクを使用して、同じリソース グループ (RSG) と場所に、同じ OS バージョンの復旧 VM イメージを作成します。
1. Azure portal を使用して、影響を受ける仮想マシンの OS ディスクのスナップショットを取得します。
1. OS ディスクのスナップショットからディスクを作成し、それを復旧 VM にアタッチします。
1. ディスクが作成されたら、復旧 VM で chroot 環境のトラブルシューティングを行います。

   1. 次のコマンドを使用し、ルート ユーザーとして VM にアクセスします。

      `#sudo su -`

   1. `dmesg` を使用してディスクを探します (別の方法を使用して新しいディスクを検出してもかまいません)。 次の例では、**SCSI** ディスクでのフィルター処理に **dmesg** を使用します。

      `dmesg | grep SCSI`

      出力は次の例のようになります。 この例では、**sdc** ディスクが必要です。

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 次のコマンドを使用して、chroot 環境にアクセスします。

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc4 /rescue
      #mount -o nouuid /dev/sdc3 /rescue/boot/
      #mount /dev/sdc2 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. chroot 環境のトラブルシューティングを行います。

   1. 次のコマンドを使用して、chroot 環境を終了します。

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue
      ```

      > [!NOTE]
      > `unable to unmount /rescue` というエラーが発生する場合は、-l オプションを umount コマンドに追加します。
      >
      > 例: `umount -l /rescue`

1. 復旧 VM からディスクをデタッチし、元の VM でディスク スワップを実行します。
1. 元の VM を起動し、接続を確認します。

## <a name="next-steps"></a>次の手順

- [SSH 接続のトラブルシューティング](troubleshoot-ssh-connection.md)
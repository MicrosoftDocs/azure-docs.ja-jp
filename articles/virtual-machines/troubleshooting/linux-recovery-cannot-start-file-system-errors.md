---
title: ファイル システム エラーによる Linux VM の起動に関する問題のトラブルシューティング | Microsoft Docs
description: Linux VM を起動できない理由と問題の解決方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 10/09/2019
ms.author: v-six
ms.openlocfilehash: e26d4070bc9fabeb3467e7bdc805faad4e6646c3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526505"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>ファイル システム エラーによる Linux VM の起動に関する問題のトラブルシューティング

Secure Shell (SSH) を使用して Azure Linux 仮想マシン (VM) に接続することはできません。 [Azure portal](https://portal.azure.com/) 上でブート診断機能を実行すると、次の例のようなログエントリが表示されます。

## <a name="examples"></a>例

考えられるエラーの例を次に示します。

### <a name="example-1"></a>例 1 

```
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1 contains a file system with errors, check forced.
/dev/sda1: Inodes that were part of a corrupted orphan linked list found.
/dev/sda1: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY
```

### <a name="example-2"></a>例 2

```
EXT4-fs (sda1): INFO: recovery required on readonly filesystem
EXT4-fs (sda1): write access will be enabled during recovery
EXT4-fs warning (device sda1): ext4_clear_journal_err:4531: Filesystem error recorded from previous mount: IO failure
EXT4-fs warning (device sda1): ext4_clear_journal_err:4532: Making fs in need of filesystem check.
```

### <a name="example-3"></a>例 3

```
[  14.252404] EXT4-fs (sda1): Couldn’t remount RDWR because of unprocessed orphan inode list.  Please unmount/remount instead
An error occurred while mounting /.
```

### <a name="example-4"></a>例 4 

この例は、クリーン fsck によって発生します。 この場合、VM には追加のデータ ディスクも接続されています (/dev/sdc1 および /dev/sde1)。

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

この問題は、ファイル システムが正常にシャットダウンされなかった場合、またはストレージに関係する問題がある場合に発生する可能性があります。 この問題には、ハードウェアまたはソフトウェアのエラー、ドライバーまたはプログラムに関する問題、書き込みエラーなどが含まれます。重要なデータはバックアップを作成しておくことが常に重要です。 この記事で説明するツールは、ファイル システムの回復に役立つ場合がありますが、まだデータの損失が発生する可能性があります。

Linux には、いくつかのファイル システム チェッカーが用意されています。 Azure でのディストリビューションで最も一般的なものは次のとおりです。[FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific)、[E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)、および [Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair)。

## <a name="resolution"></a>解像度

この問題を解決するには、[シリアル コンソール](./serial-console-linux.md)を使用して、VM を緊急モードで起動し、該当するツールを使用してファイル システムを修復します。 ご利用の VM 上でシリアル コンソールが有効になっていない場合または機能していない場合は、この記事のセクション「[VM をオフライン修復する](#repair-the-vm-offline)」を参照してください。

## <a name="use-the-serial-console"></a>シリアル コンソールの使用

1. シリアル コンソールに接続します。

   > [!Note]
   > Linux 用のシリアル コンソールの使用の詳細については、次を参照してください。
   > * [シリアル コンソールを使用して GRUB とシングル ユーザー モードにアクセスする](../linux/serial-console-grub-single-user-mode.md)
   > * [SysRq および NMI 呼び出しにシリアル コンソールを使用する](./serial-console-nmi-sysrq.md)

2. [電源] アイコンボタンを選択してから、[VM の再起動] を選択します (シリアル コンソールが有効になっていない場合または正常に接続されていない場合、そのボタンは表示されません)。

   ![イメージ](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. VM を緊急モードで起動します。

4. ご利用のルート アカウントのパスワードを入力して、緊急モードにサインインします。

5. -n オプションを指定した xfs_repair を使用して、ファイル システムでのエラーを検出します。 次のサンプルでは、システム パーティションが /dev/sda1 であることを前提としています。 これを、ご利用の VM の適切な値に置き換えます。

   ```
   xfs_repair -n /dev/sda1
   ```

6. 次のコマンドを実行して、ファイル システムを修復します。

   ```
   xfs_repair /dev/sda1
   ```

7. "エラー: ファイル システムには、再生する必要があるログに重要なメタデータの変更があります" というエラー メッセージが表示された場合は、以下のように一時ディレクトリを作成して、ファイル システムをマウントします。

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. ディスクのマウントに失敗した場合は、-L オプション (ログを強制的にゼロに設定) を指定した xfs_repair コマンドを実行します。

   ```
   xfs_repair /dev/sda1 -L
   ```

9. 次に、ファイル システムをマウントしてみます。 ディスクが正常にマウントされた場合は、次の出力が表示されます。
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. VM を再起動して、問題が解決されたかどうかを確認します。

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>VM をオフライン修復する

1. VM のシステム ディスクをデータ ディスクとして復旧 VM (任意の動作中の Linux VM) に接続します。 これを行うには、[CLI コマンド](./troubleshoot-recovery-disks-linux.md)を使用することも、[VM 修復コマンド](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)を使用して復旧 VM の設定を自動化することもできます。

2. 接続したシステム ディスクのドライブ ラベルを検索します。 この例では、接続したシステム ディスクのラベルが /dev/sdc1 であると想定します。 これを、ご利用の VM の適切な値に置き換えます。

3. -n オプションを指定した xfs_repair を使用して、ファイル システムでのエラーを検出します。

   ```
   xfs_repair -n /dev/sdc1
   ```

4. 次のコマンドを実行して、ファイル システムを修復します。

   ```
   xfs_repair /dev/sdc1
   ```

5. "エラー: ファイル システムには、再生する必要があるログに重要なメタデータの変更があります" というエラー メッセージが表示された場合は、以下のように一時ディレクトリを作成して、ファイル システムをマウントします。

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   ディスクのマウントに失敗した場合は、-L オプション (ログを強制的にゼロに設定) を指定した xfs_repair コマンドを実行します。

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. 次に、ファイル システムをマウントしてみます。 ディスクが正常にマウントされた場合は、次の出力が表示されます。

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. 元の仮想ハード ディスクをマウント解除し、切断してから、元のシステム ディスクを基に VM を作成します。 これを行うには、[CLI コマンド](troubleshoot-recovery-disks-linux.md)を使用することも、[VM 修復コマンド](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)を使用することも (復旧 VM の作成にそれらを使用した場合) できます。

8. 問題が解決されたかどうかを確認します。

## <a name="next-steps"></a>次のステップ

* [Azure CLI 2.0 で OS ディスクを復旧 VM に接続して Linux VM のトラブルシューティングを行う](./troubleshoot-recovery-disks-linux.md)
* [ポータルを利用し、データ ディスクを Linux VM に接続する](../linux/attach-disk-portal.md)

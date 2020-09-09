---
title: fstab エラーによる Linux VM の起動に関する問題のトラブルシューティング | Microsoft Docs
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
ms.openlocfilehash: cf27a842d37e96c82370e9b9b81763c8a5d1f7c9
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509054"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>fstab エラーによる Linux VM の起動に関する問題のトラブルシューティング

Secure Shell (SSH) 接続を使用して Azure Linux 仮想マシン (VM) に接続することはできません。 [Azure portal](https://portal.azure.com/) 上で [ブート診断](./boot-diagnostics.md)機能を実行すると、次の例のようなログ エントリが表示されます。

## <a name="examples"></a>例

考えられるエラーの例を次に示します。

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>例 1: ディスクは、汎用一意識別子 (UUID) ではなく、SCSI ID でマウントされています

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>例 2: CentOS 上に未接続のデバイスがありません

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sdd1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sde1: nonexistent device ("nofail" fstab option may be used to skip this device)

[/sbin/fsck.ext3 (1) — /CODE] sck.ext3 -a /dev/sdc1
fsck.ext3: No such file or directory while trying to open /dev/sdc1

/dev/sdc1:
The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:

e2fsck -b 8193 <device>

[/sbin/fsck.xfs (1) — /GLUSTERDISK] fsck.xfs -a /dev/sdd1
/sbin/fsck.xfs: /dev/sdd1 does not exist
[/sbin/fsck.ext3 (1) — /DATATEMP] fsck.ext3 -a /dev/sde1 fsck.ext3: No such file or directory while trying to open /dev/sde1
```

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>例 3: fstab の構成が間違っているため、またはディスクが未接続状態になったため、VM を起動できません

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>例 4: シリアル ログ エントリに正しくない UUID が表示されています

```
Checking filesystems
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 70442/1905008 files, 800094/7608064 blocks
[/sbin/fsck.ext4 (1) — /datadrive] fsck.ext4 -a UUID="<UUID>"
fsck.ext4: Unable to resolve UUID="<UUID>"
[FAILED

*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
*** Warning — SELinux is active
*** Disabling security enforcement for system recovery.
*** Run 'setenforce 1' to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

この問題は、ファイル システム テーブル (fstab) 構文が正しくない場合、または "/etc/fstab" ファイル内のエントリにマップされた必須のデータ ディスクが VM に接続されていない場合に発生することがあります。

## <a name="resolution"></a>解像度

この問題を解決するには、Azure Virtual Machines のシリアル コンソールを使用して、緊急モードで VM を起動します。 次に、ツールを使用して、ファイル システムを修復します。 ご利用の VM 上でシリアル コンソールが有効になっていない場合は、「[VM をオフライン修復する](#repair-the-vm-offline)」セクションに進んでください。

## <a name="use-the-serial-console"></a>シリアル コンソールの使用

### <a name="using-single-user-mode"></a>シングル ユーザー モードを使用する

1. [シリアル コンソール](./serial-console-linux.md)に接続します。
2. シリアル コンソールを使用してシングル ユーザー モード[シングル ユーザー モード](../linux/serial-console-grub-single-user-mode.md)にします
3. VM が起動されてシングル ユーザー モードになります。 任意のテキスト エディターを使用して fstab ファイルを開きます。 

   ```
   # nano /etc/fstab
   ```

4. 一覧されたファイル システムを確認します。 fstab ファイル内の各行は、VM の起動時にマウントされるファイル システムを示しています。 fstab ファイルの構文の詳細については、man fstab コマンドを実行してください。 起動エラーのトラブルシューティングを行うには、各行をレビューして、その構造と内容の両方が正しいことを確認してください。

   > [!Note]
   > * 各行のフィールドは、タブまたはスペースによって区切られます。 空白行は無視されます。 先頭文字がシャープ記号 (#) になっている行はコメントです。 コメント行は fstab ファイル内に残しておくことができますが、処理されません。 よくわからない fstab 行については、削除するのでなく、コメント化することをお勧めします。
   > * VM を復旧して起動する場合、ファイル システムのパーティションは、必須のパーティションのみとする必要があります。 VM では、コメント化された追加のパーティションに関するアプリケーション エラーが発生する可能性があります。 ただし、追加のパーティションを使用せずに VM を起動する必要があります。 コメント行をいずれも後でコメント解除できます。
   > * ファイル システム パーティションの UUID を使用して、データ ディスクを Azure VM にマウントすることをお勧めします。 たとえば、``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"`` コマンドを実行します。
   > * ファイル システムの UUID を確認するには、blkid コマンドを実行します。 構文の詳細については、man blkid コマンドを実行してください。
   > * nofail オプションを使用すると、ファイル システムが壊れている場合または起動時にファイル システムが存在しない場合でも、VM を確実に起動できます。 VM を起動するのに必要のないパーティションでエラーが発生した後も起動が続行されるようにするために、fstab ファイル内の nofail オプションを使用することをお勧めします。

5. fstab ファイル内の正しくない行または不要な行をいずれも変更またはコメント アウトして、VM が正常に起動できるようにします。

6. 変更内容を fstab ファイルに保存します。

7. 次のコマンドを使用して VM を再起動します。
   
   ```
   # reboot -f
   ```
> [!Note]
   > また、"Ctrl + x" コマンドを使用して VM を再起動することもできます。


8. エントリのコメント化または修正が成功した場合、ポータル内に bash プロンプトが表示されます。 VM に接続できるかどうかを確認します。

### <a name="using-root-password"></a>ルート パスワードを使用する

1. [シリアル コンソール](./serial-console-linux.md)に接続します。
2. ローカル ユーザーとパスワードを使用してシステムにサインインします。

   > [!Note]
   > シリアル コンソールでは、SSH キーを使用してシステムにサインインすることはできません。

3. ディスクがマウントされなかったことを示すエラーを探します。 次の例では、もはや存在しないディスクにシステムが接続しようとしました。

   ```
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view
   system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance
   (or type Control-D to continue):
   ```

4. ルート パスワード (Red Hat ベースの VM) を使用して VM に接続します。

5. 任意のテキスト エディターを使用して fstab ファイルを開きます。 ディスクがマウントされたら、Nano に対して次のコマンドを実行します。

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. 一覧されたファイル システムを確認します。 fstab ファイル内の各行は、VM の起動時にマウントされるファイル システムを示しています。 fstab ファイルの構文の詳細については、man fstab コマンドを実行してください。 起動エラーのトラブルシューティングを行うには、各行をレビューして、その構造と内容の両方が正しいことを確認してください。

   > [!Note]
   > * 各行のフィールドは、タブまたはスペースによって区切られます。 空白行は無視されます。 先頭文字がシャープ記号 (#) になっている行はコメントです。 コメント行は fstab ファイル内に残しておくことができますが、処理されません。 よくわからない fstab 行については、削除するのでなく、コメント化することをお勧めします。
   > * VM を復旧して起動する場合、ファイル システムのパーティションは、必須のパーティションのみとする必要があります。 VM では、コメント化された追加のパーティションに関するアプリケーション エラーが発生する可能性があります。 ただし、追加のパーティションを使用せずに VM を起動する必要があります。 コメント行をいずれも後でコメント解除できます。
   > * ファイル システム パーティションの UUID を使用して、データ ディスクを Azure VM にマウントすることをお勧めします。 たとえば、``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"`` コマンドを実行します。
   > * ファイル システムの UUID を確認するには、blkid コマンドを実行します。 構文の詳細については、man blkid コマンドを実行してください。
   > * nofail オプションを使用すると、ファイル システムが壊れている場合または起動時にファイル システムが存在しない場合でも、VM を確実に起動できます。 VM を起動するのに必要のないパーティションでエラーが発生した後も起動が続行されるようにするために、fstab ファイル内の nofail オプションを使用することをお勧めします。

7. fstab ファイル内の正しくない行または不要な行をいずれも変更またはコメント アウトして、VM が正常に起動できるようにします。

8. 変更内容を fstab ファイルに保存します。

9. 仮想マシンを再起動します。

10. エントリのコメント化または修正が成功した場合、ポータル内に bash プロンプトが表示されます。 VM に接続できるかどうかを確認します。

11. mount –a コマンドを実行して、fstab の変更をテストする場合は、ご利用のマウント ポイントを確認します。 エラーがなければ、そのマウント ポイントは良好です。

## <a name="repair-the-vm-offline"></a>VM をオフライン修復する

1. VM のシステム ディスクをデータ ディスクとして復旧 VM (任意の動作中の Linux VM) に接続します。 これを行うには、[CLI コマンド](./troubleshoot-recovery-disks-linux.md)を使用することも、[VM 修復コマンド](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)を使用して復旧 VM の設定を自動化することもできます。

2. システム ディスクをデータ ディスクとして復旧 VM にマウントしたら、変更を加える前に fstab ファイルをバックアップしてから、次の手順に従って fstab ファイルを修正します。

3. ディスクがマウントされなかったことを示すエラーを探します。 次の例では、もはや存在しないディスクにシステムが接続しようとしました。

   ```output
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance (or type Control-D to continue):
   ```

4. ルート パスワード (Red Hat ベースの VM) を使用して VM に接続します。

5. 任意のテキスト エディターを使用して fstab ファイルを開きます。 ディスクがマウントされたら、Nano に対して次のコマンドを実行します。 復旧 VM にある fstab ファイルに対してではなく、マウントされたディスクに置かれた fstab ファイルに対して作業していることを確認します。

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. 一覧されたファイル システムを確認します。 fstab ファイル内の各行は、VM の起動時にマウントされるファイル システムを示しています。 fstab ファイルの構文の詳細については、man fstab コマンドを実行してください。 起動エラーのトラブルシューティングを行うには、各行をレビューして、その構造と内容の両方が正しいことを確認してください。

   > [!Note]
   > * 各行のフィールドは、タブまたはスペースによって区切られます。 空白行は無視されます。 先頭文字がシャープ記号 (#) になっている行はコメントです。 コメント行は fstab ファイル内に残しておくことができますが、処理されません。 よくわからない fstab 行については、削除するのでなく、コメント化することをお勧めします。
   > * VM を復旧して起動する場合、ファイル システムのパーティションは、必須のパーティションのみとする必要があります。 VM では、コメント化された追加のパーティションに関するアプリケーション エラーが発生する可能性があります。 ただし、追加のパーティションを使用せずに VM を起動する必要があります。 コメント行をいずれも後でコメント解除できます。
   > * ファイル システム パーティションの UUID を使用して、データ ディスクを Azure VM にマウントすることをお勧めします。 たとえば、``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"`` コマンドを実行します。
   > * ファイル システムの UUID を確認するには、blkid コマンドを実行します。 構文の詳細については、man blkid コマンドを実行してください。 回復するディスクは現在新しい VM にマウントされていることに注意してください。 UUID は一貫している必要がありますが、この VM 上ではデバイスのパーティション ID ("/dev/sda1" など) が異なります。 システム以外の VHD に配置されている、障害が発生した元の VM のファイル システム パーティションを、復旧 VM で [CLI コマンドを使用](./troubleshoot-recovery-disks-linux.md)して利用することはできません。
   > * nofail オプションを使用すると、ファイル システムが壊れている場合または起動時にファイル システムが存在しない場合でも、VM を確実に起動できます。 VM を起動するのに必要のないパーティションでエラーが発生した後も起動が続行されるようにするために、fstab ファイル内の nofail オプションを使用することをお勧めします。

7. fstab ファイル内の正しくない行または不要な行をいずれも変更またはコメント アウトして、VM が正常に起動できるようにします。

8. 変更内容を fstab ファイルに保存します。

9. 仮想マシンを再起動するか、元の VM を再構築します。

10. エントリのコメント化または修正が成功した場合、ポータル内に bash プロンプトが表示されます。 VM に接続できるかどうかを確認します。

11. mount –a コマンドを実行して、fstab の変更をテストする場合は、ご利用のマウント ポイントを確認します。 エラーがなければ、そのマウント ポイントは良好です。

12. 元の仮想ハード ディスクをマウント解除し、切断してから、元のシステム ディスクを基に VM を作成します。 これを行うには、[CLI コマンド](troubleshoot-recovery-disks-linux.md)を使用することも、[VM 修復コマンド](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)を使用することも (復旧 VM の作成にそれらを使用した場合) できます。

13. VM をもう一度作成し、SSH 経由でその VM に接続できるようになったら、次の操作を行います。
    * 復旧中に変更またはコメント アウトされた fstab 行を確認します。
    * UUID と nofail オプションを適切に使用していることを確認します。
    * VM を再起動する前に、fstab の変更をテストします。 これを行うには、コマンド ``$ sudo mount -a`` を使用します。
    * 将来の回復シナリオで使用できるように、修正した fstab ファイルの追加のコピーを作成します。

## <a name="next-steps"></a>次のステップ

* [Azure CLI 2.0 で OS ディスクを復旧 VM に接続して Linux VM のトラブルシューティングを行う](./troubleshoot-recovery-disks-linux.md)
* [Azure portal で OS ディスクを復旧 VM に接続して Linux VM のトラブルシューティングを行う](./troubleshoot-recovery-disks-portal-linux.md)

---
title: Azure VM 上でローカルの Linux パスワードをリセットする方法 | Microsoft Docs
description: Azure VM 上でローカルの Linux パスワードをリセットする手順を紹介します
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 08/20/2019
ms.author: delhan
ms.openlocfilehash: 8fc51dfb90158316b3fe6c11b5265f1cf3251505
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641047"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Azure VM 上でローカルの Linux パスワードをリセットする方法

この記事では、ローカルの Linux 仮想マシン (VM) のパスワードをリセットする方法をいくつか紹介します。 ユーザー アカウントが期限切れになっている場合や、単に新しいアカウントを作成したい場合は、以下の方法で新しいローカル管理者アカウントを作成し、VM へのアクセスを再取得することができます。

## <a name="symptoms"></a>現象

VM にログインすることができず、使用したパスワードが正しくないことを示すメッセージが表示される。 さらに、Azure portal で VMAgent を使ってパスワードをリセットすることができない。

## <a name="manual-password-reset-procedure"></a>パスワードの手動リセットの手順

> [!NOTE]
> 次の手順は、アンマネージド ディスクを使用する VM には適用されません。

1. 影響のあった VM の OS ディスクのスナップショットを取得し、スナップショットからディスクを作成して、トラブルシューティング用 VM にディスクをアタッチします。 詳細については、[Azure portal を使用した OS ディスクの復旧 VM へのアタッチによる Windows VM のトラブルシューティング](troubleshoot-recovery-disks-portal-linux.md)に関するページを参照してください。

2. リモート デスクトップを使用してトラブルシューティング用 VM に接続します。

3.  トラブルシューティング用 VM 上で次の SSH コマンドを実行して、スーパーユーザーになります。

    ```bash
    sudo su
    ```

4.  **fdisk -l** を実行するか、システム ログを見て、新たにアタッチされたディスクを探します。 マウントするドライブ名を特定します。 次に、テンポラル VM 上で、関連するログ ファイルを参照します。

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    以下に、grep コマンドの出力例を示します。

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  **tempmount** と呼ばれるマウント ポイントを作成します。

    ```bash
    mkdir /tempmount
    ```

6.  マウント ポイントに OS ディスクをマウントします。 通常は、*sdc1* または *sdc2* をマウントする必要があります。 これは、切断したマシン ディスクの */etc* ディレクトリのホスティング パーティションによって決まります。

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  変更を加える前に、コア資格情報ファイルのコピーを作成します。

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  該当するユーザーのパスワードをリセットします。

    ```bash
    passwd <<USER>> 
    ```

9.  変更したファイルを、切断したマシンのディスク上の正しい場所に移動します。

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. ルートに戻り、ディスクをマウント解除します。

    ```bash
    cd /
    umount /tempmount
    ```

11. Azure portal で、トラブルシューティング VM からディスクの接続を切断します。

12. [影響を受けている VM の OS ディスクを変更します](troubleshoot-recovery-disks-portal-linux.md#swap-the-os-disk-for-the-vm)。

## <a name="next-steps"></a>次の手順

* [OS ディスクを別の Azure VM にアタッチして Azure VM のトラブルシューティングを行う](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: VHD から VM を削除して再デプロイする方法](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)

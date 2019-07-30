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
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: d96d75f4f2623476f7af4e6eea930c1f2c503e3a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60306953"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Azure VM 上でローカルの Linux パスワードをリセットする方法

この記事では、ローカルの Linux 仮想マシン (VM) のパスワードをリセットする方法をいくつか紹介します。 ユーザー アカウントが期限切れになっている場合や、単に新しいアカウントを作成したい場合は、以下の方法で新しいローカル管理者アカウントを作成し、VM へのアクセスを再取得することができます。

## <a name="symptoms"></a>現象

VM にログインすることができず、使用したパスワードが正しくないことを示すメッセージが表示される。 さらに、Azure portal で VMAgent を使ってパスワードをリセットすることができない。

## <a name="manual-password-reset-procedure"></a>パスワードの手動リセットの手順

1.  VM を削除し、アタッチされているディスクを維持します。

2.  OS ドライブをデータ ディスクとして、同じ場所の別のテンポラル VM にアタッチします。

3.  テンポラル VM 上で次の SSH コマンドを実行して、スーパーユーザーになります。

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

11. 管理ポータルからディスクをデタッチします。

12. VM を再作成します。

## <a name="next-steps"></a>次の手順

* [OS ディスクを別の Azure VM にアタッチして Azure VM のトラブルシューティングを行う](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: VHD から VM を削除して再デプロイする方法](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)

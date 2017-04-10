---
title: "Azure CLI 1.0 で SMB を使用して Linux VM に Azure File Storage をマウントする | Microsoft Docs"
description: "SMB を使用して Linux VM に Azure File Storage をマウントする方法"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/07/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 892e3c62a2ad4dc4fd0691874d46bb296e379524
ms.openlocfilehash: ac0623a32fd1fb4a3dd9e1dd06f457da6ce56199
ms.lasthandoff: 02/27/2017


---

# <a name="mount-azure-file-storage-on-linux-vms-by-using-smb-with-azure-cli-10"></a>Azure CLI 1.0 で SMB を使用して Linux VM に Azure File Storage をマウントする

この記事では、サーバー メッセージ ブロック (SMB) プロトコルを使用して、Linux VM に Azure File Storage をマウントする方法を説明します。 File Storage では、標準の SMB プロトコルを使用したクラウドでのファイル共有を提供します。 要件は次のとおりです。

* [Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)
* [Secure Shell (SSH) パブリック キー ファイルおよびプライベート キー ファイル](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="cli-versions-to-use"></a>使用する CLI バージョン
次のコマンド ライン インターフェイス (CLI) バージョンのいずれかを使用してタスクを実行できます。

- [Azure CLI 1.0](#quick-commands) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI (本記事)
- [Azure CLI 2.0](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - Resource Manager デプロイメント モデル用の次世代 CLI


## <a name="quick-commands"></a>クイック コマンド
タスクをすばやく実行するには、このセクションの手順に従ってください。 詳細情報とコンテキストを確認する場合は、「[詳細なチュートリアル](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough)」から始めてください。

### <a name="prerequisites"></a>前提条件
* リソース グループ
* Azure 仮想ネットワーク
* SSH 受信が設定されたネットワーク セキュリティ グループ
* サブネット
* Azure ストレージ アカウント
* Azure ストレージ アカウント キー
* Azure File Storage 共有
* Linux VM

各例を独自の設定に置き換えてください。

### <a name="create-a-directory-for-the-local-mount"></a>ローカル マウント用のディレクトリを作成します。

```bash
mkdir -p /mnt/mymountpoint
```

### <a name="mount-the-file-storage-smb-share-to-the-mount-point"></a>File Storage SMB 共有をマウント ポイントにマウントする

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>再起動後もマウントを維持する
`/etc/fstab` に次の行を追加します。

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>詳細なチュートリアル

File Storage では、標準の SMB プロトコルを使用したクラウドでのファイル共有を提供します。 File Storage の最新のリリースでは、SMB 3.0 をサポートしている OS からファイル共有をマウントすることもできます。 Linux で SMB マウントを使用すると、SLA でサポートされる信頼性の高い永続的なアーカイブ格納場所に簡単にバックアップできます。

File Storage でホストされている SMB マウントに VM からファイルを移動すると、ログをデバッグする際に役立ちます。 同じ SMB 共有を、Mac、Linux、または Windows ワークステーションにローカルでマウントできるためです。 SMB プロトコルは大量のログ記録を処理するように構築されていないため、Linux やアプリケーションのログをリアルタイムでストリーミングする場合、SMB は最適なソリューションではありません。 Linux やアプリケーションのログ出力を収集する場合は、Fluentd などの統合された専用のログ記録レイヤー ツールの方が SMB よりも適しています。

この詳細なチュートリアルでは、まず File Storage 共有を作成するために必要な前提条件を作成し、次に SMB 経由で Linux VM にマウントします。

1. 次のコードを使用して、Azure ストレージ アカウントを作成します。

    ```azurecli
    azure storage account create myStorageAccount \
    --sku-name lrs \
    --kind storage \
    -l westus \
    -g myResourceGroup
    ```

2. ストレージ アカウント キーを表示します。

    ストレージ アカウントを作成すると、サービスを中断させることなく交互に使用できるように、アカウント キーがペアで作成されます。 ペアの&2; つ目のキーに切り替えたら、新しいキー ペアを作成します。 新しいストレージ アカウント キーは必ずペアで作成されるので、すぐに切り換えることができる未使用のストレージ キーを常に&1; つ以上確保できます。 ストレージ アカウント キーを表示するには、次のコードを使用します。

    ```azurecli
    azure storage account keys list myStorageAccount \
    --resource-group myResourceGroup
    ```
3. File Storage 共有を作成します。

    File Storage 共有に SMB 共有が含まれます。 クォータは常にギガバイト (GB) 単位で表されます。 File Storage 共有を作成するには、次のコードを使用します。

    ```azurecli
    azure storage share create mystorageshare \
    --quota 10 \
    --account-name myStorageAccount \
    --account-key nPOgPR<--snip-->4Q==
    ```

4. マウント ポイント ディレクトリを作成します。

    SMB 共有のマウント先となる、Linux ファイル システムのローカル ディレクトリを作成する必要があります。 ローカル マウント ディレクトリに書き込まれるファイルや読み取られるファイルは、File Storage でホストされる SMB 共有に転送されます。 ディレクトリを作成するには、次のコードを使用します。

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

5. 次のコードを使用して SMB 共有をマウントします。

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
    ```

6. 再起動中に SMB マウントを維持します。

    Linux VM を再起動すると、マウントされた SMB 共有はシャットダウン中にマウント解除されます。 起動時に SMB 共有を再マウントするには、Linux の /etc/fstab に行を追加する必要があります。 Linux では、fstab ファイルを使用して、起動プロセスでマウントする必要があるファイル システムを示します。 SMB 共有を追加することによって、File Storage 共有は Linux VM に永続的にマウントされるファイル システムとして設定されます。 File Storage SMB 共有を新しい VM に追加するには、cloud-init を使用します。

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>次のステップ

- [cloud-init を利用し、作成時に Linux VM をカスタマイズする](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Linux VM へのディスクの追加](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure CLI を使って Linux VM のディスクを暗号化する](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


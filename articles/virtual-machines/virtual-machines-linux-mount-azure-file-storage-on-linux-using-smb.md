---
title: "SMB を使用して Linux VM に Azure File Storage をマウントする |Microsoft Docs"
description: "Azure CLI 2.0 で SMB を使用して Linux VM に Azure File Storage をマウントする方法"
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
ms.date: 02/13/2017
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: ff8ee82704443f3debd96ce163da810af9bb4757
ms.openlocfilehash: 92db8008eba1deb6b649df54b83e4ab0bc19d14d
ms.lasthandoff: 02/27/2017


---

# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>SMB を使用して Linux VM に Azure File Storage をマウントする

この記事では、Azure CLI 2.0 で SMB マウントを使用して、Linux VM で Azure File Storage サービスを利用する方法を説明します。 Azure File Storage は、標準の SMB プロトコルを使用したクラウドでのファイル共有を提供します。 これらの手順は、[Azure CLI 1.0](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して実行することもできます。 要件は次のとおりです。

- [Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)
- [SSH パブリック キー ファイルおよびプライベート キー ファイル](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="quick-commands"></a>クイック コマンド

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
これを行うには、`/etc/fstab` に次の行を追加します。

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>詳細なチュートリアル

File Storage では、標準の SMB プロトコルを使用したクラウドでのファイル共有を提供します。 File Storage の最新のリリースでは、SMB 3.0 をサポートしている OS からファイル共有をマウントすることもできます。 Linux で SMB マウントを使用すると、SLA でサポートされる信頼性の高い永続的なアーカイブ格納場所に簡単にバックアップできます。

File Storage でホストされている SMB マウントに VM からファイルを移動すると、ログをデバッグする際に役立ちます。 同じ SMB 共有を、Mac、Linux、または Windows ワークステーションにローカルでマウントできるためです。 SMB プロトコルは大量のログ記録を処理するように構築されていないため、Linux やアプリケーションのログをリアルタイムでストリーミングする場合、SMB は最適なソリューションではありません。 Linux やアプリケーションのログ出力を収集する場合は、Fluentd などの統合された専用のログ記録レイヤー ツールの方が SMB よりも適しています。

この詳細なチュートリアルでは、まず File Storage 共有を作成するために必要な前提条件を作成し、次に SMB 経由で Linux VM にマウントします。

1. [az group create](/cli/azure/group#create) を使用して、ファイル共有を保持するリソース グループを作成します。

    "米国西部" という場所に `myResourceGroup` という名前のリソース グループを作成するには、次の例を使用します。

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

2. [az storage account create](/cli/azure/storage/account#create) を使用して、実際のファイルを格納する Azure ストレージ アカウントを作成します。

    Standard_LRS ストレージ SKU を使用して mystorageaccount という名前のストレージ アカウントを作成するには、次の例を使用します。

    ```azurecli
    az storage account create --resource-group myResourceGroup \
        --name mystorageaccount \
        --location westus \
        --sku Standard_LRS
    ```

3. ストレージ アカウント キーを表示します。

    ストレージ アカウントを作成すると、サービスを中断させることなく交互に使用できるように、アカウント キーがペアで作成されます。 ペアの&2; つ目のキーに切り替えたら、新しいキー ペアを作成します。 新しいストレージ アカウント キーは必ずペアで作成されるので、すぐに切り換えることができる未使用のストレージ アカウント キーを常に&1; つ以上確保できます。

    [az storage account keys list](/cli/azure/storage/account/keys#list) を使用して、ストレージ アカウント キーを表示します。 次の例では、`mystorageaccount` という名前のストレージ アカウントのストレージ アカウント キーが表示されます。

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount
    ```

    1 つのキーを抽出するには、`--query` フラグを使用します。 次の例は、最初のキー (`[0]`) を抽出します。

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount \
        --query '[0].{Key:value}' --output tsv
    ```

4. File Storage 共有を作成します。

    [az storage share create](/cli/azure/storage/share#create) を使用して、File Storage 共有に SMB 共有を含めます。 クォータは常にギガバイト (GB) 単位で表されます。 前の `az storage account keys list` コマンドで取得したキーのいずれかを渡します。 次の例を使用して、mystorageshare という名前の 10 GB クオータの共有を作成します。

    ```azurecli
    az storage share create --name mystorageshare \
        --quota 10 \
        --account-name mystorageaccount \
        --account-key nPOgPR<--snip-->4Q==
    ```

5. マウント ポイント ディレクトリを作成します。

    SMB 共有のマウント先となる、Linux ファイル システムのローカル ディレクトリを作成します。 ローカル マウント ディレクトリに書き込まれるファイルや読み取られるファイルは、File Storage でホストされる SMB 共有に転送されます。 /mnt/mymountdirectory にローカル ディレクトリを作成するには、次の例を使用します。

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

6. ローカル ディレクトリに SMB 共有をマウントします。

    次の例のように、マウントの資格情報として独自のストレージ アカウント ユーザー名とストレージ アカウント キーを指定します。

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
    ```

7. 再起動中に SMB マウントを維持します。

    Linux VM を再起動すると、マウントされた SMB 共有はシャットダウン中にマウント解除されます。 起動時に SMB 共有を再マウントするには、Linux の /etc/fstab に行を追加します。 Linux では、fstab ファイルを使用して、起動プロセスでマウントする必要があるファイル システムを示します。 SMB 共有を追加することによって、File Storage 共有は Linux VM に永続的にマウントされるファイル システムとして設定されます。 File Storage SMB 共有を新しい VM に追加するには、cloud-init を使用します。

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>次のステップ

- [cloud-init を利用し、作成時に Linux VM をカスタマイズする](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Linux VM へのディスクの追加](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure CLI を使って Linux VM のディスクを暗号化する](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


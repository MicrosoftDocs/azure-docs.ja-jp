---
title: SMB を使用して Linux VM に Azure File Storage をマウントする |Microsoft Docs
description: Azure CLI で SMB を使用して Linux VM に Azure File Storage をマウントする方法
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: cynthn
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: c394b013b057a78e99cafc0adde9727d0a75a87c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091820"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>SMB を使用して Linux VM に Azure File Storage をマウントする

この記事では、Azure CLI で SMB マウントを使用して、Linux VM で Azure File Storage サービスを利用する方法を説明します。 Azure File Storage は、標準の SMB プロトコルを使用したクラウドでのファイル共有を提供します。 

File Storage では、標準の SMB プロトコルを使用したクラウドでのファイル共有を提供します。 SMB 3.0 をサポートしている任意の OS から、ファイル共有をマウントできます。 Linux で SMB マウントを使用すると、SLA でサポートされる信頼性の高い永続的なアーカイブ格納場所に簡単にバックアップできます。

File Storage でホストされている SMB マウントに VM からファイルを移動すると、ログをデバッグする際に役立ちます。 同じ SMB 共有を、Mac、Linux、または Windows ワークステーションにローカルでマウントできます。 SMB プロトコルは大量のログ記録を処理するように構築されていないため、Linux やアプリケーションのログをリアルタイムでストリーミングする場合、SMB は最適なソリューションではありません。 Linux やアプリケーションのログ出力を収集する場合は、Fluentd などの統合された専用のログ記録レイヤー ツールの方が SMB よりも適しています。

このガイドでは、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、**az --version** を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 


## <a name="create-a-resource-group"></a>リソース グループの作成

*myResourceGroup* という名前のリソース グループを "*米国東部*" に作成します。

```bash
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[az storage account create](/cli/azure/storage/account) を使用して、作成したリソース グループ内に新しいストレージ アカウントを作成します。 この例では、*mySTORAGEACCT\<random number>* という名前のストレージ アカウントを作成し、そのストレージ アカウントの名前を変数 **STORAGEACCT** に設定します。 ストレージ アカウント名は一意にする必要があります。`$RANDOM` を使用し、末尾に番号を追加して、一意にします。Ⅰ

```bash
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>ストレージ キーの取得

ストレージ アカウントを作成すると、サービスを中断させることなく交互に使用できるように、アカウント キーがペアで作成されます。 ペアの 2 つ目のキーに切り替えたら、新しいキー ペアを作成します。 新しいストレージ アカウント キーは必ずペアで作成されるので、すぐに切り換えることができる未使用のストレージ アカウント キーを常に 1 つ以上にできます。

[az storage account keys list](/cli/azure/storage/account/keys) を使用して、ストレージ アカウント キーを表示します。 この例では、**STORAGEKEY** 変数にキー 1 の値を格納しています。

```bash
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>ファイル共有を作成する

[az storage share create](/cli/azure/storage/share) を使用して、File Storage 共有を作成します。 

共有名は、すべて小文字の英字、数字、単一ハイフンにする必要があります。ただし、最初にハイフンを使用することはできません。 ファイル共有とファイルの名前付けの詳細については、「 [共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)」を参照してください。

この例では、10-GiB クォータを使用して、*myshare* という名前の共有を作成します。 

```bash
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>マウント ポイントの作成

Linux コンピューター上に Azure ファイル共有をマウントするには、**cifs-utils** パッケージを必ずインストールする必要があります。 インストール手順については、[使用する Linux ディストリビューション用の cifs-utils パッケージのインストール](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils)に関するページを参照してください。

Azure Files は SMB プロトコルを使用して、TCP ポート 445 経由で通信します。  Azure ファイル共有のマウントで問題が発生した場合、ファイアウォールが TCP ポート 445 をブロックしていないことを確認してください。


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>共有のマウント

ローカル ディレクトリに Azure ファイル共有をマウントします。 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

上記のコマンドでは、[mount](https://linux.die.net/man/8/mount) コマンドを使用して、Azure ファイル共有と [cifs](https://linux.die.net/man/8/mount.cifs) に固有のオプションをマウントします。 つまり、file_mode および dir_mode オプションでは、ファイルとディレクトリをアクセス許可 `0777` に設定します。 `0777` アクセス許可では、すべてのユーザーに対して読み取り、書き込み、実行アクセス許可が付与されます。 これらのアクセス許可は、値を他の [chmod アクセス許可](https://en.wikipedia.org/wiki/Chmod)に置き換えることによって変更できます。 gid や uid などの他の [cifs](https://linux.die.net/man/8/mount.cifs) オプションを使用することもできます。 


## <a name="persist-the-mount"></a>マウントの永続化

Linux VM を再起動すると、マウントされた SMB 共有はシャットダウン中にマウント解除されます。 起動時に SMB 共有を再マウントするには、Linux の /etc/fstab に行を追加します。 Linux では、fstab ファイルを使用して、起動プロセスでマウントする必要があるファイル システムを示します。 SMB 共有を追加することによって、File Storage 共有は Linux VM に永続的にマウントされるファイル システムとして設定されます。 File Storage SMB 共有を新しい VM に追加するには、cloud-init を使用します。

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```
実稼働環境のセキュリティを向上させるために、fstab の外部に資格情報を保存する必要があります。

## <a name="next-steps"></a>次の手順

- [cloud-init を利用し、作成時に Linux VM をカスタマイズする](using-cloud-init.md)
- [Linux VM へのディスクの追加](add-disk.md)
- [Azure CLI を使って Linux VM のディスクを暗号化する](encrypt-disks.md)


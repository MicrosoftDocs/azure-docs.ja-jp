---
title: Azure Storage の移行に関する FAQ | Microsoft Docs
description: Azure Storage の移行についてよくある質問の回答を示します。
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: cf1cba6f6d26d66fc560c86ea42459fa276cc880
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310848"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Azure Storage の移行についてよくあるご質問

この記事では、Azure Storage の移行についてよくある質問の回答を示します。 

## <a name="faq"></a>FAQ

**コンテナー間でファイルをコピーするスクリプトを作成するにはどうすればよいですか?**

コンテナー間でファイルをコピーするには、AzCopy を使用します。 次の例を参照してください。

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy では、[Copy Blob API](https://docs.microsoft.com/rest/api/storageservices/copy-blob) を使用してコンテナー内の各ファイルをコピーします。  
  
インターネットにアクセスできる仮想マシンまたはローカル コンピューターを使用して、AzCopy を実行できます。 Azure Batch スケジュールを使用して、これを自動的に実行することもできますが、複雑になります。  
  
Automation スクリプトは、ストレージ コンテンツの操作ではなく、Azure Resource Manager のデプロイを目的としています。 詳細については、「[Deploy resources with Resource Manager templates and Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)」 (Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ) を参照してください。

**同じリージョン内の同じストレージ アカウントの 2 つのファイル共有間でデータをコピーする場合、料金は発生しますか?**

いいえ。 このプロセスの料金は発生しません。

**ストレージ アカウント全体を他のストレージ アカウントにバックアップするにはどうすればよいですか?**

ストレージ アカウント全体を直接バックアップするオプションはありません。 ただし、AzCopy または Storage Explorer を使用して、そのストレージ アカウント内のコンテナーを別のアカウントに手動で移動することは可能です。 次の手順は、AzCopy を使用してコンテナーを移動する方法を示しています。  
 

1.  [AzCopy](storage-use-azcopy.md) コマンドライン ツールをインストールします。 このツールを使用して、ストレージ アカウント間で VHD ファイルを移動できます。

2.  インストーラーを使用して AzCopy を Windows にインストールしたら、コマンド プロンプト ウィンドウを開き、コンピューター上の AzCopy インストール フォルダーに移動します。 既定では、AzCopy は **%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy** または **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy** にインストールされています。

3.  次のコマンドを実行して、コンテナーを移動します。 テキストを実際の値に置き換える必要があります。   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`:ソース ストレージ アカウントの URI (コンテナーまで) を指定します。  
    - `/Dest`:ターゲット ストレージ アカウントの URI (コンテナーまで) を指定します。  
    - `/SourceKey`:ソース ストレージ アカウントの主キーを指定します。 Azure ポータルからこのキーをコピーするには、ストレージ アカウントを選択します。  
    - `/DestKey`:ターゲット ストレージ アカウントの主キーを指定します。 ポータルからこのキーをコピーするには、ストレージ アカウントを選択します。

このコマンドを実行すると、コンテナー ファイルがターゲット ストレージ アカウントに移動されます。

> [!NOTE]
> Azure BLOB 間でコピーする場合、AzCopy CLI は **Pattern** スイッチとは連携しません。
>
> AzCopy コマンドを直接コピーして編集し、**Pattern** がソースと一致することを確認します。 また、**/S** ワイルドカードが有効であることも確認します。 詳細については、「[AzCopy のパラメーター](storage-use-azcopy.md)」をご覧ください。

**ストレージ コンテナー間でデータを移動するにはどうすればよいですか?**

次の手順に従います。

1.  移動先 BLOB にコンテナー (フォルダー) を作成します。

2.  [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) を使用して、元の BLOB コンテナーの内容を別の BLOB コンテナーにコピーします。

**Azure ファイル共有から別の Azure Storage にデータを移動する PowerShell スクリプトを作成するにはどうすればよいですか?**

Azure ファイル共有から別の Azure Storage にデータを移行するには、AzCopy を使用します。 詳細については、「[AzCopy on Windows を使ったデータの転送](storage-use-azcopy.md)」および「[AzCopy on Linux を使用したデータの転送](storage-use-azcopy-linux.md)」をご覧ください。

**大きな .csv ファイルを Azure Storage にアップロードするにはどうすればよいですか?**

大きな .csv ファイルを Azure Storage にアップロードするには、AzCopy を使用します。 詳細については、「[AzCopy on Windows を使ったデータの転送](storage-use-azcopy.md)」および「[AzCopy on Linux を使用したデータの転送](storage-use-azcopy-linux.md)」をご覧ください。

**D ドライブ から Azure ストレージ アカウントにログを毎日移動する必要があります。これを自動化するにはどうすればよいですか?**

AzCopy を使用し、タスク スケジューラでタスクを作成できます。 AzCopy バッチ スクリプトを使用して、Azure ストレージ アカウントにファイルをアップロードします。 詳細については、「[クラウド サービスのスタートアップ タスクを構成して実行する方法](../../cloud-services/cloud-services-startup-tasks.md)」を参照してください。

**サブスクリプション間でストレージ アカウントを移動するにはどうすればよいですか?**

サブスクリプション間でストレージ アカウントを移動するには、AzCopy を使用します。 詳細については、「[AzCopy on Windows を使ったデータの転送](storage-use-azcopy.md)」および「[AzCopy on Linux を使用したデータの転送](storage-use-azcopy-linux.md)」をご覧ください。

**約 10 TB のデータを別のリージョンのストレージに移動するにはどうすればよいですか?**

データを移動するには、AzCopy を使用します。 詳細については、「[AzCopy on Windows を使ったデータの転送](storage-use-azcopy.md)」および「[AzCopy on Linux を使用したデータの転送](storage-use-azcopy-linux.md)」をご覧ください。

**オンプレミスから Azure Storage にデータをコピーするにはどうすればよいですか?**

データをコピーするには、AzCopy を使用します。 詳細については、「[AzCopy on Windows を使ったデータの転送](storage-use-azcopy.md)」および「[AzCopy on Linux を使用したデータの転送](storage-use-azcopy-linux.md)」をご覧ください。

**オンプレミスから Azure Files にデータを移動するにはどうすればよいですか?**

データを移動するには、AzCopy を使用します。 詳細については、「[AzCopy on Windows を使ったデータの転送](storage-use-azcopy.md)」および「[AzCopy on Linux を使用したデータの転送](storage-use-azcopy-linux.md)」をご覧ください。

**仮想マシン上のコンテナー フォルダーをマップするにはどうすればよいですか?**

Azure ファイル共有を使用します。

**Azure File Storage をバックアップするにはどうすればよいですか?**

バックアップ ソリューションはありません。 ただし、Azure Files では非同期コピーもサポートしています。 そのため、ファイルを以下のようにコピーすることができます。

- ストレージ アカウント内のファイル共有間、または別のストレージ アカウント間。

- ストレージ アカウント内のファイル共有から BLOB コンテナーへ、または別のストレージ アカウントへ。

詳細については、「[AzCopy on Windows を使ったデータの転送](storage-use-azcopy.md)」をご覧ください。

**マネージド ディスクを別のストレージ アカウントに移動するにはどうすればよいですか?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

次の手順に従います。

1.  マネージド ディスクの接続先の仮想マシンを停止します。

2.  次の Azure PowerShell スクリプトを実行して、マネージド ディスク VHD をエリア間でコピーします。

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  VHD のコピー先である別のリージョンの VHD ファイルを使用して、マネージド ディスクを作成します。 これを行うには、次の Azure PowerShell スクリプトを実行します。  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ``` 

マネージド ディスクから仮想マシンをデプロイする方法の詳細については、[CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1) を参照してください。

**Azure ポータルから 1 ～ 2 TB のデータをダウンロードする方法は?**

データをダウンロードするには、AzCopy を使用します。 詳細については、「[AzCopy on Windows を使ったデータの転送](storage-use-azcopy.md)」および「[AzCopy on Linux を使用したデータの転送](storage-use-azcopy-linux.md)」をご覧ください。

**ストレージ アカウントのセカンダリ ロケーションからヨーロッパ リージョンに変更するにはどうすればよいですか?**

ストレージ アカウントの作成時に、アカウントのプライマリ リージョンを選択します。 セカンダリ リージョンの選択はプライマリ リージョンに基づいており、変更することはできません。 詳しくは、「[geo 冗長ストレージ (GRS):Azure Storage のリージョン間レプリケーション](storage-redundancy.md)」をご覧ください。

**Azure Storage Service Encryption (SSE) の詳細はどこで入手できますか?**  
  
次の記事を参照してください。

-  [Azure Storage セキュリティ ガイド](storage-security-guide.md)

-  [Azure Storage Service Encryption for Data at Rest](storage-service-encryption.md)

**ストレージ アカウントからデータを移動またはダウンロードするにはどうすればよいですか?**

データをダウンロードするには、AzCopy を使用します。 詳細については、「[AzCopy on Windows を使ったデータの転送](storage-use-azcopy.md)」および「[AzCopy on Linux を使用したデータの転送](storage-use-azcopy-linux.md)」をご覧ください。


**ストレージ アカウント内のデータを暗号化するにはどうすればよいですか?**

ストレージ アカウントで暗号化を有効にしても、既存のデータは暗号化されません。 既存のデータを暗号化するには、データをストレージ アカウントにもう一度アップロードする必要があります。

AzCopy を使用して別のストレージ アカウントにデータをコピーした後、元のストレージ アカウントに戻します。 また、[保存時の暗号化](storage-service-encryption.md)を使用することもできます。

**ポータルでダウンロード オプションを使用する以外に、VHD をローカル コンピューターにダウンロード方法はありますか?**

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用して VHD をダウンロードできます。

**ストレージ アカウントのレプリケーションを GRS アカウントから LRS に変更する場合、前提条件はありますか?**

いいえ。 

**Azure Files の冗長ストレージにアクセスするにはどうすればよいですか?**

冗長ストレージにアクセスするには、geo 冗長ストレージの読み取りアクセス権が必要です。 ただし、Azure Files では LRS のみがサポートされており、標準の geo 冗長ストレージでは読み取り専用アクセスは許可されていません。 

**Premium Storage アカウントから Standard Storage アカウントに移行するにはどうすればよいですか?**

次の手順に従います。

1.  Standard Storage アカウントを作成します。 (またはサブスクリプションに既存の Standard Storage アカウントを使用します。)

2.  AzCopy をダウンロードします。 次のいずれかの AzCopy コマンドを実行します。
      
    ストレージ アカウント内のディスク全体をコピーするには、次のコマンドを実行します。

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    ディスクを 1 つだけコピーするには、**Pattern** にディスクの名前を指定します。

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
この操作は、完了するまで数時間かかる場合があります。

転送が正常に完了したことを確認するには、Azure ポータルでコピー先のストレージ アカウントのコンテナーを調べます。 ディスクが Standard Storage アカウントにコピーされたら、既存のディスクとして仮想マシンに接続できます。 詳細については、「[Azure Portal で Windows VM にマネージド データ ディスクを接続する方法](../../virtual-machines/windows/attach-managed-disk-portal.md)」をご覧ください。  
  
**Azure Premium Storage をファイル共有に変換するにはどうすればよいですか?**

Premium Storage は、Azure ファイル共有では使用できません。

**Standard Storage アカウントから Premium Storage アカウントにアップグレードするにはどうすればよいですか?Premium Storage アカウントから Standard Storage アカウントにダウングレードするにはどうすればよいですか?**

ターゲット ストレージ アカウントを作成し、ソース アカウントからターゲット アカウントにデータをコピーしてから、ソース アカウントを削除します。 データは、AzCopy などのツールを使用してコピーできます。

仮想マシンがある場合は、ストレージ アカウントのデータを移行する前に追加の手順を実行する必要があります。 詳細については、「[Azure Premium Storage への移行 (非管理対象ディスク)](storage-migration-to-premium-storage.md)」をご覧ください。

**クラシック ストレージ アカウントから Azure Resource Manager ストレージ アカウントに移行するにはどうすればよいですか?**

**Move-AzStorageAccount** コマンドレットを使用できます。 このコマンドレットには複数の手順 (検証、準備、コミット) があります。 移行する前に、移行を検証できます。

仮想マシンがある場合は、ストレージ アカウントのデータを移行する前に追加の手順を実行する必要があります。 詳細については、「[Azure PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md)」をご覧ください。

**Azure ストレージ アカウントから Linux ベースのコンピューターへのデータのダウンロード、または Linux コンピューターからのデータのアップロードを行うにはどうすればよいですか?**

Azure CLI をご利用いただけます。

- 1 つの BLOB をダウンロードする

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- 1 つの BLOB をアップロードする 

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**ストレージ リソースに他のユーザーがアクセスできるようにするにはどうすればよいですか?**

ストレージ リソースに他のユーザーがアクセスできるようにする方法は次のとおりです。

-   Shared Access Signature (SAS) トークンを使用して、リソースにアクセスできるようにします。 

-   ストレージ アカウントのプライマリ キーまたはセカンダリ キーをユーザーに提供します。 詳細については、「[ストレージ アカウントの管理](storage-account-manage.md#access-keys)」をご覧ください。

-   アクセス ポリシーを変更して、匿名アクセスを許可します。 詳細については、「[コンテナーと BLOB への匿名ユーザーのアクセス許可を付与します。](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs)」をご覧ください。

**AzCopy はどこにインストールされていますか?**

-   Microsoft Azure Storage のコマンド ラインから AzCopy はアクセスするには、「**AzCopy**」と入力します。 コマンド ラインは、AzCopy と同時にインストールされます。

-   32 ビット バージョンをインストールした場合は、**%ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\AzCopy** にあります。

-   64 ビット バージョンをインストールした場合は、**%ProgramFiles%\\Microsoft SDKs\\Azure\\AzCopy** にあります。

**レプリケートされたストレージ アカウント (ZRS、GRS、または読み取り GRS など) の場合、セカンダリ リージョンに格納されているデータにアクセスする方法は?**

-   ゾーン冗長ストレージまたは geo 冗長ストレージ を使用している場合は、セカンダリ リージョンへのフェールオーバーを開始していない限り、そのリージョンのデータにアクセスすることはできません。 フェールオーバー プロセスの詳細については、「[Disaster recovery and storage account failover (preview) in Azure Storage (Azure Storage でのディザスター リカバリーとストレージ アカウントのフェールオーバー (プレビュー))](storage-disaster-recovery-guidance.md)」を参照してください。

-   読み取りアクセス GRS を使用している場合は、セカンダリ リージョンのデータにいつでもアクセスできます。 次のいずれかの方法を使用します。  
      
    - **AzCopy**:**-secondary** を URL のストレージ アカウント名の後に追加して、セカンダリ エンドポイントにアクセスします。 例:   
     
      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **SAS トークン**:SAS トークンを使用してエンドポイントからデータにアクセスします。 詳細については、「[Shared Access Signatures (SAS) の使用](storage-dotnet-shared-access-signature-part-1.md)」を参照してください。

**ストレージ アカウントで HTTPS カスタム ドメインを使用するにはどうすればよいですか?たとえば、"https:\//mystorageaccountname.blob.core.windows.net/images/image.gif" を "https:\//www.contoso.com/images/image.gif" と表示するにはどうすればよいですか?**

カスタム ドメインを使用するストレージ アカウントでは、SSL は現在サポートされていません。
ただし、HTTPS 以外のカスタム ドメインはご利用いただけます。 詳細については、「[BLOB ストレージ エンドポイントのカスタム ドメイン名の構成](../blobs/storage-custom-domain-name.md)」をご覧ください。

**FTP を使用してストレージ アカウント内のデータにアクセスするにはどうすればよいですか?**

FTP を使用してストレージ アカウントに直接アクセスする方法はありません。 ただし、Azure 仮想マシンをセットアップし、仮想マシンに FTP サーバーをインストールできます。 FTP サーバーを使用して、Azure Files 共有上、または仮想マシンで使用できるデータ ディスク上にファイルを保存できます。

Storage Explorer などのアプリケーションを使用せずに、データのダウンロードのみ実行する場合は、SAS トークンを使用できます。 詳細については、「[Shared Access Signatures (SAS) の使用](storage-dotnet-shared-access-signature-part-1.md)」を参照してください。

**2 つのストレージ アカウント間で BLOB を移行するにはどうすればよいですか?**

 [BLOB 移行スクリプト](../scripts/storage-common-transfer-between-storage-accounts.md)を用意しておりますのでご利用ください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

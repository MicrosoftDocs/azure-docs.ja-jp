---
title: "Azure ファイル同期 (プレビュー) へのサーバーの登録/登録解除 | Microsoft Docs"
description: "Azure ファイル同期のストレージ同期サービスへの Windows Server の登録と登録解除の方法について説明します。"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: wgries
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 0acf183fbaea99e4316b668a3da28d79b20b7bef
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="registerunregister-a-server-with-azure-file-sync-preview"></a>Azure ファイル同期 (プレビュー) へのサーバーの登録/登録解除
Azure ファイル同期 (プレビュー) を使用すると、オンプレミスまたは Azure 内で共有をレプリケートできます。また、Windows Server 上で SMB または NFS を介して共有にアクセスすることができます。 Azure ファイル同期は、ブランチ オフィスなどの Azure データ センターから離れた場所にあるデータにアクセスして変更する必要がある場合に便利です。 複数のブランチ オフィス間など、複数の Windows Server エンドポイント間でデータをレプリケートできます。

次の記事では、サーバーのストレージ同期サービスへの登録と登録解除を行う方法について説明しています。 この処理は、サーバーの使用を中止する場合、または同期グループで新しいサーバー エンドポイントが必要になった場合に実行されることがあります。 Azure ファイル同期をエンドツーエンドでデプロイする方法の詳細については、「[How to deploy Azure File Sync (preview)](storage-sync-files-deployment-guide.md)」(Azure ファイル同期 (プレビュー) をデプロイする方法) を参照してください。

## <a name="prerequisites"></a>前提条件
ストレージ同期サービスに Windows Server を登録するには、まず必要な前提条件を満たすように Windows Server を準備する必要があります。

* ストレージ同期サービスがデプロイされていること。 ストレージ同期サービスのデプロイ方法の詳細については、「[How to deploy Azure File Sync (preview)](storage-sync-files-deployment-guide.md)」(Azure ファイル同期 (プレビュー) をデプロイする方法) を参照してください。
* サーバーがインターネットに接続され、Azure にアクセスできること。
* サーバー マネージャーの UI を使用して、管理者の [IE セキュリティ強化の構成] を無効にします。
    
    ![[IE セキュリティ強化の構成] が強調表示された サーバー マネージャーの UI](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* AzureRM PowerShell モジュールがサーバーにインストールされていること。 サーバーがフェールオーバー クラスターのメンバーである場合、そのクラスター内のすべてのノードには AzureRM モジュールが必要になります。 AzureRM モジュールをインストールする方法の詳細については、「[Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)」(Azure PowerShell のインストールと構成) を参照してください。

    > [!Note]  
    > サーバーの登録と登録解除には、最新バージョンの AzureRM PowerShell モジュールを使用することをお勧めします。 AzureRM パッケージがこのサーバーにインストールされていたことがあり、そのサーバー上の PowerShell バージョンが 5.* 以降の場合、`Update-Module` コマンドレットを使用してそのパッケージを更新することができます。 

## <a name="register-a-server-with-storage-sync-service"></a>サーバーをストレージ同期サービスに登録する
Azure ファイル同期の*同期グループ*で、*サーバー エンドポイント*として Windows Server を使用するには、Windows Server を*ストレージ同期サービス*に登録する必要があります。 1 つのサーバーは、同時に 1 つの*ストレージ同期サービス*にのみ登録できます。

### <a name="install-the-azure-file-sync-agent"></a>Azure ファイル同期エージェントをインストールする
1. [Azure ファイル同期エージェントをダウンロード](https://go.microsoft.com/fwlink/?linkid=858257)します。
2. Azure ファイル同期エージェントのインストーラーを起動します。
    
    ![Azure ファイル同期エージェントのインストーラーで最初に表示されるウィンドウ](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. 必ず、Microsoft Update による Azure ファイル同期エージェントの更新を有効にします。 サーバー パッケージの重要なセキュリティ修正プログラムと機能強化は Microsoft Update を介して提供されるため、有効にすることが重要です。

    ![Azure ファイル同期エージェントのインストーラーの [Microsoft Update] ウィンドウで、Microsoft Update が有効であることを確認する](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. サーバーがまだ登録されていない場合、インストールの完了すぐにサーバーの登録 UI が表示されます。

> [!Important]  
> サーバーがフェールオーバー クラスターのメンバーである場合、クラスターのすべてのノードに Azure ファイル同期エージェントをインストールする必要があります。

### <a name="register-the-server-using-the-server-registration-ui"></a>[サーバーの登録] UI を使用してサーバーを登録する
1. Azure ファイル同期エージェントのインストールが完了した直後に [サーバーの登録] UI が開始されない場合は、`C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe` を実行して手動で開始することができます。
2. *[サインイン]* をクリックして Azure サブスクリプションにアクセスします。 

    ![[サーバーの登録] UI のダイアログ表示](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. ダイアログから、正しいサブスクリプション、リソース グループ、およびストレージ同期サービスを選択します。

    ![ストレージ同期サービスの情報](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. プレビューでは、このプロセスを完了するために 1 回または複数回のサインインが必要です。 

    ![[サインイン] ダイアログ](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> サービスがフェールオーバー クラスターのメンバーである場合、各サーバーで [サーバーの登録] を実行する必要があります。 Azure Portal で [登録済みサーバー] を表示すると、Azure ファイル同期は、各ノードを同じフェールオーバー クラスターのメンバーとして自動的に認識し、適宜、各メンバーをグループ化します。

## <a name="unregister-the-server-with-storage-sync-service"></a>ストレージ同期サービスからサーバーの登録を解除する
ストレージ同期サービスからサービスの登録を解除するために必要な手順がいくつかあります。 サーバーの登録を適切に解除する方法を見てみましょう。

### <a name="optional-recall-all-tiered-data"></a>(省略可能) すべての階層型データを回収する
サーバー エンドポイントでクラウドの階層化を有効にすると、ファイルは Azure ファイル共有に*階層化*されます。 その結果、オンプレミスのファイル共有は、データセットの完全なコピーでなくキャッシュとして動作するので、ファイル サーバー上の領域を効率的に使用できるようになります。 ただし、階層化されたファイルがサーバーのローカルにまだある状態でサーバー エンドポイントを削除すると、それらのファイルにはアクセスできなくなります。 そのため、ファイルのアクセスを継続するには、Azure Files から階層化されたファイルをすべて回収してから、登録解除を進める必要があります。 

この処理は、次のように PowerShell コマンドレットを使用して実行できます。

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> サーバーをホストするローカル ボリュームに、階層化されたデータをすべて回収できる空き領域がない場合、`Invoke-StorageSyncFileRecall` コマンドレットは失敗します。  

### <a name="remove-the-server-from-all-sync-groups"></a>すべての同期グループからサーバーを削除する
ストレージ同期サービス上のサーバーの登録を解除する前に、そのサーバーのすべてのサーバー エンドポイントを削除する必要があります。 この処理は、Azure Portal でも実行できます。

1. サーバーが登録されているストレージ同期サービスに移動します。
2. ストレージ同期サービスの各同期グループで、このサーバーのすべてのサーバー エンドポイントを削除します。 この処理は、[同期グループ] ウィンドウの関連するサーバー エンドポイントを右クリックして実行することができます。

    ![同期グループからサーバー エンドポイントを削除する](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

この処理は、次のように単純な PowerShell スクリプトでも実行できます。

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Login-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

### <a name="unregister-the-server"></a>サーバーの登録を解除する
すべてのデータを回収し、すべての同期グループからサーバーを削除したら、サーバーの登録を解除できます。 

1. Azure Portal でストレージ同期サービスの [登録済みサーバー] セクションに移動します。
2. 登録解除するサーバーを右クリックし、[サーバーの登録解除] をクリックします。

    ![サーバーの登録解除](media/storage-sync-files-server-registration/unregister-server-1.png)

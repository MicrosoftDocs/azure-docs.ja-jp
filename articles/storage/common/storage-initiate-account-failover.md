---
title: ストレージ アカウントのフェールオーバー (プレビュー) を開始する - Azure Storage
description: ストレージ アカウントのプライマリ エンドポイントが使用できなくなった場合に、アカウントのフェールオーバーを開始する方法について説明します。 フェールオーバーでは、セカンダリ リージョンが更新されて、ストレージ アカウントのプライマリ リージョンになります。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d94f6297f27eb3ea130b443ccf94052d391eb46d
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985339"
---
# <a name="initiate-a-storage-account-failover-preview"></a>ストレージ アカウントのフェールオーバー (プレビュー) を開始する

何らかの理由で geo 冗長ストレージ アカウントのプライマリ エンドポイントが利用できなくなった場合、アカウントのフェールオーバー (プレビュー) を開始できます。 アカウントのフェールオーバーでは、セカンダリ エンドポイントが更新されて、ストレージ アカウントのプライマリ エンドポイントになります。 フェールオーバーが完了すると、クライアントは新しいプライマリ リージョンへの書き込みを開始できます。 強制フェールオーバーを使用すると、アプリケーションの高可用性を維持することができます。

この記事では、Azure portal、PowerShell、または Azure CLI を使用して、ストレージ アカウントのアカウントのフェールオーバーを開始する方法を示します。 アカウントのフェールオーバーについて詳しくは、「[Azure Storage でのディザスター リカバリーとストレージ アカウントのフェールオーバー (プレビュー)](storage-disaster-recovery-guidance.md)」をご覧ください。

> [!WARNING]
> 通常、アカウントのフェールオーバーでは若干のデータ損失が発生します。 アカウントのフェールオーバーの影響を理解し、データの損失に準備するには、「[アカウントのフェールオーバー プロセスを理解する](storage-disaster-recovery-guidance.md#understand-the-account-failover-process)」をご覧ください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

ストレージ アカウントでアカウントのフェールオーバーを実行するには、事前に次の手順を完了しておく必要があります。

- アカウントのフェールオーバー プレビューに登録します。 登録方法については、「[プレビューについて](storage-disaster-recovery-guidance.md#about-the-preview)」をご覧ください。
- お使いのストレージ アカウントが、geo 冗長ストレージ (GRS) または読み取りアクセス geo 冗長ストレージ (RA-GRS) を使用するように構成されていることを確認します。 geo 冗長ストレージについて詳しくは、「[geo 冗長ストレージ (GRS):Azure Storage のリージョン間レプリケーション](storage-redundancy-grs.md)」をご覧ください。 

## <a name="important-implications-of-account-failover"></a>アカウントのフェールオーバーの重要な影響

ストレージ アカウントのアカウントのフェールオーバーを開始すると、セカンダリ エンドポイントがプライマリ エンドポイントになるように、セカンダリ エンドポイントの DNS レコードが更新されます。 フェールオーバーを開始する前に、ストレージ アカウントに対して可能性のある影響について理解しておいてください。

フェールオーバーを始める前に、可能性のあるデータ損失の範囲を見積もるには、`-IncludeGeoReplicationStats` パラメーターを指定して `Get-AzStorageAccount` PowerShell コマンドレットを使用することで、**最終同期時刻** プロパティを確認します。 その後、アカウントの `GeoReplicationStats` プロパティを確認します。 

フェールオーバーの後、新しいプライマリ リージョンでのストレージ アカウントの種類は、ローカル冗長ストレージ (LRS) に自動的に変換されます。 アカウントに対して geo 冗長ストレージ (GRS) または読み取りアクセス geo 冗長ストレージ (RA-GRS) を再び有効にすることができます。 LRS から GRS または RA-GRS に変換すると、追加コストが発生することに注意してください。 詳しくは、「[帯域幅の料金詳細](https://azure.microsoft.com/pricing/details/bandwidth/)」をご覧ください。 

ストレージ アカウントの GRS を再度有効にすると、アカウント内のデータの新しいセカンダリ リージョンへのレプリケートが開始されます。 レプリケーションにかかる時間は、レプリケートされるデータの量に依存します。  

## <a name="azure-portal"></a>Azure ポータル

Azure portal からアカウントのフェールオーバーを開始するには、次の手順のようにします。

1. ストレージ アカウントに移動します。
2. **[設定]** で **[geo レプリケーション]** を選択します。 次の図では、ストレージ アカウントの geo レプリケーションとフェールオーバーの状態を示します。

    ![geo レプリケーションとフェールオーバーの状態を示すスクリーンショット](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. お使いのストレージ アカウントが、geo 冗長ストレージ (GRS) または読み取りアクセス geo 冗長ストレージ (RA-GRS) 用に構成されていることを確認します。 そうでない場合は、 **[設定]** の **[構成]** を選択して、アカウントを geo 冗長に更新します。 
4. **[最終同期時刻]** プロパティでは、セカンダリがプライマリからどれくらい遅れているかが示されます。 **[最終同期時刻]** では、フェールオーバー完了後に発生するデータ損失の範囲の見積もりが提供されます。
5. **[フェールオーバーの準備 (プレビュー)]** を選択します。 
6. 確認ダイアログを確認します。 準備ができていれば、 **[はい]** を選択して確認し、フェールオーバーを開始します。

    ![アカウントのフェールオーバーの確認ダイアログを示すスクリーンショット](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>PowerShell

PowerShell を使用してアカウントのフェールオーバーを開始するには、最初に 6.0.1 プレビュー モジュールをインストールする必要があります。 次の手順のようにしてモジュールをインストールします。

1. Azure PowerShell の以前のインストールがある場合はアンインストールします。

    - **[設定]** の **[アプリと機能]** 設定を使用して、Windows から Azure PowerShell の以前のインストールを削除します。
    - `%Program Files%\WindowsPowerShell\Modules` からすべての **Azure** モジュールを削除します。

1. 最新バージョンの PowerShellGet がインストールされていることを確認します。 Windows PowerShell ウィンドウを開き、次のコマンドを実行して最新バージョンをインストールします。

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. PowerShellGet のインストール後、PowerShell ウィンドウを閉じて再び開きます。 

1. 最新バージョンの Azure PowerShell をインストールします。

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. アカウントのフェールオーバーをサポートする Azure Storage プレビュー モジュールをインストールします。

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

1. PowerShell ウィンドウを閉じて再び開きます。
 
PowerShell からアカウントのフェールオーバーを開始するには、次のコマンドを実行します。

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使用してアカウントのフェールオーバーを開始するには、次のコマンドを実行します。

```cli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

## <a name="next-steps"></a>次の手順

- [Azure Storage でのディザスター リカバリーとアカウントのフェールオーバー (プレビュー)](storage-disaster-recovery-guidance.md)
- [RA-GRS を使用した高可用性アプリケーションの設計](storage-designing-ha-apps-with-ragrs.md)
- [チュートリアル:Blob Storage を使用して高可用性アプリケーションを作成する](../blobs/storage-create-geo-redundant-storage.md) 

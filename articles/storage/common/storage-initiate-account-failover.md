---
title: ストレージ アカウントのフェールオーバーを開始する
titleSuffix: Azure Storage
description: ストレージ アカウントのプライマリ エンドポイントが使用できなくなった場合に、アカウントのフェールオーバーを開始する方法について説明します。 フェールオーバーでは、セカンダリ リージョンが更新されて、ストレージ アカウントのプライマリ リージョンになります。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/11/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 74f4c30f7aa018502f3437fd4a577f8c2a9de491
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077945"
---
# <a name="initiate-a-storage-account-failover"></a>ストレージ アカウントのフェールオーバーを開始する

何らかの理由で geo 冗長ストレージ アカウントのプライマリ エンドポイントが利用できなくなった場合、アカウントのフェールオーバーを開始できます。 アカウントのフェールオーバーでは、セカンダリ エンドポイントが更新されて、ストレージ アカウントのプライマリ エンドポイントになります。 フェールオーバーが完了すると、クライアントは新しいプライマリ リージョンへの書き込みを開始できます。 強制フェールオーバーを使用すると、アプリケーションの高可用性を維持することができます。

この記事では、Azure portal、PowerShell、または Azure CLI を使用して、ストレージ アカウントのアカウントのフェールオーバーを開始する方法を示します。 アカウントのフェールオーバーの詳細については、「[ディザスター リカバリーとストレージ アカウントのフェールオーバー](storage-disaster-recovery-guidance.md)」をご覧ください。

> [!WARNING]
> 通常、アカウントのフェールオーバーでは若干のデータ損失が発生します。 アカウントのフェールオーバーの影響を理解し、データの損失に準備するには、「[アカウントのフェールオーバー プロセスを理解する](storage-disaster-recovery-guidance.md#understand-the-account-failover-process)」をご覧ください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

ストレージ アカウントでアカウントのフェールオーバーを実行する前に、ストレージ アカウントが geo レプリケーション用に構成されていることをご確認ください。 ストレージ アカウントでは、次のどの冗長オプションでも使用できます。

- geo 冗長ストレージ (GRS) または読み取りアクセス geo 冗長ストレージ (RA-GRS)
- geo ゾーン冗長ストレージ (GZRS) または読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS)

Azure Storage の冗長性の詳細については、「[Azure Storage の冗長性](storage-redundancy.md)」を参照してください。

## <a name="initiate-the-failover"></a>フェールオーバーを開始する

## <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal からアカウントのフェールオーバーを開始するには、次の手順のようにします。

1. ストレージ アカウントに移動します。
1. **[設定]** で **[geo レプリケーション]** を選択します。 次の図では、ストレージ アカウントの geo レプリケーションとフェールオーバーの状態を示します。

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-prepare.png" alt-text="geo レプリケーションとフェールオーバーの状態を示すスクリーンショット":::

1. お使いのストレージ アカウントが、geo 冗長ストレージ (GRS) または読み取りアクセス geo 冗長ストレージ (RA-GRS) 用に構成されていることを確認します。 そうでない場合は、 **[設定]** の **[構成]** を選択して、アカウントを geo 冗長に更新します。
1. **[最終同期時刻]** プロパティでは、セカンダリがプライマリからどれくらい遅れているかが示されます。 **[最終同期時刻]** では、フェールオーバー完了後に発生するデータ損失の範囲の見積もりが提供されます。 **[最終同期時刻]** プロパティの詳細については、「[ストレージ アカウントの最終同期時刻プロパティを確認する](last-sync-time-get.md)」を参照してください。
1. **[フェールオーバーの準備]** を選択します。
1. 確認ダイアログを確認します。 準備ができていれば、 **[はい]** を選択して確認し、フェールオーバーを開始します。

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-confirm.png" alt-text="アカウントのフェールオーバーの確認ダイアログを示すスクリーンショット":::

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

アカウントのフェールオーバー機能は一般提供されていますが、まだ PowerShell のプレビュー モジュールに依存しています。 PowerShell を使用してアカウントのフェールオーバーを開始するには、最初に Az.Storage [1.1.1-preview](https://www.powershellgallery.com/packages/Az.Storage/1.1.1-preview) モジュールをインストールする必要があります。 次の手順のようにしてモジュールをインストールします。

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

PowerShell からアカウントのフェールオーバーを開始するには、次のコマンドを実行します。

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name>
```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してアカウントのフェールオーバーを開始するには、次のコマンドを実行します。

```azurecli-interactive
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="important-implications-of-account-failover"></a>アカウントのフェールオーバーの重要な影響

ストレージ アカウントのアカウントのフェールオーバーを開始すると、セカンダリ エンドポイントがプライマリ エンドポイントになるように、セカンダリ エンドポイントの DNS レコードが更新されます。 フェールオーバーを開始する前に、ストレージ アカウントに対して可能性のある影響について理解しておいてください。

フェールオーバーを始める前に、可能性のあるデータ損失の範囲を見積もるには、 **[最終同期時刻]** プロパティを確認します。 **[最終同期時刻]** プロパティの詳細については、「[ストレージ アカウントの最終同期時刻プロパティを確認する](last-sync-time-get.md)」を参照してください。

フェールオーバーの後、新しいプライマリ リージョンでのストレージ アカウントの種類は、ローカル冗長ストレージ (LRS) に自動的に変換されます。 アカウントに対して geo 冗長ストレージ (GRS) または読み取りアクセス geo 冗長ストレージ (RA-GRS) を再び有効にすることができます。 LRS から GRS または RA-GRS に変換すると、追加コストが発生することに注意してください。 詳しくは、「[帯域幅の料金詳細](https://azure.microsoft.com/pricing/details/bandwidth/)」をご覧ください。

ストレージ アカウントの GRS を再度有効にすると、アカウント内のデータの新しいセカンダリ リージョンへのレプリケートが開始されます。 レプリケーションにかかる時間は、レプリケートされるデータの量に依存します。  

## <a name="next-steps"></a>次のステップ

- [ディザスター リカバリーとストレージ アカウントのフェールオーバー](storage-disaster-recovery-guidance.md)
- [ストレージ アカウントの最終同期時刻プロパティを確認する](last-sync-time-get.md)
- [geo 冗長性を使用する高可用性アプリケーションの設計](geo-redundant-design.md)
- [チュートリアル:Blob Storage を使用して高可用性アプリケーションを作成する](../blobs/storage-create-geo-redundant-storage.md)

---
title: ストレージ アカウントの最終同期時刻プロパティを確認する
titleSuffix: Azure Storage
description: geo レプリケートされたストレージ アカウントの [最終同期時刻] プロパティを確認する方法について説明します。 [最終同期時刻] プロパティは、プライマリ リージョンからのすべての書き込みがセカンダリ リージョンに正常に書き込まれた最終時刻を示します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 242700c05053aa9d07e3a561a21986c8451a46c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91612446"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>ストレージ アカウントの最終同期時刻プロパティを確認する

ストレージ アカウントを構成するときに、プライマリ リージョンから何百キロメートルも離れたセカンダリ リージョンにデータをコピーするように指定できます。 geo レプリケーションによって、自然災害など、プライマリ リージョンで重大な停電が発生した場合にデータの持続性を実現できます。 セカンダリ リージョンへの読み取りアクセスを追加で有効にすると、プライマリ リージョンが使用できなくなった場合に、データの読み取り操作が引き続き使用できます。 プライマリ リージョンが応答しない場合、セカンダリ リージョンからの読み取りにシームレスに切り替えるようにアプリケーションを設計できます。

geo 冗長ストレージ (GRS) と geo ゾーン冗長ストレージ (GZRS) のどちらを使用しても、データをセカンダリ リージョンに非同期的にレプリケートできます。 セカンダリ リージョンへの読み取りアクセスについては、読み取りアクセス geo 冗長ストレージ (RA-GRS) または読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS) を有効にします。 Azure Storage に用意されている冗長性のさまざまなオプションの詳細については、「[Azure Storage の冗長性](storage-redundancy.md)」を参照してください。

この記事では、ストレージ アカウントの **[最終同期時刻]** プロパティを確認して、プライマリ リージョンとセカンダリ リージョンの不一致を評価できるようにする方法について説明します。

## <a name="about-the-last-sync-time-property"></a>[最終同期時刻] プロパティの概要

geo レプリケーションは非同期であるため、プライマリ リージョンに書き込まれたデータが、障害が発生した時点でセカンダリ リージョンにまだ書き込まれていない可能性があります。 **[最終同期時刻]** プロパティは、プライマリ リージョンのデータがセカンダリ リージョンに正常に書き込まれた最終時刻を示します。 最終同期時刻より前にプライマリ リージョンに対して行われたすべての書き込みは、セカンダリの場所から読み取ることができます。 [最終同期時刻] プロパティの後にプライマリ リージョンに加えられた書き込みは、まだ読み取りに使用できない場合があります。

**[最終同期時刻]** プロパティは、GMT 日付/時刻の値です。

## <a name="get-the-last-sync-time-property"></a>[最終同期時刻] プロパティを取得する

PowerShell または Azure CLI を使用して、 **[最終同期時刻]** プロパティの値を取得できます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してストレージ アカウントの最終同期時刻を取得するには、バージョン 1.11.0 以降の [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) モジュールをインストールします。 次に、ストレージ アカウントの **GeoReplicationStats.LastSyncTime** プロパティを確認します。 プレースホルダー値をお客様独自の値に置き換えてください。

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してストレージ アカウントの最終同期時刻を取得するには、ストレージ アカウントの **geoReplicationStats.lastSyncTime** プロパティを確認します。 `--expand` パラメーターを使用して、**geoReplicationStats** に下に入れ子になっているプロパティの値を返します。 プレースホルダー値をお客様独自の値に置き換えてください。

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>関連項目

- [Azure Storage の冗長性](storage-redundancy.md)
- [ストレージ アカウントの冗長オプションを変更する](redundancy-migration.md)
- [geo 冗長性を使用する高可用性アプリケーションの設計](geo-redundant-design.md)

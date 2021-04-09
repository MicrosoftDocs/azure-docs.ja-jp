---
title: 暗号化キー情報の取得
titleSuffix: Azure Cognitive Search
description: Azure Key Vault でキーを管理できるように、インデックスまたはシノニム マップで使用される暗号化キーの名前とバージョンを取得します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.openlocfilehash: 37ff94608e9756142f70a4f3c64d0a6f7eeea685
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88932901"
---
# <a name="get-customer-managed-key-information-from-indexes-and-synonym-maps"></a>インデックスとシノニム マップからカスタマー マネージド キーの情報を取得する

Azure Cognitive Search では、カスタマー マネージド暗号化キーが Azure Key Vault で作成、格納、管理されます。 オブジェクトが暗号化されているかどうか、または使用されたキー名またはバージョンを確認する必要がある場合は、REST API または SDK を使用して、インデックスまたはシノニム マップの定義から **encryptionKey** プロパティを取得します。 

キーの使用状況を監視できるように、Key Vault で[ログを有効にする](../key-vault/general/logging.md)ことをお勧めします。

## <a name="get-the-admin-api-key"></a>管理 API キーを取得する

検索サービスからオブジェクト定義を取得するには、管理者権限を使用して認証を行う必要があります。 管理 API キーを取得する最も簡単な方法は、ポータルを使用することです。

1. [Azure portal](https://portal.azure.com/) にサインインして、検索サービスの概要ページを開きます。

1. 左側で **[キー]** をクリックし、管理 API をコピーします。 インデックスとシノニム マップを取得するには、管理者キーが必要です。

残りの手順では、PowerShell と REST API に切り替えます。 ポータルには、シノニム マップとインデックスの暗号化キー プロパティのどちらも表示されません。

## <a name="use-powershell-and-rest"></a>PowerShell と REST を使用する

次のコマンドを実行して、変数を設定し、オブジェクトの定義を取得します。

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>次の手順

使用する暗号化キーとバージョンを確認したので、Azure Key Vault でのキーの管理や、他の構成設定の確認を行うことができます。

+ [クイック スタート:PowerShell を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../key-vault/secrets/quick-create-powershell.md)

+ [Azure Cognitive Search のデータ暗号化のためにカスタマー マネージド キーを構成する](search-security-manage-encryption-keys.md)
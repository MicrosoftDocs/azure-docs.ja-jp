---
title: カスタマー マネージドのキーを使用した保存時の暗号化
titleSuffix: Azure Cognitive Search
description: Azure Key Vault 内で作成・管理するキーを使用して、Azure Cognitive Search 内でインデックスとシノニム マップによるサーバー側の暗号化を補完します。
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: 6b1079797f1a753fa8362d6e920f3394087d7e9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98119290"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Azure Cognitive Search のデータ暗号化のためにカスタマー マネージド キーを構成する

Azure Cognitive Search では、インデックス付きコンテンツの保存時に、[サービス マネージド キー](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components)を使用して自動的に暗号化します。 保護を強化する必要がある場合は、Azure Key Vault 内で作成して管理するキーを使用して、既定の暗号化を追加の暗号化レイヤーで補完できます。 この記事では、カスタマー マネージド キーの暗号化を設定する手順について説明します。

カスタマー マネージド キーの暗号化は、[Azure Key Vault](../key-vault/general/overview.md) に依存しています。 独自の暗号化キーを作成してキー コンテナーに格納したり、Azure Key Vault の API を使って暗号化キーを生成したりすることができます。 [ログを有効にする](../key-vault/general/logging.md)場合は、Azure Key Vault を使用してキーの使用状況を監査することもできます。  

カスタマー マネージド キーを使用した暗号化は、検索サービス レベル自体で指定されるのではなく、それらのオブジェクトの作成時に個々のインデックスまたはシノニム マップに適用されます。 暗号化できるのは新しいオブジェクトだけです。 既存のコンテンツを暗号化することはできません。

すべてのキーを同じキー コンテナーに置く必要はありません。 独自のカスタマー マネージド暗号化キーで暗号化されて異なるキー コンテナーに格納されている、複数の暗号化されたインデックスやシノニム マップを、1 つの検索サービスでホストすることができます。 また、カスタマー マネージドのキーを使用して暗号化されていない同じサービス内に、インデックスとシノニム マップを置くこともできます。

>[!Important]
> カスタマー マネージド キーを実装する場合は、キー コンテナー キーの定期的なローテーションと Active Directory アプリケーションのシークレットおよび登録時に厳密な手順に従ってください。 常に新しいシークレットとキーを使用するように暗号化されたすべてのコンテンツを更新してから、古いものを削除してください。 この手順を実行しないと、コンテンツの暗号化を解除できません。

## <a name="double-encryption"></a>二重暗号化

2020 年 8 月 1 日以降に作成され、特定のリージョン内にあるサービスの場合、カスタマー マネージド キーの暗号化のスコープには、現在以下のリージョンで使用できる[完全な二重暗号化](search-security-overview.md#double-encryption)を実現する一時ディスクが含まれています。 

+ 米国西部 2
+ 米国東部
+ 米国中南部
+ US Gov バージニア州
+ US Gov アリゾナ

異なるリージョンを使用しているか、8 月 1 日より前に作成されたサービスを使用している場合、マネージド キーの暗号化はデータ ディスクのみに限定されていて、サービスによって使用される一時ディスクは除かれています。

## <a name="prerequisites"></a>前提条件

このシナリオでは、以下のツールとサービスが使用されます。

+ [請求可能なレベル](search-sku-tier.md#tier-descriptions) (Basic 以上、任意のリージョン) の [Azure Cognitive Search](search-create-service-portal.md)。
+ [Azure Key Vault](../key-vault/general/overview.md)。[Azure portal](../key-vault//general/quick-create-portal.md)、[Azure CLI](../key-vault//general/quick-create-cli.md)、または [Azure PowerShell](../key-vault//general/quick-create-powershell.md) を使用して、キー コンテナーを作成できます (Azure Cognitive Search と同じサブスクリプション内)。 キー コンテナーでは、**論理的な削除** と **消去保護** が有効になっている必要があります。
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)。 それがない場合は、[新しいテナントをセットアップ](../active-directory/develop/quickstart-create-new-tenant.md)してください。

暗号化されたオブジェクトを作成できる検索アプリケーションが必要です。 このコード内では、キー コンテナー キーと Active Directory 登録情報を参照します。 このコードは、動作しているアプリや、[C# コードサンプル DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK) などのプロトタイプ コードなどになります。

> [!TIP]
> [Postman](search-get-started-rest.md)、[Visual Studio Code](search-get-started-vs-code.md)、または [Azure PowerShell](./search-get-started-powershell.md) を使用して、暗号化キー パラメーターを含むインデックスおよびシノニム マップを作成する REST API を呼び出すことができます。 現在のところ、ポータルでは、インデックスまたはシノニム マップへのキーの追加はサポートされていません。

## <a name="1---enable-key-recovery"></a>1 - キーの回復の有効化

カスタマー マネージド キーを使用する暗号化の性質上、Azure Key Vault キーが削除された場合、データを取得できません。 Key Vault キーの誤った削除によるデータ損失を防ぐため、キー コンテナーで論理的な削除と消去保護を有効にする必要があります。 論理的な削除は既定で有効になっているため、この機能を意図的に無効にした場合にのみ問題が発生します。 消去保護は既定では有効になりませんが、Cognitive Search のカスタマー マネージド キーの暗号化には必須です。 詳細については、[論理的な削除](../key-vault/general/soft-delete-overview.md)と[消去保護](../key-vault/general/soft-delete-overview.md#purge-protection)の概要に関するページを参照してください。

ポータル、PowerShell、または Azure CLI コマンドを使用して、両方のプロパティを設定できます。

### <a name="using-azure-portal"></a>Azure Portal の使用

1. [Azure portal にサインイン](https://portal.azure.com)して、キー コンテナーの概要ページを開きます。

1. **[概要]** ページの **[Essentials]** で、 **[論理的な削除]** と **[消去保護]** を有効にします。

### <a name="using-powershell"></a>PowerShell の使用

1. `Connect-AzAccount` を実行して Azure の資格情報を設定します。

1. 次のコマンドを実行してキー コンテナーに接続し、`<vault_name>` を有効な名前に置き換えます。

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault は、論理的な削除が有効な状態で作成されます。 これがコンテナーで無効になっている場合は、次のコマンドを実行します。

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. 消去保護を有効にします。

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. 更新内容を保存します。

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Azure CLI の使用

+ [Azure CLI のインストール](/cli/azure/install-azure-cli)がある場合は、次のコマンドを実行して必要なプロパティを有効にすることができます。

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2 - キー コンテナー内のキーの作成

Azure Key Vault に既にキーがある場合は、この手順をスキップしてください。

1. [Azure portal にサインイン](https://portal.azure.com)して、キー コンテナーの概要ページを開きます。

1. 左側の **[キー]** を選択し、 **[+ 生成/インポート]** を選択します。

1. **[キーの作成]** ウィンドウで、 **[オプション]** 一覧から、キーの作成に使用する方法を選択します。 新しいキーを **生成** したり、既存のキーを **アップロード** したり、キーのバックアップを選択して **バックアップを復元** したりできます。

1. キーの **[名前]** を入力し、必要に応じてその他のキー プロパティを選択します。

1. **[作成]** を選択してデプロイを開始します。

1. キー識別子を書き留めてください。これは、**キー値の URI**、**キー名**、および **キー バージョン** で構成されます。 Azure Cognitive Search で暗号化されたインデックスを定義するために識別子が必要になります。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="新しい Key Vault キーを作成する":::

## <a name="3---register-an-app-in-active-directory"></a>3 - Active Directory へのアプリの登録

1. [Azure portal](https://portal.azure.com) で、サブスクリプションの Azure Active Directory リソースを検索します。

1. 左の **[管理]** で、 **[アプリの登録]** を選択し、次に **[新しい登録]** を選択します。

1. 登録に名前、おそらく検索アプリケーション名に似た名前を付けます。 **[登録]** を選択します。

1. アプリの登録が作成されたら、アプリケーション ID をコピーします。 この文字列をアプリケーションに指定する必要があります。 

   [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK) をステップ実行している場合は、この値を **appsettings.json** ファイルに貼り付けます。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="[Essentials] セクションの [アプリケーション ID]":::

1. 次に、左側の **[証明書とシークレット]** を選択します。

1. **[新しいクライアント シークレット]** を選択します。 シークレットに表示名を付け、 **[追加]** を選択します。

1. アプリケーション シークレットをコピーします。 サンプルをステップ実行している場合は、この値を **appsettings.json** ファイルに貼り付けます。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="アプリケーション シークレット":::

## <a name="4---grant-key-access-permissions"></a>4 - キーのアクセス許可の付与

この手順では、キー コンテナーのアクセス ポリシーを作成します。 このポリシーにより、登録したアプリケーションに、カスタマー マネージド キーを使用するための Active Directory アクセス許可を付与します。

アクセス許可はいつでも取り消すことができます。 取り消すと、そのキー コンテナーを使用する検索サービスのインデックスまたはシノニム マップを使用できなくなります。 キー コンテナーのアクセス許可を後から復元すると、インデックス/シノニム マップへのアクセスが復元されます。 詳細については、「[キー コンテナーへのアクセスをセキュリティで保護する](../key-vault/general/secure-your-key-vault.md)」を参照してください。

1. Azure portal にまだいる場合は、キー コンテナーの **[概要]** ページを開きます。 

1. 左側の **[アクセス ポリシー]** を選択し、 **[+ アクセス ポリシーの追加]** を選択します。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="新しい Key Vault アクセス ポリシーを追加する":::

1. **[プリンシパルの選択]** を選択し、Active Directory に登録したアプリケーションを選択します。 それを名前で検索できます。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Key Vault アクセス ポリシーのプリンシパルを選択する":::

1. **[キーのアクセス許可]** で *[取得]* 、 *[キーの折り返しを解除]* と *[キーを折り返す]* を選択します。

1. **[シークレットのアクセス許可]** で、 *[取得]* を選択します。

1. **[証明書のアクセス許可]** で、 *[取得]* を選択します。

1. **[追加]** を選択し、次に **[保存]** を選択します。

> [!Important]
> Azure Cognitive Search 内の暗号化されたコンテンツは、特定の **バージョン** の特定の Azure Key Vault キーを使用するように構成されます。 キーまたはバージョンを変更する場合は、以前のキー/バージョンを削除する **前** に、新しいキー/バージョンを使用するようにインデックスまたはシノニム マップを更新する必要があります。 そうしないと、インデックスまたはシノニム マップが使用できない状態になり、キー アクセスが失われた場合にコンテンツを暗号化解除できません。

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5 - コンテンツの暗号化

インデックス、データ ソース、スキルセット、インデクサー、またはシノニム マップにカスタマー マネージド キーを追加するには、[Search REST API](/rest/api/searchservice/) または SDK を使用する必要があります。 ポータルでは、シノニム マップや暗号化プロパティが公開されません。 有効な API インデックスを使用すると、データ ソース、スキルセット、インデクサー、およびシノニム マップによって、最上位の **encryptionKey** プロパティがサポートされます。

この例では、Azure Key Vault と Azure Active Directory の値を使用して、REST API を使用します。

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

> [!Note]
> Key Vault のこれらの詳細はシークレットとは見なされず、Azure portal 内の関連する Azure Key Vault キーのページを参照して簡単に取得できます。

## <a name="example-index-encryption"></a>例:インデックスの暗号化

[インデックスの作成 (Azure Cognitive Search REST API)](/rest/api/searchservice/create-index) に関する記事を使用して、暗号化されたインデックスを作成します。 使用する暗号化キーを指定するには、`encryptionKey` プロパティを使用します。
> [!Note]
> Key Vault のこれらの詳細はシークレットとは見なされず、Azure portal 内の関連する Azure Key Vault キーのページを参照して簡単に取得できます。

## <a name="rest-examples"></a>REST の例

このセクションでは、暗号化されたインデックスとシノニム マップ用の完全な JSON について説明します

### <a name="index-encryption"></a>インデックスの暗号化

REST API を使用した新しいインデックスの作成の詳細については、[インデックスの作成 (REST API)](/rest/api/searchservice/create-index) に関するページを参照してください。こことの唯一の違いは、インデックス定義の一部として暗号化キーの詳細が指定されている点です。

```json
{
 "name": "hotels",
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

これで、インデックス作成要求を送信し、インデックスの使用を正常に開始できます。

### <a name="synonym-map-encryption"></a>シノニム マップの暗号化

[シノニム マップの作成 (Azure Cognitive Search REST API)](/rest/api/searchservice/create-synonym-map) に関する記事を使用して、暗号化されたシノニム マップを作成します。 使用する暗号化キーを指定するには、`encryptionKey` プロパティを使用します。

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

これで、シノニム マップ作成要求を送信し、シノニム マップの使用を正常に開始できます。

## <a name="example-data-source-encryption"></a>例:データ ソースの暗号化

「[データ ソースの作成 (Azure Cognitive Search REST API)](/rest/api/searchservice/create-data-source)」を使用して、暗号化されたデータ ソースを作成します。 使用する暗号化キーを指定するには、`encryptionKey` プロパティを使用します。

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

これで、データ ソース作成要求を送信すると、データ ソースの正常な使用を開始できます。

## <a name="example-skillset-encryption"></a>例:スキルセットの暗号化

[スキルセットの作成 (Azure Cognitive Search REST API)](/rest/api/searchservice/create-skillset) に関する記事を使用して、暗号化されたスキルセットを作成します。 使用する暗号化キーを指定するには、`encryptionKey` プロパティを使用します。

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

これで、スキルセット作成要求を送信すると、データ ソースの正常な使用を開始できます。

## <a name="example-indexer-encryption"></a>例:インデクサーの暗号化

[インデクサーの作成 (Azure Cognitive Search REST API)](/rest/api/searchservice/create-indexer) に関する記事を使用して、暗号化されたインデクサーを作成します。 使用する暗号化キーを指定するには、`encryptionKey` プロパティを使用します。

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

これで、インデクサー作成要求を送信すると、データ ソースの正常な使用を開始できます。

>[!Important]
> `encryptionKey` を既存の Search インデックスまたはシノニム マップに追加することはできませんが、3 つのキー コンテナーの詳細のいずれかに別の値を指定して更新することはできます (たとえば、キー バージョンの更新)。 新しいキー コンテナー キーまたは新しいキー バージョンに変更する場合は、以前のキー/バージョンを削除する **前** に、まずキーを使用するすべての Search キー インデックスまたはシノニム マップを、新しいキー/バージョンを使用するように更新する必要があります。 そうしないと、インデックスまたはシノニム マップが使用できない状態になり、キー アクセスが失われた場合にコンテンツを暗号化解除できません。 キー コンテナーのアクセス許可を後から復元すると、コンテンツへのアクセスが復元されます。

## <a name="simpler-alternative-trusted-service"></a>簡単な代替方法:信頼できるサービス

テナントの構成と認証の要件によっては、キー コンテナー キーにアクセスするためのより簡単なアプローチを実装できる場合があります。 Active Directory アプリケーションを作成して使用する代わりに、検索サービスのシステム マネージド ID を有効にすることで、それを信頼されたサービスにすることができます。 その後、信頼された検索サービスを、AD に登録されたアプリケーションではなく、セキュリティ プリンシパルとして使用して、キー コンテナー キーにアクセスします。

このアプローチにより、アプリケーションの登録の手順とアプリケーション シークレットを省略し、暗号化キーの定義を、キー コンテナー コンポーネント (URI、コンテナー名、キー バージョン) だけに簡略化できます。

通常、マネージド ID を使用すると、コードに資格情報 (ApplicationID や ApplicationSecret) を格納することなく、検索サービスで Azure Key Vault に対する認証を行うことができます。 この種類のマネージド ID のライフサイクルは、検索サービスのライフサイクルに関連付けられます。そのため、割り当てることのできるマネージド ID は 1 つだけです。 マネージド ID が機能する方法について詳しくは、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

1. 検索サービスを信頼されたサービスにします。
   ![システム割り当てマネージド ID をオンにする](./media/search-managed-identities/turn-on-system-assigned-identity.png "システム割り当てマネージド ID をオンにする")

1. Azure Key Vault でアクセス ポリシーを設定する場合は、信頼された検索サービスをプリンシパルとして選択します (AD に登録されたアプリケーションではなく)。 アクセス キーのアクセス許可の付与の手順に示されている同じアクセス許可 (複数の GET、WRAP、UNWRAP) を割り当てます。

1. Active Directory のプロパティを省略する `encryptionKey` の簡略化された構成を使用します。

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

この簡略化されたアプローチを採用できない条件には、次のようなものがあります。

+ 検索サービスのアクセス許可をキー コンテナーに直接付与することはできません (たとえば、検索サービスが Azure Key Vault ではなく別の Active Directory テナント内にある場合などです)。

+ 暗号化された複数の (別のキー コンテナーからの異なるキーをそれぞれが使用する) インデックス/シノニム マップをホストするには、1 つの検索サービスが必要です。各キー コンテナーでは **異なる ID** を使用して認証を行う必要があります。 検索サービスではマネージド ID を 1 つしか持つことができないため、複数の ID の要件によってシナリオの簡略化されたアプローチが不適格とされます。  

## <a name="work-with-encrypted-content"></a>暗号化されたコンテンツを使用する

カスタマー マネージド キーの暗号化を使用すると、暗号化/暗号化解除の処理が増えるため、インデックス作成とクエリの両方で待ち時間があることに気付きます。 Azure Cognitive Search では、暗号化アクティビティはログに記録されませんが、キー コンテナーのログを通してキーへのアクセスを監視できます。 キー コンテナー構成の一環として[ログを有効](../key-vault/general/logging.md)にすることが推奨されます。

時間の経過と共に、キーのローテーションが発生すると予想されます。 キーのローテーションのたびに、次の手順に従うことが重要です。

1. [インデックスやシノニム マップ](search-security-get-encryption-keys.md)によって使用されるキーを確認します。
1. [キー コンテナー内に新しいキーを作成します](../key-vault/keys/quick-create-portal.md)が、元のキーは使用可能なままにしておきます。
1. インデックスまたはシノニム マップの [encryptionKey プロパティを更新](/rest/api/searchservice/update-index)して新しい値を使用します。 別の値を使用するように更新できるのは、当初このプロパティを使用して作成されたオブジェクトだけです。
1. キー コンテナーで以前のキーを無効にするか削除します。 キー アクセスを監視して、新しいキーが使用されていることを確認します。

パフォーマンス上の理由で、検索サービスでは、キーが最大で数時間キャッシュされます。 新しいキーを指定せずにキーを無効にした、または削除した場合、キャッシュの有効期限が切れるまで、クエリは一時的に機能し続けます。 ただし、検索サービスでコンテンツの暗号化解除ができなくなると、次のメッセージが表示されます。"アクセスが禁止されています。 使用されたクエリ キーが失効している可能性があります - もう一度お試しください。" 

## <a name="next-steps"></a>次のステップ

Azure セキュリティ アーキテクチャを使い慣れていない場合は、[Azure のセキュリティのドキュメント](../security/index.yml)を確認してください。具体的には次の記事です。

> [!div class="nextstepaction"]
> [保存時のデータ暗号化](../security/fundamentals/encryption-atrest.md)
---
title: カスタマー マネージドのキーを使用した保存時の暗号化
titleSuffix: Azure Cognitive Search
description: Azure Key Vault 内で作成・管理するキーを使用して、Azure Cognitive Search 内でインデックスとシノニム マップによるサーバー側の暗号化を補完します。
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: 2dc7458dd905ff84455927c81b4ea93765d4f5cb
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928821"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Azure Cognitive Search のデータ暗号化のためにカスタマー マネージド キーを構成する

Azure Cognitive Search では、インデックス付きコンテンツの保存時に、[サービス マネージド キー](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components)を使用して自動的に暗号化します。 保護を強化する必要がある場合は、Azure Key Vault 内で作成して管理するキーを使用して、既定の暗号化を追加の暗号化レイヤーで補完できます。 この記事では、CMK 暗号化を設定する手順について説明します。

CMK 暗号化は、[Azure Key Vault](../key-vault/general/overview.md) に依存しています。 独自の暗号化キーを作成してキー コンテナーに格納したり、Azure Key Vault の API を使って暗号化キーを生成したりすることができます。 [ログを有効にする](../key-vault/general/logging.md)場合は、Azure Key Vault を使用してキーの使用状況を監査することもできます。  

カスタマー マネージド キーを使用した暗号化は、検索サービス レベル自体で指定されるのではなく、それらのオブジェクトの作成時に個々のインデックスまたはシノニム マップに適用されます。 暗号化できるのは新しいオブジェクトだけです。 既存のコンテンツを暗号化することはできません。

すべてのキーを同じキー コンテナーに置く必要はありません。 独自のカスタマー マネージド暗号化キーで暗号化されて異なるキー コンテナーに格納されている、複数の暗号化されたインデックスやシノニム マップを、1 つの検索サービスでホストすることができます。 また、カスタマー マネージドのキーを使用して暗号化されていない同じサービス内に、インデックスとシノニム マップを置くこともできます。 

## <a name="double-encryption"></a>二重暗号化

2020 年 8 月 1 日以降に作成され、特定のリージョン内にあるサービスの場合、CMK 暗号化のスコープには、現在以下のリージョンで使用できる[完全な二重暗号化](search-security-overview.md#double-encryption)を実現する一時ディスクが含まれています。 

+ 米国西部 2
+ 米国東部
+ 米国中南部
+ US Gov バージニア州
+ US Gov アリゾナ

異なるリージョンを使用しているか、8 月 1 日より前に作成されたサービスを使用している場合、CMK 暗号化はデータ ディスクのみに限定されていて、サービスによって使用される一時ディスクは除かれています。

## <a name="prerequisites"></a>前提条件

この例では、以下のサービスが使用されます。 

+ [Azure Cognitive Search サービスを作成](search-create-service-portal.md)するか、[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 

+ [Azure Key Vault リソースを作成する](../key-vault/secrets/quick-create-portal.md#create-a-vault)か、Azure Cognitive Search と同じサブスクリプション内で既存のコンテナーを見つけます。 この機能には同じサブスクリプション要件があります。

+ [Azure PowerShell](/powershell/azure/) または [Azure CLI](/cli/azure/install-azure-cli) を構成タスクに使用します。

+ [Postman](search-get-started-postman.md)、[Azure PowerShell](./search-get-started-powershell.md)、および [.NET SDK プレビュー](https://aka.ms/search-sdk-preview)を使用して、暗号化キー パラメーターを含むインデックスおよびシノニム マップを作成する REST API を呼び出すことができます。 現在のところ、ポータルでは、インデックスまたはシノニム マップへのキーの追加はサポートされていません。

>[!Note]
> カスタマー マネージド キーを使用する暗号化の性質上、Azure Key Vault キーが削除されている場合は、Azure Cognitive Search でデータを取得できません。 Key Vault キーの誤った削除によるデータ損失を防ぐため、キー コンテナーで論理的な削除と消去保護を有効にする必要があります。 論理的な削除は既定で有効になっているため、この機能を意図的に無効にした場合にのみ問題が発生します。 消去保護は既定では有効になりませんが、Azure Cognitive Search の CMK 暗号化には必須です。 詳細については、[論理的な削除](../key-vault/general/soft-delete-overview.md)と[消去保護](../key-vault/general/soft-delete-overview.md#purge-protection)の概要に関するページを参照してください。

## <a name="1---enable-key-recovery"></a>1 - キーの回復の有効化

キー コンテナーでは、**論理的な削除**と**消去保護**が有効になっている必要があります。 これらの機能は、ポータルまたは下記の PowerShell または Azure CLI コマンドを使用して設定することができます。

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

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 - 新しいキーの作成

既存のキーを使用して Azure Cognitive Search コンテンツを暗号化する場合は、この手順をスキップしてください。

1. [Azure portal にサインイン](https://portal.azure.com)して、キー コンテナーの概要ページを開きます。

1. 左側のナビゲーション ウィンドウで **[キー]** 設定を選択し、 **[+ 生成/インポート]** をクリックします。

1. **[キーの作成]** ウィンドウで、 **[オプション]** 一覧から、キーの作成に使用する方法を選択します。 新しいキーを**生成**したり、既存のキーを**アップロード**したり、キーのバックアップを選択して**バックアップを復元**したりできます。

1. キーの **[名前]** を入力し、必要に応じてその他のキー プロパティを選択します。

1. **[作成]** ボタンをクリックしてデプロイを開始します。

キー識別子を書き留めてください。これは、**Key Vault の URI**、**キー名**、および**キー バージョン**で構成されます。 これらは、暗号化されたインデックスを Azure Cognitive Search で定義するために必要になります。
 
![新しい Key Vault キーを作成する](./media/search-manage-encryption-keys/create-new-key-vault-key.png "新しい Key Vault キーを作成する")

## <a name="3---create-a-service-identity"></a>3 - サービス ID の作成

検索サービスに ID を割り当てると、キー コンテナーのアクセス許可を検索サービスに付与することができます。 検索サービスでは、その ID を使用して Azure Key Vault で認証を行います。

Azure Cognitive Search では、ID を割り当てるための 2 つの方法 (マネージド ID または外部で管理される Azure Active Directory アプリケーション) がサポートされます。 

可能であれば、マネージド ID を使用してください。 これが検索サービスに ID を割り当てる最も簡単な方法であり、ほとんどのシナリオに対応します。 インデックスとシノニム マップに対して複数のキーを使用する場合や、ID ベースの認証に適合しない分散アーキテクチャ内にソリューションがある場合は、この記事の最後で説明する高度な方法である、[外部で管理される Azure Active Directory のアプローチ](#aad-app)を使用してください。

 通常、マネージド ID を使用すると、コードに資格情報を格納することなく、検索サービスで Azure Key Vault に対する認証を行うことができます。 この種類のマネージド ID のライフサイクルは、検索サービスのライフサイクルに関連付けられます。そのため、割り当てることのできるマネージド ID は 1 つだけです。 [マネージド ID の詳細を参照してください](../active-directory/managed-identities-azure-resources/overview.md)。

1. [Azure portal にサインイン](https://portal.azure.com)して、検索サービスの概要ページを開きます。 

1. 左側のナビゲーション ウィンドウにある **[ID]** をクリックして、その状態を **[オン]** に変更し、 **[保存]** をクリックします。

![マネージド ID を有効化する](./media/search-enable-msi/enable-identity-portal.png "マネージド ID を有効化する")

## <a name="4---grant-key-access-permissions"></a>4 - キーのアクセス許可の付与

検索サービスで Key Vault キーを使用できるようにするには、検索サービスに特定のアクセス許可を付与する必要があります。

アクセス許可はいつでも取り消すことができます。 取り消すと、そのキー コンテナーを使用する検索サービスのインデックスまたはシノニム マップを使用できなくなります。 キー コンテナーのアクセス許可を後から復元すると、インデックス/シノニム マップへのアクセスが復元されます。 詳細については、「[キー コンテナーへのアクセスをセキュリティで保護する](../key-vault/general/secure-your-key-vault.md)」を参照してください。

1. [Azure portal にサインイン](https://portal.azure.com)して、キー コンテナーの概要ページを開きます。 

1. 左側のナビゲーション ウィンドウで **[アクセス ポリシー]** 設定を選択し、 **[+ 新規追加]** をクリックします。

   ![新しい Key Vault アクセス ポリシーを追加する](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "新しい Key Vault アクセス ポリシーを追加する")

1. **[プリンシパルの選択]** をクリックして Azure Cognitive Search サービスを選択します。 名前またはマネージド ID を有効にした後に表示されたオブジェクト ID で検索できます。

   ![Key Vault アクセス ポリシーのプリンシパルを選択する](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Key Vault アクセス ポリシーのプリンシパルを選択する")

1. **[キーのアクセス許可]** をクリックし、 *[取得]* 、 *[キーの折り返しを解除]* 、および *[キーを折り返す]* を選択します。 *[Azure Data Lake Storage または Azure Storage]* テンプレートを使用すると、必要なアクセス許可をすぐに選択できます。

   Azure Cognitive Search には次の[アクセス許可](../key-vault/keys/about-keys.md#key-operations)を付与する必要があります。

   * *取得* - 検索サービスでは、キー コンテナー内のユーザーのキーの公開部分を取得できます。
   * *キーを折り返す* - 検索サービスでは、ユーザーのキーを使用して内部の暗号化キーを保護できます。
   * *キーの折り返しを解除* - 検索サービスでは、ユーザーのキーを使用して内部の暗号化キーの折り返しを解除できます。

   ![Key Vault アクセス ポリシー キーのアクセス許可を選択する](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Key Vault アクセス ポリシー キーのアクセス許可を選択する")

1. **[シークレットのアクセス許可]** で、 *[取得]* を選択します。

1. **[証明書のアクセス許可]** で。 *[取得]* を選択します。

1. **[OK]** 、 **[保存]** の順にクリックしてアクセス ポリシーの変更を保存します。

> [!Important]
> Azure Cognitive Search 内の暗号化されたコンテンツは、特定の**バージョン**の特定の Azure Key Vault キーを使用するように構成されます。 キーまたはバージョンを変更する場合は、以前のキー/バージョンを削除する**前**に、新しいキー/バージョンを使用するようにインデックスまたはシノニム マップを更新する必要があります。 そうしないと、インデックスまたはシノニム マップが使用できない状態になり、キー アクセスが失われた場合にコンテンツを暗号化解除できません。   

## <a name="5---encrypt-content"></a>5 - コンテンツの暗号化

インデックスまたはシノニム マップにカスタマー マネージド キーを追加するには、[Search REST API](/rest/api/searchservice/) または SDK を使用する必要があります。 ポータルでは、シノニム マップや暗号化プロパティが公開されません。 有効な API を使用すると、インデックスとシノニム マップの両方で最上位の **encryptionKey** プロパティがサポートされます。 

キー コンテナーのキーの**キー コンテナー URI**、**キー名**、および**キー バージョン**を使用して、以下のように **encryptionKey** 定義を作成します。

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> Key Vault のこれらの詳細はシークレットとは見なされず、Azure portal 内の関連する Azure Key Vault キーのページを参照して簡単に取得できます。

マネージド ID ではなく AAD アプリケーションを Key Vault 認証に使用する場合は、AAD アプリケーションの**アクセス資格情報**を暗号化キーに追加してください。 
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

## <a name="example-index-encryption"></a>例:インデックスの暗号化
REST API を使用した新しいインデックスの作成方法の詳細については、「[インデックスの作成 (Azure Cognitive Search REST API)](/rest/api/searchservice/create-index)」を参照してください。ここに示す例における唯一の違いは、インデックス定義の一部として暗号化キーの詳細が指定されている点です。 

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
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
これで、インデックス作成要求を送信し、インデックスの使用を正常に開始できます。

## <a name="example-synonym-map-encryption"></a>例:シノニム マップの暗号化

REST API を使用した新しいシノニム マップの作成方法の詳細については、[シノニム マップを作成する (Azure Cognitive Search REST API)](/rest/api/searchservice/create-synonym-map) 方法に関する記事を参照してください。ここに示す例における唯一の違いは、シノニム マップ定義の一部として暗号化キーの詳細が指定されている点です。 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
これで、シノニム マップ作成要求を送信し、シノニム マップの使用を正常に開始できます。

>[!Important] 
> **encryptionKey** を Azure Cognitive Search の既存のインデックスまたはシノニム マップに追加することはできませんが、Key Vault の 3 つの詳細のいずれかに別の値を指定して更新することはできます (たとえば、キー バージョンを更新します)。 新しい Key Vault キーまたはキー バージョンに変更する場合は、以前のキー/バージョンを削除する**前**に、キーを使用する Azure Cognitive Search のインデックスまたはシノニム マップを、新しいキー/バージョンを使用するように更新する必要があります。 そうしないと、インデックスまたはシノニム マップが使用できない状態になり、キー アクセスが失われた場合にコンテンツを暗号化解除できません。   
> キー コンテナーのアクセス許可を後から復元すると、コンテンツへのアクセスが復元されます。

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a> 高度な方法:外部で管理される Azure Active Directory アプリケーションを使用する

マネージド ID を使用できない場合は、Azure Cognitive Search サービスのセキュリティ プリンシパルを持つ Azure Active Directory アプリケーションを作成できます。 具体的には、以下の条件下ではマネージド ID を使用できません。

* 検索サービスのアクセス許可をキー コンテナーに直接付与することはできません (たとえば、検索サービスが Azure Key Vault ではなく別の Active Directory テナント内にある場合などです)。

* 暗号化された複数の (別のキー コンテナーからの異なるキーをそれぞれが使用する) インデックス/シノニム マップをホストするには、1 つの検索サービスが必要です。各キー コンテナーでは**異なる ID** を使用して認証を行う必要があります。 異なる ID を使用して別のキー コンテナーを管理する必要がない場合は、前述のマネージド ID オプションの使用を検討してください。  

このようなトポロジに対応するために、Azure Cognitive Search では、Azure Active Directory (AAD) アプリケーションを使用した検索サービスとキー コンテナー間の認証がサポートされます。    
ポータルで AAD アプリケーションを作成するには、次の手順を実行します。

1. [Azure Active Directory アプリケーションを作成します](../active-directory/develop/howto-create-service-principal-portal.md)。

1. [アプリケーション ID と認証キーを取得](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)します。これらは、暗号化されたインデックスの作成に必要です。 指定する必要のある値は、**アプリケーション ID** と**認証キー**です。

>[!Important]
> マネージド ID ではなく AAD アプリケーションを認証に使用することを決定する際は、AAD アプリケーションを管理する権限が Azure Cognitive Search にはなく、AAD アプリケーションの管理 (アプリケーションの認証キーの定期的なローテーションなど) はユーザーが行う必要があるという事実を考慮してください。
> AAD アプリケーションまたはその認証キーを変更する場合は、以前のアプリケーションまたはその認証キーを削除する**前**、およびそれらへのキー コンテナー アクセスを取り消す前に、アプリケーションを使用する Azure Cognitive Search のインデックスまたはシノニム マップを、新しいアプリケーション ID/キーを使用するように更新する必要があります。
> そうしないと、インデックスまたはシノニム マップが使用できない状態になり、キー アクセスが失われた場合にコンテンツを暗号化解除できません。

## <a name="work-with-encrypted-content"></a>暗号化されたコンテンツを使用する

CMK 暗号化を使用すると、暗号化/暗号化解除の処理が増えるため、インデックス作成とクエリの両方で待ち時間があることに気付きます。 Azure Cognitive Search では、暗号化アクティビティはログに記録されませんが、キー コンテナーのログを通してキーへのアクセスを監視できます。 キー コンテナーの設定の一環として[ログを有効](../key-vault/general/logging.md)にすることをお勧めします。

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
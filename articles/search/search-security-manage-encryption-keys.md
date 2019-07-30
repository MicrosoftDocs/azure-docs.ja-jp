---
title: Azure Key Vault (プレビュー) で保存時の暗号化に顧客が管理するキーを使用する - Azure Search
description: Azure Key Vault 内で作成して管理するキーを使用して、Azure Search 内でインデックスとシノニム マップによるサーバー側暗号化を補完します。
author: NatiNimni
manager: jlembicz
ms.author: natinimn
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: ''
ms.openlocfilehash: 949628fa52b4b020d70b75f4a0e7895f1e0f8bba
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485314"
---
# <a name="azure-search-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault でユーザーが管理するキーを Azure Search 暗号化に使用する

> [!Note]
> 顧客が管理するキーを使った暗号化はプレビュー段階にあり、運用環境での使用は意図していません。 [REST API バージョン 2019-05-06-Preview](search-api-preview.md) でこの機能を提供します。 .NET SDK バージョン 8.0-preview を使用することもできます。
>
> この機能は、無料のサービスには使用できません。 2019-01-01 以降に作成された課金対象の検索サービスを使用する必要があります。 現時点では、ポータルはサポートされていません。

既定では、Azure Search では[サービス管理キー](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest#data-encryption-models)を使用してユーザー コンテンツを保存時に暗号化します。 Azure Key Vault 内で作成して管理するキーを使用すると、既定の暗号化を追加の暗号化レイヤーで補完することができます。 この記事では、その手順について説明します。

サーバー側暗号化は、[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) との統合によってサポートされます。 独自の暗号化キーを作成してキー コンテナーに格納したり、Azure Key Vault の API を使って暗号化キーを生成したりすることができます。 Azure Key Vault を使用してキー使用法を監査することもできます。 

ユーザーが管理するキーを使用した暗号化は、検索サービス レベルではなく、それらのオブジェクトの作成時にインデックス レベルまたはシノニム マップ レベルで構成されます。 既存のコンテンツを暗号化することはできません。 

別のキー コンテナーからの異なるキーを使用することができます。 これは、1 つの検索サービスが (それぞれ異なる、ユーザーが管理するキーを使用して) 暗号化された複数のインデックス/シノニム マップを、ユーザーが管理するキーを使用して暗号化されていないインデックス/シノニム マップと共にホストできることを意味します。 

## <a name="prerequisites"></a>前提条件

この例では、次のサービスを使用します。 

+ [Azure Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このチュートリアル用には、無料のサービスを使用できます。

+ [Azure Key Vault リソースを作成](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault)するか、サブスクリプションから既存のコンテナーを見つけます。

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) または [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) を構成タスクに使用します。

+ プレビュー REST API の呼び出しには、[Postman](search-get-started-postman.md)、[Azure PowerShell](search-create-index-rest-api.md)、および [Azure Search SDK](https://aka.ms/search-sdk-preview) を使用できます。 現時点では、ユーザーが管理する暗号化のポータル サポートまたは .NET SDK サポートはありません。

## <a name="1---enable-key-recovery"></a>1 - キーの回復の有効化

この手順は省略可能ですが、強くお勧めします。 Azure Key Vault リソースを作成したら、次に示す PowerShell コマンドまたは Azure CLI コマンドを実行して、選択したキー コンテナーで**論理的な削除**と**消去保護**を有効にします。   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

>[!Note]
> ユーザーが管理するキーを使用した暗号化機能の本質により、Azure Key Vault キーが削除されている場合、Azure Search ではデータを取得できません。 Key Vault キーの誤削除によるデータ損失を防ぐには、選択したキー コンテナーで論理的な削除と消去保護を有効にすることを強くお勧めします。 詳細については、[Azure Key Vault の論理的な削除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)に関する記事を参照してください。   

## <a name="2---create-a-new-key"></a>2 - 新しいキーの作成

既存のキーを使用して Azure Search コンテンツを暗号化する場合は、この手順をスキップしてください。

1. [Azure portal にサインイン](https://portal.azure.com)して、キー コンテナー ダッシュボードに移動します。

1. 左側のナビゲーション ウィンドウで **[キー]** 設定を選択し、 **[+ 生成/インポート]** をクリックします。

1. **[キーの作成]** ウィンドウで、 **[オプション]** 一覧から、キーの作成に使用する方法を選択します。 新しいキーを**生成**したり、既存のキーを**アップロード**したり、キーのバックアップを選択して**バックアップを復元**したりできます。

1. キーの **[名前]** を入力し、必要に応じてその他のキー プロパティを選択します。

1. **[作成]** ボタンをクリックしてデプロイを開始します。

キー識別子を書き留めてください。これは、**Key Vault の URI**、**キー名**、および**キー バージョン**で構成されます。 これらは、暗号化されたインデックスを Azure Search で定義するために必要になります。
 
![新しい Key Vault キーの作成](./media/search-manage-encryption-keys/create-new-key-vault-key.png "新しい Key Vault キーの作成")

## <a name="3---create-a-service-identity"></a>3 - サービス ID の作成

検索サービスに ID を割り当てると、キー コンテナーのアクセス許可を検索サービスに付与することができます。 検索サービスでは、その ID を使用して Azure Key Vault で認証を行います。

Azure Search では、ID を割り当てるための 2 つの方法 (マネージド ID または外部で管理される Azure Active Directory アプリケーション) がサポートされます。 

可能であれば、マネージド ID を使用してください。 これが検索サービスに ID を割り当てる最も簡単な方法であり、ほとんどのシナリオに対応します。 インデックスとシノニム マップに対して複数のキーを使用する場合や、ID ベースの認証に適合しない分散アーキテクチャ内にソリューションがある場合は、この記事の最後で説明する高度な方法である、[外部で管理される Azure Active Directory のアプローチ](#aad-app)を使用してください。

 通常、マネージド ID を使用すると、コードに資格情報を格納することなく、検索サービスで Azure Key Vault に対する認証を行うことができます。 この種類のマネージド ID のライフサイクルは、検索サービスのライフサイクルに関連付けられます。そのため、割り当てることのできるマネージド ID は 1 つだけです。 [マネージド ID の詳細を参照してください](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

1. マネージド ID を作成するには、[Azure portal にサインイン](https://portal.azure.com)して検索サービスのダッシュボードを開きます。 

1. 左側のナビゲーション ウィンドウにある **[ID]** をクリックして、その状態を **[オン]** に変更し、 **[保存]** をクリックします。

![マネージド ID の有効化](./media/search-enable-msi/enable-identity-portal.png "マネージド ID の有効化")

## <a name="4---grant-key-access-permissions"></a>4 - キーのアクセス許可の付与

検索サービスで Key Vault キーを使用できるようにするには、検索サービスに特定のアクセス許可を付与する必要があります。

アクセス許可はいつでも取り消すことができます。 取り消すと、そのキー コンテナーを使用する検索サービスのインデックスまたはシノニム マップを使用できなくなります。 キー コンテナーのアクセス許可を後から復元すると、インデックス/シノニム マップへのアクセスが復元されます。 詳細については、「[キー コンテナーへのアクセスをセキュリティで保護する](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)」を参照してください。

1. [Azure portal にサインイン](https://portal.azure.com)して、キー コンテナーの概要ページを開きます。 

1. 左側のナビゲーション ウィンドウで **[アクセス ポリシー]** 設定を選択し、 **[+ 新規追加]** をクリックします。

   ![新しいキー コンテナー アクセス ポリシーの追加](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "新しいキー コンテナー アクセス ポリシーの追加")

1. **[プリンシパルの選択]** をクリックして Azure Search サービスを選択します。 名前またはマネージド ID を有効にした後に表示されたオブジェクト ID で検索できます。

   ![キー コンテナー アクセス ポリシーのプリンシパルの選択](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "キー コンテナー アクセス ポリシーのプリンシパルの選択")

1. **[キーのアクセス許可]** をクリックし、 *[取得]* 、 *[キーの折り返しを解除]* 、および *[キーを折り返す]* を選択します。 *[Azure Data Lake Storage または Azure Storage]* テンプレートを使用すると、必要なアクセス許可をすぐに選択できます。

   Azure Search には次の[アクセス許可](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)を付与する必要があります。

   * *取得* - 検索サービスでは、キー コンテナー内のユーザーのキーの公開部分を取得できます。
   * *キーを折り返す* - 検索サービスでは、ユーザーのキーを使用して内部の暗号化キーを保護できます。
   * *キーの折り返しを解除* - 検索サービスでは、ユーザーのキーを使用して内部の暗号化キーの折り返しを解除できます。

   ![キー コンテナー アクセス ポリシーのキーのアクセス許可の選択](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "キー コンテナー アクセス ポリシーのキーのアクセス許可の選択")

1. **[OK]** 、 **[保存]** の順にクリックしてアクセス ポリシーの変更を保存します。

> [!Important]
> Azure Search 内の暗号化されたコンテンツは、特定の**バージョン**の特定の Azure Key Vault キーを使用するように構成されます。 キーまたはバージョンを変更する場合は、以前のキー/バージョンを削除する**前**に、新しいキー/バージョンを使用するようにインデックスまたはシノニム マップを更新する必要があります。 そうしないと、インデックスまたはシノニム マップが使用できない状態になり、キー アクセスが失われた場合にコンテンツを暗号化解除できません。   

## <a name="5---encrypt-content"></a>5 - コンテンツの暗号化

Azure portal を使用して、ユーザーが管理するキーで暗号化されたインデックスまたはシノニム マップを作成することはできません。 このようなインデックスまたはシノニム マップを作成するには、Azure Search REST API を使用します。

インデックスとシノニム マップの両方では、キーを指定するために使用する新しい最上位の **encryptionKey** プロパティがサポートされます。 

Key Vault キーの **Key Vault の URI**、**キー名**、および**キー バージョン**を使用して、**encryptionKey** 定義を作成できます。

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
REST API を使用した新しいインデックスの作成方法の詳細については、「[Create Index (Azure Search Service REST API) (インデックスを作成する (Azure Search サービス REST API))](https://docs.microsoft.com/rest/api/searchservice/create-index)」を参照してください。ここに示す例における唯一の違いは、インデックス定義の一部として暗号化キーの詳細が指定されている点です。 

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

REST API を使用した新しいシノニム マップの作成方法の詳細については、「[Create Synonym Map (Azure Search Service REST API) (シノニム マップを作成する (Azure Search サービス REST API))](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)」を参照してください。ここに示す例における唯一の違いは、シノニム マップ定義の一部として暗号化キーの詳細が指定されている点です。 

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
> **encryptionKey** を Azure Search の既存のインデックスまたはシノニム マップに追加することはできませんが、Key Vault の 3 つの詳細のいずれかに別の値を指定して更新することはできます (たとえば、キー バージョンを更新します)。 新しい Key Vault キーまたはキー バージョンに変更する場合は、以前のキー/バージョンを削除する**前**に、キーを使用する Azure Search のインデックスまたはシノニム マップを、新しいキー/バージョンを使用するように更新する必要があります。 そうしないと、インデックスまたはシノニム マップが使用できない状態になり、キー アクセスが失われた場合にコンテンツを暗号化解除できません。   
> キー コンテナーのアクセス許可を後から復元すると、コンテンツへのアクセスが復元されます。

## <a name="aad-app"></a> 高度な方法:外部で管理される Azure Active Directory アプリケーションを使用する

マネージド ID を使用できない場合は、Azure Search サービスのセキュリティ プリンシパルを持つ Azure Active Directory アプリケーションを作成できます。 具体的には、以下の条件下ではマネージド ID を使用できません。

* 検索サービスのアクセス許可をキー コンテナーに直接付与することはできません (たとえば、検索サービスが Azure Key Vault ではなく別の Active Directory テナント内にある場合などです)。

* 暗号化された複数の (別のキー コンテナーからの異なるキーをそれぞれが使用する) インデックス/シノニム マップをホストするには、1 つの検索サービスが必要です。各キー コンテナーでは**異なる ID** を使用して認証を行う必要があります。 異なる ID を使用して別のキー コンテナーを管理する必要がない場合は、前述のマネージド ID オプションの使用を検討してください。  

このようなトポロジに対応するために、Azure Search では、Azure Active Directory (AAD) アプリケーションを使用した検索サービスとキー コンテナー間の認証がサポートされます。    
ポータルで AAD アプリケーションを作成するには、次の手順を実行します。

1. [Azure Active Directory アプリケーションを作成します](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)。

1. [アプリケーション ID と認証キーを取得](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)します。これらは、暗号化されたインデックスの作成に必要です。 指定する必要のある値は、**アプリケーション ID** と**認証キー**です。

>[!Important]
> マネージド ID ではなく AAD アプリケーションを認証に使用することを決定する際は、AAD アプリケーションを管理する権限が Azure Search にはなく、AAD アプリケーションの管理 (アプリケーションの認証キーの定期的なローテーションなど) はユーザーが行う必要があるという事実を考慮してください。
> AAD アプリケーションまたはその認証キーを変更する場合は、以前のアプリケーションまたはその認証キーを削除する**前**、およびそれらへのキー コンテナー アクセスを取り消す前に、アプリケーションを使用する Azure Search のインデックスまたはシノニム マップを、新しいアプリケーション ID/キーを使用するように更新する必要があります。
> そうしないと、インデックスまたはシノニム マップが使用できない状態になり、キー アクセスが失われた場合にコンテンツを暗号化解除できません。   

## <a name="next-steps"></a>次の手順

Azure セキュリティ アーキテクチャを使い慣れていない場合は、[Azure のセキュリティのドキュメント](https://docs.microsoft.com/azure/security/)を確認してください。具体的には次の記事です。

> [!div class="nextstepaction"]
> [保存時のデータ暗号化](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

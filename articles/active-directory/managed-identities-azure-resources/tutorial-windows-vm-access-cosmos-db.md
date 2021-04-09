---
title: チュートリアル:マネージド ID を使用して Azure Cosmos DB にアクセスする - Windows - Azure AD
description: Windows VM 上でシステム割り当てマネージド ID を使用して Azure Cosmos DB にアクセスするプロセスについて説明するチュートリアルです。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04508f1aa8ee9d6b4f730f57c60d959fab209122
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093794"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>チュートリアル:Windows VM のシステム割り当てマネージド ID を使用して Azure Cosmos DB にアクセスする

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Windows 仮想マシン (VM) のシステム割り当てマネージド ID を使用して Cosmos DB にアクセスする方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Cosmos DB アカウントを作成する
> * Windows VM のシステム割り当てマネージド ID に Cosmos DB アカウントのアクセス キーへのアクセス権を付与する
> * Windows VM のシステム割り当てマネージド ID を使用して、Azure Resource Manager を呼び出すためのアクセス トークンを取得する
> * Cosmos DB 呼び出しを行うために Azure Resource Manager からアクセス キーを取得する

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID 機能に慣れていない場合は、こちらの[概要](overview.md)を参照してください。 
- Azure アカウントをお持ちでない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- 必要なリソース作成およびロール管理を実行するために、お使いのアカウントには、適切な範囲 (サブスクリプションまたはリソース グループ) を対象とする "所有者" アクセス許可が必要となります。 ロールの割り当てに関するサポートが必要な場合は、[Azure ロールの割り当てによる Azure サブスクリプション リソースへのアクセスの管理](../../role-based-access-control/role-assignments-portal.md)に関するページをご覧ください。
- 最新バージョンの [Azure PowerShell](/powershell/azure/install-az-ps) をインストールします
- システム割り当てマネージド ID が有効になっている Windows 仮想マシンも必要です。
  - このチュートリアル用に仮想マシンを作成する必要がある場合は、[システム割り当てマネージド ID を有効にした仮想マシンの作成](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)に関する記事に従ってください。

## <a name="create-a-cosmos-db-account"></a>Cosmos DB アカウントを作成する 

Cosmos DB アカウントがまだない場合は作成します。 この手順をスキップし、既存の Cosmos DB アカウントを使用することもできます。 

1. Azure Portal の左上隅にある **[+ リソースの作成]** ボタンをクリックします。
2. **[データベース]** 、 **[Azure Cosmos DB]** の順にクリックします。[新しいアカウント] パネルが表示されます。
3. Cosmos DB アカウントの **[ID]** を入力します。この ID は後で使用されます。  
4. **[API]** は「SQL」に設定します。 このチュートリアルで説明されている方法は、他の利用可能な API の種類と併用できますが、このチュートリアルの手順は SQL API 向けです。
5. **[サブスクリプション]** と **[リソース グループ]** が、前の手順で VM を作成したときに指定したものと一致していることを確認します。  Cosmos DB を使用できる **[場所]** を選択します。
6. **Create** をクリックしてください。

### <a name="create-a-collection"></a>コレクションの作成 

次に、後の手順でクエリを実行できるデータ コレクションを Cosmos DB アカウントに追加します。

1. 新しく作成した Cosmos DB アカウントに移動します。
2. **[概要]** タブで **[+ コレクションの追加]** ボタンをクリックします。[コレクションの追加] パネルが表示されます。
3. コレクションにデータベース ID、コレクション ID を指定し、ストレージ容量を選択し、パーティション キーを入力し、スループット値を入力し、 **[OK]** をクリックします。  このチュートリアルでは、データベース ID とコレクション ID として "Test" を使用し、固定ストレージ容量と最低スループット (400 RU/s) を選択する設定で十分です。  


## <a name="grant-access"></a>アクセス権の付与

このセクションでは、Windows VM のシステム割り当てマネージド ID に Cosmos DB アカウントのアクセス キーへのアクセス権を付与する方法を説明します。 Cosmos DB は、ネイティブでは Azure AD 認証をサポートしていません。 ただし、システム割り当てマネージド ID を使用して Resource Manager から Cosmos DB のアクセス キーを取得し、そのキーを使用して Cosmos DB にアクセスできます。 この手順では、Windows VM のシステム割り当てマネージド ID に Cosmos DB アカウントのキーへのアクセス権を付与します。

Azure Resource Manager で PowerShell を使用して、Windows VM のシステム割り当てマネージド ID に Cosmos DB アカウントへのアクセス権を付与するには、次の値を更新します。

- `<SUBSCRIPTION ID>`
- `<RESOURCE GROUP>`
- `<COSMOS DB ACCOUNT NAME>`

Cosmos DB は、アクセス キーを使用する場合、アカウントへの読み取り/書き込みアクセス権と、アカウントへの読み取り専用アクセス権という 2 レベルの粒度をサポートしています。  アカウントの読み取り/書き込みキーを取得する場合は `DocumentDB Account Contributor` ロールを割り当て、アカウントの読み取り専用キーを取得する場合は `Cosmos DB Account Reader Role` ロールを割り当てます。  このチュートリアルでは、`Cosmos DB Account Reader Role` を割り当てます。

```azurepowershell
$spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Cosmos DB Account Reader Role" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>"
```

>[!NOTE]
> 操作を実行できない場合は、適切なアクセス許可が付与されていない可能性があります。 キーへの書き込みアクセス権が必要な場合は、DocumentDB Account Contributor などの Azure ロールを使用するか、カスタム役割を作成する必要があります。 詳細については、「[Azure Cosmos DB での Azure ロールベースのアクセス制御](../../cosmos-db/role-based-access-control.md)」を参照してください。

## <a name="access-data"></a>データにアクセスする

このセクションでは、Windows VM のシステム割り当てマネージド ID のアクセス トークンを使用して、Azure Resource Manager を呼び出す方法を説明します。 チュートリアルの残りの部分では、以前に作成した VM から作業を行います。 

最新バージョンの [Azure CLI](/cli/azure/install-azure-cli) を Windows VM にインストールする必要があります。

### <a name="get-an-access-token"></a>アクセス トークンを取得する

1. Azure Portal で **[Virtual Machines]** にナビゲートして Windows 仮想マシンに移動し、**[概要]** ページの上部にある **[接続]** をクリックします。 
2. Windows VM を作成したときに追加した **ユーザー名** と **パスワード** を入力します。 
3. これで、仮想マシンを使用する **リモート デスクトップ接続** が作成されました。リモート セッションで PowerShell を開きます。
4. PowerShell の Invoke-WebRequest を使用して、Azure リソース エンドポイントのローカル マネージド ID に、Azure Resource Manager のアクセス トークンを取得するよう要求します。

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
   ```

   > [!NOTE]
   > "resource"パラメーターの値は、Azure AD で予期される値と完全に一致している必要があります。 Azure Resource Manager のリソース ID を使用する場合は、URI の末尾にスラッシュを含める必要があります。
    
   次に、$response オブジェクト内で JavaScript Object Notation (JSON) 形式の文字列として格納されている "Content" 要素を抽出します。 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```
   次に、アクセス トークンを応答から抽出します。
    
   ```powershell
   $ArmToken = $content.access_token
   ```

### <a name="get-access-keys"></a>アクセス キーを取得する 

このセクションでは、Cosmos DB 呼び出しを行うために Azure Resource Manager からアクセス キーを取得する方法を説明します。 PowerShell を使用して、以前に取得したアクセス トークンで Resource Manager を呼び出し、Cosmos DB アカウントのアクセス キーを取得します。 アクセス キーを取得したら、Cosmos DB に対してクエリを実行できます。 次のエントリを実際の値に置き換えてください。

- `<SUBSCRIPTION ID>`
- `<RESOURCE GROUP>`
- `<COSMOS DB ACCOUNT NAME>` 
- `<ACCESS TOKEN>` の値は、以前に取得したアクセス トークンに置き換えます。 

>[!NOTE]
>読み取り/書き込みキーを取得する場合は、キー操作の種類 `listKeys` を使用します。  読み取り専用キーを取得する場合は、キー操作の種類 `readonlykeys` を使用します。 "listkeys" を使用できない場合は、マネージド ID に[適切なロール](../../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)が割り当てられていることを確認してください。

```powershell
Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/readonlykeys/?api-version=2016-03-31' -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
応答ではキーのリストが返されます。  たとえば、読み取り専用キーを取得する場合は次のようになります。

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
これで Cosmos DB アカウントのアクセス キーが取得できました。これを Cosmos DB SDK に渡して、アカウントにアクセスするための呼び出しを行うことができます。  簡単な例として、アクセス キーを Azure CLI に渡す場合があります。  Azure Portal の Cosmos DB アカウント ブレードの **[概要]** タブから `<COSMOS DB CONNECTION URL>` を取得できます。  `<ACCESS KEY>` を前述の手順で取得した値に置き換えます。

```azurecli
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

この CLI コマンドは、コレクションに関する詳細を返します。

```output
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```


## <a name="disable"></a>Disable

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Cosmos DB にアクセスするための Windows VM のシステム割り当て ID の使用方法について説明しました。  Cosmos DB の詳細については、以下を参照してください：

> [!div class="nextstepaction"]
>[Azure Cosmos DB の概要 ](../../cosmos-db/introduction.md)
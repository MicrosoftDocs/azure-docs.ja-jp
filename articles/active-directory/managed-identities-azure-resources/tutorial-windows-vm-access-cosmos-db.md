---
title: Windows VM のシステム割り当てマネージド ID を使用して Azure Cosmos DB にアクセスする
description: Windows VM 上でシステム割り当てマネージド ID を使用して Azure Cosmos DB にアクセスするプロセスについて説明するチュートリアルです。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e5858fe392629d61b3f0b8833db3af959a16a8b
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59525186"
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

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- 最新バージョンの [Azure PowerShell](/powershell/azure/install-az-ps) をインストールします

## <a name="create-a-cosmos-db-account"></a>Cosmos DB アカウントを作成する 

Cosmos DB アカウントがまだない場合は作成します。 この手順をスキップし、既存の Cosmos DB アカウントを使用することもできます。 

1. Azure Portal の左上隅にある **[+/新しいサービスの作成]** ボタンをクリックします。
2. **[データベース]**、**[Azure Cosmos DB]** の順にクリックします。[新しいアカウント] パネルが表示されます。
3. Cosmos DB アカウントの **[ID]** を入力します。この ID は後で使用されます。  
4. **[API]** は「SQL」に設定します。 このチュートリアルで説明されている方法は、他の利用可能な API の種類と併用できますが、このチュートリアルの手順は SQL API 向けです。
5. **[サブスクリプション]** と **[リソース グループ]** が、前の手順で VM を作成したときに指定したものと一致していることを確認します。  Cosmos DB を使用できる **[場所]** を選択します。
6. **Create** をクリックしてください。

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Cosmos DB アカウントでコレクションを作成する

次に、後の手順でクエリを実行できるデータ コレクションを Cosmos DB アカウントに追加します。

1. 新しく作成した Cosmos DB アカウントに移動します。
2. **[概要]** タブで **[+ コレクションの追加]** ボタンをクリックします。[コレクションの追加] パネルが表示されます。
3. コレクションにデータベース ID、コレクション ID を指定し、ストレージ容量を選択し、パーティション キーを入力し、スループット値を入力し、**[OK]** をクリックします。  このチュートリアルでは、データベース ID とコレクション ID として "Test" を使用し、固定ストレージ容量と最低スループット (400 RU/s) を選択する設定で十分です。  

## <a name="grant-windows-vm-system-assigned-managed-identity-access-to-the-cosmos-db-account-access-keys"></a>Windows VM のシステム割り当てマネージド ID に Cosmos DB アカウントのアクセス キーへのアクセス権を付与する

Cosmos DB は、ネイティブでは Azure AD 認証をサポートしていません。 ただし、システム割り当てマネージド ID を使用して Resource Manager から Cosmos DB のアクセス キーを取得し、そのキーを使用して Cosmos DB にアクセスできます。 この手順では、Windows VM のシステム割り当てマネージド ID に Cosmos DB アカウントのキーへのアクセス権を付与します。

Azure Resource Manager で PowerShell を使用して Windows VM のシステム割り当てマネージド ID に Cosmos DB アカウントへのアクセス権を付与するには、`<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、`<COSMOS DB ACCOUNT NAME>` の値を環境に合わせて更新します。 Cosmos DB は、アクセス キーを使用する場合、アカウントへの読み取り/書き込みアクセス権と、アカウントへの読み取り専用アクセス権という 2 レベルの粒度をサポートしています。  アカウントの読み取り/書き込みキーを取得する場合は `DocumentDB Account Contributor` ロールを割り当て、アカウントの読み取り専用キーを取得する場合は `Cosmos DB Account Reader Role` ロールを割り当てます。  このチュートリアルでは、`Cosmos DB Account Reader Role` を割り当てます。

```azurepowershell
$spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Cosmos DB Account Reader Role" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>"
```
## <a name="get-an-access-token-using-the-windows-vm-system-assigned-managed-identity-to-call-azure-resource-manager"></a>Windows VM のシステム割り当てマネージド ID を使用して、Azure Resource Manager を呼び出すためのアクセス トークンを取得する

チュートリアルの残りの部分では、以前に作成した VM から作業を行います。 

最新バージョンの [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) を Windows VM にインストールする必要があります。

1. Azure Portal で **[Virtual Machines]** にナビゲートして Windows 仮想マシンに移動し、**[概要]** ページの上部にある **[接続]** をクリックします。 
2. Windows VM を作成したときに追加した**ユーザー名**と**パスワード**を入力します。 
3. これで、仮想マシンを使用する**リモート デスクトップ接続**が作成されました。リモート セッションで PowerShell を開きます。
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

## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Cosmos DB 呼び出しを行うために Azure Resource Manager からアクセス キーを取得する

ここで、PowerShell を使用して、前のセクションで取得したアクセス トークンで Resource Manager を呼び出し、Cosmos DB アカウント アクセス キーを取得します。 アクセス キーを取得したら、Cosmos DB に対してクエリを実行できます。 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、および `<COSMOS DB ACCOUNT NAME>` の各パラメーターの値は、必ず実際の値に置き換えてください。 `<ACCESS TOKEN>` の値は、以前に取得したアクセス トークンに置き換えます。  読み取り/書き込みキーを取得する場合は、キー操作の種類 `listKeys` を使用します。  読み取り専用キーを取得する場合は、キー操作の種類 `readonlykeys` を使用します。

```powershell
Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/listKeys/?api-version=2016-03-31' -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
応答では、キーのリストが返されます。  たとえば、読み取り専用キーを取得する場合は次のようになります。

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
これで Cosmos DB アカウントのアクセス キーが取得できました。これを Cosmos DB SDK に渡して、アカウントにアクセスするための呼び出しを行うことができます。  簡単な例として、アクセス キーを Azure CLI に渡す場合があります。  Azure Portal の Cosmos DB アカウント ブレードの **[概要]** タブから `<COSMOS DB CONNECTION URL>` を取得できます。  `<ACCESS KEY>` を前述の手順で取得した値に置き換えます。

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

この CLI コマンドは、コレクションに関する詳細を返します。

```bash
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

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Cosmos DB にアクセスするための Windows VM のシステム割り当て ID の使用方法について説明しました。  Cosmos DB の詳細については、以下を参照してください：

> [!div class="nextstepaction"]
>[Azure Cosmos DB の概要 ](/azure/cosmos-db/introduction)



---
title: チュートリアル`:`マネージド ID を使用して Azure Cosmos DB にアクセスする - Linux - Azure AD
description: Linux VM のシステム割り当てマネージド ID を使用して Azure Cosmos DB にアクセスするプロセスについて説明するチュートリアルです。
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
ms.openlocfilehash: b81f80af69b47152f7111066070e173bb1ede5f4
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093952"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>チュートリアル: Linux VM のシステム割り当てマネージド ID を使用して Azure Cosmos DB にアクセスする 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Linux 仮想マシン (VM) のシステム割り当てマネージド ID を使用して Azure Cosmos DB にアクセスする方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Cosmos DB アカウントを作成する
> * Cosmos DB アカウントでコレクションを作成する
> * システム割り当てマネージド ID に Azure Cosmos DB インスタンスへのアクセスを許可する
> * Linux VM のシステム割り当てマネージド ID の `principalID` を取得する
> * アクセス トークンを取得し、そのアクセス トークンを使用して Azure Resource Manager を呼び出す
> * Cosmos DB 呼び出しを行うために Azure Resource Manager からアクセス キーを取得する

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID 機能に慣れていない場合は、こちらの[概要](overview.md)を参照してください。 
- Azure アカウントをお持ちでない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- 必要なリソース作成およびロール管理を実行するために、お使いのアカウントには、適切な範囲 (サブスクリプションまたはリソース グループ) を対象とする "所有者" アクセス許可が必要となります。 ロールの割り当てに関するサポートが必要な場合は、[Azure ロールの割り当てによる Azure サブスクリプション リソースへのアクセスの管理](../../role-based-access-control/role-assignments-portal.md)に関するページをご覧ください。
- サンプル スクリプトを実行するには、次の 2 つのオプションがあります。
    - [Azure Cloud Shell](../../cloud-shell/overview.md) を使用する。これは、コード ブロックの右上隅にある **[Try It]\(試してみる\)** ボタンを使用して開くことができます。
    - 最新バージョンの [Azure CLI](/cli/azure/install-azure-cli) をインストールしてスクリプトをローカルで実行した後、[az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインします。 リソースを作成する Azure サブスクリプションに関連付けられているアカウントを使用します。

## <a name="create-a-cosmos-db-account"></a>Cosmos DB アカウントを作成する 

Cosmos DB アカウントがまだない場合は作成します。 この手順をスキップし、既存の Cosmos DB アカウントを使用することもできます。 

1. Azure Portal の左上隅にある **[+ リソースの作成]** ボタンをクリックします。
2. **[データベース]** 、 **[Azure Cosmos DB]** の順にクリックします。[新しいアカウント] パネルが表示されます。
3. Cosmos DB アカウントの **[ID]** を入力します。この ID は後で使用されます。  
4. **[API]** は「SQL」に設定します。 このチュートリアルで説明されている方法は、他の利用可能な API の種類と併用できますが、このチュートリアルの手順は SQL API 向けです。
5. **[サブスクリプション]** と **[リソース グループ]** が、前の手順で VM を作成したときに指定したものと一致していることを確認します。  Cosmos DB を使用できる **[場所]** を選択します。
6. **[作成]** をクリックします。

### <a name="create-a-collection-in-the-cosmos-db-account"></a>Cosmos DB アカウントでコレクションを作成する

次に、後の手順でクエリを実行できるデータ コレクションを Cosmos DB アカウントに追加します。

1. 新しく作成した Cosmos DB アカウントに移動します。
2. **[概要]** タブで **[+ コレクションの追加]** ボタンをクリックします。[コレクションの追加] パネルが表示されます。
3. コレクションにデータベース ID、コレクション ID を指定し、ストレージ容量を選択し、パーティション キーを入力し、スループット値を入力し、 **[OK]** をクリックします。  このチュートリアルでは、データベース ID とコレクション ID として "Test" を使用し、固定ストレージ容量と最低スループット (400 RU/s) を選択する設定で十分です。  

## <a name="grant-access"></a>アクセス権の付与

次のセクションで Resource Manager から Cosmos DB アカウントのアクセス キーにアクセスするために、Linux VM のシステム割り当てマネージド ID の `principalID` を取得しておく必要があります。  必ず、`<SUBSCRIPTION ID>`、`<RESOURCE GROUP>` (自分の VM が存在するリソース グループ)、および `<VM NAME>` のパラメーター値を独自の値に置き換えてください。

```azurecli-interactive
az resource show --id /subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe> --api-version 2017-12-01
```
応答には、システム割り当てマネージド ID の詳細が含まれています (次のセクションで使用するので、principalID をメモしてください)。

```output  
{
    "id": "/subscriptions/<SUBSCRIPTION ID>/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe>",
  "identity": {
    "principalId": "6891c322-314a-4e85-b129-52cf2daf47bd",
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533f8",
    "type": "SystemAssigned"
 }
```

### <a name="grant-your-linux-vms-system-assigned-identity-access-to-the-cosmos-db-account-access-keys"></a>Linux VM のシステム割り当て ID に Cosmos DB アカウントのアクセス キーへのアクセス権を付与する

Cosmos DB は、ネイティブでは Azure AD 認証をサポートしていません。 ただし、マネージド ID を使用して Resource Manager から Cosmos DB のアクセス キーを取得し、そのキーを使用して Cosmos DB にアクセスできます。 この手順では、システム割り当てマネージド ID に Cosmos DB アカウントのキーへのアクセス権を付与します。

Azure Resource Manager で Azure CLI を使用してシステム割り当てマネージド ID に Cosmos DB アカウントへのアクセス権を付与するには、`<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、`<COSMOS DB ACCOUNT NAME>` の値を環境に合わせて更新します。 `<MI PRINCIPALID>` は、Linux VM の MI の principalID の取得で `az resource show` コマンドによって返された `principalId` プロパティに置き換えます。  Cosmos DB は、アクセス キーを使用する場合、アカウントへの読み取り/書き込みアクセス権と、アカウントへの読み取り専用アクセス権という 2 レベルの粒度をサポートしています。  アカウントの読み取り/書き込みキーを取得する場合は `DocumentDB Account Contributor` ロールを割り当て、アカウントの読み取り専用キーを取得する場合は `Cosmos DB Account Reader Role` ロールを割り当てます。

```azurecli-interactive
az role assignment create --assignee <MI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

応答には、作成されたロール割り当ての詳細が含まれています。

```output
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="access-data"></a>データにアクセスする

チュートリアルの残りの部分では、仮想マシンから作業を行います。

これらの手順を完了するには、SSH クライアントが必要です。 Windows を使用している場合は、[Windows Subsystem for Linux](/windows/wsl/install-win10) で SSH クライアントを使用することができます。 SSH クライアント キーの構成について支援が必要な場合は、「[Azure 上の Windows で SSH キーを使用する方法](../../virtual-machines/linux/ssh-from-windows.md)」または「[Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法](../../virtual-machines/linux/mac-create-ssh-keys.md)」をご覧ください。

1. Azure Portal で **[Virtual Machines]** にナビゲートして Linux 仮想マシンに移動し、 **[概要]** ページの上部にある **[接続]** をクリックします。 VM に接続する文字列をコピーします。 
2. SSH クライアントを使用して VM に接続します。  
3. 次に、**Linux VM** の作成時に追加した **パスワード** の入力を求められます。 パスワードを入力すると、正常にサインインできます。  
4. CURL を使用して Azure Resource Manager のアクセス トークンを取得します。 
     
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
 
    > [!NOTE]
    > 前述の要求では、"resource" パラメーターの値は、Azure AD で予期される値と完全に一致している必要があります。 Azure Resource Manager のリソース ID を使用する場合は、URI の末尾にスラッシュを含める必要があります。
    > 次の応答では、簡潔にするため access_token 要素が短縮されています。
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
### <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Cosmos DB 呼び出しを行うために Azure Resource Manager からアクセス キーを取得する  

ここで、CURL を使用して、前のセクションで取得したアクセス トークンを使って Resource Manager を呼び出し、Cosmos DB アカウント アクセス キーを取得します。 アクセス キーを取得したら、Cosmos DB に対してクエリを実行できます。 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、および `<COSMOS DB ACCOUNT NAME>` の各パラメーターの値は、必ず実際の値に置き換えてください。 `<ACCESS TOKEN>` の値は、以前に取得したアクセス トークンに置き換えます。  読み取り/書き込みキーを取得する場合は、キー操作の種類 `listKeys` を使用します。  読み取り専用キーを取得する場合は、キー操作の種類 `readonlykeys` を使用します。

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> 上記の URL のテキストでは大文字と小文字が区別されるので、リソース グループの名前に使用されている大文字小文字と一致する文字を使用してください。 また、これは GET 要求ではなく POST 要求であることを認識し、-d (NULL を指定可能) を指定して長さの制限を取得するための値を渡すことが重要です。  

CURL 応答では、キーのリストが返されます。  たとえば、読み取り専用キーを取得する場合は次のようになります。  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

これで Cosmos DB アカウントのアクセス キーが取得できました。これを Cosmos DB SDK に渡して、アカウントにアクセスするための呼び出しを行うことができます。  簡単な例として、アクセス キーを Azure CLI に渡す場合があります。  Azure Portal の Cosmos DB アカウント ブレードの **[概要]** タブから `<COSMOS DB CONNECTION URL>` を取得できます。  `<ACCESS KEY>` を前述の手順で取得した値に置き換えます。

```azurecli-interactive
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

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Linux 仮想マシン上のシステム割り当てマネージド ID を使用し、Cosmos DB にアクセスする方法について学びました。  Cosmos DB の詳細については、以下を参照してください：

> [!div class="nextstepaction"]
>[Azure Cosmos DB の概要 ](../../cosmos-db/introduction.md)

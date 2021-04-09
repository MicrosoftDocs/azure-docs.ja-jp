---
title: カスタム Azure Resource Manager ロールを作成し、サービス プリンシパルに割り当てる - Azure
description: この記事では、Azure CLI を使用して、カスタム Azure Resource Manager ロールを作成し、Live Video Analytics on IoT Edge のサービス プリンシパルに割り当てる方法に関するガイダンスを提供します。
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 80974c111dd451314635d06334766322bc68e437
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210446"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>カスタム Azure Resource Manager ロールを作成し、サービス プリンシパルに割り当てる

Live Video Analytics on IoT Edge モジュール インスタンスが正常に機能するには、アクティブな Azure Media Services アカウントが必要です。 Live Video Analytics on IoT Edge モジュールと Azure Media Services アカウントのリレーションシップは、一連のモジュール ツイン プロパティを使用して確立されます。 これらのツイン プロパティのうちの 1 つが[サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)で、モジュール インスタンスが Media Services アカウントと通信して必要な操作をトリガーできるようします。 誤用の可能性や、エッジ デバイスからの偶発的なデータの漏えいを最小限に抑えるために、このサービス プリンシパルに最小限の特権を与える必要があります。

この記事では、Azure Cloud Shell を使用してカスタム Azure Resource Manager ロールを作成する手順について説明します。このロールはその後、サービス プリンシパルの作成に使用されます。

## <a name="prerequisites"></a>前提条件  

この記事の前提条件は次のとおりです。

* 所有者サブスクリプションがある Azure サブスクリプション。
* アプリを作成し、サービス プリンシパルをロールに割り当てるための特権を持つ Azure Active Directory。

自分のアカウントに適切なアクセス許可があるかどうかを確認する最も簡単な方法は、ポータルを使用することです。 [必要なアクセス許可のチェック](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)に関するページを参照してください。

## <a name="overview"></a>概要  

ここでは、カスタム ロールを作成し、それを次の順序でサービス プリンシパルにリンクする手順について説明します。

1. Media Service アカウントをまだお持ちでない場合は、作成します。
1. サービス プリンシパルを作成する。
1. 特権が制限されたカスタム Azure Resource Manager ロールを作成します。
1. 作成したカスタム ロールを使用して、サービス プリンシパルの特権を "制限" します。
1. 簡単なテストを実行して、サービス プリンシパルを正常に制限できるかどうかを確認します。
1. IoT Edge 配置マニフェストで使用されるパラメーターをキャプチャします。

### <a name="create-a-media-services-account"></a>Media Services アカウントを作成する  

Media Service アカウントをお持ちでない場合は、次の手順を使用して作成してください。

1. [Cloud Shell](https://shell.azure.com/) を参照します。
1. シェル ウィンドウの左側にあるドロップダウンから "Bash" をご利用の環境として選択します。

    ![シェル ウィンドウで Bash が選択されていることを示す画面キャプチャ。](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. 次のコマンド テンプレートを使用して、ご自分の Azure サブスクリプションを既定のアカウントとして設定します。
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. [リソース グループ](/cli/azure/group#az-group-create)と[ストレージ アカウント](/cli/azure/storage/account#az-storage-account-create)を作成します。
1. ここで、Cloud Shell で次のコマンド テンプレートを使用して、Azure Media Service アカウントを作成します。

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>サービス プリンシパルの作成  

ここでは、新しいサービス プリンシパルを作成し、それをご自分の Media Service アカウントにリンクします。

認証パラメーターを指定しない場合、パスワードベースの認証がサービス プリンシパルのランダム パスワードと共に使用されます。 Cloud Shell で次のコマンド テンプレートを使用します。

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

このコマンドからは、次のような応答が生成されます。

```
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "<yourServicePrincipalPassword>",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": " <yourAMSAccountName >",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "Central US",
  "ResourceGroup": " <yourResouceGroup >",
  "SubscriptionId": "<yourSubscriptionId>"
}

```
1. パスワード認証を使用したサービス プリンシパルの出力には、パスワード キー (この場合は "AadSecret" パラメーター) が含まれます。 

    この値は必ずコピーしてください。取得することはできません。 パスワードを忘れた場合は、[サービス プリンシパルの資格情報をリセット](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials)します。
1. appId とテナント キーは、それぞれ "AadClientId" と "AadTenantId" として出力に表示されます。 これらは、サービス プリンシパル認証で使用されます。 これらの値を記録してください。ただし、[az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) を使用していつでも取得できます。

### <a name="create-a-custom-role-definition"></a>カスタム ロールの定義の作成  

カスタム ロールを作成するには、次の手順に従う必要があります。

1. ご使用のローカル システムにロールの定義 JSON ファイルを作成し、ファイルに次のテキストを保存します。 
    1. < yourSubscriptionId> を、ご自分の Azure サブスクリプション ID に置き換えます。
    1. このロールで許可されるアクションは次のとおりです。
        * listContainerSas – アセット コンテンツをアップロードおよびダウンロードするために、モジュールが共有アクセス署名 (SAS) を使用してストレージ コンテナーの URL を一覧表示できるようにします。
        * Write assets – モジュールが任意のアセットを作成または更新できるようにします。
        * listEdgePolicies – エッジ デバイスに適用されているポリシーの一覧を表示します。  
        
        ```
        {
          "Name": "LVAEdge User",
          "IsCustom": true,
          "Description": "Can create assets, view list of containers and view Edge policies",
          "Actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action"
          ],
          "NotActions": [],
          "DataActions": [],
          "NotDataActions": [],
          "AssignableScopes": [
            "/subscriptions/<yourSubscriptionId>"
          ]
        }
        ```  
          
1. 作成したら、次のコマンド テンプレートを実行して、サブスクリプションに新しいロールの定義を作成します。
    
    ```
    az role definition create --role-definition "<location of the Role Definition JSON file >"
    ```

    コマンドが正常に実行されると、次の出力が表示されます。
    
    ```
    {
      "assignableScopes": [
      "/subscriptions/<yourSubscriptionId>"
      ],
      "description": "Can create assets, view list of containers and view Edge policies",
      "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<unique name>",
      "name": "<unique name>",
      "permissions": [
        {
          "actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action",
          ],
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": " LVAEdge User ",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

### <a name="create-role-assignment"></a>ロール割り当ての作成  

ロールの割り当てを追加するには、作成したカスタム ロールを割り当てるサービス プリンシパルの objectId が必要になります。

objectId を取得するには、Cloud Shell で次のコマンドを使用します。

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>` は、[サービス プリンシパルの作成](#create-service-principal)手順の出力から取得できます。

上記のコマンドにより、サービス プリンシパルの objectId が出力されます。 

```
“objectId” : “<yourObjectId>”,
```

[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) コマンド テンプレートを使用して、カスタム ロールとサービス プリンシパルをリンクします。

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

パラメーター:

|パラメーター|説明| 
|---|---|
|--role |カスタム ロール名または ID。 この例では、"LVAEdge User"。|
|--assignee-object-id|使用するサービス プリンシパルのオブジェクト ID。|

結果は次のようになります。

```
{
  "canDelegate": null,
  "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleAssignments/<yourCustomRoleId>",
  "name": "<yourCustomRoleId>",
  "principalId": "<yourServicePrincipalId>",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<yourRoleDefinitionId>",
  "scope": "/subscriptions/<yourSubscriptionId>",
  "type": "Microsoft.Authorization/roleAssignments"
} 
```

### <a name="confirm-that-role-assignment-happened"></a>ロールの割り当てが行われたことを確認する

サービス プリンシパルが、先ほど作成したカスタム ロールとリンクされていることを確認するには、次のコマンドを実行します。

```
az role assignment list  --assignee < objectId>
```

結果は次のようになります。

```
[
  {
    "canDelegate": null,
    "id": "/subscriptions/xxx/providers/Microsoft.Authorization/roleAssignments/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "principalId": "<yourServicePrincipalID>",
    "principalName": "<yourServicePrincipalName>",
    "principalType": "ServicePrincipal",
    "roleDefinitionId": "/subscriptions/xxx/providers/Microsoft.Authorization/roleDefinitions/zzz",
    "roleDefinitionName": "LVAEdge User",
    "scope": "/subscriptions/<yourSubscription ID>",
    "type": "Microsoft.Authorization/roleAssignments
  }
]  
```
 
"roleDefinitionName" を検索し、その値が "LVAEdge User" に設定されていることを確認します。 

これにより、カスタム ユーザー ロールと、アプリケーションで使用されるサービス プリンシパルがリンクされていることが確認されます。

### <a name="test-the-service-principal-access-control"></a>サービス プリンシパルへのアクセス制御のテスト

1. サービス プリンシパルを使用してログインします。 これには、[サービス プリンシパルの作成](#create-service-principal)手順の出力から取得できる適切なアクセス トークンが付与されるためには、Azure Active Directory の次の 3 つの情報が必要になります。
    1. AadClientID 
    1. AadSecret
    1. AadTenantId
1. では、次のコマンド テンプレートを使用してログインしてみましょう。
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  次に、ログインが "LVAEdge User" ロールを持つサービス プリンシパルに限定されているかどうかを確認してみましょう。これには、リソース グループを作成してみて、それが失敗することを確認します。 Cloud Shell で次のコマンドを実行します。

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    このコマンドは失敗し、次のようになります。
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>次のステップ  

この記事の次の値をメモしてください。 これらの値は Live Video Analytics on IoT Edge モジュールのツイン プロパティを構成するために必要となります。「[モジュール ツインの JSON スキーマ](module-twin-configuration-schema.md)」を参照してください。

| この記事の変数|Live Video Analytics on IoT Edge のツイン プロパティ名|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|AadTenantId |  aadTenantId|
|AadClientId    |aadServicePrincipalAppId|

---
title: REST API を使用して Azure NetApp Files 用の開発を行う |Microsoft Docs
description: Azure NetApp Files サービス用の REST API では、NetApp アカウント、容量プール、ボリューム、スナップショットなどのリソースに対する HTTP 操作が定義されます。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/02/2020
ms.author: b-juche
ms.openlocfilehash: c5993dc1dc645319e272ab310a97bc3ff8ac495d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174242"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>REST API を使用して Azure NetApp Files 用の開発を行う 

Azure NetApp Files サービス用の REST API では、NetApp アカウント、容量プール、ボリューム、スナップショットなどのリソースに対する HTTP 操作が定義されます。 この記事は、Azure NetApp Files REST API の使用を開始するために役立ちます。

## <a name="azure-netapp-files-rest-api-specification"></a>Azure NetApp Files の REST API 仕様

Azure NetApp Files の REST API 仕様は、[GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) を介して公開されています。

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Azure NetApp Files REST API にアクセスする  

1. まだ行っていなければ、[Azure CLI をインストール](/cli/azure/install-azure-cli)します。
2. Azure Active Directory (Azure AD) 内にサービス プリンシパルを作成します。
   1. [十分なアクセス許可](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)を持っていることを確認します。

   2. Azure CLI で、次のコマンドを入力します。 
    
        ```azurecli
        az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE
        ```

      コマンドの出力は次の例のようになります。  

        ```output
        { 
            "appId": "appIDgoeshere", 
            "displayName": "APPNAME", 
            "name": "http://APPNAME", 
            "password": "supersecretpassword", 
            "tenant": "tenantIDgoeshere" 
        } 
        ```

      このコマンドの出力をキープしておきます。  `appId`、`password`、および `tenant` の値が必要になります。 

3. OAuth アクセス トークンを要求します。

    この記事の例では、cURL を使用しています。 [Postman](https://www.getpostman.com/)、[Insomnia](https://insomnia.rest/)、[Paw](https://paw.cloud/) などのさまざまな API ツールを使用することもできます。  

    次の例の変数を、上記手順 2 のコマンドの出力に置き換えます。 
    
    ```azurecli
    curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token
    ```

    出力で、次の例のようなアクセス トークンが提供されます。

    `eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9`

    表示されたトークンは、3,600 秒間有効です。 その後は新しいトークンを要求する必要があります。 
    トークンをテキスト エディターに保存します。  次のステップで必要になります。

4. テスト呼び出しを送信し、REST API へのアクセスを検証するトークンを含めます。

    ```azurecli
    curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2019-11-01
    ```

## <a name="examples-using-the-api"></a>API の使用例  

この記事では、要求のベースラインで次の URL を使用します。 この URL は、Azure NetApp Files 名前空間のルートを指します。 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01`

以下の例では、`SUBIDGOESHERE` と `RESOURCEGROUPGOESHERE` の値をご自身の値に置き換える必要があります。 

### <a name="get-request-examples"></a>GET 要求の例

次の例に示すように、GET 要求を使用して、サブスクリプション内の Azure NetApp Files オブジェクトのクエリを実行します。 

```azurecli
#get NetApp accounts 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01
```

```azurecli
#get capacity pools for NetApp account 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2019-11-01
```

```azurecli
#get volumes in NetApp account & capacity pool 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2019-11-01
```

```azurecli
#get snapshots for a volume 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2019-11-01
```

### <a name="put-request-examples"></a>PUT 要求の例

次の例に示すように、PUT 要求を使用して、Azure NetApp Files 内に新しいオブジェクトを作成します。 PUT 要求の本文には、変更の JSON 形式のデータを含めることができます。 curl コマンドにテキストとして含めるか、ファイルとして参照する必要があります。 ファイルとして本文を参照するには、json の例をファイルに保存し、curl コマンドに `-d @<filename>` を追加します。

```azurecli
#create a NetApp account  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2019-11-01
```

```azurecli
#create a capacity pool  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2019-11-01
```

```azurecli
#create a volume  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2019-11-01
```

```azurecli
 #create a volume snapshot  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2019-11-01
```

### <a name="json-examples"></a>JSON の使用例

次の例は、NetApp アカウントを作成する方法を示しています。

```json
{ 
    "name": "MYNETAPPACCOUNT", 
    "type": "Microsoft.NetApp/netAppAccounts", 
    "location": "westus2", 
    "properties": { 
        "name": "MYNETAPPACCOUNT" 
    }
} 
```

次の例は、容量プールを作成する方法を示しています。 

```json
{
    "name": "MYNETAPPACCOUNT/POOLNAME",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
    "location": "westus2",
    "properties": {
        "name": "POOLNAME",
        "size": "4398046511104",
        "serviceLevel": "Premium"
    }
}
```

次の例は、新しいボリュームを作成する方法を示しています (ボリュームの既定のプロトコルは NFSV3 です)。 

```json
{
    "name": "MYNEWVOLUME",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
    "location": "westus2",
    "properties": {
        "serviceLevel": "Premium",
        "usageThreshold": "322122547200",
        "creationToken": "MY-FILEPATH",
        "snapshotId": "",
        "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
         }
}
```

次の例は、ボリュームのスナップショットを作成する方法を示しています。 

```json
{
    "name": "apitest2/apiPool01/apiVol01/snap02",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
    "location": "westus2",
    "properties": {
         "name": "snap02",
        "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
    }
}
```

> [!NOTE] 
> スナップショットの作成では、`fileSystemId` を指定する必要があります。  `fileSystemId` の値は、ボリュームに対する GET 要求によって取得できます。 

## <a name="next-steps"></a>次のステップ

[Azure NetApp Files REST API リファレンスを参照する](/rest/api/netapp/)
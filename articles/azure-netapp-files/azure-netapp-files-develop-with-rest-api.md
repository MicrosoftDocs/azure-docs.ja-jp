---
title: REST API を使用して Azure NetApp Files 用の開発を行う |Microsoft Docs
description: Azure NetApp Files REST API を使用して開発を開始する方法について説明します。
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
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: b-juche
ms.openlocfilehash: 56667b9a47411b2abae30ff159fa6bc555fec070
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104626"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>REST API を使用して Azure NetApp Files 用の開発を行う 

Azure NetApp Files サービス用の REST API では、NetApp アカウント、容量プール、ボリューム、スナップショットなどのリソースに対する HTTP 操作が定義されます。 この記事は、Azure NetApp Files REST API の使用を開始するために役立ちます。

## <a name="access-the-azure-netapp-files-rest-api"></a>Azure NetApp Files REST API にアクセスする  

1. まだ行っていなければ、[Azure CLI をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)します。
2. Azure Active Directory (Azure AD) 内にサービス プリンシパルを作成します。
   1. [十分なアクセス許可](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)を持っていることを確認します。

   1. Azure CLI で、次のコマンドを入力します。  

           az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE--password $YOURGENERATEDPASSWORDGOESHERE

      コマンドの出力は次の例のようになります。  

           { 
               "appId": "appIDgoeshere", 
               "displayName": "APPNAME", 
               "name": "http://APPNAME", 
               "password": "supersecretpassword", 
               "tenant": "tenantIDgoeshere" 
           } 

      このコマンドの出力をキープしておきます。  `appId`、`password`、および `tenant` の値が必要になります。 

3. OAuth アクセス トークンを要求します。

    この記事の例では、cURL を使用しています。  [Postman](https://www.getpostman.com/)、[Insomnia](https://insomnia.rest/)、[Paw](https://paw.cloud/) などのさまざまな API ツールを使用することもできます。  

    次の例の変数を、上記手順 2 のコマンドの出力に置き換えます。 

        curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token

    出力で、次の例のようなアクセス トークンが提供されます。

        eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9

    表示されたトークンは、3,600 秒間有効です。 その後は新しいトークンを要求する必要があります。 
    トークンをテキスト エディターに保存します。  次のステップで必要になります。

4. テスト呼び出しを送信し、REST API へのアクセスを検証するトークンを含めます。

        curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2016-08-01

## <a name="examples-using-the-api"></a>API の使用例  

この記事では、要求のベースラインで次の URL を使用します。 この URL は、Azure NetApp Files 名前空間のルートを指します。 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15`

以下の例では、`subID` と `resourceGroups` の値をご自身の値に置き換える必要があります。 

### <a name="get-request-examples"></a>GET 要求の例

次の例に示すように、GET 要求を使用して、サブスクリプション内の Azure NetApp Files オブジェクトのクエリを実行します。 

        #get NetApp accounts 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15

        #get capacity pools for NetApp account 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2017-08-15

        #get volumes in NetApp account & capacity pool 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2017-08-15

        #get snapshots for a volume 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2017-08-15

### <a name="put-request-examples"></a>PUT 要求の例

次の例に示すように、PUT 要求を使用して、Azure NetApp Files 内に新しいオブジェクトを作成します。 PUT 要求の本文には、変更するための JSON 形式のデータを含めたり、読み取るファイルを指定したりできます。 

        #create a NetApp account  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2017-08-15

        #create a capacity pool  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2017-08-15

        #create a volume  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2017-08-15

        #create a volume snapshot  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2017-08-15

### <a name="json-examples"></a>JSON の使用例

次の例は、NetApp アカウントを作成する方法を示しています。

    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 

次の例は、容量プールを作成する方法を示しています。 

    {
        "name": "MYNETAPPACCOUNT/POOLNAME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
        "location": "westus2",
        "properties": {
            "name": "POOLNAME"
            "size": "4398046511104",
            "serviceLevel": "Premium"
        }
    }

次の例は、新しいボリュームを作成する方法を示しています。 

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

次の例は、ボリュームのスナップショットを作成する方法を示しています。 

    {
        "name": "apitest2/apiPool01/apiVol01/snap02",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
        "location": "westus2",
        "properties": {
            "name": "snap02",
            "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
        }
    }

> [!NOTE] 
> スナップショットの作成では、`fileSystemId` を指定する必要があります。  `fileSystemId` の値は、ボリュームに対する GET 要求によって取得できます。 

## <a name="next-steps"></a>次の手順

[Azure NetApp Files REST API リファレンスを参照する](https://docs.microsoft.com/rest/api/netapp/)

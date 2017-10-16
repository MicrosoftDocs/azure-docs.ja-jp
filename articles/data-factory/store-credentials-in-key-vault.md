---
title: "Azure Key Vault への資格情報の格納 | Microsoft Docs"
description: "Azure Data Factory で実行時に自動的に取得できる、Azure Key Vault で使用されたデータ ストアの資格情報を格納する方法を説明します。"
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: cb4545dcd41fe9a2c73bc86096843b717d61a785
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Azure Key Vault への資格情報の格納

[Azure Key Vault](../key-vault/key-vault-whatis.md) 内のデータ ストアに資格情報を格納することができます。 Azure Data Factory は、データ ストアを使用するアクティビティの実行時に、資格情報を取得します。

> [!NOTE]
> 現時点では、[Dynamics コネクタ](connector-dynamics-crm-office-365.md)でのみこの機能がサポートされます。

## <a name="steps"></a>手順

データ ファクトリの作成時に、ファクトリの作成に加えてサービス ID を作成することができます。 サービス ID は、Azure Activity Directory に登録されているマネージ アプリケーションであり、この特定のデータ ファクトリを表します。

- パブリック プレビュー以降は、**Azure Portal または PowerShell** を介してデータ ファクトリを作成すると、サービス ID が常に自動的に作成されます。
- **SDK** を介してデータ ファクトリを作成する場合、作成用のファクトリ オブジェクトで "Identity = new FactoryIdentity()" を指定したときのみサービス ID が作成されます。 [.NET のクイックスタート - データ ファクトリの作成](quickstart-create-data-factory-dot-net.md#create-a-data-factory)に関する記事の例をご覧ください。
- **REST API** を介してデータ ファクトリを作成する場合、要求本文で "Identity" セクションを指定したときのみサービス ID が作成されます。 [REST のクイックスタート - データ ファクトリの作成](quickstart-create-data-factory-rest-api.md#create-a-data-factory)に関する記事の例をご覧ください。

Azure Key Vault に格納されている資格情報を参照するには、次の手順に従う必要があります。

1. データ ファクトリと共に生成された "サービス ID アプリケーション ID" をコピーします。 「[サービス ID の取得](#retrieve-service-identity)」を参照してください。
2. サービス ID に、Azure Key Vault へのアクセス権を付与します。 キー コンテナー -> [アクセス制御]-> [追加] で、このサービス ID アプリケーション ID を検索して閲覧者のアクセス許可を追加します。 この指定されたファクトリで、キー コンテナー内のシークレットにアクセスできます。
3. Azure Key Vault をポイントするリンクされたサービスを作成します。 「[Azure Key Vault のリンクされたサービス](#azure-key-vault-linked-service)」をご覧ください。
4. データ ストアのリンクされたサービスを作成します。その内部で、キー コンテナーに格納されている対応するシークレットを参照します。 「[キー コンテナーに格納された資格情報の参照](#reference-credential-stored-in-key-vault)」をご覧ください。

## <a name="retrieve-service-identity"></a>サービス ID の取得

Azure Portal から、またはプログラムによって、サービス ID を取得することができます。 以降のセクションでは、いくつかの例を示します。

>[!TIP]
> サービス ID が表示されない場合は、ファクトリを更新して[サービス ID を生成](#generate-service-identity)してください。

### <a name="using-azure-portal"></a>Azure Portal の使用

[Azure Portal] -> 自分のデータ ファクトリ -> [設定] -> [プロパティ] で、サービス ID 情報を検索できます。

- サービス ID
- サービス ID のテナント
- **サービス ID アプリケーション ID**: Key Vault でのアクセス権を付与するには、この値をコピーします

![サービス ID の取得](media/store-credentials-in-key-vault/retrieve-service-identity-portal.png)

### <a name="using-powershell"></a>PowerShell の使用

特定のデータ ファクトリを取得すると、次のように、サービス ID のプリンシパル ID およびテナント ID が返されます。

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

**ApplicationId** を取得するには、プリンシパル ID をコピーし、プリンシパル ID をパラメーターとして指定して次の Azure Active Directory コマンドを実行します。ApplicationId は、Key Vault でのアクセス権を付与するために使用します。

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault のリンクされたサービス

Azure Key Vault のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **AzureKeyVault** に設定する必要があります。 | あり |
| baseUrl | Azure Key Vault の URL を指定します。 | あり |

**例:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
        "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-credential-stored-in-key-vault"></a>キー コンテナーに格納された資格情報の参照

キー コンテナーのシークレットを参照するリンクされたサービスのフィールドを構成する場合は、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | フィールドの type プロパティは **AzureKeyVaultSecret** に設定する必要があります。 | あり |
| secretName | Azure Key Vault のシークレットの名前。 | あり |
| secretVersion | Azure Key Vault のシークレットのバージョン。<br/>指定しない場合は、常に最新バージョンのシークレットが使用されます。<br/>指定した場合は、その特定のバージョンに固定されます。| いいえ |
| store | 資格情報の格納に使用する Azure Key Vault のリンクされたサービスを表します。 | あり |

**例: ("password" のセクションをご覧ください)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="generate-service-identity"></a>サービス ID の生成

「[サービス ID の取得](#retrieve-service-identity)」の手順に従ってデータ ファクトリにサービス ID がまだ関連付けられていない場合は、ID イニシエーターを使用してプログラムでデータ ファクトリを更新することで、サービス ID を生成できます。

> [!NOTE]
> - **サービス ID は変更できません**。 サービス ID が既に存在するデータ ファクトリを更新しても影響はありません。サービス ID は変更なく保持されます。
> - **サービス ID は削除できません**。 ファクトリ オブジェクトで "identity" パラメーターを指定せずに、または REST 要求本文で "identity" セクションを指定せずに、サービス ID が既に存在するデータ ファクトリを更新した場合、エラーが発生します。

次のセクションでは、サービス ID が存在しない場合にファクトリのサービス ID を生成する方法の例を示します。

### <a name="using-powershell"></a>PowerShell の使用

**Set-AzureRmDataFactoryV2** コマンドを再度呼び出すと、"Identity" フィールドが新たに生成されているのを確認できます。

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="using-rest-api"></a>REST API の使用

要求本文の "identity" セクションを使用して、次の API を呼び出します。

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
```

**要求本文**: "identity": { "type": "SystemAssigned" } を追加します。

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**応答**: サービス ID が自動的に作成され、これに応じて "identity" セクションが設定されます。

```json
{
    "name": "ADFV2DemoFactory",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2017-09-01-preview"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "EastUS"
}
```

### <a name="using-sdk"></a>SDK の使用

Identity=new FactoryIdentity() を指定して、データ ファクトリの作成または更新関数を呼び出します。 .NET を使用したサンプル コードは以下のとおりです。

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
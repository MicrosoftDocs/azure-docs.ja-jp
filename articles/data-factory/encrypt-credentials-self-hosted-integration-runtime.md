---
title: Azure Data Factory での資格情報の暗号化
description: オンプレミスのデータ ストアの資格情報を暗号化して、自己ホスト型の統合ランタイムを備えるコンピューターに格納する方法について説明します。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: bcca2e6168baafe9f98f663298790841c0f1f450
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927425"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Azure Data Factory でオンプレミスのデータ ストアの資格情報を暗号化する
オンプレミスのデータ ストアの資格情報を暗号化し、自己ホスト型の統合ランタイムを備えるコンピューターに格納することができます (機密情報に関するリンクされたサービス)。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

資格情報を含む JSON 定義ファイルを <br/>[**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) コマンドレットに渡して、暗号化された資格情報が入った出力 JSON 定義ファイルを生成します。 更新された JSON 定義を使用して、リンクされたサービスを作成します。

## <a name="author-sql-server-linked-service"></a>SQL Server のリンクされているサービスを作成する
以下の内容を含む、**SqlServerLinkedService.json** という名前の JSON ファイルを任意のフォルダーに作成します。  

ファイルを保存する前に、`<servername>`、`<databasename>`、`<username>`、および`<password>` を SQL Server 用の値に置き換えます。 `<integration runtime name>` を統合ランタイムの名前に置き換えます。 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
            }
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>資格情報を暗号化する
JSON ペイロードからの機密データを暗号化して、オンプレミスのセルフホステッド統合ランタイムに格納するには、**New-AzDataFactoryV2LinkedServiceEncryptedCredential** を実行して、JSON ペイロードを渡します。 このコマンドレットでは、DPAPI によって資格情報が暗号化され、自己ホスト型の統合ランタイム ノードにローカルに格納されます。 資格情報への暗号化された参照が含まれる出力ペイロードは、別の JSON ファイル (この場合は "encryptedLinkedService.json") にリダイレクトできます。

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>資格情報が暗号化された JSON を使用する
ここで、暗号化した資格情報が含まれている、前のコマンドから出力された JSON ファイルを使用して、**SqlServerLinkedService** を設定します。

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>次の手順
データ移動のためのセキュリティに関する考慮事項については、[データ移動時のセキュリティに関する考慮事項](data-movement-security-considerations.md)に関するページを参照してください。


---
title: "Azure Data Factory での資格情報の暗号化 | Microsoft Docs"
description: "オンプレミスのデータ ストアの資格情報を暗号化して、自己ホスト型の統合ランタイムを備えるコンピューターに格納する方法について説明します。"
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: abnarain
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: 8363187b7c8492e9386715324f87e70d7d8615af
ms.contentlocale: ja-jp
ms.lasthandoff: 09/28/2017

---

# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Azure Data Factory でオンプレミスのデータ ストアの資格情報を暗号化する
オンプレミスのデータ ストアの資格情報を暗号化し、自己ホスト型の統合ランタイムを備えるコンピューターに格納することができます (機密情報に関するリンクされたサービス)。 

資格情報を含む JSON 定義ファイルを  <br/>[**New-AzureRmDataFactoryV2LinkedServiceEncryptCredential** ](https://docs.microsoft.com/powershell/module/azurerm.datafactoryv2/New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential?view=azurermps-4.4.0) コマンドレットに渡して、暗号化された資格情報が入った出力 JSON 定義ファイルを生成します。 更新された JSON 定義を使用して、リンクされたサービスを作成します。

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
JSON ペイロードからの機密データを暗号化して、オンプレミスの自己ホスト型の統合ランタイムに格納するには、**New-AzureRmDataFactoryV2LinkedServiceEncryptCredential** を実行して、JSON ペイロードを渡します。 このコマンドレットでは、DPAPI によって資格情報が暗号化され、自己ホスト型の統合ランタイム ノードにローカルに格納されます。 暗号化された資格情報が含まれる出力ペイロードは別の JSON ファイル (この場合は 'encryptedLinkedService.json') にリダイレクトできます。

```powershell
New-AzureRmDataFactoryV2LinkedServiceEncryptCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>資格情報が暗号化された JSON を使用する
ここで、暗号化した資格情報が含まれている、前のコマンドから出力された JSON ファイルを使用して、**SqlServerLinkedService** を設定します。

```powershell
Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>次のステップ
データ移動のためのセキュリティに関する考慮事項については、[データ移動時のセキュリティに関する考慮事項](data-movement-security-considerations.md)に関するページを参照してください。



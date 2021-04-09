---
title: Azure Purview ラベルを使用して Azure SQL データを分類する
description: Azure Purview から Azure SQL Database および Azure Synpase Analytics に分類をインポートします
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: azurepowershell
ms.topic: sample
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/17/2021
ms.openlocfilehash: 2eab7c535ff0c68da772e8a45ead12420734279c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714903"
---
# <a name="classify-your-azure-sql-data-using-azure-purview-labels"></a>Azure Purview ラベルを使用して Azure SQL データを分類する
[!INCLUDE[appliesto-sqldb-asa](../../azure-sql/includes/appliesto-sqldb-asa.md)]

このドキュメントでは、Azure SQL Database および Azure Synapse Analytics (以前の SQL DW) に Azure Purview ラベルを追加する方法について説明します。

## <a name="create-an-application"></a>アプリケーションの作成

1. Azure portal で、 **[Azure Active Directory]** を開きます。
2. **[管理]** で **[アプリの登録]** を選択します。
3. **[新しいアプリケーション]** を選択して、新しい Azure Active Directory アプリを作成します。
4. アプリケーションの名前を入力し、 **[登録]** を選択します。
5. アプリケーションが作成されたら、 **[管理者]** の **[証明書とシークレット]** を開きます。
6. **[クライアント シークレット]** の **[新しいクライアント シークレット]** を選択して、新しいクライアント シークレットを作成します。
7. クライアント シークレットに説明を追加し、有効期限を選択して、 **[追加]** を選択します。
8. 後で使用できるように、**値** を保存します。

   > [!NOTE]
   > ページを閉じると、値はマスクされます。 ページに戻っても、クライアント シークレットの値を取得することはできなくなります。 新しいクライアント シークレットを生成する必要があります。

9. 新しく作成したアプリケーションの [概要] ページに戻り、後で使用できるように次の値をコピーします。
    1. アプリケーション (クライアント) ID
    1. ディレクトリ (テナント) ID

## <a name="provide-permissions-to-the-application"></a>アプリケーションへのアクセス許可の付与

1. Azure portal で、"**Purview アカウント**" を検索します。
2. SQL データベースおよび Synapse を分類する Purview アカウントを選択します。
3. **[アクセス制御 (IAM)]** を開き、 **[追加]** を選択します。

4. **[ロールの割り当ての追加]** を選択します。
5. **[ロール]** セクションで、"**Purview データ閲覧者**" を検索して選択します。
6. **[選択]** セクションで、前に作成したアプリケーションを検索して選択し、 **[保存]** をクリックします。

## <a name="extract-the-classification-from-azure-purview"></a>Azure Purview からの分類の抽出

1. Purview アカウントを開き、ホーム ページで、ラベルをコピーする Azure SQL Database または Azure Synapse Analytics を検索します。
2. **[プロパティ]** の qualifiedName をコピーし、後で使用できるように保存します。
3. PowerShell シェルを開きます。

4. SQL のアセットの種類 (Azure SQL Database または Azure Synapse) に応じて、次のスクリプトのいずれかをコピーします。
5. 上でコピーした各値をパラメーターに入力します。

   a. $TenantID: セクション 1 の手順 9.
   
   b. $ClientID: セクション 1 の手順 9.
   
   c. $SecretID: セクション 1 の手順 8.
   
   d. $purviewAccountName: セクション 2 の手順 2.
   
   e. $sqlDatabaseName: セクション 3 の手順 2.

6. 後で使用できるように、スクリプトの出力をコピーします。

### <a name="for-azure-sql-database"></a>Azure SQL Database の場合

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$sqlDatabaseName="mssql://sql_server_name.database.windows.net/db_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_db?attr:qualifiedName=" + $sqlDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

### <a name="for-azure-synapse-analytics"></a>Azure Synapse Analytics の場合

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$dwDatabaseName="mssql://dw_server_name.database.windows.net/dw_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_dw?attr:qualifiedName=" + $dwDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_dw_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

## <a name="run-the-t-sql-command-on-your-sql-asset"></a>SQL アセットでの T-SQL コマンドの実行

1. 任意のツールを使用して、Azure SQL Database または Azure Synapse に接続します。
2. 前のセクションからコピーした T-SQL コマンドを実行します。

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/)を参照してください。

Azure Purview の詳細については、「[Azure Purview のドキュメント](../../purview/index.yml)」を参照してください。
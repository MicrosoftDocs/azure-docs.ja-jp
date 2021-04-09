---
title: Azure Resource Manager:単一データベースを作成する
description: Azure Resource Manager テンプレートを使用して Azure SQL Database に単一データベースを作成します。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: sstein
ms.date: 06/24/2020
ms.openlocfilehash: b7883144509760c9670decd50062d2595b8dc495
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96187087"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して Azure SQL Database で単一データベースを作成する

[単一データベース](single-database-overview.md)の作成は、Azure SQL Database でデータベースを作成する場合の最も迅速かつ簡単なオプションです。 このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して単一データベースを作成する方法について説明します。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-database%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="review-the-template"></a>テンプレートを確認する

単一データベースには、2 種類の[購入モデル](purchasing-models.md)のいずれかを使用して定義されたコンピューティング、メモリ、IO、ストレージのリソースのセットがあります。 単一データベースを作成するときは、それを管理するための[サーバー](logical-servers.md)も定義し、指定したリージョンの [Azure リソース グループ](../../active-directory-b2c/overview.md)内にそれを配置します。

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-sql-database/)からのものです。

:::code language="json" source="~/quickstart-templates/101-sql-database/azuredeploy.json":::

テンプレートでは、次のリソースが定義されています。

- [**Microsoft.Sql/servers**](/azure/templates/microsoft.sql/servers)
- [**Microsoft.Sql/servers/databases**](/azure/templates/microsoft.sql/servers/databases)

その他の Azure SQL Database テンプレートのサンプルは、[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular)のページから入手できます。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

次の PowerShell コード ブロックから **[使ってみる]** を選択して Azure Cloud Shell を開きます。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-database/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="validate-the-deployment"></a>デプロイの検証

データベースに対してクエリを実行するには、「[データベースのクエリを実行する](single-database-create-quickstart.md#query-the-database)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

「[次の手順](#next-steps)」に進む場合は、このリソース グループ、サーバー、単一データベースをそのままにしてください。 次のステップでは、データベースに接続してクエリを実行するさまざまな方法を紹介しています。

リソース グループを削除するには:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>次のステップ

- オンプレミスまたはリモート ツールから単一データベースに接続するために、サーバー レベルのファイアウォール規則を作成します。 詳細については、「[サーバーレベルのファイアウォール規則を作成する](firewall-create-server-level-portal-quickstart.md)」を参照してください。
- サーバーレベルのファイアウォール規則を作成した後に、いくつかの異なるツールと言語を使用して、データベースに[接続し、クエリを実行](connect-query-content-reference-guide.md)します。
  - [SQL Server Management Studio を使用して接続およびクエリを実行する](connect-query-ssms.md)
  - [Azure Data Studio を使用して接続およびクエリを実行する](/sql/azure-data-studio/quickstart-sql-database?toc=%2fazure%2fsql-database%2ftoc.json)
- Azure CLI を使用して単一データベースを作成するには、「[Azure CLI のサンプル](az-cli-script-samples-content-guide.md)」をご覧ください。
- Azure PowerShell を使用して単一データベースを作成するには、「[Azure PowerShell サンプル](powershell-script-content-guide.md)」をご覧ください。
- ARM テンプレートを作成する方法については、[初めてのテンプレートの作成](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)に関するページをご覧ください。
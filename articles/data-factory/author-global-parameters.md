---
title: グローバル パラメーター
description: Azure Data Factory 環境ごとにグローバル パラメーターを設定する
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.date: 08/05/2020
ms.openlocfilehash: 052f502ed27db9ade0fd2916f91d6922c52a5a98
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854146"
---
# <a name="global-parameters-in-azure-data-factory"></a>Data Factory のグローバル パラメーター

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

グローバル パラメーターは、データ ファクトリ全体での定数で、任意の式のパイプラインで使用できます。 これらは、パラメーター名と値が同一のパイプラインが複数ある場合に便利です。 継続的インテグレーションとデプロイ プロセス (CI/CD) を使用してデータ ファクトリを昇格させる場合は、各環境のこれらのパラメーターを上書きできます。 

## <a name="creating-global-parameters"></a>グローバル パラメーターの作成

グローバル パラメーターを作成するには、 *[管理]* セクションの *[グローバル パラメーター]* タブに移動します。 **[新規]** を選択して作成サイド ナビゲーションを開きます。

![グローバル パラメーターを作成する](media/author-global-parameters/create-global-parameter-1.png)

サイド ナビゲーションで、名前を入力し、データ型を選択して、パラメーターの値を指定します。

![グローバル パラメーターを作成する](media/author-global-parameters/create-global-parameter-2.png)

グローバル パラメーターが作成されたら、パラメーターの名前をクリックすると編集できます。 複数のパラメーターを一度に変更するには、 **[すべて編集]** を選択します。

![グローバル パラメーターを作成する](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>パイプラインでのグローバル パラメーターの使用

グローバル パラメーターは、任意の[パイプライン式](control-flow-expression-language-functions.md)で使用できます。 パイプラインが、データセットやデータ フローなどの別のリソースを参照している場合は、そのリソースのパラメーターを介してグローバル パラメーター値を渡すことができます。 グローバル パラメーターは `pipeline().globalParameters.<parameterName>` として参照されます。

![グローバル パラメーターの使用](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a> CI/CD でのグローバル パラメーター

グローバル パラメーターには、Azure Data Factory 内の他のエンティティと比べて独特な CI/CD プロセスがあります。 グローバル パラメーターを使用してファクトリを発行したり、Resource Manager テンプレートをエクスポートしたりすると、*your-factory-name_GlobalParameters.json* という名前のファイルが含まれる *globalParameters* という名前のフォルダーが作成されます。 このファイルは、発行されたファクトリ内の各グローバル パラメーターの型と値を含む JSON オブジェクトです。

![グローバル パラメーターの発行](media/author-global-parameters/global-parameters-adf-publish.png)

TEST や PROD などの新しい環境にデプロイする場合は、このグローバル パラメーター ファイルのコピーを作成し、適切な環境固有の値を上書きすることをお勧めします。 再発行時に元のグローバル パラメーター ファイルは上書きされますが、他の環境用のコピーはそのままになります。

たとえば、"ADF-DEV" という名前のファクトリと、値 "DEV" を持つ "environment" という名前の文字列型のグローバル パラメーターがある場合、発行時には *ADF-DEV_GlobalParameters.json* という名前のファイルが生成されます。 "ADF_TEST" という名前のテスト ファクトリにデプロイする場合は、JSON ファイルのコピーを (ADF-TEST_GlobalParameters.json などの名前で) 作成し、パラメーター値を環境固有の値に置き換えます。 これでパラメーター "environment" の値は、"test" になる可能性があります。 

![グローバル パラメーターのデプロイ](media/author-global-parameters/powershell-task.png)

次の PowerShell スクリプトを使用して、グローバル パラメーターを他の環境に昇格させます。 Resource Manager テンプレートのデプロイ前に、Azure PowerShell DevOps タスクを追加します。 DevOps タスクでは、新しいパラメーター ファイルの場所、ターゲット リソース グループ、ターゲット データ ファクトリを指定する必要があります。

> [!NOTE]
> PowerShell を使用してグローバル パラメーターをデプロイするには、バージョン 4.4.0 以降の Az モジュールを使用する必要があります。

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>次の手順

* Azure Data Factory における[継続的インテグレーションとデプロイ プロセス](continuous-integration-deployment.md)について確認する
* [制御フローの式言語](control-flow-expression-language-functions.md)の使用方法を確認します
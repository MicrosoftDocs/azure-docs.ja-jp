---
title: 管理ソリューションのビュー | Microsoft Docs
description: '管理ソリューションには、通常データを視覚化する 1 つまたは複数のビューが含まれています。  この記事では、ビュー デザイナーで作成したビューをエクスポートし、管理ソリューションに含める方法について説明します。 '
services: monitoring
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.openlocfilehash: cefb83d5336bb99fd09001b5ea369a0b8fc4b942
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769132"
---
# <a name="views-in-management-solutions-preview"></a>管理ソリューションのビュー (プレビュー)
> [!NOTE]
> 本記事は、現在プレビュー段階である管理ソリューションの作成手順に関する暫定版ドキュメントです。 本記事で説明するスキーマは、変更されることがあります。    


[管理ソリューション](solutions.md)には、通常データを視覚化する 1 つまたは複数のビューが含まれています。  この記事では、[ビュー デザイナー](../../azure-monitor/platform/view-designer.md)で作成したビューをエクスポートし、管理ソリューションに含める方法について説明します。  

> [!NOTE]
> この記事のサンプルでは、管理ソリューションに必須であるかまたは一般的に用いられるパラメーターと変数を使用します。これらについては、「[Azure での管理ソリューションの設計とビルド](solutions-creating.md)」で説明しています。
>
>

## <a name="prerequisites"></a>前提条件
この記事では、[管理ソリューションの作成方法](solutions-creating.md)およびソリューション ファイルの構造を理解していることを前提としています。

## <a name="overview"></a>概要
ビューを管理ソリューションに含めるには、[ソリューション ファイル](solutions-creating.md)でビューの**リソース**を作成します。  ただし、ビューの詳細な構成を表す JSON は通常複雑で、典型的なソリューション作成者が手動で作成できるものではありません。  [ビュー デザイナー](../../azure-monitor/platform/view-designer.md)を使用してビューの作成とエクスポートを行い、詳細な構成をソリューションに追加するのが最も一般的な方法です。

ビューをソリューションに追加する基本的な手順は次のとおりです。  各手順の詳細については、以降のセクションで詳しく説明します。

1. ビューをファイルにエクスポートします。
2. ソリューションでビュー リソースを作成します。
3. ビューの詳細を追加します。

## <a name="export-the-view-to-a-file"></a>ビューをファイルにエクスポートする
[Log Analytics ビュー デザイナー](../../azure-monitor/platform/view-designer.md)の指示に従って、ビューをファイルにエクスポートします。  エクスポートされたファイルは JSON 形式で、[ソリューション ファイルと同じ要素](solutions-solution-file.md)が含まれています。  

ビュー ファイルの **resources** 要素には、Log Analytics ワークスペースを表す **Microsoft.OperationalInsights/workspaces** 型のリソースがあります。  この要素には、ビューを表し、その詳細な構成を含む**views** 型のサブ要素があります。  この要素の詳細をコピーしてからソリューションにコピーします。

## <a name="create-the-view-resource-in-the-solution"></a>ソリューションでビュー リソースを作成する
次のビュー リソースをソリューション ファイルの **resources** 要素に追加します。  これは以下に説明する変数を使用して、追加する必要があります。  **Dashboard** と **OverviewTile** プロパティは、エクスポートしたビュー ファイルの対応するプロパティで上書きするプレースホルダーであることに注意してください。

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

次の変数をソリューション ファイルの variables 要素に追加して、値をソリューションの値に置き換えます。

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

エクスポートした ビュー ファイルからビュー リソースがすべてコピーされる場合がありますが、ソリューションで動作するには次の変更を行う必要があることに注意してください。  

* ビュー リソースの**型**を、**views** から **Microsoft.OperationalInsights/workspaces** に変更する必要があります。
* ビュー リソースの **name** プロパティを、ワークスペース名を含むように変更する必要があります。
* ワークスペース リソースはソリューションで定義されないため、ワークスペースの依存関係を削除する必要があります。
* **DisplayName** プロパティをビューに追加する必要があります。  **Id**、**Name**、および **DisplayName** は、すべて一致する必要があります。
* パラメーター名は、必要な一連のパラメーターに一致するように変更する必要があります。
* 変数はソリューションで定義され、適切なプロパティで使用する必要があります。

### <a name="log-analytics-api-version"></a>Log Analytics API バージョン
Resource Manager テンプレートで定義された Log Analytics リソースはすべて、そのリソースで使用する API のバージョンを定義するプロパティ **apiVersion** を保持しています。  このバージョンは、[レガシおよびアップグレードされたクエリ言語](../../azure-monitor/log-query/log-query-overview.md)を使うクエリのあるビューでは異なります。  

 次の表では、レガシ ワークスペースとアップグレードされたワークスペースでのビューに対する Log Analytics API のバージョンを指定します。 

| ワークスペースのバージョン | API バージョン | Query |
|:---|:---|:---|
| v1 (レガシ)   | 2015-11-01-preview | レガシ形式。<br> 例:Type=Event EventLevelName=error  |
| v2 (アップグレード) | 2015-11-01-preview | レガシ形式。  インストール時にアップグレードされた形式に変換されます。<br> 例:Type=Event EventLevelName=error<br>変換後:Event &#124; where EventLevelName == "Error"  |
| v2 (アップグレード) | 2017-03-03-preview | アップグレード形式。 <br>例:Event &#124; where EventLevelName == "Error"  |


## <a name="add-the-view-details"></a>ビューの詳細を追加する
エクスポートしたビュー ファイル内のビュー リソースには、**properties** 要素に、ビューの詳細な構成を含む 2 つの要素 (**Dashboard** と **OverviewTile**) が含まれています。  これら 2 つの要素とその内容を、ソリューション ファイル内のビュー リソースの **properties** 要素にコピーします。

## <a name="example"></a>例
たとえば次のサンプルは、ビューを含むシンプルなソリューション ファイルを示しています。  スペース上の理由から、**Dashboard** と **OverviewTile** の内容には省略記号 (...) が表示されています。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>次の手順
* [管理ソリューション](solutions-creating.md)の作成についての詳細を参照する。
* [Automation Runbook を管理ソリューションに](solutions-resources-automation.md)含める。

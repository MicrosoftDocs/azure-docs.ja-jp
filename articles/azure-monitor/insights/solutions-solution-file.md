---
title: Azure での管理ソリューション ファイルの作成 | Microsoft Docs
description: 管理ソリューションには、パッケージ化された管理シナリオが用意されており、お客様はそれを Azure 環境に追加できます。  この記事では、管理ソリューションを作成してお使いの環境で使用したり顧客に提供したりする方法について、詳しく説明します。
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4e5c27911fe86a6916235014f8602327df929e20
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526369"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Azure での管理ソリューション ファイルの作成 (プレビュー)
> [!NOTE]
> 本記事は、現在プレビュー段階である Azure の管理ソリューションの作成に関する暫定版ドキュメントです。 本記事で説明するスキーマは、変更されることがあります。  

Azure での管理ソリューションは、[Resource Manager テンプレート](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)として実装されます。  管理ソリューションの作成を理解するには、[テンプレートを作成する](../../azure-resource-manager/resource-group-authoring-templates.md)方法を参照してください。  この記事では、ソリューションに使用するテンプレートと、典型的なソリューション リソースを構成する方法について、詳細を説明します。


## <a name="tools"></a>ツール

ソリューション ファイルの操作には任意のテキスト エディターを使用できますが、次の記事で説明するように Visual Studio や Visual Studio Code で提供される機能を活用することをお勧めします。

- [Visual Studio での Azure リソース グループの作成とデプロイ](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Visual Studio Code で Azure Resource Manager テンプレートを操作する](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>Structure
管理ソリューション ファイルの基本的な構造は、次のような [Resource Manager テンプレート](../../azure-resource-manager/resource-group-authoring-templates.md#template-format)と同じです。  以下の各セクションでは、最上位レベルの要素と、ソリューションにおけるその内容について説明します。  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>parameters
[パラメーター](../../azure-resource-manager/resource-group-authoring-templates.md#parameters)は、管理ソリューションをインストールするときに、ユーザーから必要とする値です。  すべてのソリューションが持つ標準のパラメーターがありますが、特定のソリューションに必要な追加のパラメーターを加えることができます。  ソリューションのインストール時にユーザーがパラメーター値を設定する方法は、特定のパラメーターおよびソリューションのインストール方法によって異なります。

ユーザーが [Azure Marketplace](solutions.md#install-a-monitoring-solution) または Azure クイック スタート テンプレートから管理ソリューションをインストールすると、[Log Analytics ワークスペースと Automation アカウント](solutions.md#log-analytics-workspace-and-automation-account)を選択するよう求めるメッセージが表示されます。  これらは、各標準パラメーターの値の設定に使用されます。  ユーザーは、標準パラメーターに値を直接入力することは求められませんが、追加のパラメーターには値を入力することが求められます。


サンプル パラメーターを次に示します。  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

次の表で、パラメーターの属性について説明します。

| Attribute | 説明 |
|:--- |:--- |
| type |パラメーターのデータ型。 ユーザーに表示される入力コントロールは、データ型によって異なります。<br><br>bool - ドロップダウン ボックス<br>string - テキスト ボックス<br>int - テキスト ボックス<br>securestring - パスワード フィールド<br> |
| category |パラメーターの任意のカテゴリ。  同じカテゴリのパラメーターはグループ化されます。 |
| control |文字列パラメーターの追加の機能。<br><br>datetime - Datetime コントロールが表示されます。<br>guid - GUID 値が自動的に生成され、パラメーターは表示されません。 |
| description |パラメーターの説明です (省略可能)。  パラメーターの横の情報バルーンに表示されます。 |

### <a name="standard-parameters"></a>標準パラメーター
次の表に、すべての管理ソリューションの標準パラメーターを一覧表示します。  Azure Marketplace や クイック スタート テンプレートからソリューションをインストールすると、ユーザーに設定が求められることはなく、これらの値が設定されます。  ソリューションを別の方法でインストールした場合、ユーザーは値を入力する必要があります。

> [!NOTE]
> Azure Marketplace と クイック スタート テンプレートのユーザー インターフェイスには、表に記載したパラメーター名が必要です。  別のパラメーター名を使用すると、ユーザーに表のパラメーター名を設定するよう求めるメッセージが表示されます。パラメーター名は自動的に設定されません。
>
>

| パラメーター | Type | 説明 |
|:--- |:--- |:--- |
| accountName |string |Azure automation アカウント名。 |
| pricingTier |string |Log Analytics ワークスペースと Azure Automation アカウントの両方の価格レベル。 |
| regionId |string |Azure Automation アカウントのリージョン。 |
| solutionName |string |ソリューションの名前。  クイックスタート テンプレートを使用してソリューションをデプロイする場合は、solutionName をパラメーターとして定義し、ユーザーに指定を求める代わりに文字列を定義できるようにする必要があります。 |
| workspaceName |string |Log Analytics ワークスペース名。 |
| workspaceRegionId |string |Log Analytics ワークスペースのリージョン。 |


次に、コピーして、ソリューション ファイルに貼り付けることができる標準パラメーターの構造を示します。  

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        }
    }


ソリューション内のその他の要素のパラメーター値は、 **parameters('parameter name')** の構文を使用して参照します。  たとえば、ワークスペース名にアクセスするには、**parameters('workspaceName')** を使用します。

## <a name="variables"></a>variables
[Variables](../../azure-resource-manager/resource-group-authoring-templates.md#variables) は、その他の管理ソリューションで使用する値です。  これらの値は、ソリューションをインストールするユーザーには公開されません。  これは、作成者に 1 つの場所を提供することを意図しており、それによって、ソリューション全体にわたって何度も使用する値を管理できるようにします。 **resources** 要素でハードコーディングするのとは異なり、ソリューション固有の値を変数で記述する必要があります。  これによってコードが読みやすくなるとともに、後のバージョンでこれらの値を簡単に変更することができます。

次の例は、ソリューションで使用される一般的なパラメーターを持つ**variables**要素です。

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

構文**variables('variable name')** ソリューションの変数値を参照します。  たとえば、SolutionName 変数にアクセスするには、**variables('solutionName')** を使います。

複数の値のセットに使用する複合型の変数を定義することもできます。  これらは、異なる種類のリソースに対して複数のプロパティを定義する管理ソリューションで特に便利です。  たとえば、上記のソリューション変数を次のように再構築することができます。

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

この場合、構文**variables('variable name').property** を使用してソリューションの変数値を参照します。  たとえば、SolutionName 変数にアクセスするには、**variables('Solution').Name** を使います。

## <a name="resources"></a>リソース
[Resources](../../azure-resource-manager/resource-group-authoring-templates.md#resources) は、管理ソリューションがインストールおよび構成するさまざまなリソースを定義します。  この部分が、テンプレートの大半を占め、最も複雑な部分です。  resource 要素の構造と詳細な説明については、[Azure Resource Manager テンプレートの作成](../../azure-resource-manager/resource-group-authoring-templates.md#resources)に関するページを参照してください。  このドキュメントの他の記事では、通常定義するその他のリソースについて説明します。 


### <a name="dependencies"></a>依存関係
**dependsOn** 要素は、他のリソースの[依存関係](../../azure-resource-manager/resource-group-define-dependencies.md)を指定します。  ソリューションをインストールすると、すべての依存関係が作成されるまでリソースは作成されません。  たとえば、[ジョブ リソース](solutions-resources-automation.md#automation-jobs)を使用してソリューションをインストールすると、ソリューションが [Runbook を開始する](solutions-resources-automation.md#runbooks)ことがあります。  ジョブ リソースは、ジョブが作成される前に Runbook が作成されたことを確認するために Runbook リソースに依存します。

### <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics ワークスペースと Automation アカウント
管理ソリューションでは、ビューを格納するために [Log Analytics ワークスペース](../../azure-monitor/platform/manage-access.md)が、Runbook と関連リソースを格納するために [Automation アカウント](../../automation/automation-security-overview.md#automation-account-overview)が必要です。  これらはソリューションのリソースが作成される前に使用できるようにする必要があり、ソリューションそのもので定義すべきではありません。  ユーザーはソリューションのデプロイ時に[ワークスペースとアカウントを指定](solutions.md#log-analytics-workspace-and-automation-account)しますが、作成者としては、次の点を考慮する必要があります。


## <a name="solution-resource"></a>ソリューションのリソース
各ソリューションは、ソリューション自体を定義する**resources**要素の中にリソース エントリが必要です。  これは**Microsoft.OperationsManagement/solutions**の 1 つを有し、次の構造を持ちます。 これには、ソリューションのプロパティの定義に通常使用される[標準パラメーター](#parameters)と[変数](#variables)が含まれます。


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>依存関係
ソリューション リソースは、ソリューションが作成される前に存在している必要があるため、ソリューションの別のリソースごとに[依存関係](../../azure-resource-manager/resource-group-define-dependencies.md)を持つ必要があります。  このために、**dependsOn** 要素にある各リソースにエントリを追加します。

### <a name="properties"></a>Properties
このソリューション リソースには、次の表のプロパティがあります。  これには、ソリューションに含まれ参照されるリソースが含まれます。ソリューションをインストールした後に、どのようにリソースを管理するかを定義しています。  ソリューション内の各リソースは、**referencedResources** または **containedResources** プロパティのいずれかに表示される必要があります。

| プロパティ | 説明 |
|:--- |:--- |
| workspaceResourceId |*\<リソース グループ ID>/プロバイダー/Microsoft.OperationalInsights/ワークスペース/\<ワークスペース名\>* という形式の Log Analytics ワークスペースの ID。 |
| referencedResources |ソリューション削除時に削除すべきではないソリューション内のリソースの一覧。 |
| containedResources |ソリューション削除時に削除すべきではないソリューション内のリソースの一覧。 |

上記の例は、Runbook、スケジュール、およびビューを含むソリューションが対象です。  スケジュールと Runbook は **properties** 要素で*参照されている*ため、ソリューションが削除されても、削除されません。  ビューが *含まれている* ので、ソリューションが削除されたときでも、削除されません。

### <a name="plan"></a>プラン
ソリューション リソースの**プラン** エンティティには、次の表のプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| name |ソリューションの名前。 |
| version |作成者によって決定されるソリューションのバージョン。 |
| product |ソリューションを特定する一意の文字列。 |
| publisher |ソリューションの発行者。 |



## <a name="next-steps"></a>次の手順
* 管理ソリューションに、[保存した検索とアラートを追加する](solutions-resources-searches-alerts.md)。
* 管理ソリューションに[ビューを追加する](solutions-resources-views.md)。
* 管理ソリューションに [Runbook とその他の Automation リソースを追加する](solutions-resources-automation.md)。
* [Azure Resource Manager のテンプレートの作成](../../azure-resource-manager/resource-group-authoring-templates.md)の詳細について
* Resource Manager テンプレートの様々なサンプルは、[Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates) で検索できます。

---
title: 管理ソリューションの Azure Automation リソース | Microsoft Docs
description: 一般的な管理ソリューションでは、Azure Automation の Runbook を追加して、監視データの収集や処理などのプロセスを自動化します。  この記事では、ソリューションに Runbook とその関連リソースを追加する方法について説明します。
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1c9b13f44dae068597cb82a0aa803283ad5e67bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "62110363"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>管理ソリューションへの Azure Automation リソースの追加 (プレビュー)
> [!NOTE]
> 本記事は、現在プレビュー段階である管理ソリューションの作成手順に関する暫定版ドキュメントです。 本記事で説明するスキーマは、変更されることがあります。   


一般的な[管理ソリューション]( solutions.md)では、Azure Automation の Runbook を追加して、監視データの収集や処理などのプロセスを自動化します。  Automation アカウントには、Runbook だけでなく、ソリューションで用いる Runbook をサポートする変数やスケジュールなどのアセットも用意されています。  この記事では、ソリューションに Runbook とその関連リソースを追加する方法について説明します。

> [!NOTE]
> この記事のサンプルでは、管理ソリューションに必須であるかまたは一般的に用いられるパラメーターと変数を使用します。これらについては、「[Azure での管理ソリューションの設計とビルド]( solutions-creating.md)」で説明しています。 


## <a name="prerequisites"></a>前提条件
この記事は、次の情報を理解していることを前提としています。

- [管理ソリューションの作成]( solutions-creating.md)方法
- [ソリューション ファイル]( solutions-solution-file.md)の構造
- [Resource Manager テンプレートの作成](../../azure-resource-manager/resource-group-authoring-templates.md)方法

## <a name="automation-account"></a>Automation アカウント
Azure Automation のリソースはすべて、[Automation アカウント](../../automation/automation-security-overview.md#automation-account-overview) に含まれています。  [Log Analytics ワークスペースと Automation アカウント]( solutions.md#log-analytics-workspace-and-automation-account)の説明にあるように、Automation アカウントは管理ソリューションに含まれていませんが、ソリューションのインストール前に追加する必要があります。  このアカウントが含まれていない場合、ソリューションのインストールは失敗します。

各 Automation リソースの名前には、Automation アカウントの名前を含めます。  これは、次の Runbook リソースの例で示すように **accountName** パラメーターをソリューションで指定することにより行います。

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
ソリューションをインストールした際に Runbook が作成されるようにするため、ソリューション ファイルにはそのソリューションで使用されるすべての Runbook を含める必要があります。  ただし、テンプレートには Runbook の本体を含めることはできないため、そのソリューションをインストールするユーザーがアクセスできる公開場所に Runbook を発行する必要があります。

[Azure Automation Runbook](../../automation/automation-runbook-types.md) リソースのタイプは **Microsoft.Automation/automationAccounts/runbooks** であり、次のような構造をしています。 ソリューション ファイルにコード スニペットをコピーして貼り付け、パラメータ名を変更できるように、一般的な変数やパラメータが使用されています。 

    {
        "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
        "type": "Microsoft.Automation/automationAccounts/runbooks",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "location": "[parameters('regionId')]",
        "tags": { },
        "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
                "uri": "[variables('Runbook').Uri]",
                "version": [variables('Runbook').Version]"
            }
        }
    }


次の表では、Runbook のプロパティについて説明します。

| プロパティ | 説明 |
|:--- |:--- |
| runbookType |Runbook のタイプを指定します。 <br><br> Script - PowerShell スクリプト <br>PowerShell - PowerShell ワークフロー <br> GraphPowerShell - グラフィカル PowerShell スクリプト Runbook <br> GraphPowerShellWorkflow - グラフィカル PowerShell ワークフロー Runbook |
| logProgress |Runbook の[進捗状況レコード](../../automation/automation-runbook-output-and-messages.md)を生成するかどうかを指定します。 |
| logVerbose |Runbook の[詳細レコード](../../automation/automation-runbook-output-and-messages.md)を生成するかどうかを指定します。 |
| description |Runbook の説明です (省略可能)。 |
| publishContentLink |Runbook のコンテンツを指定します。 <br><br>uri - Runbook のコンテンツへの URI です。  PowerShell Runbook およびスクリプト Runbook の場合は .ps1 ファイル、Graph Runbook の場合はエクスポート済みのグラフィカル Runbook ファイルになります。  <br> version - 追跡対象の Runbook のバージョンです。 |


## <a name="automation-jobs"></a>Automation ジョブ
Azure Automation で Runbook が起動する際、Automation ジョブが作成されます。  管理ソリューションのインストール時に Runbook を自動で起動するため、ソリューションに Automation ジョブ リソースを追加できます。  このメソッドは通常、ソリューションの初期構成に使用される Runbook を起動するために使用されます。  Runbook を定期的に起動するには、[スケジュール](#schedules)と[ジョブ スケジュール](#job-schedules)を作成します。

ジョブ リソースのタイプは **Microsoft.Automation/automationAccounts/jobs** であり、次のような構造をしています。  ソリューション ファイルにコード スニペットをコピーして貼り付け、パラメータ名を変更できるように、一般的な変数やパラメータが使用されています。 

    {
      "name": "[concat(parameters('accountName'), '/', parameters('Runbook').JobGuid)]",
      "type": "Microsoft.Automation/automationAccounts/jobs",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
      ],
      "tags": { },
      "properties": {
        "runbook": {
          "name": "[variables('Runbook').Name]"
        },
        "parameters": {
          "Parameter1": "[[variables('Runbook').Parameter1]",
          "Parameter2": "[[variables('Runbook').Parameter2]"
        }
      }
    }

次の表では、Automation ジョブのプロパティについて説明します。

| プロパティ | 説明 |
|:--- |:--- |
| runbook |起動する Runbook の名前を指定する単一の name エンティティです。 |
| parameters |Runbook に必要な各パラメーター値のエンティティです。 |

ジョブでは、Runbook 名と、Runbook に送信するパラメーター値を指定します。  Runbook はジョブよりも前に作成しなければならないため、ジョブは起動対象の Runbook に[依存]( solutions-solution-file.md#resources)させる必要があります。  起動すべき Runbook が複数ある場合は、ジョブを最初に実行する必要のある別のジョブに依存させることによって順番を定義できます。

ジョブ リソースの名前には GUID を含める必要があります。これは、通常はパラメーターで割り当てます。  GUID パラメーターの詳細は、「[Azure での管理ソリューション ファイルの作成]( solutions-solution-file.md#parameters)」に記載されています。  


## <a name="certificates"></a>証明書
[Azure Automation 証明書](../../automation/automation-certificates.md)のタイプは **Microsoft.Automation/automationAccounts/certificates** であり、次のような構造をしています。 ソリューション ファイルにコード スニペットをコピーして貼り付け、パラメータ名を変更できるように、一般的な変数やパラメータが使用されています。 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



次の表では、証明書リソースのプロパティについて説明します。

| プロパティ | 説明 |
|:--- |:--- |
| base64Value |証明書の Base 64 値です。 |
| thumbprint |証明書の拇印です。 |



## <a name="credentials"></a>資格情報
[Azure Automation 資格情報](../../automation/automation-credentials.md)のタイプは **Microsoft.Automation/automationAccounts/credentials** であり、次のような構造をしています。  ソリューション ファイルにコード スニペットをコピーして貼り付け、パラメータ名を変更できるように、一般的な変数やパラメータが使用されています。 


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

次の表では、資格情報リソースのプロパティについて説明します。

| プロパティ | 説明 |
|:--- |:--- |
| userName |資格情報のユーザー名です。 |
| password |資格情報のパスワードです。 |


## <a name="schedules"></a>スケジュール
[Azure Automation スケジュール](../../automation/automation-schedules.md) のタイプは **Microsoft.Automation/automationAccounts/schedules** であり、次のような構造をしています。 ソリューション ファイルにコード スニペットをコピーして貼り付け、パラメータ名を変更できるように、一般的な変数やパラメータが使用されています。 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

次の表では、スケジュール リソースのプロパティについて説明します。

| プロパティ | 説明 |
|:--- |:--- |
| description |スケジュールの説明です (省略可能)。 |
| startTime |スケジュールの開始時刻を DateTime オブジェクトとして指定します。 有効な DateTime に変更可能な文字列を指定することもできます。 |
| isEnabled |スケジュールを有効にするかどうかを指定します。 |
| interval |スケジュールの間隔の種類です。<br><br>day<br>hour |
| frequency |スケジュールの起動頻度を日数または時間数で指定します。 |

スケジュールの開始時刻は現在の時刻より大きい値で指定する必要があります。  インストール日時が不明なため、この値を変数で指定することはできません。

ソリューション内のスケジュール リソースを使用する場合は、次の 2 つの方法のいずれかを選択します。

- スケジュールの開始時刻のパラメーターを使用します。  この方法では、ユーザーがソリューションをインストールする際に、ユーザーに値を入力するよう要求します。  スケジュールが複数ある場合は、1 つ以上のスケジュールに対して単一のパラメーター値を使用できます。
- ソリューションのインストール時に起動する Runbook を使用して、スケジュールを作成します。  この方法では、ユーザーに時刻を入力するよう要求しません。ただし、ソリューションにスケジュールを含めることはできないため、ソリューションが削除される際にスケジュールも削除されます。


### <a name="job-schedules"></a>ジョブ スケジュール
ジョブ スケジュール リソースは、Runbook をスケジュールとリンクさせます。  ジョブ スケジュール リソースのタイプは **Microsoft.Automation/automationAccounts/jobSchedules** であり、次のような構造をしています。  ソリューション ファイルにコード スニペットをコピーして貼り付け、パラメータ名を変更できるように、一般的な変数やパラメータが使用されています。 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }


次の表では、ジョブ スケジュールのプロパティについて説明します。

| プロパティ | 説明 |
|:--- |:--- |
| schedule name |スケジュールの名前を指定する単一の **name** エンティティです。 |
| runbook name  |Runbook の名前を指定する単一の **name** エンティティです。  |



## <a name="variables"></a>変数
[Azure Automation 変数](../../automation/automation-variables.md)のタイプは **Microsoft.Automation/automationAccounts/variables** であり、次のような構造をしています。  ソリューション ファイルにコード スニペットをコピーして貼り付け、パラメータ名を変更できるように、一般的な変数やパラメータが使用されています。

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

次の表では、変数リソースのプロパティについて説明します。

| プロパティ | 説明 |
|:--- |:--- |
| description | 変数の説明です (省略可能)。 |
| isEncrypted | 変数を暗号化するかどうかを指定します。 |
| type | 現在、このプロパティにはどのような効果もありません。  変数のデータ型は、初期値によって決定されます。 |
| value | 変数の値です。 |

> [!NOTE]
> 現在、**type** プロパティは作成される変数に対してどのような影響も与えません。  変数のデータ型は、値によって決定されます。  

変数の初期値を設定する場合、正しいデータ型として構成する必要があります。  次の表では、使用可能なさまざまなデータ型とその構文を示します。  JSON の値は常に引用符で囲まれ、特殊文字は引用符で囲まれているものと想定されていることに注意してください。  たとえば、文字列値は文字列を囲む (エスケープ文字 (\\) を使った) 引用符で指定され、数値は 1 組の引用符で指定されます。

| データ型 | 説明 | 例 | 結果 |
|:--|:--|:--|:--|
| string   | 値を 2 組の引用符で囲みます。  | "\"Hello world\"" | "Hello world" |
| numeric  | 数値を 1 組の引用符で囲みます。| "64" | 64 |
| boolean  | 引用符で囲まれた **true** または **false**。  この値は小文字にする必要があることに注意してください。 | "true" | true |
| datetime | シリアル化された日付の値。<br>PowerShell の ConvertTo-Json コマンドレットを使って、特定の日付に対するこの値を生成できます。<br>例: get-date "5/24/2017 13:14:57" \| ConvertTo-Json | "\\/Date(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>モジュール
Runbook で使用する[グローバル モジュール](../../automation/automation-integration-modules.md)は Automation アカウントで常に利用可能であるため、管理ソリューションで定義する必要はありません。  ただし、Runbook で使用する他のモジュールのリソースを含める必要があります。

[統合モジュール](../../automation/automation-integration-modules.md)のタイプは **Microsoft.Automation/automationAccounts/modules** であり、次のような構造をしています。  ソリューション ファイルにコード スニペットをコピーして貼り付け、パラメータ名を変更できるように、一般的な変数やパラメータが使用されています。

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


次の表では、モジュール リソースのプロパティについて説明します。

| プロパティ | 説明 |
|:--- |:--- |
| contentLink |モジュールのコンテンツを指定します。 <br><br>uri - モジュールのコンテンツへの URI です。  PowerShell Runbook およびスクリプト Runbook の場合は .ps1 ファイル、Graph Runbook の場合はエクスポート済みのグラフィカル Runbook ファイルになります。  <br> version - 追跡対象のモジュールのバージョンです。 |

モジュール リソースが Runbook の前に作成されていることを確認するために、Runbook をモジュール リソースに依存させる必要があります。

### <a name="updating-modules"></a>モジュールの更新
スケジュールを使用する Runbook が含まれる管理ソリューションを更新する場合、新バージョンのソリューションにこの Runbook で使用する新しいモジュールを含めても、Runbook では旧バージョンのモジュールが使用される場合があります。  ソリューションに次の各 Runbook を含め、その他の Runbook よりも先にこれらの Runbook を実行するジョブを作成する必要があります。  こうすることで、Runbook のロード前に、必要に応じてモジュールが更新されます。

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/): ソリューション内の Runbook で使用されるすべてのモジュールを最新バージョンに保ちます。  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/): スケジュール リソースにリンクされている Runbook で最新のモジュールが使用されるように、すべてのスケジュール リソースを再登録します。




## <a name="sample"></a>サンプル
以下のリソースを含むソリューションのサンプルを次に示します。

- Runbook。  これは、パブリック GitHub リポジトリに格納されている Runbook のサンプルです。
- ソリューションのインストール時に Runbook を起動する Automation ジョブ。
- 定期的に Runbook を起動するスケジュールとジョブ スケジュール。
- 証明書。
- 資格情報。
- 変数。
- モジュール。  これは、Log Analytics にデータを書き込むための [OMSIngestionAPI モジュール](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5)です。 

このサンプルでは、リソースの定義に値をハードコーディングするのではなく、ソリューションでよく使われる[標準ソリューション パラメーター]( solutions-solution-file.md#parameters)の変数を使っています。


    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Log Analytics workspace."
          }
        },
        "accountName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Automation account."
          }
        },
        "workspaceregionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Log Analytics workspace."
          }
        },
        "regionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Automation account."
          }
        },
        "pricingTier": {
          "type": "string",
          "metadata": {
            "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account."
          }
        },
        "certificateBase64Value": {
          "type": "string",
          "metadata": {
            "Description": "Base 64 value for certificate."
          }
        },
        "certificateThumbprint": {
          "type": "securestring",
          "metadata": {
            "Description": "Thumbprint for certificate."
          }
        },
        "credentialUsername": {
          "type": "string",
          "metadata": {
            "Description": "Username for credential."
          }
        },
        "credentialPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Password for credential."
          }
        },
        "scheduleStartTime": {
          "type": "string",
          "metadata": {
            "Description": "Start time for schedule."
          }
        },
        "scheduleTimeZone": {
          "type": "string",
          "metadata": {
            "Description": "Time zone for schedule."
          }
        },
        "scheduleLinkGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the schedule link to runbook.",
            "control": "guid"
          }
        },
        "runbookJobGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the runbook job.",
            "control": "guid"
          }
        }
      },
      "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
    
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31",
    
        "Runbook": {
          "Name": "MyRunbook",
          "Description": "Sample runbook",
          "Type": "PowerShell",
          "Uri": "https://raw.githubusercontent.com/user/myrepo/master/samples/MyRunbook.ps1",
          "JobGuid": "[parameters('runbookJobGuid')]"
        },
    
        "Certificate": {
          "Name": "MyCertificate",
          "Base64Value": "[parameters('certificateBase64Value')]",
          "Thumbprint": "[parameters('certificateThumbprint')]"
        },
    
        "Credential": {
          "Name": "MyCredential",
          "UserName": "[parameters('credentialUsername')]",
          "Password": "[parameters('credentialPassword')]"
        },
    
        "Schedule": {
          "Name": "MySchedule",
          "Description": "Sample schedule",
          "IsEnabled": "true",
          "Interval": "1",
          "Frequency": "hour",
          "StartTime": "[parameters('scheduleStartTime')]",
          "TimeZone": "[parameters('scheduleTimeZone')]",
          "LinkGuid": "[parameters('scheduleLinkGuid')]"
        },
    
        "Variable": {
          "Name": "MyVariable",
          "Description": "Sample variable",
          "Encrypted": 0,
          "Type": "string",
          "Value": "'This is my string value.'"
        },
    
        "Module": {
          "Name": "OMSIngestionAPI",
          "Uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
        }
      },
      "resources": [
        {
          "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
          "location": "[parameters('workspaceRegionId')]",
          "tags": { },
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "[variables('LogAnalyticsApiVersion')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
            "referencedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
            ],
            "containedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]"
            ]
          },
          "plan": {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
            "Version": "[variables('SolutionVersion')]",
            "product": "[variables('ProductName')]",
            "publisher": "[variables('SolutionPublisher')]",
            "promotionCode": ""
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
              "uri": "[variables('Runbook').Uri]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').JobGuid)]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('Runbook').Name]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
          "type": "Microsoft.Automation/automationAccounts/certificates",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "Base64Value": "[variables('Certificate').Base64Value]",
            "Thumbprint": "[variables('Certificate').Thumbprint]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
          "type": "Microsoft.Automation/automationAccounts/credentials",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "userName": "[variables('Credential').UserName]",
            "password": "[variables('Credential').Password]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
          "type": "microsoft.automation/automationAccounts/schedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Schedule').Description]",
            "startTime": "[variables('Schedule').StartTime]",
            "timeZone": "[variables('Schedule').TimeZone]",
            "isEnabled": "[variables('Schedule').IsEnabled]",
            "interval": "[variables('Schedule').Interval]",
            "frequency": "[variables('Schedule').Frequency]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
          "type": "microsoft.automation/automationAccounts/jobSchedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
          ],
          "tags": {
          },
          "properties": {
            "schedule": {
              "name": "[variables('Schedule').Name]"
            },
            "runbook": {
              "name": "[variables('Runbook').Name]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
          "type": "microsoft.automation/automationAccounts/variables",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Variable').Description]",
            "isEncrypted": "[variables('Variable').Encrypted]",
            "type": "[variables('Variable').Type]",
            "value": "[variables('Variable').Value]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
          "type": "Microsoft.Automation/automationAccounts/modules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "properties": {
            "contentLink": {
              "uri": "[variables('Module').Uri]"
            }
          }
        }
    
      ],
      "outputs": { }
    }




## <a name="next-steps"></a>次の手順
* [ビューをソリューションに追加]( solutions-resources-views.md)して、収集したデータを視覚化します。

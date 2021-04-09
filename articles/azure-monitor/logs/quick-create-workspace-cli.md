---
title: Azure CLI を使用して Log Analytics ワークスペースを作成する | Microsoft Docs
description: Azure CLI で Log Analytics ワークスペースを作成して、クラウドおよびオンプレミス環境から管理ソリューションを有効にし、データを収集できるようにする方法を学習します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 8e1fc816e32e563161e1604bdcd7a7006353e4ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047385"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Azure CLI 2.0 を使用して Log Analytics ワークスペースを作成する

Azure CLI 2.0 は、コマンド ラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイック スタートでは、Azure CLI 2.0 を使って、Azure Monitor に Log Analytics ワークスペースをデプロイする方法を示します。 Log Analytics ワークスペースは、Azure Monitor ログ データ用の一意の環境です。 各ワークスペースには、独自のデータ リポジトリと構成があり、データ ソースとソリューションは、特定のワークスペースにデータを格納するように構成されます。 次のソースからデータを収集しようとする場合、Log Analytics ワークスペースが必要です。

* サブスクリプション内の Azure リソース  
* System Center Operations Manager によって監視されているオンプレミスのコンピューター  
* Configuration Manager のデバイス コレクション  
* Azure ストレージからの診断またはログ データ  

環境内の Azure VM、Windows VM、Linux VM などの他のソースについては、次のトピックを参照してください。

* [Azure Virtual Machines に関するデータの収集](../vm/quick-collect-azurevm.md)
* [ハイブリッド Linux コンピューターからのデータの収集](../vm/quick-collect-linux-computer.md)
* [ハイブリッド Windows コンピューターからのデータの収集](../vm/quick-collect-windows-computer.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.30 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-workspace"></a>ワークスペースの作成
[az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) を使用してワークスペースを作成します。 次の例では、ローカル コンピューターから Resource Manager テンプレートを使用して、*eastus* の場所にワークスペースを作成します。 JSON テンプレートは、ワークスペースの名前の入力だけをユーザーに求め、環境の標準構成として使用される可能性のある他のパラメーターには既定値を指定するように構成されています。 または、組織内の共有アクセス用に Azure ストレージ アカウントにテンプレートを格納することができます。 テンプレートを操作する方法の詳細については、「[Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/templates/deploy-cli.md)」を参照してください

サポートされているリージョンについては、[Log Analytics を使用できるリージョン](https://azure.microsoft.com/regions/services/)に関するページを参照し、 **[製品を検索する]** フィールドから Azure Monitor を検索してください。

以下のパラメーターには、既定値が設定されます。

* location - 既定値は米国東部
* sku - 既定値は、2018 年 4 月の価格モデルでリリースされた新しい 1 GB あたりの価格レベル

>[!WARNING]
>新しい 2018 年 4 月の価格モデルを選択したサブスクリプションで Log Analytics ワークスペースを作成または構成する場合、有効な Log Analytics 価格レベルは **PerGB2018** のみです。
>

### <a name="create-and-deploy-template"></a>テンプレートの作成とデプロイ

1. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. 要件に合わせてテンプレートを編集します。 どのプロパティと値がサポートされているかを調べるには、[Microsoft.OperationalInsights/workspaces テンプレート](/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces)のリファレンスを参照してください。
3. このファイルを **deploylaworkspacetemplate.json** としてローカル フォルダーに保存します。   
4. これでこのテンプレートをデプロイする準備が整いました。 テンプレートがあるフォルダーから以下のコマンドを使用します。 ワークスペース名の入力を求められたら、すべての Azure サブスクリプションでグローバルに一意の名前を指定します。

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

デプロイが完了するまでに数分かかる場合があります。 完了すると、次のような結果を含むメッセージが表示されます。

![デプロイ完了時の結果の例](media/quick-create-workspace-cli/template-output-01.png)

## <a name="troubleshooting"></a>トラブルシューティング
過去 14 日間に削除され、[論理的な削除状態](../logs/delete-workspace.md#soft-delete-behavior)になっているワークスペースを作成した場合は、ワークスペースの構成に応じて、操作の結果が異なる可能性があります。
1. 削除されたワークスペースと同じワークスペース名、リソース グループ、サブスクリプション、リージョンを指定した場合は、データ、構成、および接続されたエージェントを含むワークスペースが復旧されます。
2. 同じワークスペース名を使用していて、リソース グループ、サブスクリプション、またはリージョンが異なる場合は、*ワークスペース名 'workspace-name' が一意ではない*、または *競合している* というエラーが表示されます。 ご自分のワークスペースの論理的な削除をオーバーライドし、完全に削除して同じ名前の新しいワークスペースを作成するには、次の手順に従って、最初にワークスペースを回復してから、完全な削除を実行します。
   * ワークスペースを[回復します](../logs/delete-workspace.md#recover-workspace)
   * ワークスペースを[完全に削除](../logs/delete-workspace.md#permanent-workspace-delete)します
   * 同じワークスペース名を使用して新しいワークスペースを作成します

## <a name="next-steps"></a>次のステップ
使用できるワークスペースが用意されたので、管理テレメトリの収集の構成、ログ検索の実行によるデータの分析、管理ソリューションの追加による追加データと分析的な考察の提供を行うことができます。  

* Microsoft Azure Diagnostics または Azure ストレージを使用して Azure リソースからデータを収集できるようにするには、「[Log Analytics で Azure サービスのログとメトリックを使用できるように収集する](../essentials/resource-logs.md#send-to-log-analytics-workspace)」を参照してください。  
* Operations Manager 管理グループに報告するエージェントからデータを収集して Log Analytics ワークスペースに格納するには、[データ ソースとして System Center Operations Manager を追加](../agents/om-agents.md)します。  
* 階層内のコレクションのメンバーであるコンピュータをインポートするには、[構成マネージャー](../logs/collect-sccm.md)に接続します。  
* 使用可能な[監視ソリューション](../insights/solutions.md)と、ソリューションをワークスペースに対して追加または削除する方法を確認します。


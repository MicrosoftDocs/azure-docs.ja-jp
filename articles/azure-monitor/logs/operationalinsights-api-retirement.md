---
title: Azure Monitor API の提供終了
description: 以前のバージョンの OperationalInsights リソース プロバイダー API の提供終了について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/29/2020
ms.openlocfilehash: 7b5d105f6a93102ff1b9142fbdf690c3873d872f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032026"
---
# <a name="operationalinsights-api-version-retirement"></a>OperationalInsights API バージョンの提供終了
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、API の提供終了を少なくともその 12 か月前に通知します。 **OperationalInsights** リソース プロバイダー API の新しいバージョン (2020-08-01) がリリースされたため、2024 年 2 月 29 日に以前の API バージョンは廃止されます。

バージョン 2020-08-01 の使用をすぐに開始することをお勧めします。これにより、[専用クラスター](./logs-dedicated-clusters.md)、[カスタマー マネージド キー](../logs/customer-managed-keys.md)、[プライベート リンク](./private-link-security.md)、[データ エクスポート](./logs-data-export.md)などの新機能のメリットを得ることができます。 また、新しい機能や機能性、および最適化は、現在の API にのみ追加されます。

2024 年 2 月 29 日以降、Azure Monitor では 2020-08-01 より前の API バージョンがサポートされなくなります。 アップグレードしない場合でも、以前のバージョンから送信される要求は、2024 年 2 月 29 日まで引き続き Azure Monitor サービスによって処理されます。

## <a name="migration-steps"></a>移行の手順
使用する構成方法に応じて、**REST** 要求と **Resource Manager テンプレート** で新しいバージョンを更新する必要があります。 API バージョンを更新するには、下の例に従います。

1. REST API 要求では、要求の URL で API バージョンを使用します。 次の例に示すように、そのバージョンを最新バージョン (2020-08-01) に置き換えます。

    ```rest
    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
    ```

2. Azure Resource Manager テンプレートでは、リソースの **apiVersion** プロパティで API バージョンを使用します。 次の例に示すように、そのバージョンを最新バージョン (2020-08-01) に置き換えます。


    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                "description": "Name of the workspace."
                }
            },
            "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('workspaceName')]",
                "apiVersion": "2020-08-01",
                "location": "westus",
                "properties": {
                    "sku": {
                        "name": "pergb2018"
                    },
                    "retentionInDays": 30,
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0,
                        "enableLogAccessUsingOnlyResourcePermissions": true
                    }
                }
            }
        ]
    }
    }
    ```


### <a name="more-information"></a>詳細情報
質問がある場合は、[技術コミュニティの専門家]( https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor)から回答を得ることができます。 サポート プランに加入していて技術的な支援が必要な場合は、[サポート リクエスト]( https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を作成してください。 
1.  *[問題の種類]* で **[技術]** を選択します。 
2.  *[サブスクリプション]* でご使用のサブスクリプションを選択します。 
3.  *[サービス]* で、 **[使用中のサービス]** を選択し、 **[Log Analytics]** を選択します。 
4.  *[Summary]\(要約\)* に、問題の説明を入力します。 
5.  *[問題の種類]* で、[**Log Analytics workspace management]\(Log Analytics ワークスペースの管理\)** を選択します。  
6.  *[Problem subtype]\(問題のサブタイプ\)* で、 **[ARM templates, PowerShell and CLI]\(ARM テンプレート、PowerShell、および CLI\)** を選択します。 

## <a name="next-steps"></a>次のステップ

- [OperationalInsights ワークスペース API についてのリファレンス](/rest/api/loganalytics/workspaces)をご覧ください。
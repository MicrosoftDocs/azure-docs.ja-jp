---
title: Azure CLI のサンプル スクリプト - ロジック アプリを作成する
description: Azure CLI の Logic Apps 拡張機能を使用してロジック アプリを作成するサンプル スクリプト。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc, devx-track-azurecli
ms.date: 07/30/2020
ms.openlocfilehash: a4553ceee482fb232e9ab56deca650be93f9dc6b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218045"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Azure CLI のサンプル スクリプト - ロジック アプリを作成する

このスクリプトでは、[Azure CLI Logic Apps 拡張機能](/cli/azure/ext/logic/logic) (`az logic`) を使用して、サンプルのロジック アプリを作成します。 Azure CLI を使用してロジック アプリを作成および管理するための詳細なガイドについては、[Azure CLI の Logic Apps クイックスタート](quickstart-logic-apps-azure-cli.md)を参照してください。

> [!WARNING]
> Azure CLI Logic Apps 拡張機能は、現在、"*試験段階*" であり、"*カスタマーサポートの対象外*" です。 この CLI 拡張機能は、特に運用環境での使用を選択する場合、慎重に使用してください。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のローカル コンピューターに [Azure CLI](/cli/azure/install-azure-cli) がインストールされていること。
* ご利用のローカル コンピューターに [Azure CLI Logic Apps 拡張機能](/cli/azure/azure-cli-extensions-list) がインストールされていること。 この拡張機能をインストールするには、コマンド `az extension add --name logic` を使用します。
* ロジック アプリの[ワークフロー定義](quickstart-logic-apps-azure-cli.md#workflow-definition)。 この JSON ファイルは、[ワークフロー定義言語スキーマ](logic-apps-workflow-definition-language.md)に従う必要があります。
* お使いのロジック アプリと同じリソース グループ内にある、サポートされている [Logic Apps コネクタ](../connectors/apis-list.md)を経由した電子メール アカウントへの API 接続。 この例では [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md) コネクタを使用しますが、[Outlook.com](../connectors/connectors-create-api-outlook.md) などの他のコネクタを使用することもできます。

### <a name="prerequisite-check"></a>前提条件のチェック

開始する前に、環境を検証します。

* Azure portal にサインインし、`az login` を実行して、ご利用のサブスクリプションがアクティブであることを確認します。

* `az --version` を実行して、ターミナルまたはコマンド ウィンドウの Azure CLI のバージョンを確認します。 最新バージョンについては、[最新のリリース ノート](/cli/azure/release-notes-azure-cli)を参照してください。

  * 最新バージョンを使用していない場合は、[オペレーティング システムまたはプラットフォーム用のインストール ガイド](/cli/azure/install-azure-cli)に従ってインストールを更新します。

### <a name="sample-workflow-explanation"></a>サンプル ワークフローの説明

このサンプル ワークフローの定義ファイルでは、[Azure portal の Logic Apps クイックスタート](quickstart-create-first-logic-app-workflow.md)と同じ基本的なロジック アプリを作成します。 

このサンプル ワークフローでは、次のことを行います。 

1. ロジック アプリのスキーマ (`$schema`) を指定します。

1. トリガーの一覧 (`triggers`) にロジック アプリのトリガーを定義します。 トリガーは、3 時間ごとに繰り返されます (`recurrence`)。 アクションは、指定された RSS フィード (`feedUrl`) に対して新しいフィード項目が発行されたとき (`When_a_feed_item_is_published`) にトリガーされます。

1. アクションの一覧 (`actions`) にロジック アプリのアクションを定義します。 このアクションによって、アクションの入力 (`inputs`) の本文セクション (`body`) で指定されている RSS フィード項目の詳細が Microsoft 365 経由で電子メールで送信されます (`Send_an_email_(V2)`)。

## <a name="sample-workflow-definition"></a>サンプル ワーフクローの定義

サンプル スクリプトを実行する前に、最初にサンプル [ワークフローの定義](#prerequisites)を作成する必要があります。

1. お使いのコンピューターに JSON ファイル `testDefinition.json` を作成します。 

1. 次の内容をその JSON ファイルにコピーします。 
    ```json
    
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {
                "Send_an_email_(V2)": {
                    "inputs": {
                        "body": {
                            "Body": "<p>@{triggerBody()?['publishDate']}<br>\n@{triggerBody()?['title']}<br>\n@{triggerBody()?['primaryLink']}</p>",
                            "Subject": "@triggerBody()?['title']",
                            "To": "test@example.com"
                        },
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['office365']['connectionId']"
                            }
                        },
                        "method": "post",
                        "path": "/v2/Mail"
                    },
                    "runAfter": {},
                    "type": "ApiConnection"
                }
            },
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {
                "$connections": {
                    "defaultValue": {},
                    "type": "Object"
                }
            },
            "triggers": {
                "When_a_feed_item_is_published": {
                    "inputs": {
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['rss']['connectionId']"
                            }
                        },
                        "method": "get",
                        "path": "/OnNewFeed",
                        "queries": {
                            "feedUrl": "https://www.pbs.org/now/rss.xml"
                        }
                    },
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 3
                    },
                    "splitOn": "@triggerBody()?['value']",
                    "type": "ApiConnection"
                }
            }
        },
        "parameters": {
            "$connections": {
                "value": {
                    "office365": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/office365",
                        "connectionName": "office365",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/office365"
                    },
                    "rss": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/rss",
                        "connectionName": "rss",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/rss"
                    }
                }
            }
        }
    }
    
    ```

1. プレースホルダーの値を独自の情報に更新します。

    1. プレースホルダーの電子メール アドレス (`"To": "test@example.com"`) を置き換えます。 Logic Apps コネクタと互換性のある電子メール アドレスを使用する必要があります。 詳細については、[前提条件](#prerequisites)を参照してください。

    1. Office 365 Outlook コネクタとは異なる電子メール コネクタを使用している場合は、追加のコネクタの詳細情報を置き換えます。

    1. connections パラメーター (`$connections`) の下にある接続識別子 (`connectionId` と `id`) のプレースホルダー サブスクリプション値 (`00000000-0000-0000-0000-000000000000`) を独自のサブスクリプション値に置き換えます。

1. 変更を保存します。

## <a name="sample-script"></a>サンプル スクリプト

> [!NOTE]
> このサンプルは、`bash` シェル用に記述されています。 このサンプルを別のシェル (Windows PowerShell、コマンド プロンプトなど) で実行する場合は、スクリプトに変更を加える必要がある場合があります。

このサンプル スクリプトを実行する前に、このコマンドを実行して Azure に接続します。

```azurecli-interactive

az login

```

次に、ワークフロー定義を作成したディレクトリに移動します。 たとえば、ワークフロー定義の JSON ファイルをデスクトップに作成した場合は、次のようになります。

```azurecli

cd ~/Desktop

```

次に、このスクリプトを実行してロジック アプリを作成します。 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>デプロイのクリーンアップ

サンプル スクリプトの使用が完了したら、次のコマンドを実行して、リソース グループと、そのすべての入れ子になったリソース (ロジック アプリを含む) を削除します。

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>スクリプトの説明

このサンプル スクリプトでは、次のコマンドを使用して新しいリソース グループとロジック アプリを作成します。

| command | メモ |
| ------- | ----- |
| [`az group create`](/cli/azure/group#az-group-create) | ロジック アプリのリソースを格納するリソース グループを作成します。 |
| [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) | パラメーター `--definition` で定義されているワークフローに基づいてロジック アプリを作成します。 |
| [`az group delete`](/cli/azure/vm/extension) | リソース グループと、そのすべての入れ子になったリソースを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure/)のページを参照してください。

その他の Logic Apps CLI スクリプトのサンプルについては、[Microsoft のコード サンプル ブラウザー](/samples/browse/?products=azure-logic-apps)を参照してください。

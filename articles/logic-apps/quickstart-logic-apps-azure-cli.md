---
title: Azure CLI を使用してロジック アプリを作成し、管理する
description: Azure CLI を使用してロジック アプリを作成し、list、show (get)、update、delete などの操作を使用して管理することができます。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli, contperf-fy21q2
ms.date: 11/23/2020
ms.openlocfilehash: bc172fd1702addf8f4e34094452a779b09320a4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033376"
---
# <a name="quickstart-create-and-manage-logic-apps-using-the-azure-cli"></a>クイック スタート:Azure CLI を使用してロジック アプリを作成し、管理する

このクイックスタートでは、[Azure CLI Logic Apps 拡張機能](/cli/azure/ext/logic/logic) (`az logic`) を使用してロジック アプリを作成し、管理する方法について説明します。 ロジック アプリ ワークフロー定義用の JSON ファイルを使用して、コマンド ラインからロジック アプリを作成することができます。 その後、コマンド ラインから、`list`、`show` (`get`)、`update`、`delete` などの操作を実行してロジック アプリを管理できます。

> [!WARNING]
> Azure CLI Logic Apps 拡張機能は、現在、"*試験段階*" であり、"*カスタマーサポートの対象外*" です。 この CLI 拡張機能は、特に運用環境での使用を選択する場合、慎重に使用してください。

Logic Apps を初めて使用する場合は、[Azure portal](quickstart-create-first-logic-app-workflow.md)、[Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)、および [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md) を使用して初めてのロジック アプリを作成する方法も学習できます。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のローカル コンピューターに [Azure CLI](/cli/azure/install-azure-cli) がインストールされていること。
* ご利用のローカル コンピューターに [Azure CLI Logic Apps 拡張機能](/cli/azure/azure-cli-extensions-list) がインストールされていること。 この拡張機能をインストールするには、コマンド `az extension add --name logic` を使用します。
* ロジック アプリを作成する [Azure リソース グループ](#example---create-resource-group)。

### <a name="prerequisite-check"></a>前提条件のチェック

開始する前に、環境を検証します。

* Azure portal にサインインし、`az login` を実行して、ご利用のサブスクリプションがアクティブであることを確認します。
* `az --version` を実行して、ターミナルまたはコマンド ウィンドウの Azure CLI のバージョンを確認します。 最新バージョンについては、[最新のリリース ノート](/cli/azure/release-notes-azure-cli?tabs=azure-cli)を参照してください。
  * 最新バージョンを使用していない場合は、[オペレーティング システムまたはプラットフォーム用のインストール ガイド](/cli/azure/install-azure-cli)に従ってインストールを更新します。

### <a name="example---create-resource-group"></a>例 - リソース グループの作成

ロジック アプリのリソース グループがまだない場合は、コマンド `az group create` を実行してグループを作成します。 たとえば、次のコマンドを実行すると、場所 `westus` に `testResourceGroup` という名前のリソース グループが作成されます。

```azurecli-interactive

az group create --name testResourceGroup --location westus

```

リソース グループが正常に作成された場合、出力には、`provisioningState` が `Succeeded` として示されます。

```output

<...>
  "name": "testResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
<...>

```

## <a name="workflow-definition"></a>ワークフロー定義

Azure CLI を使用して、[新しいロジック アプリを作成する](#create-logic-apps-from-cli)か、または[既存のロジック アプリを更新する](#update-logic-apps-from-cli)前に、ロジック アプリのワークフロー定義が必要です。 Azure portal で、**デザイナー** ビューから **コード ビュー** に切り替えると、ロジック アプリの基になるワークフロー定義を JSON 形式で表示できます。

コマンドを実行してロジック アプリを作成または更新する場合、ワークフロー定義は必須パラメーター (`--definition`) としてアップロードされます。 ワークフロー定義は、[ワークフロー定義言語のスキーマ](./logic-apps-workflow-definition-language.md)に従う JSON ファイルとして作成する必要があります。

## <a name="create-logic-apps-from-cli"></a>CLI からロジック アプリを作成する

コマンド [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) と定義の JSON ファイルを使用して、Azure CLI からロジック アプリのワークフローを作成することができます。

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

コマンドには、次の[必須パラメーター](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters)を含める必要があります。

| パラメーター | 値 | 説明 |
| --------- | ----- | ----------- |
| ワークフロー定義 | `--definition` | ロジック アプリの[ワークフロー定義](#workflow-definition)を含む JSON ファイル。 |
| 場所 | `--location -l` | ロジック アプリを配置する Azure リージョン。 |
| 名前 | `--name -n` | ロジック アプリの名前。 名前に含めることができるのは、文字、数字、ハイフン (`-`)、アンダースコア (`_`)、かっこ (`()`)、ピリオド (`.`) のみです。 また、名前はリージョン全体で一意である必要があります。 |
| リソース グループ名 | `--resource-group -g` | ロジック アプリを作成する [Azure リソース グループ](../azure-resource-manager/management/overview.md)。 ロジック アプリのリソース グループがまだない場合は、開始する前に、[リソース グループを作成](#example---create-resource-group)します。 |

さらに、[省略可能なパラメーター](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters)を追加して、ロジック アプリのアクセス制御、エンドポイント、統合アカウント、統合サービス環境、状態、リソース タグを構成することもできます。

### <a name="example---create-logic-app"></a>例 - ロジック アプリの作成

この例では、`testLogicApp` という名前のワークフローを場所 `westus` にあるリソース グループ `testResourceGroup` に作成します。 JSON ファイル `testDefinition.json` には、ワークフロー定義が含まれています。

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

ワークフローが正常に作成されると、CLI によって、新しいワークフロー定義の JSON コードが表示されます。 ワークフローの作成に失敗した場合は、[発生する可能性のあるエラーの一覧](#errors)を参照してください。

## <a name="update-logic-apps-from-cli"></a>CLI からロジック アプリを更新する

コマンド [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) を使用して、Azure CLI からロジック アプリのワークフローを更新することもできます。

コマンドには、[ロジック アプリを作成する](#create-logic-apps-from-cli)場合と同じ[必須パラメーター](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters)を含める必要があります。 また、ロジック アプリを作成する場合と同じ[省略可能なパラメーター](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters)を追加することもできます。

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

### <a name="example---update-logic-app"></a>例 - ロジック アプリの更新

この例では、[前のセクションで作成したサンプル ワークフロー](#example---create-logic-app)を更新して、別の JSON 定義ファイル `newTestDefinition.json` を使用し、説明の値を含む 2 つのリソース タグ `testTag1` と `testTag2` を追加します。

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "newTestDefinition.json" --tags "testTag1=testTagValue1" "testTag2=testTagValue"

```

ワークフローが正常に更新されると、CLI によって、ロジック アプリの更新されたワークフロー定義が表示されます。 更新が失敗した場合は、[発生する可能性のあるエラーの一覧](#errors)を参照してください。

## <a name="delete-logic-apps-from-cli"></a>CLI からロジック アプリを削除する

コマンド [`az logic workflow delete`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete) を使用して、Azure CLI からロジック アプリのワークフローを削除することができます。

コマンドには、次の[必須パラメーター](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-required-parameters)を含める必要があります。

| パラメーター | 値 | 説明 |
| --------- | ----- | ----------- |
| 名前 | `--name -n` | ロジック アプリの名前。 |
| リソース グループ名 | `-resource-group -g` | ロジック アプリを配置するリソース グループ。 |

[省略可能なパラメーター](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-optional-parameters)を含めて、確認プロンプト `--yes -y` をスキップすることもできます。

```azurecli

az logic workflow delete --name
                         --resource-group
                         [--yes]

```

この後、CLI によって、ロジック アプリの削除を確認するように求められます。 コマンドで省略可能なパラメーター `--yes -y` を使用すると、確認プロンプトをスキップできます。

```azurecli

Are you sure you want to perform this operation? (y/n):

```

ロジック アプリの定義を確認するには、[CLI でロジック アプリの一覧を表示する](#list-logic-apps-in-cli)か、または Azure portal でロジック アプリを表示します。

### <a name="example---delete-logic-app"></a>例 - ロジック アプリの削除

この例では、[前のセクションで作成したサンプル ワークフロー](#example---create-logic-app)を削除します。

```azurecli-interactive

az logic workflow delete --resource-group "testResourceGroup" --name "testLogicApp"

```

確認プロンプトに `y` で応答すると、ロジック アプリが削除されます。

## <a name="show-logic-apps-in-cli"></a>CLI でロジック アプリを表示する

コマンド [`az logic workflow show`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show) を使用して、特定のロジック アプリ ワークフローを取得することができます。

```azurecli

az logic workflow show --name
                       --resource-group

```

コマンドには、次の[必須パラメーター](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show-required-parameters)を含める必要があります

| パラメーター | 値 | 説明 |
| --------- | ----- | ----------- |
| 名前 | `--name -n` | ロジック アプリの名前。 |
| リソース グループ名 | `--resource-group -g` | ロジック アプリを配置するリソース グループの名前。 |

### <a name="example---get-logic-app"></a>例 - ロジック アプリの取得

この例では、デバッグ用に、リソース グループ `testResourceGroup` 内のロジック アプリ `testLogicApp` が、完全なログと共に返されます。

```azurecli-interactive

az logic workflow show --resource-group "testResourceGroup" --name "testLogicApp" --debug

```

## <a name="list-logic-apps-in-cli"></a>CLI でロジック アプリを一覧表示する

コマンド [`az logic workflow list`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list) を使用して、ロジック アプリをサブスクリプション別に一覧表示できます。 このコマンドでは、ロジック アプリのワークフローの JSON コードが返されます。

次の[省略可能なパラメーター](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list-optional-parameters)を使用して、結果をフィルター処理することができます。

| パラメーター | 値 | 説明 |
| --------- | ----- | ----------- |
| リソース グループ名 | `--resource-group -g` | 結果をフィルター処理するリソース グループの名前。 |
| 項目数 | `--top` | 結果に含める項目の数。 |
| Assert | `--filter` | 一覧で使用するフィルターの種類。 状態 (`State`)、トリガー (`Trigger`)、参照先リソースの識別子 (`ReferencedResourceId`) でフィルター処理できます。 |

```azurecli

az logic workflow list [--filter]
                       [--resource-group]
                       [--top]

```

### <a name="example---list-logic-apps"></a>例 - ロジック アプリの一覧表示

この例では、リソース グループ `testResourceGroup` 内の有効化されたすべてのワークフローが ASCII テーブル形式で返されます。

```azurecli-interactive

az logic workflow list --resource-group "testResourceGroup" --filter "(State eq 'Enabled')" --output "table"

```

## <a name="errors"></a>エラー

次のエラーは、Azure Logic Apps CLI 拡張機能がインストールされていないことを示します。 前提条件の手順に従って、ご利用のコンピューターに [Logic Apps 拡張機能をインストール](#prerequisites)します。

```output

az: 'logic' is not in the 'az' command group. See 'az --help'. If the command is from an extension, please make sure the corresponding extension is installed. To learn more about extensions, please visit https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview

```

次のエラーは、ワークフロー定義をアップロードするためのファイル パスが正しくないことを示している可能性があります。

```output

Expecting value: line 1 column 1 (char 0)

```

## <a name="global-parameters"></a>グローバル パラメーター

`az logic` コマンドでは、次の省略可能なグローバル Azure CLI パラメーターを使用できます。

| パラメーター | 値 | 説明 |
| --------- | ----- | ----------- |
| 出力形式 | `--output -o` | [出力形式](/cli/azure/format-output-azure-cli)を既定の JSON から変更します。 |
| エラーのみを表示 | `--only-show-errors` | 警告を表示せず、エラーのみを表示します。 |
| "詳細" | `--verbose` | 詳細ログを表示します。 |
| デバッグ | `--debug` | すべてのデバッグ ログを表示します。 |
| ヘルプ メッセージ | `--help -h` | ヘルプ ダイアログを表示します。 |
| クエリ | `--query` | JSON 形式の出力の JMESPath クエリ文字列を設定します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure/)のページを参照してください。

その他の Logic Apps CLI スクリプトのサンプルについては、[Microsoft のコード サンプル ブラウザー](/samples/browse/?products=azure-logic-apps)を参照してください。

次に、サンプル スクリプトとワークフロー定義を使用して、Azure CLI からサンプル アプリ ロジックを作成できます。

> [!div class="nextstepaction"]
> [サンプル スクリプトを使用してロジック アプリを作成する](sample-logic-apps-cli-script.md)。

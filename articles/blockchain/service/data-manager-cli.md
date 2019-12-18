---
title: Azure CLI を使用してブロックチェーン データ マネージャーを構成する - Azure Blockchain Service
description: Azure CLI を使用して、Azure Blockchain Service 用のブロックチェーン データ マネージャーを作成および管理します。
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: a8061aad6d6a1513de70e7c2bc57aa109c666611
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455930"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Azure CLI を使用してブロックチェーン データ マネージャーを構成する

ブロックチェーン データをキャプチャして Azure Event Grid トピックに送信するように、Azure Blockchain Service のブロックチェーン データ マネージャーを構成します。

ブロックチェーン データ マネージャー インスタンスを構成するには、次のようにします。

* ブロックチェーン マネージャー インスタンスを作成する
* Azure Blockchain Service のトランザクション ノードへの入力を作成する
* Azure Event Grid トピックへの出力を作成する
* ブロックチェーン アプリケーションを追加する
* インスタンスを開始する

## <a name="prerequisites"></a>前提条件

* 最新の [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) をインストールし、`az login` を使用してサインインします。
* 「[Quickstart: Visual Studio Code を使用して Azure Blockchain Service コンソーシアム ネットワークに接続する](connect-vscode.md)」が完了していること
* [Event Grid トピック](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)を作成する
* [Azure Event Grid のイベント ハンドラー](../../event-grid/event-handlers.md)について学習する

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

CLI をローカルにインストールして使用する場合、このクイックスタートでは、Azure CLI バージョン 2.0.51 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](https://docs.microsoft.com/cli/azure/group) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>インスタンスを作成する

ブロックチェーン データ マネージャー インスタンスは、Azure Blockchain Service トランザクション ノードを監視します。 インスタンスは、トランザクション ノードから生のブロックと生のトランザクション データをすべてキャプチャします。

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| パラメーター | 説明 |
|-----------|-------------|
| resource-group | ブロックチェーン データ マネージャー インスタンスの作成先のリソース グループ名。 |
| name | ブロックチェーン データ マネージャー インスタンスの名前。 |
| resource-type | ブロックチェーン データ マネージャー インスタンスのリソースの種類は **Microsoft.blockchain/watchers** です。 |
| is-full-object | プロパティにウォッチャー リソース用のオプションが含まれることを示します。 |
| properties | ウォッチャー リソースのプロパティを含む JSON 形式の文字列。 文字列またはファイルとして渡すことができます。  |

### <a name="create-instance-examples"></a>インスタンス作成の例

**米国東部**リージョンにブロックチェーン マネージャー インスタンスを作成する JSON 構成の例です。

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| 要素 | 説明 |
|---------|-------------|
| location | ウォッチャー リソースの作成先のリージョン |
| properties | ウォッチャー リソースの作成時に設定するプロパティ |

構成用の JSON 文字列を使用して、*mywatcher* という名前のブロックチェーン データ マネージャー インスタンスを作成します。

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

JSON 構成ファイルを使用して、*mywatcher* という名前のブロックチェーン データ マネージャー インスタンスを作成します。

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>入力を作成する

入力により、ブロックチェーン データ マネージャーを Azure Blockchain Service トランザクション ノードに接続します。 トランザクション ノードにアクセスできるユーザーのみが接続を作成できます。

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| パラメーター | 説明 |
|-----------|-------------|
| resource-group | 入力リソースの作成先となるリソース グループの名前。 |
| name | 入力の名前。 |
| namespace | プロバイダー名前空間として **Microsoft.Blockchain** を使用します。 |
| resource-type | ブロックチェーン データ マネージャーの入力リソースの種類は **inputs** です。 |
| parent | 入力の関連付け先となるウォッチャーへのパス。 たとえば、**watchers/mywatcher** となります。 |
| is-full-object | プロパティに入力リソース用のオプションが含まれることを示します。 |
| properties | 入力リソースのプロパティを含む JSON 形式の文字列。 文字列またはファイルとして渡すことができます。 |

### <a name="input-examples"></a>入力の例

\<ブロックチェーン メンバー\>に接続されている*米国東部*リージョン内に入力リソースを作成する構成 JSON の例。

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| 要素 | 説明 |
|---------|-------------|
| location | 入力リソースの作成先のリージョン。 |
| inputType | Azure Blockchain Service のメンバーの台帳の種類。 現時点では、**Ethereum** がサポートされています。 |
| resourceId | 入力接続先のトランザクション ノード。 \<Subscription ID\>、\<Resource group\>、および \<Blockchain member\> を、トランザクション ノード リソースの値で置き換えます。 入力は、Azure Blockchain Service メンバーの既定のトランザクション ノードに接続します。 |

構成用の JSON 文字列を使用して、*mywatcher* 用に *myInput* という名前の入力を作成します。

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

JSON 構成ファイルを使用して、*mywatcher* 用に *myInput* という名前の入力を作成します。

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>出力を作成する

送信接続により、ブロックチェーン データを Azure Event Grid に送信します。 1 つの宛先にブロックチェーン データを送信することも、複数の宛先にブロックチェーン データを送信することもできます。 ブロックチェーン データ マネージャーは、特定のどのブロックチェーン データ マネージャー インスタンスに対しても、複数の Event Grid トピック送信接続をサポートしています。

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| パラメーター | 説明 |
|-----------|-------------|
| resource-group | 出力リソースの作成先となるリソース グループの名前。 |
| name | 出力の名前。 |
| namespace | プロバイダー名前空間として **Microsoft.Blockchain** を使用します。 |
| resource-type | ブロックチェーン データ マネージャーの出力リソースの種類は **outputs** です。 |
| parent | 出力の関連付け先となるウォッチャーへのパス。 たとえば、**watchers/mywatcher** となります。 |
| is-full-object | プロパティに出力リソース用のオプションが含まれることを示します。 |
| properties | 出力リソースのプロパティを含む JSON 形式の文字列。 文字列またはファイルとして渡すことができます。 |

### <a name="output-examples"></a>出力の例

\<event grid topic\> という名前のイベント グリッド トピックに接続される出力リソースを*米国東部*リージョン内に作成する構成 JSON の例。

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| 要素 | 説明 |
|---------|-------------|
| location | 出力リソースの作成先のリージョン。 |
| outputType | 出力の種類。 現時点では **EventGrid** がサポートされています。 |
| resourceId | 出力の接続先となるリソース。 \<Subscription ID\>、\<Resource group\>、および \<Blockchain member\> を、イベント グリッド リソースの値で置き換えます。 |

JSON 構成文字列を使用して、イベント グリッド トピックに接続する *mywatcher* 用に、*myoutput* という名前の出力を作成します。

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

JSON 構成ファイルを使用して、イベント グリッド トピックに接続する *mywatcher* 用に、*myoutput* という名前の出力を作成します。

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>ブロックチェーン アプリケーションを追加する

ブロックチェーン アプリケーションを追加すると、ブロックチェーン データ マネージャーがアプリケーションのイベントとプロパティの状態をデコードします。 それ以外の場合は、生のブロックと生のトランザクション データのみが送信されます。 ブロックチェーン データ マネージャーは、コントラクトがデプロイされているときにはコントラクト アドレスも検出します。 1 つのブロックチェーン データ マネージャー インスタンスには、複数のブロックチェーン アプリケーションを追加できます。


> [!IMPORTANT]
> 現在、Solidity の[配列型](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays)または [mapping 型](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types)を宣言するブロックチェーン アプリケーションは、完全にはサポートされません。 配列型または mapping 型として宣言されたプロパティは、*ContractPropertiesMsg* メッセージや *DecodedContractEventsMsg* メッセージではデコードされません。

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| パラメーター | 説明 |
|-----------|-------------|
| resource-group | アプリケーション リソースの作成先となるリソース グループの名前。 |
| name | アプリケーションの名前。 |
| namespace | プロバイダー名前空間として **Microsoft.Blockchain** を使用します。 |
| resource-type | ブロックチェーン データ マネージャーのアプリケーション リソースの種類は **artifacts** です。 |
| parent | アプリケーションの関連付け先となるウォッチャーへのパス。 たとえば、**watchers/mywatcher** となります。 |
| is-full-object | プロパティにアプリケーション リソース用のオプションが含まれることを示します。 |
| properties | アプリケーション リソースのプロパティを含む JSON 形式の文字列。 文字列またはファイルとして渡すことができます。 |

### <a name="blockchain-application-examples"></a>ブロックチェーン アプリケーションの例

コントラクトの ABI とバイトコードによって定義されたスマート コントラクトを監視するアプリケーション リソースを*米国東部*リージョン内に作成する構成 JSON の例。

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| 要素 | 説明 |
|---------|-------------|
| location | アプリケーション リソースの作成先となるリージョン。 |
| artifactType | アプリケーションの種類。 現時点では、**EthereumSmartContract** がサポートされています。 |
| abiFileUrl | スマート コントラクトの ABI JSON ファイルの URL。 コントラクト ABI の取得と URL の作成の詳細については、[コントラクト ABI とバイトコードの取得](data-manager-portal.md#get-contract-abi-and-bytecode)に関するページと、[コントラクト ABI とバイトコード URL の作成](data-manager-portal.md#create-contract-abi-and-bytecode-url)に関するページを参照してください。 |
| bytecodeFileUrl | スマート コントラクトのデプロイ済みバイトコード JSON ファイルの URL。 スマート コントラクトのデプロイ済みバイトコードの取得と URL の作成の詳細については、[コントラクト ABI とバイトコードの取得](data-manager-portal.md#get-contract-abi-and-bytecode)に関するページと、[コントラクト ABI とバイトコード URL の作成](data-manager-portal.md#create-contract-abi-and-bytecode-url)に関するページを参照してください。 注:ブロックチェーン データ マネージャーには、**デプロイ済みバイトコード**が必要です。 |
| queryTargetTypes | パブリッシュされるメッセージの種類。 **ContractProperties** を指定すると、メッセージの種類 *ContractPropertiesMsg* が公開されます。 **ContractEvents** を指定すると、メッセージの種類 *DecodedContractEventsMsg* が公開されます。 注:メッセージの種類 *RawBlockAndTransactionMsg* および *RawTransactionContractCreationMsg* は常に公開されます。 |

JSON 文字列によって定義されたスマート コントラクトを監視する *myApplication* という名前のアプリケーションを、*mywatcher* 用に作成します。

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

JSON 構成ファイルを使用して定義されたスマート コントラクトを監視する *myApplication* という名前のアプリケーションを、*mywatcher* 用に作成します。

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>インスタンスを開始する

ブロックチェーン マネージャー インスタンスは、実行時に、定義されている入力からのブロックチェーン イベントを監視し、定義されている出力にデータを送信します。

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| パラメーター | 説明 |
|-----------|-------------|
| action | **start** を使用してウォッチャーを実行します。 |
| ids | ウォッチャーのリソース ID。 \<Subscription ID\>、\<Resource group\>、および \<Watcher name\> を、ウォッチャー リソースの値で置き換えます。|

### <a name="start-instance-example"></a>インスタンス開始の例

*mywatcher* という名前のブロックチェーン データ マネージャー インスタンスを開始します。

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>インスタンスを停止する

ブロックチェーン データ マネージャー インスタンスを停止します。

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| パラメーター | 説明 |
|-----------|-------------|
| action | **stop** を使用してウォッチャーを停止します。 |
| ids | ウォッチャーの名前。 \<Subscription ID\>、\<Resource group\>、および \<Watcher name\> を、ウォッチャー リソースの値で置き換えます。 |

### <a name="stop-watcher-example"></a>ウォッチャー停止の例

*mywatcher* という名前のインスタンスを停止します。

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>インスタンスを削除する

ブロックチェーン データ マネージャー インスタンスを削除します。

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| パラメーター | 説明 |
|-----------|-------------|
| resource-group | 削除するウォッチャーのリソースグループの名前。 |
| name | 削除するウォッチャーの名前。 |
| resource-type | ブロックチェーン データ マネージャーのウォッチャー リソースの種類は **Microsoft.blockchain/watchers** です。 |

### <a name="delete-instance-example"></a>インスタンス削除の例

*myRG* リソース グループ内の *mywatcher* という名前のインスタンスを削除します。

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>次の手順

次のチュートリアルで、ブロックチェーン データ マネージャーと Azure Cosmos DB を使用してブロックチェーン トランザクション メッセージ エクスプローラーの作成を試みます。

> [!div class="nextstepaction"]
> [Blockchain Data Manager を使用して Azure Cosmos DB にデータを送信する](data-manager-cosmosdb.md)

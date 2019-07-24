---
title: Azure CLI を使用したイベント ハブの作成 - Azure Event Hubs | Microsoft Docs
description: このクイック スタートでは、Azure CLI を使用してイベント ハブを作成したうえで、Java を使用してイベントの送受信を行う方法について説明します。
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: java
ms.topic: quickstart
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: a26084480f8ca24f2b66375e863dd2231fff6b00
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103508"
---
# <a name="quickstart-create-an-event-hub-using-azure-cli"></a>クイック スタート: Azure CLI を使用したイベント ハブの作成

Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 Event Hubs の詳しい概要については、[Event Hubs の概要](event-hubs-about.md)と [Event Hubs の機能](event-hubs-features.md)に関するページをご覧ください。

このクイック スタートでは、Azure CLI を使用してイベント ハブを作成します。

## <a name="prerequisites"></a>前提条件
このクイック スタートを完了するには、Azure サブスクリプションが必要です。 お持ちでない場合は、開始する前に[無料アカウントを作成][]してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行していることが要件です。 バージョンを確認するには `az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Cloud Shell でコマンドを実行している場合、次の手順は必要ありません。 ローカルで CLI を実行している場合、次の手順を実行して Azure にサインインし、現在のサブスクリプションを設定します。

次のコマンドを実行して、Azure にサインインします。

```azurecli-interactive
az login
```

現在のサブスクリプションのコンテキストを設定します。 `MyAzureSub` は、使用する Azure サブスクリプションの名前に置き換えてください。

```azurecli-interactive
az account set --subscription MyAzureSub
``` 

## <a name="create-a-resource-group"></a>リソース グループの作成
リソース グループは、Azure リソースの論理的なコレクションです。 すべてのリソースのデプロイと管理はリソース グループで行われます。 次のコマンドを実行して、リソース グループを作成します。

```azurecli-interactive
# Create a resource group. Specify a name for the resource group.
az group create --name <resource group name> --location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します
Event Hubs 名前空間は一意のスコープ コンテナーを提供します。このコンテナーは、1 つ以上のイベント ハブを作成する完全修飾ドメイン名によって参照されます。 リソース グループに名前空間を作成するには、次のコマンドを実行します。

```azurecli-interactive
# Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
```

## <a name="create-an-event-hub"></a>イベント ハブの作成
次のコマンドを実行して、イベント ハブを作成します。

```azurecli-interactive
# Create an event hub. Specify a name for the event hub. 
az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
```

お疲れさまでした。 Azure CLI を使用して Event Hubs 名前空間を作成し、その名前空間内にイベント ハブを作成しました。 

## <a name="next-steps"></a>次の手順

この記事では、リソース グループ、Event Hubs 名前空間、イベント ハブを作成しました。 イベント ハブに対してイベントを送信または受信するためのステップ バイ ステップの手順については、次のチュートリアルをご覧ください。  

- **イベントをイベント ハブに送信する**: [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)、[Java](event-hubs-java-get-started-send.md)、[Python](event-hubs-python-get-started-send.md)、[Node.js](event-hubs-node-get-started-send.md)、[Go](event-hubs-go-get-started-send.md)、[C](event-hubs-c-getstarted-send.md)
- **イベント ハブからイベントを受信する**: [.NET Core](event-hubs-dotnet-standard-getstarted-receive-eph.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md)、[Java](event-hubs-java-get-started-receive-eph.md)、[Python](event-hubs-python-get-started-receive.md)、[Node.js](event-hubs-node-get-started-receive.md)、[Go](event-hubs-go-get-started-receive-eph.md)、[Apache Storm](event-hubs-storm-getstarted-receive.md)

[無料アカウントを作成]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name

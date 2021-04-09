---
title: Azure Maps と統合する
titleSuffix: Azure Digital Twins
description: Azure Maps フロア ガイドを更新するために、ツイン グラフと Azure Digital Twins の通知を使用できる関数を Azure Functions で作成する方法を確認します。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 990a0ee73bd91ccb748c948b5fcf0e6124d84a03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102201432"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>Azure Digital Twins を使用して Azure Maps の屋内マップを更新する

この記事では、Azure Digital Twins のデータを使用し、[Azure Maps](../azure-maps/about-azure-maps.md) を使って "*屋内マップ*" に表示される情報を更新するために必要な手順について説明します。 Azure Digital Twins によって、IoT デバイスの関係のグラフが格納され、テレメトリがさまざまなエンドポイントにルーティングされます。これにより、マップ上の情報オーバーレイを更新するための完全なサービスとなります。

この攻略ガイドでは、以下について説明します。

1. [Azure Functions](../azure-functions/functions-overview.md) の関数にツイン更新イベントを送信するように、Azure Digital Twins インスタンスを構成する。
2. Azure Maps 屋内マップの地物状態セットを更新するために関数を作成する。
3. マップ ID と地物状態セット ID を Azure Digital Twins のグラフに格納する方法。

### <a name="prerequisites"></a>前提条件

* Azure Digital Twins の [*チュートリアル: エンド ツー エンドのソリューションの接続*](./tutorial-end-to-end.md)に関するページを参照してください。
    * 追加のエンドポイントとルートを使用して、このツインを拡張します。 また、このチュートリアルでは、関数アプリに別の関数を追加します。 
* Azure Maps の [*チュートリアル: Azure Maps Creator を使用する屋内マップの作成*](../azure-maps/tutorial-creator-indoor-maps.md)方法に従って、"*地物状態セット*" を含む Azure Maps の屋内マップを作成します。
    * [地物状態セット](../azure-maps/creator-indoor-maps.md#feature-statesets)は、部屋や機器といったデータセットの地物に割り当てられた動的なプロパティ (状態) のコレクションです。 上記の Azure Maps チュートリアルでは、地物状態セットによって、マップに表示される部屋の状態が格納されます。
    * 地物の "*状態セット ID*" と Azure Maps の "*サブスクリプション キー*" が必要になります。

### <a name="topology"></a>トポロジ

次の図は、このチュートリアルの屋内マップの統合要素が、より大きなエンドツーエンドの Azure Digital Twins シナリオに該当する場所を示しています。

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="屋内マップの統合部分が強調表示されている、エンドツーエンドのシナリオにおける Azure サービスのビュー" lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>ツインの更新時にマップを更新する関数を作成する

まず、すべてのツイン更新イベントをイベント グリッド トピックに転送するために、Azure Digital Twins でルートを作成します。 その後、関数を使用して、これらの更新メッセージを読み取り、Azure Maps で地物状態セットを更新します。 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>ルートを作成してツイン更新通知にフィルターを適用する

ツインの状態が更新されるたびに、Azure Digital Twins インスタンスでツイン更新イベントを生成できます。 Azure Digital Twins の [*チュートリアル:エンドツーエンドのソリューションの接続*](./tutorial-end-to-end.md)に関するページ (上記のリンク) では、温度計を使用して、部屋のツインに付属する温度属性を更新するシナリオについて説明しています。 ツインの更新通知をサブスクライブし、その情報を使用してマップを更新することで、このソリューションを拡張します。

このパターンでは、IoT デバイスではなく、部屋のツインから直接読み取りを行います。これにより、マッピング ロジックを更新しなくても、基になるデータ ソースを温度に合わせて柔軟に変更することができます。 たとえば、複数の温度計を追加したり、別の部屋と温度計を共有するようにこの部屋を設定したりすることができます。これらはすべて、マップ ロジックを更新せずに行うことができます。

1. Azure Digital Twins インスタンスからイベントを受信する、イベント グリッド トピックを作成します。
    ```azurecli-interactive
    az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
    ```

2. イベント グリッド トピックを Azure Digital Twins にリンクするエンドポイントを作成します。
    ```azurecli-interactive
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
    ```

3. Azure Digital Twins でルートを作成し、ツイン更新イベントをエンドポイントに送信します。

    >[!NOTE]
    >Cloud Shell には現在、`az dt route`、`az dt model`、`az dt twin` の各コマンド グループに影響する **既知の問題** があります。
    >
    >解決するには、コマンドを実行する前に Cloud Shell で `az login` を実行するか、Cloud Shell ではなく[ローカル CLI](/cli/azure/install-azure-cli) を使用します。 この件の詳細については、「[*トラブルシューティング: Azure Digital Twins の既知の問題*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)」を参照してください。

    ```azurecli-interactive
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-a-function-to-update-maps"></a>マップを更新する関数を作成する

エンドツーエンドのチュートリアルで、**Event Grid によってトリガーされる関数** を関数アプリ内に作成します ([*チュートリアル: エンド ツー エンドのソリューションの接続*](./tutorial-end-to-end.md)に関するページを参照)。 この関数によって、これらの通知がアンパックされ、1 つの部屋の温度を更新するために Azure Maps の地物状態セットに更新が送信されます。

参照情報については、次のドキュメントを参照してください: [*Azure Functions の Azure Event Grid トリガー*](../azure-functions/functions-bindings-event-grid-trigger.md)。

関数コードを次のコードに置き換えます。 これにより、空間のツインに対する更新のみが除外され、更新された温度が読み取られ、その情報が Azure Maps に送信されるようになります。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateMaps.cs":::

関数アプリで 2 つの環境変数を設定する必要があります。 1 つは [Azure Maps のプライマリ サブスクリプション キー](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account)で、もう 1 つは [Azure Maps の状態セット ID](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset) です。

```azurecli-interactive
az functionapp config appsettings set --name <your-App-Service-(function-app)-name> --resource-group <your-resource-group> --settings "subscription-key=<your-Azure-Maps-primary-subscription-key>"
az functionapp config appsettings set --name <your-App-Service-(function-app)-name>  --resource-group <your-resource-group> --settings "statesetID=<your-Azure-Maps-stateset-ID>"
```

### <a name="view-live-updates-on-your-map"></a>マップでライブ更新を表示する

ライブ更新温度を表示するには、以下の手順に従います。

1. **DeviceSimulator** プロジェクトを実行し、シミュレートされた IoT データの送信を Azure Digital Twins から開始します ([*チュートリアル:エンド ツー エンドのソリューションの接続*](tutorial-end-to-end.md)に関するページを参照)。 この手順は、「[*シミュレーションを構成して実行する*](././tutorial-end-to-end.md#configure-and-run-the-simulation)」セクションにあります。
2. [**Azure Maps Indoor** モジュール](../azure-maps/how-to-use-indoor-module.md)を使用して、Azure Maps Creator で作成された屋内マップをレンダリングします。
    1. 「[*例: Indoor Maps モジュールを使用する*](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module)」 (Indoor Maps の「[*チュートリアル: Azure Maps の Indoor Maps モジュールを使用する*](../azure-maps/how-to-use-indoor-module.md)」に含まれるセクション) の HTML をローカル ファイルにコピーします。
    1. ローカル HTML ファイルの "*サブスクリプション キー*"、*tilesetId*、*statesetID* を、実際の値に置き換えます。
    1. ブラウザーでそのファイルを開きます。

どちらのサンプルでも、互換性のある範囲で温度が送信されるので、マップには約 30 秒ごとに 121 号室の更新の色が表示されるはずです。

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="121 号室がオレンジ色で表示されているオフィス マップ":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>Azure Digital Twins にマップ情報を格納する

これでマップ情報を更新するためのハードコードされたソリューションが完成したので、Azure Digital Twins のグラフを使用して、屋内マップの更新に必要なすべての情報を格納できます。 これには、各マップと場所の状態セット ID、マップ サブスクリプション ID、および地物 ID がそれぞれ含まれます。 

この特定の例のソリューションには、状態セット ID とマップ サブスクリプション ID に属性を設定するための各最上レベルの空間の更新、および地物 ID を設定するための各部屋の更新が含まれます。 ツイン グラフを初期化するときにこれらの値を 1 回設定し、ツインの更新イベントごとにそれらの値に対してクエリを実行する必要があります。

トポロジの構成によっては、これら 3 つの属性を、マップの細分性に関連付けてさまざまなレベルで格納できます。

## <a name="next-steps"></a>次のステップ

ツイン グラフの情報の管理、アップグレード、および取得について詳しくは、以下の参照情報をご覧ください。

* [*方法: Digital Twins を管理する*](./how-to-manage-twin.md)
* [*方法: ツイン グラフにクエリを実行する*](./how-to-query-graph.md)

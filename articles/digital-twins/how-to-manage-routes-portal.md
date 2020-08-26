---
title: エンドポイントとルートを管理する (ポータル)
titleSuffix: Azure Digital Twins
description: Azure portal を使用して、Azure Digital Twins データのエンドポイントとイベント ルートを設定し、管理する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 87b674a9c6b7f7d591b39f1baf54c4d54082f306
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987309"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Azure Digital Twins でのエンドポイントとルートの管理 (ポータル)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Azure Digital Twins では、ダウンストリームのサービスや接続されているコンピューティング リソースに[イベント通知](how-to-interpret-event-data.md)をルーティングすることができます。 これを行うには、まず、イベントを受信できる**エンドポイント**を設定します。 そのうえで、Azure Digital Twins によって生成されるどのイベントをどのエンドポイントに配信するかを指定する[**イベント ルート**](concepts-route-events.md)を作成できます。

この記事では、[Azure portal](https://portal.azure.com) を使用して、エンドポイントとルートを作成する手順を説明します。

エンドポイントとルートは、[EventRoutes API](how-to-use-apis-sdks.md)、[.NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)、または [Azure Digital Twins CLI](how-to-use-cli.md) を使用して管理することもできます。 ポータルの代わりにこれらのメカニズムを使用するこの記事のバージョンについては、[*エンドポイントとルートを管理する方法 (API と CLI)* ](how-to-manage-routes-apis-cli.md) に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* **Azure アカウント**が必要となります ([こちら](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)から無料で設定できます)。
* ご利用の Azure サブスクリプションに **Azure Digital Twins インスタンス**が必要となります。 まだインスタンスをお持ちでない場合は、インスタンスを作成してください。その手順については、[*インスタンスと認証を設定する方法*](how-to-set-up-instance-scripted.md)に関するページを参照してください。 セットアップ中、次の値をメモしておいてください。後でこの記事の中で使用します。
    - インスタンス名
    - Resource group

これらの詳細については、インスタンスの設定後に、[Azure portal](https://portal.azure.com) で確認できます。 ポータルにログインし、ポータルの検索バーでインスタンスの名前を検索します。
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Azure portal 検索バーのスクリーンショット。":::

結果からインスタンスを選択すると、インスタンスの詳細ページが表示されます。

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="ADT インスタンスの詳細のスクリーンショット。" border="false":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Azure Digital Twins のエンドポイントの作成

インスタンスに作成できるエンドポイントのタイプは次のとおりです。
* [Event Grid](../event-grid/overview.md) 
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

さまざまなエンドポイントのタイプの詳細については、"[*Azure メッセージング サービスの選択*](../event-grid/compare-messaging-services.md)" に関する記事を参照してください。

エンドポイントを Azure Digital Twins にリンクするには、エンドポイントに使用する Event Grid トピック、イベント ハブ、または Service Bus が既に存在している必要があります。 

### <a name="create-an-event-grid-endpoint"></a>Event Grid のエンドポイントを作成する

**前提条件**:Event Grid の*カスタム イベント* クイックスタートの[「*カスタム トピックの作成*」セクション](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)の手順に従って、イベント グリッド トピックを作成します。

トピックを作成したら、[Azure portal](https://portal.azure.com) の Azure Digital Twins インスタンスのページから Azure Digital Twins にそれをリンクすることができます (インスタンスを見つけるには、ポータルの検索バーにその名前を入力します)。

インスタンスのメニューから、 _[エンドポイント]_ を選択します。 次に、 *[エンドポイント]* ページで、 *[+ エンドポイントの作成]* を選択します。 

表示される *[エンドポイントの作成]* ページで、対応するラジオ ボタンを選択して、_Event Grid_ の種類のエンドポイントを作成できます。 その他の詳細を入力します。 _[名前]_ フィールドにエンドポイントの名前を入力し、ドロップダウンから _[サブスクリプション]_ を選択して、3 つ目のドロップダウンから、事前作成済みの _Event Grid トピック_を選択します。

次に、 _[保存]_ をクリックしてエンドポイントを作成します。

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Event Grid の種類のエンドポイントを作成するスクリーンショット。":::

上部の Azure portal バーの通知アイコンを確認して、エンドポイントが正常に作成されたことを確認できます。 

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="エンドポイントの作成を確認する通知のスクリーンショット。" border="false":::

また、Azure Digital Twins インスタンスの *[エンドポイント]* ページに戻って、作成したエンドポイントを表示することもできます。

エンドポイントの作成に失敗した場合は、エラー メッセージを確認し、数分後に再試行してください。

これで、 _[名前]_ フィールドに指定した名前の下で、Event Grid トピックを Azure Digital Twins 内のエンドポイントとして使用できるようになりました。 通常は、**イベント ルート**のターゲットとしてこの名前を使用します。これは、[この記事の中で後から](#event-routes)作成します。

### <a name="create-an-event-hubs-endpoint"></a>Event Hubs のエンドポイントを作成する

**前提条件**: 
* _Event Hubs 名前空間_と_イベント ハブ_が必要です。 この両方を作成するには、Event Hubs の[*イベント ハブの作成*](../event-hubs/event-hubs-create.md) クイックスタートの手順に従います。
* _承認規則_が必要になります。 Event Hubs の「[*Shared Access Signature を使用する Event Hubs リソースへのアクセスの承認*](../event-hubs/authorize-access-shared-access-signature.md)」の記事を参照してください。

[Azure portal](https://portal.azure.com) で Azure Digital Twins インスタンスの詳細ページに移動します (ポータルの検索バーに名前を入力して、それを見つけることができます)。

インスタンスのメニューから、 _[エンドポイント]_ を選択します。 次に、 *[エンドポイント]* ページで、 *[+ エンドポイントの作成]* を選択します。 

表示される *[エンドポイントの作成]* ページで、対応するラジオ ボタンを選択して、_イベント ハブ_の種類のエンドポイントを作成できます。 _[名前]_ フィールドにエンドポイントの名前を入力します。 次に、 _[サブスクリプション]_ 、事前作成済みの _[イベント ハブの名前空間]_ 、 _[イベント ハブ]_ 、および _[承認規則]_ を各ドロップダウンから選択します。

次に、 _[保存]_ をクリックしてエンドポイントを作成します。

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub.png" alt-text="イベント ハブの種類のエンドポイントを作成するスクリーンショット。":::

上部の Azure portal バーの通知アイコンを確認して、エンドポイントが正常に作成されたことを確認できます。 

エンドポイントの作成に失敗した場合は、エラー メッセージを確認し、数分後に再試行してください。

これで、 _[名前]_ フィールドに指定した名前の下で、イベント ハブを Azure Digital Twins 内のエンドポイントとして使用できるようになりました。 通常は、**イベント ルート**のターゲットとしてこの名前を使用します。これは、[この記事の中で後から](#event-routes)作成します。

### <a name="create-a-service-bus-endpoint"></a>Service Bus エンドポイントを作成する

**前提条件**: 
* _Service Bus 名前空間_と _Service Bus トピック_が必要です。 これらの両方を作成するには、Service Bus の[*トピックとサブスクリプションの作成*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)クイックスタートの手順に従います。 「[*トピックに対するサブスクリプションを作成する*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md#create-subscriptions-to-the-topic)」セクションを完了する必要はありません。
* _承認規則_が必要になります。 これを作成するには、Service Bus の[*認証と承認*](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature)に関する記事を参照してください。

[Azure portal](https://portal.azure.com) で Azure Digital Twins インスタンスの詳細ページに移動します (ポータルの検索バーに名前を入力して、それを見つけることができます)。

インスタンスのメニューから、 _[エンドポイント]_ を選択します。 次に、 *[エンドポイント]* ページで、 *[+ エンドポイントの作成]* を選択します。 

表示される *[エンドポイントの作成]* ページで、対応するラジオ ボタンを選択して、_Service Bus_ の種類のエンドポイントを作成できます。 _[名前]_ フィールドにエンドポイントの名前を入力します。 次に、 _[サブスクリプション]_ 、事前作成済みの _[Service Bus 名前空間]_ 、 _[Service Bus トピック]_ 、および _[承認規則]_ を各ドロップダウンから選択します。

次に、 _[保存]_ をクリックしてエンドポイントを作成します。

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-service-bus.png" alt-text="Service Bus の種類のエンドポイントを作成するスクリーンショット。":::

上部の Azure portal バーの通知アイコンを確認して、エンドポイントが正常に作成されたことを確認できます。 

エンドポイントの作成に失敗した場合は、エラー メッセージを確認し、数分後に再試行してください。

これで、 _[名前]_ フィールドに指定した名前の下で、Service Bus トピックを Azure Digital Twins 内のエンドポイントとして使用できるようになりました。 通常は、**イベント ルート**のターゲットとしてこの名前を使用します。これは、[この記事の中で後から](#event-routes)作成します。

## <a name="event-routes"></a>イベント ルート

Azure Digital Twins からエンドポイントに実際にデータを送信するには、**イベント ルート**を定義する必要があります。 これらのルートにより、開発者はシステム全体およびダウンストリーム サービスへのイベント フローを結び付けることができます。 イベント ルートの詳細については、[*Azure Digital Twins イベントのルーティングの概念*](concepts-route-events.md)に関する記事を参照してください。

**前提条件**:ルートの作成に進む前に、この記事の前出の説明に従ってエンドポイントを作成する必要があります。 エンドポイントの設定が完了したら、イベント ルートの作成に進むことができます。

>[!NOTE]
>エンドポイントを最近デプロイした場合は、新しいイベント ルートでそれらの使用を試みる**前に**、それらのデプロイが完了していることを確認します。 エンドポイントの準備ができていないためにルートを設定できない場合は、数分待ってからやり直してください。

### <a name="create-an-event-route"></a>イベント ルートを作成する 

イベントルート定義には、これらの要素が含まれます。
* 使用するルート名
* 使用するエンドポイントの名前
* エンドポイントに送信されるイベントを定義するフィルター
    - イベントが送信されないようにルートを無効にするには、`false` のフィルター値を使用します
    - 特定のフィルターを適用しないルートを有効にするには、`true` のフィルター値を使用します
    - その他の種類のフィルターの詳細については、後述の「[*フィルター イベント*](#filter-events)」を参照してください。

1 つのルートで、複数の通知とイベントの種類を選択できるようにします。

イベント ルートを作成するには、[Azure portal](https://portal.azure.com) で Azure Digital Twins インスタンスの詳細ページに移動します (インスタンスを見つけるには、ポータルの検索バーにその名前を入力します)。

インスタンスのメニューから、 _[イベント ルート]_ を選択します。 次に、 *[イベント ルート]* ページで、 *[+ イベント ルートの作成]* を選択します。 

開いた *[イベント ルートの作成]* ページで、少なくとも次を選択します。
* _[名前]_ フィールドでルートの名前
* ルートの作成に使用する _[エンドポイント]_ 

有効にするルートについて、少なくとも `true` の**イベントルート フィルターを追加する**必要もあります。 (`false` の既定値のままにするとルートが作成されますが、イベントがそれに送信されません)。これを行うには、 _[詳細エディター]_ のスイッチを切り替えて有効にし、 *[フィルター]* ボックスに `true` と書き込みます。

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="インスタンスのイベント ルートを作成するスクリーンショット。" lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

完了したら、 _[保存]_ ボタンをクリックして、イベント ルートを作成します。

### <a name="filter-events"></a>イベントのフィルター処理

前述のように、ルートには **[フィルター]** フィールドがあります。 ルートのフィルター値が `false` の場合、エンドポイントにイベントが送信されません。 

`true` の最小フィルターを有効にすると、エンドポイントでは Azure Digital Twins からさまざまなイベントを受信します。
* Azure Digital Twins サービス API を使用して[デジタル ツイン](concepts-twins-graph.md)で発生するテレメトリ
* Azure Digital Twins インスタンスでツインのプロパティが変更されたときに発生する、ツインのプロパティ変更通知
* ツインまたはリレーションシップが作成または削除されたときに発生するライフサイクル イベント
* Azure Digital Twins インスタンスで構成された[モデル](concepts-models.md)が追加または削除されたときに発生するモデル変更イベント

より具体的なフィルターを定義することで、送信されるイベントの種類を制限できます。

イベント ルートの作成中にイベント フィルターを追加するには、 *[イベント ルートを作成する]* ページの _[イベント ルート フィルターの追加]_ セクションを使用します。 

いくつかの基本的な共通フィルター オプションから選択することも、高度なフィルター オプションを使用して独自のカスタム フィルターを作成することもできます。

#### <a name="use-the-basic-filters"></a>基本的なフィルターを使用する

基本的なフィルターを使用するには、 _[イベントの種類]_ オプションを展開し、エンドポイントに送信するイベントに対応するチェックボックスをオンにします。 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="基本フィルターでイベントルートを作成するスクリーンショット。イベントのチェックボックスが選択されている。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

これにより、選択したフィルターのテキストがフィルター テキスト ボックスに自動的に設定されます。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="基本フィルターでイベントルートを作成するスクリーンショット。イベントの選択後の自動設定されたフィルター テキストを示している。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>高度なフィルターを使用する

または、[高度なフィルター] オプションを使用して、独自のカスタム フィルターを作成することもできます。

高度なフィルター オプションを使用してイベント ルートを作成するには、 _[詳細エディター]_ のスイッチを切り替えて、有効にします。 それにより、 *[フィルター]* ボックスに独自のイベント フィルターを記述できます。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="高度なフィルターを使用してイベント ルートを作成するスクリーンショット。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

サポートされているルート フィルターを次に示します。 *フィルター テキスト スキーマ*列の詳細は、[フィルター] ボックスに入力できるテキストです。

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>次のステップ

受信できるさまざまな種類のイベント メッセージについてご確認ください。
* [*方法: イベント データを解釈する*](how-to-interpret-event-data.md)

---
title: エンドポイントとルートを管理する (ポータル)
titleSuffix: Azure Digital Twins
description: Azure portal を使用して、Azure Digital Twins データのエンドポイントとイベント ルートを設定し、管理する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0f705aa61f1fe627dc0c8227242538e01ffce1d5
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070832"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Azure Digital Twins でのエンドポイントとルートの管理 (ポータル)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Azure Digital Twins では、ダウンストリームのサービスや接続されているコンピューティング リソースに[イベント通知](how-to-interpret-event-data.md)をルーティングすることができます。 これを行うには、まず、イベントを受信できる **エンドポイント** を設定します。 そのうえで、Azure Digital Twins によって生成されるどのイベントをどのエンドポイントに配信するかを指定する [**イベント ルート**](concepts-route-events.md)を作成できます。

この記事では、[Azure portal](https://portal.azure.com) を使用して、エンドポイントとルートを作成する手順を説明します。

別の方法として、エンドポイントとルートは、[Event Routes API](/rest/api/digital-twins/dataplane/eventroutes)、[SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)、または [Azure Digital Twins CLI](how-to-use-cli.md) を使用して管理することもできます。 ポータルの代わりにこれらのメカニズムを使用するこの記事のバージョンについては、[*エンドポイントとルートを管理する方法 (API と CLI)*](how-to-manage-routes-apis-cli.md) に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* **Azure アカウント** が必要となります ([こちら](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)から無料で設定できます)。
* ご利用の Azure サブスクリプションに **Azure Digital Twins インスタンス** が必要となります。 まだインスタンスをお持ちでない場合は、インスタンスを作成してください。その手順については、[*インスタンスと認証を設定する方法*](how-to-set-up-instance-portal.md)に関するページを参照してください。 セットアップ中、次の値をメモしておいてください。後でこの記事の中で使用します。
    - インスタンス名
    - Resource group

これらの詳細については、インスタンスの設定後に、[Azure portal](https://portal.azure.com) で確認できます。 ポータルにログインし、ポータルの検索バーでインスタンスの名前を検索します。
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Azure portal 検索バーのスクリーンショット。" lightbox="media/how-to-manage-routes-portal/search-field-portal.png":::

結果からインスタンスを選択すると、インスタンスの [概要] ページに詳細が表示されます。

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Azure Digital Twins インスタンスの [概要] ページのスクリーンショット。名前とリソース グループが強調表示されている。":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Azure Digital Twins のエンドポイントの作成

インスタンスに作成できるエンドポイントのタイプは次のとおりです。
* [Event Grid](../event-grid/overview.md) 
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

さまざまなエンドポイントのタイプの詳細については、"[*Azure メッセージング サービスの選択*](../event-grid/compare-messaging-services.md)" に関する記事を参照してください。

このセクションでは、[Azure portal](https://portal.azure.com) でこれらのエンドポイントのいずれかを作成する方法について説明します。

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>エンドポイントを作成する 

エンドポイント リソースを作成したら、Azure Digital Twins エンドポイントにそれらを使用できます。 新しいエンドポイントを作成するには、[Azure portal](https://portal.azure.com) でインスタンスのページに移動します (ポータルの検索バーにインスタンスの名前を入力することで、それを見つけることができます)。

1. インスタンスのメニューから、 _[エンドポイント]_ を選択します。 次に、 *[エンドポイント]* ページで、 *[+ エンドポイントの作成]* を選択します。 これにより、 *[エンドポイントの作成]* ページが開きます。次の手順でフィールドに入力します。

    :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Event Grid の種類のエンドポイントを作成するスクリーンショット。" lightbox="media/how-to-manage-routes-portal/create-endpoint-event-grid.png":::

1. エンドポイントの **[名前]** を入力し、 **[エンドポイントの種類]** を選択します。

1. エンドポイントの種類にとって必要なその他の詳細 (サブスクリプションや[上](#prerequisite-create-endpoint-resources)のエンドポイント リソースなど) を入力します。
    1. Event Hub と Service Bus エンドポイントでのみ、 **[認証の種類]** を選択する必要があります。 事前に作成された承認規則によるキーベースの認証を使用できます。エンドポイントで Azure Digital Twins インスタンスの[マネージド ID](concepts-security.md#managed-identity-for-accessing-other-resources-preview) を使用する場合は、ID ベースの認証を使用できます。 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="種類が Event Hub のエンドポイントを作成するスクリーンショット。" lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. _[保存]_ を選択して、エンドポイントの作成を終了します。

>[!IMPORTANT]
> エンドポイントに対する ID ベースの認証を問題なく使用するには、「[*方法: イベントをルーティングするためにマネージド ID を有効にする (プレビュー)* ](how-to-enable-managed-identities.md)」の手順に従って、インスタンスのマネージド ID を作成する必要があります。

エンドポイントを作成したら、上部の Azure portal バーの通知アイコンを確認することで、エンドポイントが正常に作成されたことを確認できます。 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="エンドポイントの作成を確認する通知のスクリーンショット。ポータルの上部にあるバーのベルの形のアイコンが選択され、&quot;エンドポイント ADT-eh-endpoint が正常に作成されました&quot; という通知が表示されている。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

エンドポイントの作成に失敗した場合は、エラー メッセージを確認し、数分後に再試行してください。

また、Azure Digital Twins インスタンスの *[エンドポイント]* ページに戻って、作成したエンドポイントを表示することもできます。

これで、エンドポイント用に選択した名前で、ベント グリッド、イベント ハブ、または Service Bus トピックが Azure Digital Twins の内部のエンドポイントとして使用できるようになりました。 通常、この名前を、**イベント ルート** ([この記事の中で後で](#create-an-event-route)作成します) のターゲットとして使用します。

### <a name="create-an-endpoint-with-dead-lettering"></a>配信不能処理を行うエンドポイントを作成する

エンドポイントでは、一定期間内にイベントを配信できない場合や、イベントの配信を一定回数試行した後も配信できない場合、未配信イベントをストレージ アカウントに送信できます。 このプロセスは **配信不能処理** と呼ばれます。

配信不能処理が有効なエンドポイントを作成するには、Azure portal ではなく [CLI コマンド](how-to-use-cli.md)または[コントロール プレーン API](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) を使用してエンドポイントを作成する必要があります。

これらのツールを使用してこれを行う方法については、この記事の "[*API と CLI*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering)" のバージョンに関する記事を参照してください。

## <a name="create-an-event-route"></a>イベント ルートを作成する

Azure Digital Twins からエンドポイントに実際にデータを送信するには、**イベント ルート** を定義する必要があります。 これらのルートにより、開発者はシステム全体およびダウンストリーム サービスへのイベント フローを結び付けることができます。 イベント ルートの詳細については、[*Azure Digital Twins イベントのルーティングの概念*](concepts-route-events.md)に関する記事を参照してください。

**前提条件**:ルートの作成に進む前に、この記事の前出の説明に従ってエンドポイントを作成する必要があります。 エンドポイントの設定が完了したら、イベント ルートの作成に進むことができます。

>[!NOTE]
>エンドポイントを最近デプロイした場合は、新しいイベント ルートでそれらの使用を試みる **前に**、それらのデプロイが完了していることを確認します。 エンドポイントの準備ができていないためにルートを設定できない場合は、数分待ってからやり直してください。

### <a name="creation-steps-with-the-azure-portal"></a>Azure portal での作成手順

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

有効にするルートについて、少なくとも `true` の **イベントルート フィルターを追加する** 必要もあります。 (`false` の既定値のままにするとルートが作成されますが、イベントがそれに送信されません)。これを行うには、 _[詳細エディター]_ のスイッチを切り替えて有効にし、 *[フィルター]* ボックスに `true` と書き込みます。

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="インスタンスのイベント ルートを作成するスクリーンショット。" lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

完了したら、 _[保存]_ ボタンをクリックして、イベント ルートを作成します。

## <a name="filter-events"></a>イベントのフィルター処理

前述のように、ルートには **[フィルター]** フィールドがあります。 ルートのフィルター値が `false` の場合、エンドポイントにイベントが送信されません。 

`true` の最小フィルターを有効にすると、エンドポイントでは Azure Digital Twins からさまざまなイベントを受信します。
* Azure Digital Twins サービス API を使用して[デジタル ツイン](concepts-twins-graph.md)で発生するテレメトリ
* Azure Digital Twins インスタンスでツインのプロパティが変更されたときに発生する、ツインのプロパティ変更通知
* ツインまたはリレーションシップが作成または削除されたときに発生するライフサイクル イベント

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

サポートされているルート フィルターを次に示します。 *フィルター テキスト スキーマ* 列の詳細は、[フィルター] ボックスに入力できるテキストです。

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>次のステップ

受信できるさまざまな種類のイベント メッセージについてご確認ください。
* [*方法: イベント データを解釈する*](how-to-interpret-event-data.md)

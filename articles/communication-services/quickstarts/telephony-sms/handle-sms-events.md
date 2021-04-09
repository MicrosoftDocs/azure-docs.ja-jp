---
title: クイックスタート - SMS イベントの処理
titleSuffix: An Azure Communication Services quickstart
description: Azure Communication Services を使用して SMS イベントを処理する方法について説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 6db817999b415ebe2c4bb7cb4216ed1e9ad9c325
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655990"
---
# <a name="quickstart-handle-sms-events"></a>クイック スタート:SMS イベントの処理

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure Event Grid を使用して Communication Services の SMS イベントを処理することにより、Azure Communication Services を開始します。

## <a name="about-azure-event-grid"></a>Azure Event Grid について

[Azure Event Grid](../../../event-grid/overview.md) は、クラウドベースのイベント処理サービスです。 この記事では、[通信サービス イベント](../../../event-grid/event-schema-communication-services.md)のイベントをサブスクライブし、イベントをトリガーして結果を表示する方法について説明します。 通常は、イベント データを処理し、アクションを実行するエンドポイントにイベントを送信します。 この記事では、メッセージを収集して表示する Web アプリにイベントを送信します。

## <a name="prerequisites"></a>前提条件
- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure Communication Services リソース。 詳細については、[Azure Communication リソースの作成](../create-communication-resource.md)のクイックスタートに関するページを参照してください。
- SMS が有効になっている電話番号。 [電話番号を取得します](./get-phone-number.md)。

## <a name="setting-up"></a>設定

### <a name="enable-event-grid-resource-provider"></a>Event Grid リソース プロバイダーを有効にする

以前に Azure サブスクリプションで Event Grid を使用したことがない場合は、以下の手順に従って、Event Grid リソース プロバイダーを登録する必要がある可能性があります。

Azure Portal で次の操作を行います。

1. 左メニューの **[サブスクリプション]** を選択します。
2. Event Grid に使用するサブスクリプションを選択します。
3. 左側のメニューの **[設定]** で、 **[リソース プロバイダー]** を選択します。
4. **Microsoft.EventGrid** を探します。
5. 登録されていない場合は、 **[登録]** を選択します。

登録完了まで少し時間がかかることがあります。 **[最新の情報に更新]** を選択して、状態を更新します。 **[状態]** が **[登録済み]** に になったら、次に進めることができます。

### <a name="event-grid-viewer-deployment"></a>Event Grid ビューアーのデプロイ

このクイックスタートでは、 [Azure Event Grid ビューアーのサンプル](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/)を使用して、ほぼリアルタイムでイベントを表示します。 これにより、リアルタイム フィードのエクスペリエンスがユーザーに提供されます。 さらに、各イベントのペイロードも検査に使用できるようにする必要があります。

## <a name="subscribe-to-the-sms-events-using-web-hooks"></a>Webhook を使用して SMS イベントをサブスクライブする

ポータルで、作成した Azure Communication Services リソースに移動します。 Communication Service リソース内で、 **[Communication Services]** ページの左側のメニューから **[イベント]** を選択します。

:::image type="content" source="./media/handle-sms-events/select-events.png" alt-text="リソースのイベント ページ内のイベント サブスクリプション ボタンの選択を示すスクリーンショット。":::

**[イベント サブスクリプションの追加]** をクリックして、作成ウィザードに入ります。

**[イベント サブスクリプションの作成]** ページで、イベント サブスクリプションの **名前** を入力します。

特定のイベントをサブスクライブすることで、どの SMS イベントを追跡し、どこにイベントを送信するかを Event Grid に伝えることができます。 サブスクライブするイベントをドロップダウン メニューから選択します。 SMS の場合は、`SMS Received` と `SMS Delivery Report Received` を選択できます。

**システム トピック名** の入力を求められた場合は、一意の文字列を自由に指定してかまいません。 このフィールドはエクスペリエンスに影響を与えず、内部テレメトリの目的で使用されます。

[Azure Communication Services でサポートされているイベント](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services)の完全な一覧を確認してください。

:::image type="content" source="./media/handle-sms-events/select-events-create-eventsub.png" alt-text="SMS Received および SMS Delivery Report Received のイベントの種類が選択されていることを示すスクリーンショット。":::

**[エンドポイントのタイプ]** には **[Webhook]** を選択します。

:::image type="content" source="./media/handle-sms-events/select-events-create-linkwebhook.png" alt-text="[エンドポイントのタイプ] フィールドが [Webhook] に設定されていることを示すスクリーンショット。":::

**[エンドポイント]** については、 **[エンドポイントの選択]** をクリックして、お使いの Web アプリの URL を入力します。

ここでは、クイックスタートで前に設定した [Azure Event Grid ビューアーのサンプル](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/)の URL を使用します。 このサンプルの URL は次の形式になります。`https://{{site-name}}.azurewebsites.net/api/updates`

次に、 **[選択の確認]** を選択します。

:::image type="content" source="./media/handle-sms-events/select-events-create-selectwebhook-epadd.png" alt-text="Webhook エンドポイントの確認を示すスクリーンショット。":::

## <a name="viewing-sms-events"></a>SMS イベントの表示

### <a name="triggering-sms-events"></a>SMS イベントのトリガー

イベント トリガーを表示するには、最初にイベントを生成する必要があります。

- `SMS Received` イベントは、Communication Services の電話番号がテキスト メッセージを受信したときに生成されます。 イベントをトリガーするには、お使いの電話から、Communication Services リソースに接続されている電話番号にメッセージを送信するだけです。
- `SMS Delivery Report Received` イベントは、Communication Services の電話番号を使用してユーザーに SMS を送信したときに生成されます。 イベントをトリガーするには、[送信された SMS](../telephony-sms/send.md) のオプションで `Delivery Report` を有効にする必要があります。 `Delivery Report` を使用して、お使いの電話にメッセージを送信してみてください。 このアクションを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

[Azure Communication Services でサポートされているイベント](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services)の完全な一覧を確認してください。

### <a name="receiving-sms-events"></a>SMS イベントの受信

上記のいずれかのアクションを完了すると、`SMS Received` および `SMS Delivery Report Received` イベントがお使いのエンドポイントに送信されることがわかります。 これらのイベントは、最初に設定した [Azure Event Grid ビューアーのサンプル](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/)に表示されます。 イベントの横にある目のアイコンを押すと、ペイロード全体を表示できます。 イベントは次のようになります。

:::image type="content" source="./media/handle-sms-events/sms-received.png" alt-text="SMS 受信イベントの Event Grid スキーマを示すスクリーンショット。":::

:::image type="content" source="./media/handle-sms-events/sms-delivery-report-received.png" alt-text="SMS Delivery Report Event の Event Grid スキーマを示すスクリーンショット。":::

詳細については、[イベント スキーマとその他のイベントの概念](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services)に関する記事を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、SMS イベントの使用方法について説明しました。 Event Grid サブスクリプションを作成することによって SMS メッセージを受信できます。

> [!div class="nextstepaction"]
> [SMS を送信する](../telephony-sms/send.md)

次のことも実行できます。

 - [イベント処理の概念の詳細を確認する](../../../event-grid/event-schema-communication-services.md)
 - [Event Grid の詳細を確認する](../../../event-grid/overview.md)

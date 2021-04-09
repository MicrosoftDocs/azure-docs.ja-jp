---
title: インクルード ファイル
description: インクルード ファイル
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0e487b3ab3663c765c052f2064201865508ef57f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510829"
---
## <a name="trusted-microsoft-services"></a>信頼できる Microsoft サービス
**[Allow trusted Microsoft services to bypass this firewall]\(信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する\)** 設定を有効にした場合、次のサービスに Event Hubs リソースへのアクセス権が与えられます。

| 信頼できるサービス | サポートされる使用シナリオ | 
| --------------- | ------------------------- | 
| Azure Event Grid | Event Hubs 名前空間のイベント ハブにイベントを送信することを Azure Event Grid に許可します。 次の手順も行う必要があります。 <ul><li>トピックまたはドメインに対してシステム割り当て ID を有効にする</li><li>Event Hubs 名前空間で Azure Event Hubs データ送信者ロールに ID を追加する</li><li>次に、システムによって割り当てられた ID を使用するために、イベント ハブをエンドポイントとして使用するイベント サブスクリプションを構成する</li></ul> <p>詳細については、「[マネージド ID を使用したイベント配信](../articles/event-grid/managed-service-identity.md)」を参照してください</p>|
| Azure Monitor (診断設定) | Event Hubs 名前空間のイベント ハブに診断情報を送信することを Azure Monitor に許可します。 Azure Monitor では、イベント ハブから読み取ることができ、イベント ハブにデータを書き込むこともできます。 |
| Azure Stream Analytics | Event Hubs 名前空間のイベント ハブからのデータの読み取り ([入力](../articles/stream-analytics/stream-analytics-add-inputs.md)) またはイベント ハブへの書き込み ([出力](../articles/stream-analytics/event-hubs-output.md)) を Azure Stream Analytics ジョブに許可します。 <p>**重要**:Stream Analytics ジョブは、**マネージド ID** を使用してイベント ハブにアクセスするように構成されている必要があります。 詳細については、「[Azure Stream Analytics ジョブからマネージド ID を使用してイベント ハブにアクセスする (プレビュー)](../articles/stream-analytics/event-hubs-managed-identity.md)」を参照してください。 </p>|
| Azure IoT Hub | Event Hubs 名前空間のイベント ハブにメッセージを送信することを IoT Hub に許可します。 次の手順も行う必要があります。 <ul><li>IoT ハブのシステム割り当て ID を有効にする</li><li>Event Hubs 名前空間で Azure Event Hubs データ送信者ロールに ID を追加する。</li><li>その後、ID ベースの認証を使用するため、イベント ハブをカスタム エンドポイントとして使用するように IoT Hub を構成する。</li></ul>
| Azure API Management | <p>API Management サービスを使用すると、Event Hubs 名前空間のイベント ハブにイベントを送信できます。</p> <ul><li>[send-request ポリシー](../articles/api-management/api-management-sample-send-request.md)を使用して API が呼び出されたときにイベント ハブにイベントを送信することで、カスタム ワークフローをトリガーできます。</li><li>また、API でイベント ハブをバックエンドとして扱うこともできます。 サンプル ポリシーについては、[イベント ハブにアクセスするためのマネージド ID を使用した認証](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Event%20Hub.xml)に関するページを参照してください。 次の手順も行う必要があります。<ol><li>API Management インスタンスでシステム割り当て ID を有効にします。 手順については、「[Azure API Management でマネージド ID を使用する](../articles/api-management/api-management-howto-use-managed-service-identity.md)」を参照してください。</li><li>Event Hubs 名前空間で **Azure Event Hubs データ送信者** ロールに ID を追加します</li></ol></li></ul> | 

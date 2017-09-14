---
title: "Azure Event Grid の概要"
description: "Azure Event Grid とその概念について説明します。"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/18/2017
ms.author: babanisa
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 59a834f32793e349d5639baf3c80dbcba274dfa8
ms.contentlocale: ja-jp
ms.lasthandoff: 08/19/2017

---

# <a name="an-introduction-to-azure-event-grid"></a>Azure Event Grid の概要

Azure Event Grid では、イベント ベースのアーキテクチャを備えたアプリケーションを簡単に作成することができます。 サブスクライブ対象の Azure リソースを選択し、イベントの送信先となるイベント ハンドラーまたは webhook エンドポイントを指定します。 Event Grid には、Storage Blob やリソース グループなどの Azure サービスから送信されるイベントに対するサポートが組み込まれています。 また、Event Grid では、カスタム トピックとカスタム webhook を使用して、アプリケーションおよびサード パーティのイベントに対するカスタム サポートも提供しています。 

フィルターを使用することで、特定のイベントをさまざまなエンドポイントにルーティングしたり、複数のエンドポイントにマルチキャストしたり、イベントを確実に配信したりできます。 Event Grid には、カスタムおよびサード パーティのイベントに対するサポートも組み込まれています。

プレビュー リリースの Event Grid では、**westus2** と **westcentralus** の場所がサポートされます。 他のリージョンも追加される予定です。

この記事では、Azure Event Grid の概要を示します。 Event Grid の使用をすぐに開始するには、「[Azure Event Grid を使ったカスタム イベントの作成とルーティング](custom-event-quickstart.md)」を参照してください。

![Event Grid 機能モデル](./media/overview/event-grid-functional-model.png)

現在、Blob Storage は、発行元として公開されていません。

## <a name="concepts"></a>概念

Azure Event Grid には、作業開始にあたり理解する必要がある、5 つの概念があります。

* **イベント** - 発生内容
* **イベント ソース/発行元** - イベントの発生元
* **トピック** - 発行元がイベントを送信するエンドポイント
* **イベント サブスクリプション** - イベントを (時には複数のハンドラーに) ルーティングするエンドポイントまたは組み込みメカニズム サブスクリプションは、受信イベントをインテリジェントにフィルター処理するために、ハンドラーによっても使用されます。
* **イベント ハンドラー** - イベントに反応するアプリまたはサービス

これらの概念の詳細については、「[Azure Event Grid の概念](concepts.md)」を参照してください。

## <a name="capabilities"></a>機能

Azure Event Grid の主要な特長を次に示します。

* **簡単** - Azure リソースから任意のイベント ハンドラーまたはエンドポイントまで、イベントにカーソルを合わせてクリックするだけで利用できます。
* **高度なフィルター処理** - イベントの種類またはイベント発行パスに基づいてフィルター処理することで、イベント ハンドラーが関連するイベントのみを受け取るようにできます。
* **ファンアウト** - 複数のエンドポイントを同じイベントにサブスクライブし、必要な数の場所にイベントのコピーを送信できます。
* **信頼性** - 指数バックオフによる 24 時間の再試行を活用して、確実にイベントが配信されるようにします。
* **イベントごとの支払** - Event Grid の使用量に対して料金を支払います。
* **高スループット** - 1 秒あたり数百万イベントをサポートして、Event Grid での大量ワークロードを作成できます。
* **組み込みイベント** - リソース定義の組み込みイベントにより、迅速に開始および実行できます。
* **カスタム イベント** - Event Grid ルートとフィルター処理を使用して、アプリで信頼性の高いカスタム イベント配信を実現します。

## <a name="built-in-publisher-and-handler-integration"></a>組み込みの発行元とハンドラーの統合

Azure では、発行元およびハンドラーの両方を含む、多数のサービスを使用して、組み込みイベントがサポートされています。

### <a name="publishers"></a>発行元

現時点で、次の Azure サービスで、イベント グリッドの組み込み発行元がサポートされています。

* リソース グループ (管理操作)
* Azure サブスクリプション (管理操作)
* Event Hubs
* カスタム トピック

今年は、他の Azure サービスが追加される予定です。

### <a name="handlers"></a>ハンドラー

現時点で、次の Azure サービスで、Event Grid の組み込みハンドラーがサポートされています。 

* Azure Functions
* Logic Apps
* Azure Automation
* WebHook

今年は、他の Azure サービスが追加される予定です。

## <a name="what-can-i-do-with-event-grid"></a>Event Grid でできること

Azure Event Grid は、サーバーレス、操作の自動化、および統合作業を大幅に向上する複数の機能を提供します。 

### <a name="serverless-application-architectures"></a>サーバーレス アプリケーション アーキテクチャ

![サーバーレス アプリケーション](./media/overview/serverless_web_app.png)

Event Grid はデータ ソースとイベント ハンドラーを接続します。 たとえば、Event Grid を使用すると、Blob Storage コンテナーに新しい写真が追加されるたびに画像分析を実行するサーバーレス関数を即時トリガーできます。 

### <a name="ops-automation"></a>操作の自動化

![操作の自動化](./media/overview/Ops_automation.png)

Event Grid を使用すると、自動化を迅速にして、ポリシーの適用を簡素化できます。 たとえば、仮想マシンが作成された場合や SQL Database がスピンアップされた場合、Event Grid は Azure Automation に通知できます。 これらのイベントを使用して、サービス構成が準拠していることの自動確認、操作ツールへのメタデータの配置、仮想マシンのタグ付け、作業項目を登録などを実行できます。

### <a name="application-integration"></a>アプリケーションの統合

![アプリケーションの統合](./media/overview/app_integration.png)

Event Grid はお客様のアプリを他のサービスにつなげます。 たとえば、お客様のアプリのイベント データを Event Grid に送信するカスタム トピックを作成すると、Event Grid の信頼性の高い配信、高度なルーティング、Azure との直接統合を利用できます。 また、Event Grid を Logic Apps と共に使用して、コードを作成することなく、場所を問わずにデータを処理することもできます。 

## <a name="how-is-event-grid-different-from-other-azure-integration-services"></a>Event Grid と他の Azure 統合サービスの違い

Event Grid は、イベント駆動のリアクティブ プログラミングを可能にするイベント バックプレーンです。 Azure サービスと緊密に統合されており、サード パーティのサービスと統合することもできます。 イベント メッセージには、サービスやアプリケーションの変更に対応するために必要な情報が含まれています。 Event Grid は、データ パイプラインではないため、更新された実際のオブジェクトは配信しません。

Service Bus は、トランザクション、順序付け、重複の検出、および瞬間的な整合性を必要とする従来のエンタープライズ アプリケーションに適しています。 Event Grid は、リアクティブ モデルにおける速度、スケール、幅、および低コストを目的に設計されています。 このため、サーバーレス アーキテクチャに適してします。

Event Grid は、Logic Apps や Event Hubs などの他の Azure サービスを補完します。 また、ワークフローを開始するロジック アプリをトリガーします。 Event Hubs は、Event Hubs Capture からのイベントに対する反応と、データの受信および変換パイプラインの作成を可能にすることで、Event Grid と連携します。

## <a name="how-much-does-event-grid-cost"></a>Event Grid のコスト

Azure Event Grid では、イベントごとに課金される価格モデルを使用しているため、使用した分だけお支払いいただきます。

Event Grid のコストは 100万回の処理ごとに 0.60 ドル (プレビュー期間中は 0.30 ドル) で、毎月最初の 100,000 回の処理は無料です。 操作は、イベントの受信、詳細一致、配信試行、管理呼び出しとして定義されています。  詳細については、[価格](https://azure.microsoft.com/pricing/details/event-grid/)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [カスタム イベントを作成してサブスクライブする](custom-event-quickstart.md) すぐに着手し、Azure Event Grid のクイックスタートを使用して任意のエンドポイントに独自のカスタム イベントの送信を開始します。
* [イベント ハンドラーとして Logic Apps を使用する](monitor-virtual-machine-changes-event-grid-logic-app.md) Event Grid によってプッシュされるイベントに反応する、Logic Apps を使用したアプリの作成に関するチュートリアルです。
* [Event Grid REST API リファレンス](/rest/api/eventgrid)  
  Azure Event Grid についての詳細な技術情報と、イベント サブスクリプション、ルーティング、フィルター処理を管理するためのリファレンスを提供します。

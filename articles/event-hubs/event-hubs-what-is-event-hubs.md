---
title: "Azure Event Hubs の説明と使用する理由 | Microsoft Docs"
description: "Azure Event Hubs の概要 - Web サイト、アプリ、デバイスからのテレメトリをクラウド規模で収集"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: sethm; babanisa
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 1a6bf0a0352e6d9e3a22586ac825558d12e1307a
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017


---
<a id="what-is-event-hubs" class="xliff"></a>

# Event Hubs とは

Azure Event Hubs は高度にスケーラブルなデータ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスでもあります。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 短い待ち時間かつ大きなスケールで[パブリッシュ/サブスクライブ機能](https://msdn.microsoft.com/library/aa560414.aspx)を実現できるので、Event Hubs はビッグ データの "オン ランプ" として機能します。

<a id="why-use-event-hubs" class="xliff"></a>

## Event Hubs を使用する理由

Event Hubs のイベントとテレメトリの処理機能は、次のような場合に特に役立ちます。

* アプリケーションのインストルメンテーション
* ユーザー エクスペリエンスやワークフロー処理
* モノのインターネット (IoT) のシナリオ

たとえば Event Hubs は、モバイル アプリでの動作追跡、Web ファームからのトラフィック情報、コンソール ゲームにおけるゲーム中のイベント キャプチャのほか、産業機器、コネクテッド カー、またはその他のデバイスから収集されたテレメトリ データにも対応します。

<a id="azure-event-hubs-overview" class="xliff"></a>

## Azure Event Hubs の概要

ソリューション アーキテクチャで Event Hubs が果たす一般的な役割は、イベント パイプラインの "玄関口" です (しばしば "*イベント取り込み*" と呼ばれます)。 イベント取り込みとは、イベント ストリームの生成とそのようなイベントの消費とを分離するために、イベント パブリッシャーとイベント コンシューマーとの間に置かれるコンポーネントやサービスです。 次の図は、このアーキテクチャを表しています。

![Event Hubs](./media/event-hubs-what-is-event-hubs/event_hubs_full_pipeline.png)

Event Hubs はメッセージ ストリーム処理機能を備えていますが、従来のエンタープライズ メッセージングとは特性が異なります。 Event Hubs の機能は、高スループットおよびイベント処理のシナリオに基づいて構築されています。 そのため、Event Hubs は [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) メッセージングとは異なり、トピックなどの [Service Bus メッセージング](/azure/service-bus-messaging/) エンティティで利用できる一部の機能が実装されていません。

<a id="event-hubs-features" class="xliff"></a>

## Event Hubs の機能

Event Hubs の主な要素を以下に示します。

- [**イベント生成元/発行元**](event-hubs-features.md#event-publishers): イベント ハブにデータを送信するエンティティ。 イベントは AMQP 1.0 または HTTPS を介して発行されます。
- [**Capture**](event-hubs-features.md#capture): Event Hubs ストリーミング データをキャプチャし、Azure BLOB ストレージ アカウントに格納できるようになります。
- [**パーティション**](event-hubs-features.md#partitions): 各コンシューマーがイベント ストリームの特定のサブセットまたはパーティションのみを読み取れるようになります。
- [**SAS トークン**](event-hubs-features.md#sas-tokens): イベント発行元を識別および認証します。
- [**イベント コンシューマー**](event-hubs-features.md#event-consumers): イベント ハブからイベント データを読み取るエンティティ。 イベント コンシューマーは AMQP 1.0 を介して接続します。 
- [**コンシューマー グループ**](event-hubs-features.md#consumer-groups): 複数のコンシューマー アプリケーションに個別のイベント ストリーム ビューを提供することで、コンシューマーごとに独立した操作を実現します。
- [**スループット単位**](event-hubs-features.md#capacity): 購入済みの容量の単位。 単一のパーティションの最大スケールは 1 つのスループット単位です。

これらの機能や Event Hubs の他の機能の技術的な詳細については、「[Event Hubs features overview (Event Hubs の機能の概要)](event-hubs-features.md)」を参照してください。 

<a id="next-steps" class="xliff"></a>

## 次のステップ

Event Hubs の価格の詳細については、「[Event Hubs の価格](https://azure.microsoft.com/pricing/details/event-hubs/)」を参照してください。

Event Hubs の詳細については、次のリンクを参照してください。

* [Event Hubs のチュートリアル](event-hubs-dotnet-standard-getstarted-send.md)を開始する
* [Event Hubs の FAQ](event-hubs-faq.md)
* [Event Hubs を使用するサンプル アプリケーション](https://github.com/Azure/azure-event-hubs/tree/master/samples)
 
 



---
title: Apache Kafka 対応のイベント ハブを作成する - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure portal を使用して Apache Kafka 対応の Azure Event Hubs 名前空間を作成する手順について説明します。
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 125da95349fce0e75b44b5619baba28d34a74be1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58000492"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Apache Kafka 対応イベント ハブの作成

Azure Event Hubs はビッグ データ ストリーミング PaaS (サービスとしてのプラットフォーム) です。毎秒数百万のイベントが取り込まれ、リアルタイムの分析と視覚化のために低遅延かつ高いスループットが提供されます。

Azure Event Hubs は、Kafka エンドポイントを提供します。 このエンドポイントにより、Event Hubs 名前空間で [Apache Kafka](https://kafka.apache.org/intro) メッセージ プロトコルと API をネイティブに認識することができます。 この機能を利用すれば、プロトコル クライアントを変更したり、独自のクラスターを実行したりすることなく、Kafka トピックの場合と同様に、イベント ハブと通信することができます。 Event Hubs は、[Apache Kafka バージョン 1.0](https://kafka.apache.org/10/documentation.html) 以降をサポートします。

この記事では、Event Hubs 名前空間を作成し、Kafka 対応イベント ハブに Kafka アプリケーションを接続するために必要な接続文字列を取得する方法を説明します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Kafka 対応 Event Hubs 名前空間の作成

1. [Azure Portal][Azure portal] にサインインし、画面の左上にある **[リソースの作成]** をクリックします。

2. Event Hubs を検索し、以下に示すオプションを選択します。
    
    ![ポータルでの Event Hubs の検索](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. 一意の名前を指定し、名前空間で Kafka を有効にします。 **Create** をクリックしてください。
    
    ![名前空間の作成](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.jpg)
 
4. 名前空間が作成されたら、**[設定]** タブで **[共有アクセス ポリシー]** をクリックして接続文字列を取得します。

    ![[共有アクセス ポリシー] をクリックする](./media/event-hubs-create/create-event-hub7.png)

5. 既定値の **RootManageSharedAccessKey** を選択することも、新しいポリシーを追加することもできます。 ポリシー名をクリックして、接続文字列をコピーします。 
    
    ![ポリシーの選択](./media/event-hubs-create/create-event-hub8.png)
 
6. この接続文字列を Kafka アプリケーション構成に追加します。

これで、Kafka プロトコルを使用するアプリケーションからイベントを Event Hubs にストリーミングできます。

## <a name="next-steps"></a>次の手順

Event Hubs の詳細については、次のリンク先を参照してください。

* [Kafka アプリケーションから Event Hubs へのストリーム](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Kafka 用 Event Hubs について確認する](event-hubs-for-kafka-ecosystem-overview.md)
* [Event Hubs について確認する](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/

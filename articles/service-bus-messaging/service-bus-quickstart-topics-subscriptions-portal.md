---
title: Azure portal を使用して Service Bus のトピックとサブスクリプションを作成する
description: クイック スタート:このクイック スタートでは、Azure portal を使用して、Service Bus のトピックとそのサブスクリプションを作成する方法について説明します。
author: spelluru
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: d3dc63106e1ca5d9db2b14392ca1fb97e3a68289
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88191541"
---
# <a name="use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Azure portal を使用して Service Bus トピックとそのサブスクリプションを作成する
このクイック スタートでは、Azure portal を使用して Service Bus トピックを作成した後、そのトピックのサブスクリプションを作成します。 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Service Bus トピックとサブスクリプションとは
Service Bus のトピックとサブスクリプションは、メッセージ通信の *発行/サブスクライブ* モデルをサポートします。 トピックとサブスクリプションを使用すると、分散アプリケーションのコンポーネントが互いに直接通信することがなくなり、仲介者の役割を果たすトピックを介してメッセージをやり取りすることになります。

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

各メッセージが 1 つのコンシューマーによって処理される Service Bus キューとは異なり、トピックとサブスクリプションは、発行/サブスクライブ パターンを使用して "1 対多" 形式の通信を行います。 複数のサブスクリプションを 1 つのトピックに登録できます。 トピックに送信されたメッセージはサブスクリプションに渡され、各サブスクリプションで独立して処理できます。 トピックにとってのサブスクリプションは、トピックに送信されたメッセージのコピーを受け取る仮想キューのようなものです。 トピックに対するフィルター ルールをサブスクリプション単位で登録することもできます。この場合、トピックに送信されるどのメッセージをどのトピック サブスクリプションで受信するかのフィルター処理や制限が可能になります。

Service Bus のトピックとサブスクリプションを使用すると、多数のユーザーとアプリケーションの間でやり取りされる膨大な数のメッセージを処理することもできます。

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> Service Bus リソースは、[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) で管理できます。 Service Bus Explorer を使用すると、ユーザーは Service Bus 名前空間に接続し、簡単な方法でメッセージング エンティティを管理できます。 このツールには、インポート/エクスポート機能や、トピック、キュー、サブスクリプション、リレー サービス、通知ハブ、イベント ハブをテストする機能などの高度な機能が用意されています。 

## <a name="next-steps"></a>次のステップ
トピックにメッセージを送信してそれらのメッセージをサブスクリプション経由で受信する方法については、次の記事を参照してください。TOC からプログラミング言語を選択してください。 

> [!div class="nextstepaction"]
> [メッセージの発行とサブスクライブ](service-bus-dotnet-how-to-use-topics-subscriptions.md)

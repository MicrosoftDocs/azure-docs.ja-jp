---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 7b05f3d8bcca5f26161f4c362078fa134518cafd
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588655"
---
## <a name="what-are-service-bus-topics-and-subscriptions"></a>Service Bus トピックとサブスクリプションとは
Service Bus のトピックとサブスクリプションは、メッセージ通信の *発行/サブスクライブ* モデルをサポートします。 トピックとサブスクリプションを使用すると、分散アプリケーションのコンポーネントが互いに直接通信することがなくなり、仲介者の役割を果たすトピックを介してメッセージをやり取りすることになります。

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

すべてのメッセージが 1 つのコンシューマーによって処理される Service Bus キューとは異なり、トピックとサブスクリプションでは発行/サブスクライブ パターンを使用した "1 対多" 形式の通信を行います。 複数のサブスクリプションを 1 つのトピックに登録できます。 トピックに送信されたメッセージはサブスクリプションに渡され、各サブスクリプションで独立して処理できます。

トピックにとってのサブスクリプションは、トピックに送信されたメッセージのコピーを受け取る仮想キューのようなものです。 必要に応じて、サブスクリプションごとにトピックのフィルター規則を登録できます。 フィルター規則を使用すると、トピックへのどのメッセージをどのトピック サブスクリプションで受信するかを、フィルター処理したり制限したりできます。

Service Bus のトピックとサブスクリプションを使用することで、多くのユーザーやアプリケーションの間でやり取りされる大量のメッセージを処理することもできます。

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png



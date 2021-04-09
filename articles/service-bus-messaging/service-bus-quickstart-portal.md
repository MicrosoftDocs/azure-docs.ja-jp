---
title: Azure portal を使用して Service Bus キューを作成する
description: このクイックスタートでは、Service Bus 名前空間とその名前空間のキューを Azure portal を使用して作成する方法について説明します。
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 79dd751c43443790aafc494d89ad45e3b6705a64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95799223"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>Azure portal を使用して Service Bus 名前空間とキューを作成する
このクイックスタートでは、[Azure portal][Azure portal] を使用して Service Bus 名前空間とキューを作成する方法について説明します。 また、クライアント アプリケーションがキューとの間でメッセージを送受信するときに使用できる承認の資格情報を取得する方法についても説明します。 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント][]を作成できます。

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>次の手順
この記事では、Service Bus 名前空間とその名前空間のキューを作成しました。 キューとの間でメッセージを送受信する方法については、「**メッセージを送受信する**」セクションで次のいずれかのクイックスタートを参照してください。 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[無料アカウント]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png

---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181865"
---
[Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview) は、エンタープライズ[統合](https://azure.microsoft.com/product-categories/integration/)メッセージ ブローカーです。 Service Bus は、キューとトピックの 2 種類の通信をサポートしています。 

キューは、アプリケーション間の非同期通信をサポートします。 あるアプリが、メッセージを格納するキューにメッセージを送信します。 次に受信側アプリケーションがキューに接続し、メッセージを読み取ります。

トピックでは発行-サブスクライブ パターンがサポートされ、メッセージ送信側とメッセージ受信側の一対多の関係を有効にできます。
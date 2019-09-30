---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180976"
---
このサンプルでは、[Twilio](https://www.twilio.com/) サービスを使って、携帯電話に SMS メッセージを送信します。 Azure Functions では [Twilio バインディング](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio)により Twilio が既にサポートされており、サンプルではその機能を使います。

最初に必要なものは Twilio アカウントです。 [https://www.twilio.com/try-twilio](https://www.twilio.com/try-twilio ) で無料で作成できます。 アカウントを作成したら、次の 3 つの**アプリ設定**を関数アプリに追加します。

| アプリ設定の名前 | 値の説明 |
| - | - |
| **TwilioAccountSid**  | Twilio アカウントの SID |
| **TwilioAuthToken**   | Twilio アカウントの認証トークン |
| **TwilioPhoneNumber** | Twilio アカウントに関連付けられている電話番号。 これは、SMS メッセージの送信に使われます。 |
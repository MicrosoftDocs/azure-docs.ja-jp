---
title: Azure Communication Services での SMS の概念
titleSuffix: An Azure Communication Services concept document
description: Communication Services SMS の概念について説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c866629677790447d0ed730ae9cc62cb37e338e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495506"
---
# <a name="sms-concepts"></a>SMS の概念

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure Communication Services では、Communication Services SMS クライアント ライブラリを使用して SMS テキスト メッセージを送受信できます。 これらのクライアント ライブラリを使用して、カスタマー サービスのシナリオ、予定リマインダー、2 要素認証、その他のリアルタイム通信のニーズをサポートできます。 Communication Services SMS を使用すると、メッセージを確実に送信しながら、キャンペーンに関連する配信性と反応率の分析情報を明らかにすることができます。

Azure Communication Services SMS クライアント ライブラリの主な機能を次に示します。

-  アプリケーションに SMS 機能を追加するための **シンプルな** セットアップ エクスペリエンス。
- 米国内での A2P (アプリケーション対人) ユース ケースのフリー ダイヤル番号で **高速** メッセージをサポート。
- カスタマー サポート、アラート、予定リマインダーなどのシナリオをサポートする **双方向** の会話。
- アプリケーションから送信されるメッセージのリアルタイム配信レポートを備えた **信頼性の高い配信**。
- 使用パターンと顧客エンゲージメントを追跡するための **分析**。
- フリー ダイヤル番号のオプトアウトを自動的に検出して配慮するための **オプトアウト** 処理のサポート。 米国の無料電話番号に使用されるオプトアウトは、米国の通信事業者によって管理、適用されます。
  - STOP - テキスト メッセージの受信者がオプトアウトを希望する場合、無料電話番号に "STOP" を送信できます。 通信事業者は、STOP に対する既定の応答として、"*NETWORK MSG: You replied with the word "stop" which blocks all texts sent from this number. Text back "unstop" to receive messages again. (ネットワーク メッセージ: "stop" という単語で応答しました。この番号から送信されたテキストはすべてブロックされます。再度メッセージを受信するには、"unstop" というテキストを返信してください。)* " を送信します。
  - START または UNSTOP - 無料電話番号からのテキスト メッセージに対し、再度受信登録を希望する場合、受信者は、その無料電話番号に "START" または "UNSTOP" を送信できます。 通信事業者は、START と UNSTOP に対する既定の応答として、"*NETWORK MSG: You have replied "unstop" and will begin receiving messages again from this number. (ネットワーク メッセージ: 応答として "unstop" が送信されました。この番号からのメッセージの受信が再開されます。)* " を送信します。
  - Azure Communication Services は STOP メッセージを検出し、以降その受信者に送信されるメッセージをすべてブロックします。 配信レポートには、"Sender blocked for given recipient. (送信者は特定の受信者に関してブロックされています。)" というステータス メッセージで配信に失敗したことが示されます。
  - STOP、UNSTOP、START メッセージは、自分にリレー バックされます。 Azure Communication Services では、通信をオプトアウトした受信者にそれ以上メッセージ送信が試行されないように、これらのオプトアウトを監視して実行することが推奨されます。


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [SMS の送信を開始する](../../quickstarts/telephony-sms/send.md)

次のドキュメントもご覧ください。

- [SMS クライアント ライブラリ](../telephony-sms/sdk-features.md)について理解する
- SMS 対応の[電話番号](../../quickstarts/telephony-sms/get-phone-number.md)を取得する
- [Azure Communication Services での電話番号の種類](../telephony-sms/plan-solution.md)

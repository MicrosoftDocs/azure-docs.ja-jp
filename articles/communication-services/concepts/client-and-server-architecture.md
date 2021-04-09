---
title: クライアントとサーバーのアーキテクチャ
titleSuffix: An Azure Communication Services concept document
description: Communication Services のアーキテクチャについて説明します。
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 74a48b05e7e3b00d331da4fcf66092490ae70374
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490672"
---
# <a name="client-and-server-architecture"></a>クライアントとサーバーのアーキテクチャ

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Need to add security best practices for token management here
> - Reference docs:
> - https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/create-a-token-object
> - https://docs.microsoft.com/azure/aks/operator-best-practices-identity
> - https://docs.microsoft.com/cloud-app-security/api-tokens?view=gestures-1.0-->

すべての Azure Communication Services アプリケーションには、**サービス** を使用して個人対個人の接続を容易にする **クライアント アプリケーション** があります。 このページでは、さまざまなシナリオにおける一般的なアーキテクチャ要素について説明します。

## <a name="user-access-management"></a>ユーザー アクセス管理

Azure Communication Services クライアント ライブラリが Communication Services のリソースに安全にアクセスするためには、`user access tokens`が必要です。 トークンとその生成に必要な接続文字列の機密性により、`User access tokens`は、信頼できるサービスによって生成および管理される必要があります。 アクセス トークンを適切に管理できないと、リソースの誤用によって追加料金が発生する可能性があります。 ユーザー管理には、信頼できるサービスを使用することを強くお勧めします。 信頼できるサービスによって、トークンが作成され、適切な暗号化を使用してクライアントに返されます。 アーキテクチャのフローの例を以下に示します。

:::image type="content" source="../media/scenarios/archdiagram-access.png" alt-text="ユーザー アクセス トークンのアーキテクチャを示す図。":::

詳細については、[ID 管理のベストプラクティスに関する記事](../../security/fundamentals/identity-management-best-practices.md)を参照してください。

## <a name="browser-communication"></a>ブラウザーの通信

Azure Communication JavaScript クライアント ライブラリにより、Web アプリケーションでリッチ テキスト、音声、ビデオのやり取りが可能になります。 アプリケーションはクライアント ライブラリを介して Azure Communication Services と直接やり取りしてデータ プレーンにアクセスし、リアルタイムのテキスト、音声、ビデオの通信を提供します。 アーキテクチャのフローの例を以下に示します。

:::image type="content" source="../media/scenarios/archdiagram-browser.png" alt-text="Communication Services のブラウザー対ブラウザーのアーキテクチャを示す図。":::

## <a name="native-app-communication"></a>ネイティブ アプリの通信

ネイティブ アプリケーションは多くのシナリオに最適です。 Azure Communication Services では、ブラウザー対アプリとアプリ対アプリの両方の通信がサポートされます。  ネイティブ アプリケーションのエクスペリエンスを構築するときは、プッシュ通知を使用すると、アプリケーションが実行されていないときでもユーザーが呼び出しを受信できるようになります。 Azure Communication Services では、Google Firebase、Apple Push Notification Service、Windows Push Notifications に統合されたプッシュ通知によって、これを簡単に実行できます。 アーキテクチャのフローの例を以下に示します。

:::image type="content" source="../media/scenarios/archdiagram-app.png" alt-text="Communication Services のネイティブ アプリの通信のアーキテクチャを示す図。":::

## <a name="voice-and-sms-over-the-public-switched-telephony-network-pstn"></a>公衆交換電話網 (PSTN) を介した音声と SMS

電話システムを介して通信すると、アプリケーションのリーチを大幅に拡大できます。 PSTN の音声と SMS のシナリオをサポートするために、Azure Communication Services では、 Azure portal から直接、または REST API とクライアント ライブラリを使用して[電話番号を取得する](../quickstarts/telephony-sms/get-phone-number.md)ことができます。 電話番号を取得したら、それを使用して、受信と発信の両方のシナリオで、PSTN の通話と SMS の両方を使用して顧客にリーチできます。 アーキテクチャのフローの例を以下に示します。

> [!Note]
> パブリック プレビュー期間中は、米国およびカナダ内にある請求先住所を持つお客様が、米国の電話番号のプロビジョニングを利用できます。

:::image type="content" source="../media/scenarios/archdiagram-pstn.png" alt-text="Communication Services の PSTN のアーキテクチャを示す図。":::

PSTN の電話番号の詳細については、[電話番号の種類](../concepts/telephony-sms/plan-solution.md)に関するページを参照してください。

## <a name="humans-communicating-with-bots-and-other-services"></a>人とボットやその他のサービスの通信

Azure Communication Services では、Azure Communication Services データ プレーンに直接アクセスするサービスを使用した、テキストおよび音声チャネル経由での人対システムの通信がサポートされます。 たとえば、ボットを着信通話に応答させたり、Web チャットに参加させたりできます。 Azure Communication Services には、通話とチャットでこれらのシナリオを実現するクライアント ライブラリが用意されています。 アーキテクチャのフローの例を以下に示します。

:::image type="content" source="../media/scenarios/archdiagram-bot.png" alt-text="Communication Services のボットのアーキテクチャを示す図。":::

## <a name="networking"></a>ネットワーキング

たとえば共有された Mixed Reality やゲームのエクスペリエンスを同期するなどの目的で、ユーザー間で任意のデータを交換したい場合があります。 テキスト、音声、ビデオの通信に使用されるリアルタイムのデータ プレーンは、次の 2 つの方法で直接使用できます。

- **クライアント ライブラリの呼び出し** - 呼び出し内のデバイスが、呼び出しチャネルを介してデータを送受信するための API にアクセスできます。 これは、既存のやり取りにデータ通信を追加する最も簡単な方法です。
- **STUN/TURN** - Azure Communication Services には、標準に準拠した STUN および TURN サービスが用意されています。 これを使用して、これらの標準化されたプリミティブの上に、大幅にカスタマイズされたトランスポート レイヤーを構築できます。 標準に準拠した独自のクライアントを作成することも、[WinRTC](https://github.com/microsoft/winrtc) などのオープンソース ライブラリを使用することもできます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ユーザー アクセス トークンの作成](../quickstarts/access-tokens.md)

詳細については、次の記事を参照してください。

- [認証](../concepts/authentication.md)について学習する
- [電話番号の種類](../concepts/telephony-sms/plan-solution.md)について学習する

- [チャットをアプリに追加する](../quickstarts/chat/get-started.md)
- [音声通話をアプリに追加する](../quickstarts/voice-video-calling/getting-started-with-calling.md)

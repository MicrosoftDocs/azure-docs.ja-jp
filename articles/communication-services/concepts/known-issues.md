---
title: Azure Communication Services - よくあるご質問と既知の問題
description: Azure Communication Services について説明します
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: 2c6ac34d8daf00578cb1d03833a28eb8535708b7
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493647"
---
# <a name="faq--known-issues"></a>よくあるご質問と既知の問題
この記事では、Azure Communication Services に関連する既知の問題とよくあるご質問について説明します。

## <a name="faq"></a>よく寄せられる質問

### <a name="why-is-the-quality-of-my-video-degraded"></a>ビデオの品質が低下するのはなぜですか?

ビデオ ストリームの品質は、そのストリームを開始するために使用されたクライアント側レンダラーのサイズによって決まります。 受信者は、リモート ストリームをサブスクライブするときに、送信者のクライアント側レンダラーのディメンションに基づいて独自の解像度を決定します。

### <a name="why-is-it-not-possible-to-enumerateselect-micspeaker-devices-on-safari"></a>Safari でマイクやスピーカー デバイスを列挙したり選択したりできないのはなぜですか?

Safari iOS や iPad では、アプリケーションでマイクやスピーカー デバイス (Bluetooth など) を列挙したり選択したりできません。 これは OS の制限事項であり、常に 1 つのデバイスしか存在しません。

MacOS の Safari では、アプリは Communication Services のデバイス マネージャーを使用してスピーカーを列挙したり選択したりできません。これらは、OS を介して選択する必要があります。 MacOS で Chrome を使用している場合、アプリは Communication Services のデバイス マネージャーを通じてデバイスを列挙したり選択したりできます。

## <a name="known-issues"></a>既知の問題

このセクションでは、Azure Communication Services に関連する既知の問題について説明します。

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>ビデオ デバイスを繰り返し切り替えると、ビデオ ストリーミングが一時的に停止する場合がある

ビデオ デバイスを切り替えると、選択したデバイスからストリームが取得される間、ビデオ ストリームが一時停止する場合があります。

#### <a name="possible-causes"></a>考えられる原因
メディア デバイスからのストリーミング、およびメディア デバイスの切り替えでは、コンピューティング処理の負荷が大きくなります。 頻繁に切り替えを行うと、パフォーマンスが低下する場合があります。 開発者は、1 つのデバイス ストリームを停止してから別のストリームを開始することをお勧めします。

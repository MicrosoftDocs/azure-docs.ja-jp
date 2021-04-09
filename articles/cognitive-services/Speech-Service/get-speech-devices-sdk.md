---
title: Speech Devices SDK を取得する
titleSuffix: Azure Cognitive Services
description: 音声サービスは、さまざまなデバイスやオーディオ ソースと連携します。 適合するハードウェアやソフトウェアで音声アプリケーションを次のレベルに引き上げることができます。 この記事では、Speech Devices SDK を利用して開発を始める方法について説明します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: 69898e64934c363a18a3ce2fa5e678116624bd24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026371"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Cognitive Services Speech Devices SDK を取得する

Speech Devices SDK は、専用の開発キットやさまざまなマイク アレイの構成に対応するように設計された調整済みのライブラリです。

## <a name="choose-a-development-kit"></a>開発キットを選択する

|デバイス|仕様|説明|シナリオ|
|--|--|--|--|
|[Urbetter Dev Kit](http://www.urbetter.com/products_56/278.html)![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 マイク アレイ、ARM SOC、WIFI、イーサネット、HDMI、USB カメラ。 <br>Linux|Microsoft マイク アレイを採用し、HDMI/Ethernet やその他の USB 周辺機器などの拡張 I/O をサポートする業界レベルの Speech Devices SDK <br> [Urbetter に問い合わせる](http://www.urbetter.com/products_56/278.html)|会話の文字起こし、教育、病院、ロボット、OTT Box、音声エージェント、Drive Thru|
|[Roobo Smart Audio Dev Kit](http://ddk.roobo.com)<br>[設定](speech-devices-sdk-roobo-v1.md) / [クイックスタート](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)![Roobo Smart Audio Dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 マイク アレイ、ARM SOC、WIFI、オーディオ出力、IO。 <br>[Android](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)|Microsoft Mic Array およびフロント プロセス SDK を採用する、高品質の文字起こしとスピーチ シナリオ開発用の初の Speech Devices SDK|会話の文字起こし、スマート スピーカー、音声エージェント、ウェアラブル|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[設定](../../kinect-dk/set-up-azure-kinect-dk.md) / [クイック スタート](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 マイク アレイ RGB および深度カメラ。 <br>[Windows](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows)/[Linux](./speech-devices-sdk-quickstart.md?pivots=platform-linux%253fpivots%253dplatform-linux)|高度な人工知能 (AI) センサーを採用した、先進的なコンピューター ビジョンと音声モデルを構築するための開発者キット。 クラス最高の空間マイク アレイ、ビデオ カメラ付きの深度カメラ、方位センサーが 1 つにまとめられています。さまざまな計算の種類に対応する複数のモード、オプション、SDK を備えたオールインワンの小型デバイスです。|会話の文字起こし、ロボット工学、スマート ビルディング|
|Roobo Smart Audio Dev Kit 2<br>[セットアップ](speech-devices-sdk-roobo-v2.md)<br>![Roobo Smart Audio Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 マイク アレイ、ARM SOC、WIFI、Bluetooth、IO。 <br>Linux|コスト効率の高いリファレンス設計で代替 OS などの機能を提供する第 2 世代の Speech Devices SDK。|会話の文字起こし、スマート スピーカー、音声エージェント、ウェアラブル|


## <a name="download-the-speech-devices-sdk"></a>Speech Devices SDK をダウンロードする

[Speech Devices SDK](./speech-devices-sdk.md) をダウンロードします。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Speech Devices SDK を使ってみる](./speech-devices-sdk-quickstart.md?pivots=platform-android)
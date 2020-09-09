---
title: Azure Kinect DK について
description: Azure Kinect 開発者キット (DK) のツールおよび統合サービスの概要。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: overview
ms.date: 06/26/2019
keywords: Azure, Kinect, 概要, 開発キット, DK, デバイス, 深度, 体のトラッキング, 音声, Cognitive Services, SDK, SDK, ファームウェア
ms.openlocfilehash: 6b3215ba6761521214ebffa2a100c5a1a030ab4c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "85277862"
---
# <a name="about-azure-kinect-dk"></a>Azure Kinect DK について

 ![Azure Kinect DK](./media/index/device-image.jpg)

Azure Kinect DK は、高度な AI センサーを採用した、先進的なコンピューター ビジョンと音声モデルを構築するための開発者キットです。  Kinect は、深度センサー、空間マイク配列とビデオ カメラ、および方位センサーを備えたオールインワンの小型デバイスです。複数のモード、オプション、およびソフトウェア開発キット (SDK) が用意されています。 [Microsoft オンライン ストア](https://www.microsoft.com/p/azure-kinect-dk/8pp5vxmd9nhq)で購入できます。

Azure Kinect DK 開発環境は、次の複数の SDK で構成されています。

- 低レベル センサーおよびデバイス アクセス用の Sensor SDK。
- 3D で体をトラッキングするための Body Tracking SDK。
- マイク アクセスと Azure クラウド ベースの音声サービスを有効にするための Speech Cognitive Services SDK。

さらに、デバイスの RGB カメラで Cognitive Vision サービスを使用できます。

   ![Azure Kinect SDK の図](./media/quickstarts/sdk-diagram.jpg)

## <a name="azure-kinect-sensor-sdk"></a>Azure Kinect Sensor SDK

Azure Kinect Sensor SDK では、Azure Kinect DK ハードウェア センサーおよびデバイス構成のための低レベル センサー アクセスを提供します。

Azure Kinect Sensor SDK の詳細については、[Sensor SDK の使用](about-sensor-sdk.md)に関する記事を参照してください。

### <a name="azure-kinect-sensor-sdk-features"></a>Azure Kinect Sensor SDK の機能

Sensor SDK には、Azure Kinect DK にインストールして実行すると動作する次の機能があります。

- 深度カメラへのアクセスとモード制御 (パッシブ IR モードに加えて、広視野と狭視野の深度モード) 
- RGB カメラへのアクセスと制御 (露出やホワイト バランスなど) 
- モーション センサー (ジャイロスコープと加速度計) へのアクセス 
- 深度カメラと RGB カメラの同期ストリーミング (カメラ間の遅延を設定可能) 
- 外部デバイスの同期制御 (デバイス間の遅延オフセットを設定可能) 
- カメラ フレームのメタデータ (画像の解像度やタイムスタンプなど) へのアクセス 
- デバイス較正データへのアクセス 

### <a name="azure-kinect-sensor-sdk-tools"></a>Azure Kinect Sensor SDK のツール

Sensor SDK では、次のツールを使用できます。

- デバイスのデータ ストリームを監視したり、さまざまなモードを構成したりするためのビューアー ツール。
- Matroska コンテナー形式を使用するセンサー記録ツールと再生リーダー API。
- Azure Kinect DK ファームウェア更新ツール。

## <a name="azure-kinect-body-tracking-sdk"></a>Azure Kinect Body Tracking SDK

Body Tracking SDK には、Azure Kinect DK ハードウェアと組み合わせて使用したときに 3D で体をトラッキングするための Windows ライブラリおよびランタイムが含まれています。

### <a name="azure-kinect-body-tracking-features"></a>Azure Kinect Body Tracking の機能

付属の SDK では、次に示す体のトラッキング機能を利用できます。

- 体のセグメント化を提供します。
- 認識範囲内の体の各部位または全身の解剖学的に正しい骨格を含みます。
- 体ごとに一意の ID を提供します。
- 時間の経過と共に体をトラッキングできます。

### <a name="azure-kinect-body-tracking-tools"></a>Azure Kinect Body Tracking のツール

- Body Tracker には、3D で体をトラッキングするビューアー ツールがあります。

## <a name="speech-cognitive-services-sdk"></a>Speech Cognitive Services SDK

Speech SDK により、Azure に接続された音声サービスが有効になります。

### <a name="speech-services"></a>音声サービス

- 音声テキスト変換
- 音声翻訳
- テキスト読み上げ

>[!NOTE]
>Azure Kinect DK には、スピーカーはありません。

その他の詳細および情報については、[Speech Service のドキュメント](https://docs.microsoft.com/azure/cognitive-services/speech-service/)を参照してください。

## <a name="vision-services"></a>Vision Services

以下の [Azure Cognitive Vision Services](https://azure.microsoft.com/services/cognitive-services/directory/vision/) では、画像やビデオ内のコンテンツを特定および分析できる Azure サービスを提供します。

- [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/)
- [Face](https://azure.microsoft.com/services/cognitive-services/face/)
- [ビデオ インデクサー](https://azure.microsoft.com/services/media-services/video-indexer/)
- [コンテンツ モデレーター](https://azure.microsoft.com/services/cognitive-services/content-moderator/)
- [カスタム ビジョン](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)

サービスは絶えず進化し、改良されますので、アプリケーションを改良するために、新規または追加の [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) を定期的にご確認ください。 新しく登場したサービスをいち早く確認するには、[Cognitive Services ラボ](https://labs.cognitive.microsoft.com/)をご覧ください。

## <a name="azure-kinect-hardware-requirements"></a>Azure Kinect のハードウェア要件

Azure Kinect DK によって、Microsoft の最新のセンサー テクノロジが USB 接続の単一のアクセサリに統合されます。 詳細については、「[Azure Kinect DK のハードウェアの仕様](hardware-specification.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Kinect DK の概要は以上です。 次のステップでは、その設定について詳しく説明します。

> [!div class="nextstepaction"]
>[クイック スタート: Azure Kinect DK を設定する](set-up-azure-kinect-dk.md)

---
title: Azure Media Services で推奨されるエンコーダーについて知る | Microsoft Docs
description: この記事では、Azure Media Services によって推奨されるオンプレミス エンコーダーの一覧を示します。
services: media-services
keywords: エンコード;エンコーダー;メディア
author: IngridAtMicrosoft
manager: johndeu
ms.author: johndeu
ms.date: 03/10/2021
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 98d78a105bf06a2e49dee0b8c2be710b220a0023
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009438"
---
# <a name="recommended-on-premises-encoders"></a>推奨のオンプレミス エンコーダー

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services でライブ ストリーミングを行う際には、チャネルでの入力ストリームの受信方法を指定できます。 ライブ エンコード チャネルを使用したオンプレミス エンコーダーを使用する場合、エンコーダーでは、出力として高品質のシングルビットレート ストリームをプッシュする必要があります。 パススルー チャネルを使用したオンプレミス エンコーダーを使用する場合、エンコーダーでは、目的の出力品質をすべて満たした出力として、マルチビット レート ストリームをプッシュする必要があります。 詳細については、[オンプレミス エンコーダーを使用したライブ ストリーミング](media-services-live-streaming-with-onprem-encoders.md)に関する記事を参照してください。

## <a name="encoder-requirements"></a>エンコーダーの要件

HTTPS または RTMPS プロトコルを使用する場合、エンコーダーで TLS 1.2 がサポートされている必要があります。

## <a name="live-encoders-that-output-rtmp"></a>RTMP を出力するライブ エンコーダー 

Azure Media Services では、RTMP を使用した、次のいずれかのライブ エンコーダーを出力として使用することを推奨しています。

- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast (TLS 1.2 の要件によりバージョン 13.0.2 以降)

  RTMPS プロトコルを使用する場合、エンコーダーで TLS 1.2 がサポートされている必要があります。
- Teradek Slice 756
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

## <a name="live-encoders-that-output-fragmented-mp4"></a>フラグメント化 MP4 を出力するライブ エンコーダー 

Azure Media Services では、マルチビットレートの fragmented-MP4 (Smooth Streaming) を使用した、次のいずれかのライブ エンコーダーを出力として使用することを推奨しています。

- Media Excel Hero Live と Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (TLS 1.2 の要件により 2.14.15 以降)

  HTTPS プロトコルを使用する場合、エンコーダーで TLS 1.2 がサポートされている必要があります。
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3

> [!NOTE]
> ライブ エンコーダーではパススルー チャネルにシングルビットレート ストリームを送信することができますが、クライアントへのアダプティブ ビットレート ストリーミングができなくなるため、この構成は推奨されません。

## <a name="how-to-become-an-on-premises-encoder-partner"></a>オンプレミス エンコーダー パートナーになる方法

Media Services は、Azure Media Services オンプレミス エンコーダー パートナーとして企業のお客様にエンコーダーを推奨することで、製品の販売を促進します。 オンプレミス エンコーダー パートナーになるには、オンプレミス エンコーダーと Media Services との互換性を確認する必要があります。 これを行うには、次の検証を実行します。

パススルー チャネルの検証
1. Azure Media Services アカウントを作成するか、またはアカウントにアクセスします
2. **パススルー** チャネルを作成し、起動します
3. マルチビット レートのライブ ストリームをプッシュするようにエンコーダーを構成します。
4. 公開済みのライブ イベントを作成します
5. ライブ エンコーダーを約 10 分間実行します
6. ライブ イベントを停止します
7. ストリーミング エンドポイントを作成して開始し、[Azure Media Player](https://aka.ms/azuremediaplayer) などのプレーヤーを使用して、すべての品質レベルで再生に目に見える異常がないことを確認するため、アーカイブされたアセットを監視します (または、手順 6 の前のライブ セッションでプレビュー URL を使用して監視と検証を行います)
8. アセット ID、ライブ アーカイブの公開済みストリーミング URL、およびライブ エンコーダーから使用されている設定とバージョンを記録します
9. 各サンプルの作成後に、チャネルの状態をリセットします
10. エンコーダーでサポートされているすべての構成について、手順 3 - 9 を繰り返します (必要に応じて、広告信号/キャプション/異なるエンコード速度を使用します)

ライブ エンコード チャネルの検証
1. Azure Media Services アカウントを作成するか、またはアカウントにアクセスします
2. **ライブ エンコード** チャネルを作成し、起動します
3. シングルビット レートのライブ ストリームをプッシュするようにエンコーダーを構成します。
4. 公開済みのライブ イベントを作成します
5. ライブ エンコーダーを約 10 分間実行します
6. ライブ イベントを停止します
7. ストリーミング エンドポイントを作成して開始し、[Azure Media Player](https://aka.ms/azuremediaplayer) などのプレーヤーを使用して、すべての品質レベルで再生に目に見える異常がないことを確認するため、アーカイブされたアセットを監視します (または、手順 6 の前のライブ セッションでプレビュー URL を使用して監視と検証を行います)
8. アセット ID、ライブ アーカイブの公開済みストリーミング URL、およびライブ エンコーダーから使用されている設定とバージョンを記録します
9. 各サンプルの作成後に、チャネルの状態をリセットします
10. エンコーダーでサポートされているすべての構成について、手順 3 - 9 を繰り返します (必要に応じて、広告信号/キャプション/さまざまなエンコード速度を使用します)

持続時間の検証
1. Azure Media Services アカウントを作成するか、またはアカウントにアクセスします
2. **パススルー** チャネルを作成し、起動します
3. マルチビット レートのライブ ストリームをプッシュするようにエンコーダーを構成します。
4. 公開済みのライブ イベントを作成します
5. ライブ エンコーダーを 1 週間以上実行します
6. 再生に目に見える異常がないことを確認するため、[Azure Media Player](https://aka.ms/azuremediaplayer) などのプレーヤーを使用して、時折ライブ ストリーミング (またはアーカイブされたアセット) を監視します
7. ライブ イベントを停止します
8. アセット ID、ライブ アーカイブの公開済みストリーミング URL、およびライブ エンコーダーから使用されている設定とバージョンを記録します

最後に、amsstreaming@microsoft.com 宛に電子メールを送信して、記録した設定とライブ アーカイブ パラメーターを Media Services に送信します。 電子メールを受信すると、Media Services はライブ エンコーダーからのサンプルに対する検証テストを実行します。 このプロセスに関して質問がある場合は、Media Services に問い合わせることができます。

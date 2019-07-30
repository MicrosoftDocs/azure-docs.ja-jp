---
title: Media Services で推奨されるライブ ストリーミング エンコーダー - Azure | Microsoft Docs
description: Media Services で推奨されるライブ ストリーミング オンプレミス エンコーダーについて知る
services: media-services
keywords: エンコード;エンコーダー;メディア
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 08/16/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 1f152f7f94c12beecf015d9389ed85cb310573bd
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297736"
---
# <a name="recommended-live-streaming-encoders"></a>おすすめのライブ ストリーミング エンコーダー

Azure Media Services では、[ライブ イベント](https://docs.microsoft.com/rest/api/media/liveevents) (チャネル) は、ライブ ストリーミング コンテンツを処理するためのパイプラインを表します。 ライブ イベントは、次の 2 つの方法のいずれかでライブ入力ストリームを受信します。

* オンプレミスのライブ エンコーダーは、マルチビットレート RTMP またはスムーズ ストリーミング (フラグメント化 MP4) のストリームを、Media Services によるライブ エンコードの実行が無効な Live Event に送信します。 取り込んだストリームは、追加の処理なしで Live Event を通過します。 この方式は、 **パススルー**と呼ばれます。 ライブ エンコーダーは、パススルー チャネルに単一ビットレートのストリームを送信できます。 この構成は、クライアントへのアダプティブ ビットレート ストリーミングができないため推奨されません。

  > [!NOTE]
  > パススルー方式を使用することが、ライブ ストリーミングを行う最も経済的な方法です。
 
* オンプレミスのライブ エンコーダーでは、次のいずれかの形式で、Media Services によるライブ エンコードが有効な Live Event にシングル ビットレート ストリームが送信されます。RTMP またはスムーズ ストリーミング (フラグメント化 MP4)。 次に、受信したシングル ビットレート ストリームのマルチ ビットレート (アダプティブ) ビデオ ストリームへのライブ エンコードが Live Event で実行されます。

Media Services でのライブ エンコードの詳細については、[Media Services v3 でのライブ ストリーミング](live-streaming-overview.md)に関するページをご覧ください。

## <a name="live-encoders-that-output-rtmp"></a>RTMP を出力するライブ エンコーダー

Media Services では、RTMP を使用した、次のいずれかのライブ エンコーダーを出力として使用することを推奨しています。 サポートされる URL スキームは `rtmp://` または `rtmps://` です。

> [!NOTE]
> RTMP を使用してデータをストリーミングしている場合は、ファイアウォールまたはプロキシ、あるいはその両方の設定で、送信 TCP ポート 1935 と 1936 が開いていることを確認します。

- Adobe Flash Media Live Encoder 3.2
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast 8.1 以降
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>フラグメント化 MP4 を出力するライブ エンコーダー

Media Services では、マルチビットレートのスムーズ ストリーミング (フラグメント化 MP4) を使用した、次のいずれかのライブ エンコーダーを出力として使用することを推奨しています。 サポートされる URL スキームは `http://` または `https://` です。

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3
- Media Excel Hero Live と Hero 4K (UHD/HEVC)

> [!TIP]
>  複数の言語でライブ イベント (たとえば、英語のオーディオ トラックやスペイン語のオーディオ トラックなど) をストリーミングする場合は、パススルー ライブ イベントにライブ フィードを送信するように構成された Media Excel ライブ エンコーダーを利用して、これを実現できます。

## <a name="configuring-on-premises-live-encoder-settings"></a>オンプレミス ライブ エンコーダーの設定を構成する

ご利用のライブ イベントの種類に有効な設定に関する詳細については、「[ライブ イベントの種類の比較](live-event-types-comparison.md)」を参照してください。

### <a name="playback-requirements"></a>再生要件

コンテンツを再生するには、音声と映像の両方のストリームが存在する必要があります。 映像のみをストリーム再生することはできません。

### <a name="configuration-tips"></a>構成上のヒント

- 可能な限り、有線のインターネット接続を使用します。
- 帯域幅要件の目安は、ストリーミングのビットレートの 2 倍です。 必須ではありませんが、この単純なルールはネットワークの混雑による影響を軽減するのに役立ちます。
- ソフトウェア ベースのエンコーダーを使用する際は、不要なプログラムを終了します。
- プッシュの開始後にエンコーダーの構成を変更すると、イベントに悪影響を与えます。 構成を変更すると、イベントが不安定になる可能性があります。 
- 十分な時間をかけてイベントを設定してください。 高スケールのイベントの場合、イベントの 1 時間前に設定を開始することをお勧めします。

## <a name="becoming-an-on-premises-encoder-partner"></a>オンプレミス エンコーダー パートナーになる

Media Services は、Azure Media Services オンプレミス エンコーダー パートナーとして企業のお客様にエンコーダーを推奨することで、製品の販売を促進します。 オンプレミス エンコーダー パートナーになるには、オンプレミス エンコーダーと Media Services との互換性を確認する必要があります。 これを行うには、次の検証を実行します。

### <a name="pass-through-live-event-verification"></a>パススルー Live Event 検証

1. Media Services アカウントで、**ストリーミング エンドポイント**が実行されていることを確認します。 
2. **パススルー** Live Event を作成し、起動します。 <br/> 詳細については、[ライブ イベントの状態と課金](live-event-states-billing.md)に関するページを参照してください。
3. 取り込み URL を取得し、URL を使用してマルチビットレート ライブ ストリームを Media Services に送信するようにオンプレミス エンコーダーを構成します。
4. プレビュー URL を取得し、それを使用して、エンコーダーからの入力が実際に受信されていることを確認します。
5. 新しい**資産**オブジェクトを作成します。
6. **ライブ出力**を作成し、作成した資産の名前を使用します。
7. 組み込みの**ストリーミング ポリシー** タイプで**ストリーミング ロケーター**を作成します。
8. 使用する URL を返すためのパスを**ストリーミング ロケーター**に列挙します。
9. ストリーミングする**ストリーミング エンドポイント**のホスト名を取得します。
10. 手順 8.の URL と手順 9 のホスト名を組み合わせて、完全な URL を取得します。
11. ライブ エンコーダーを約 10 分間実行します。
12. ライブ イベントを停止します。 
13. [Azure Media Player](https://aka.ms/azuremediaplayer) などのプレーヤーを使用してアーカイブされた資産を再生し、すべての品質レベルで目に見える異常がないことを確認します。 または、ライブ セッション中にプレビュー URL を使用して再生し、検証します。
14. 資産 ID、ライブ アーカイブの公開済みストリーミング URL、およびライブ エンコーダーから使用されている設定とバージョンを記録します。
15. 各サンプルの作成後に、Live Event の状態をリセットします。
16. エンコーダーでサポートされているすべての構成について、手順 5 - 15 を繰り返します (必要に応じて、広告信号、キャプション、または異なるエンコード速度を使用します)。

### <a name="live-encoding-live-event-verification"></a>ライブ エンコード Live Event 検証

1. Media Services アカウントで、**ストリーミング エンドポイント**が実行されていることを確認します。 
2. **ライブ エンコード** Live Event を作成し、起動します。 <br/> 詳細については、[ライブ イベントの状態と課金](live-event-states-billing.md)に関するページを参照してください。
3. 取り込み URL を取得し、シングルビットレート ライブ ストリームを Media Services にプッシュするようにエンコーダーを構成します。
4. プレビュー URL を取得し、それを使用して、エンコーダーからの入力が実際に受信されていることを確認します。
5. 新しい**資産**オブジェクトを作成します。
6. **ライブ出力**を作成し、作成した資産の名前を使用します。
7. 組み込みの**ストリーミング ポリシー** タイプで**ストリーミング ロケーター**を作成します。
8. 使用する URL を返すためのパスを**ストリーミング ロケーター**に列挙します。
9. ストリーミングする**ストリーミング エンドポイント**のホスト名を取得します。
10. 手順 8.の URL と手順 9 のホスト名を組み合わせて、完全な URL を取得します。
11. ライブ エンコーダーを約 10 分間実行します。
12. ライブ イベントを停止します。
13. [Azure Media Player](https://aka.ms/azuremediaplayer) などのプレーヤーを使用してアーカイブされた資産を再生し、すべての品質レベルについて目に見える異常がないことを確認します。 または、ライブ セッション中にプレビュー URL を使用して再生し、検証します。
14. 資産 ID、ライブ アーカイブの公開済みストリーミング URL、およびライブ エンコーダーから使用されている設定とバージョンを記録します。
15. 各サンプルの作成後に、Live Event の状態をリセットします。
16. エンコーダーでサポートされているすべての構成について、手順 5 - 15 を繰り返します (必要に応じて、広告信号、キャプション、または異なるエンコード速度を使用します)。

### <a name="longevity-verification"></a>持続時間の検証

[パススルー ライブ イベント検証](#pass-through-live-event-verification)と同じ手順に従います。ただし、手順 11 を除きます。 <br/>10 分ではなく、1 週間以上ライブ エンコーダーを実行します。 再生に目に見える異常がないことを確認するため、[Azure Media Player](https://aka.ms/azuremediaplayer) などのプレーヤーを使用して、時折ライブ ストリーミング (またはアーカイブされた資産) を監視します。

### <a name="email-your-recorded-settings"></a>記録した設定をメールで送信する

最後に、すべての自己検証チェックに成功したことを知らせる通知として、amshelp@microsoft.com 宛の電子メールで記録した設定とライブ アーカイブ パラメーターを Azure Media Services に送信します。 また、すべてのフォローアップに関する自分の連絡先情報を含めます。 このプロセスに関して質問がある場合は、Azure Media Services チームに問い合わせることができます。

## <a name="next-steps"></a>次の手順

[Media Services v3 を使用したライブ ストリーミング](live-streaming-overview.md)

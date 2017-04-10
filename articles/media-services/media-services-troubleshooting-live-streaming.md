---
title: "ライブ ストリーミングのトラブルシューティング ガイド | Microsoft Docs"
description: "このトピックでは、ライブ ストリーミングに関する問題のトラブルシューティング方法について推奨事項を説明します。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 3a7f6c1d-ce57-4fa4-a7a6-edb526b3ffbf
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 0c77ee0f612c1cbef551a129a22cf3f125e6f29d
ms.openlocfilehash: 69fa0a7802ca39f86ada0af47b7a99e56436f973
ms.lasthandoff: 01/11/2017


---
# <a name="troubleshooting-guide-for-live-streaming"></a>ライブ ストリーミングのトラブルシューティング ガイド
このトピックでは、一部のライブ ストリーミングの問題のトラブルシューティング方法に関する推奨事項を説明します。

## <a name="issues-related-to-on-premises-encoders"></a>オンプレミスのエンコーダーに関連する問題
このセクションでは、ライブ エンコードが有効になっている AMS チャネルに、単一ビットレートのストリームを送信するよう構成したオンプレミスのエンコーダーに関連する問題のトラブルシューティング方法について推奨事項を説明します。

### <a name="problem-would-like-to-see-logs"></a>問題: ログを確認したい
* **潜在的な問題**: 問題のデバッグに役立つエンコーダーのログが見つかりません。
  
  * **Telestream Wirecast**: 通常、ログは C:\Users\{username}\AppData\Roaming\Wirecast\ にあります。 
  * **Elemental Live**: 管理ポータルでログへのリンクを見つけることができます。 **[統計]** をクリックし、**[ログ]** をクリックします。 **[ログ ファイル]** ページに、すべての LiveEvent 項目のログの一覧が表示されます。現在のセッションに一致するものを選択します。 
  * **Flash Media Live Encoder**: **[エンコーディング ログ]** タブに移動すると、**[ログ ディレクトリ...]** があります。

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>問題: プログレッシブ ストリームを出力するためのオプションがない
* **潜在的な問題**: 使用しているエンコーダーでは、自動的にインターレースが解除されません。 
  
    **トラブルシューティングの手順**: エンコーダーのインターフェイス内でインターレースを解除するオプションを探します。 インターレースの解除を有効にしたら、プログレッシブ出力の設定をもう一度確認します。 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>問題: エンコーダーの出力設定をいくつか試したが、まだ接続できない
* **潜在的な問題**: Azure のエンコード チャネルが正しくリセットされませんでした。 
  
    **トラブルシューティングの手順**: エンコーダーが AMS にプッシュしなくなったことを確認してから、チャネルを停止してリセットします。 再度実行されたら、新しい設定でエンコーダーを接続します。 それでも問題が解決しない場合は、まったく新しいチャネルを作成します。場合によっては、何回か失敗した後に、チャネルが破損している可能性があります。  
* **潜在的な問題**: GOP サイズまたはキーフレームの設定が最適でない 
  
    **トラブルシューティングの手順**: 推奨する GOP サイズまたはキーフレームの間隔は 2 秒です。 エンコーダーには、この設定の計算にフレーム数を使用するものと、秒を使用するものがあります。 例: 30 fps を出力する場合、GOP サイズは 60 フレームとなり、これは 2 秒に相当します。  
* **潜在的な問題**: 閉じたポートが、ストリームをブロックしています。 
  
    **トラブルシューティングの手順**: RTMP を使用してデータをストリーミングしている場合は、ファイアウォールまたはプロキシ、あるいはその両方の設定で、送信ポート 1935 と 1936 が開いていることを確認します。 RTP ストリームを使用している場合は、送信ポート 2010 が開いていることを確認します。 

### <a name="problem-when-configuring-the-encoder-to-stream-with-the-rtp-protocol-there-is-no-place-to-enter-a-host-name"></a>問題: ストリームで RTP プロトコルを使用するようエンコーダーを構成する際、ホスト名を入力する場所がない
* **潜在的な問題**: 多くの RTP エンコーダーではホスト名は許可されていません。IP アドレスを取得する必要があります。  
  
    **トラブルシューティングの手順**: IP アドレスを検索するには、任意のコンピューターでコマンド プロンプトを開きます。 これを Windows で行う場合は、[ファイル名を指定して実行]\(Win + R) を開き、「cmd」と入力して開きます。  
  
    コマンド プロンプトが開いたら、「Ping [AMS のホスト名]」を入力します。 
  
    次の例で示すように、Azure 取り込み URL からポート番号を省略することでホスト名を取得できます。 
  
    rtp://test2-amstest009.rtp.channel.mediaservices.windows.net:2010/ 
  
    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

> [!NOTE]
> このトラブルシューティングの手順に従っても正常にストリーミングできない場合は、Azure Portal を使用して、サポート チケットを送信してください。
> 
> 

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



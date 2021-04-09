---
title: ライブ ストリーミングのトラブルシューティング ガイド | Microsoft Docs
description: この記事では、Azure Media Services のライブ ストリーミングの問題のトラブルシューティング方法に関する推奨事項を説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 1b7a7ec746f5400fe65e3e1db88ae61e97ae710a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009047"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>ライブ ストリーミングのトラブルシューティング ガイド

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

この記事では、一部のライブ ストリーミングの問題のトラブルシューティング方法に関する推奨事項を説明します。

## <a name="issues-related-to-on-premises-encoders"></a>オンプレミスのエンコーダーに関連する問題
このセクションでは、ライブ エンコードが有効になっている AMS チャネルに、単一ビットレートのストリームを送信するよう構成したオンプレミスのエンコーダーに関連する問題のトラブルシューティング方法について推奨事項を説明します。

### <a name="problem-would-like-to-see-logs"></a>問題: ログを確認したい
* **潜在的な問題**:問題のデバッグに役立つ可能性のあるエンコーダーのログが見つかりません。
  
  * **Telestream Wirecast**: 通常、ログは C:\Users\{ユーザー名}\AppData\Roaming\Wirecast\ で見つけることができます。 
  * **Elemental Live**: 管理ポータルでログへのリンクを見つけることができます。 **[統計]** をクリックし、 **[ログ]** をクリックします。 **[ログ ファイル]** ページに、すべての LiveEvent 項目のログの一覧が表示されます。現在のセッションに一致するものを選択します。 
  * **Flash Media Live Encoder**: **[エンコーディング ログ]** タブに移動することにより **[ログ ディレクトリ...]** を見つけることができます。

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>問題: プログレッシブ ストリームを出力するためのオプションがない
* **潜在的な問題**:使用されているエンコーダーでは、インターレースが自動的には解除されません。 
  
    **トラブルシューティングの手順**: エンコーダーのインターフェイス内でインターレース解除オプションを探します。 インターレースの解除を有効にしたら、プログレッシブ出力の設定をもう一度確認します。 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>問題: エンコーダーの出力設定をいくつか試したが、まだ接続できない
* **潜在的な問題**:Azure のエンコーディング チャネルが正しくリセットされませんでした。 
  
    **トラブルシューティングの手順**: エンコーダーが AMS にプッシュしなくなったことを確認してから、チャネルを停止してリセットします。 再度実行されたら、新しい設定でエンコーダーを接続します。 それでも問題が解決しない場合は、まったく新しいチャネルを作成します。場合によっては、何回か失敗した後に、チャネルが破損している可能性があります。  
* **潜在的な問題**:GOP サイズまたはキー フレームの設定が最適でない 
  
    **トラブルシューティングの手順**: 推奨される GOP サイズまたはキーフレームの間隔は 2 秒です。 エンコーダーには、この設定の計算にフレーム数を使用するものと、秒を使用するものがあります。 次に例を示します。30 fps を出力する場合、GOP サイズは 60 フレーム (これは 2 秒に相当します) になります。  
* **潜在的な問題**:閉じられたポートがストリームをブロックしている 
  
    **トラブルシューティングの手順**: RTMP 経由でストリーミングしている場合は、ファイアウォールまたはプロキシ、あるいはその両方の設定をチェックして、送信ポート 1935 および 1936 が開いていることを確認します。 

> [!NOTE]
> このトラブルシューティングの手順に従っても正常にストリーミングできない場合は、Azure Portal を使用して、サポート チケットを送信してください。
> 
> 

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


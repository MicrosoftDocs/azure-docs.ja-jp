---
title: オンプレミス エンコーダー パートナーになる - Azure Media Services
description: この記事では、オンプレミス ライブ ストリーミング エンコーダーを検証する方法について説明します。
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 08/31/2020
ms.topic: how-to
ms.service: media-services
ms.openlocfilehash: 1383b6afac9ad2332169328ef71981e23e86bdcc
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297452"
---
# <a name="how-to-verify-your-on-premises-live-streaming-encoder"></a>オンプレミス ライブ ストリーミング エンコーダーを検証する方法

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services は、Azure Media Services オンプレミス エンコーダー パートナーとして企業のお客様にエンコーダーを推奨することで、製品の販売を促進します。 オンプレミス エンコーダー パートナーになるには、オンプレミス エンコーダーと Media Services との互換性を確認する必要があります。 これを行うには、次の検証を実行します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="pass-through-live-event-verification"></a>パススルー Live Event 検証

1. Media Services アカウントで、**ストリーミング エンドポイント**が実行されていることを確認します。 
2. **パススルー** Live Event を作成し、起動します。 <br/> 詳細については、[ライブ イベントの状態と課金](live-event-states-billing.md)に関するページを参照してください。
3. 取り込み URL を取得し、URL を使用してマルチビットレート ライブ ストリームを Media Services に送信するようにオンプレミス エンコーダーを構成します。
4. プレビュー URL を取得し、それを使用して、エンコーダーからの入力が実際に受信されていることを確認します。
5. 新しい**アセット**オブジェクトを作成します。
6. **ライブ出力**を作成し、作成したアセットの名前を使用します。
7. 組み込みの**ストリーミング ポリシー** タイプで**ストリーミング ロケーター**を作成します。
8. 使用する URL を返すためのパスを**ストリーミング ロケーター**に列挙します。
9. ストリーミングする**ストリーミング エンドポイント**のホスト名を取得します。
10. 手順 8.の URL と手順 9 のホスト名を組み合わせて、完全な URL を取得します。
11. ライブ エンコーダーを約 10 分間実行します。
12. ライブ イベントを停止します。 
13. [Azure Media Player](https://aka.ms/azuremediaplayer) などのプレーヤーを使用してアーカイブされたアセットを再生し、すべての品質レベルで目に見える異常がないことを確認します。 または、ライブ セッション中にプレビュー URL を使用して再生し、検証します。
14. アセット ID、ライブ アーカイブの公開済みストリーミング URL、およびライブ エンコーダーから使用されている設定とバージョンを記録します。
15. 各サンプルの作成後に、Live Event の状態をリセットします。
16. エンコーダーでサポートされているすべての構成について、手順 5 - 15 を繰り返します (必要に応じて、広告信号、キャプション、または異なるエンコード速度を使用します)。

## <a name="live-encoding-live-event-verification"></a>ライブ エンコード Live Event 検証

1. Media Services アカウントで、**ストリーミング エンドポイント**が実行されていることを確認します。 
2. **ライブ エンコード** Live Event を作成し、起動します。 <br/> 詳細については、[ライブ イベントの状態と課金](live-event-states-billing.md)に関するページを参照してください。
3. 取り込み URL を取得し、シングルビットレート ライブ ストリームを Media Services にプッシュするようにエンコーダーを構成します。
4. プレビュー URL を取得し、それを使用して、エンコーダーからの入力が実際に受信されていることを確認します。
5. 新しい**アセット**オブジェクトを作成します。
6. **ライブ出力**を作成し、作成したアセットの名前を使用します。
7. 組み込みの**ストリーミング ポリシー** タイプで**ストリーミング ロケーター**を作成します。
8. 使用する URL を返すためのパスを**ストリーミング ロケーター**に列挙します。
9. ストリーミングする**ストリーミング エンドポイント**のホスト名を取得します。
10. 手順 8.の URL と手順 9 のホスト名を組み合わせて、完全な URL を取得します。
11. ライブ エンコーダーを約 10 分間実行します。
12. ライブ イベントを停止します。
13. [Azure Media Player](https://aka.ms/azuremediaplayer) などのプレーヤーを使用してアーカイブされたアセットを再生し、すべての品質レベルについて目に見える異常がないことを確認します。 または、ライブ セッション中にプレビュー URL を使用して再生し、検証します。
14. アセット ID、ライブ アーカイブの公開済みストリーミング URL、およびライブ エンコーダーから使用されている設定とバージョンを記録します。
15. 各サンプルの作成後に、Live Event の状態をリセットします。
16. エンコーダーでサポートされているすべての構成について、手順 5 - 15 を繰り返します (必要に応じて、広告信号、キャプション、または異なるエンコード速度を使用します)。

## <a name="longevity-verification"></a>持続時間の検証

[パススルー ライブ イベント検証](#pass-through-live-event-verification)と同じ手順に従います。ただし、手順 11 を除きます。 <br/>10 分ではなく、1 週間以上ライブ エンコーダーを実行します。 再生に目に見える異常がないことを確認するため、[Azure Media Player](https://aka.ms/azuremediaplayer) などのプレーヤーを使用して、時折ライブ ストリーミング (またはアーカイブされたアセット) を監視します。

## <a name="email-your-recorded-settings"></a>記録した設定をメールで送信する

最後に、すべての自己検証チェックに成功したことを知らせる通知として、amshelp@microsoft.com 宛の電子メールで記録した設定とライブ アーカイブ パラメーターを Azure Media Services に送信します。 また、すべてのフォローアップに関する自分の連絡先情報を含めます。 このプロセスに関して質問がある場合は、Azure Media Services チームに問い合わせることができます。

## <a name="see-also"></a>関連項目

[テスト済みのオンプレミス エンコーダー](recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>次のステップ

[Media Services v3 を使用したライブ ストリーミング](live-streaming-overview.md)

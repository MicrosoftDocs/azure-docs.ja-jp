---
title: パートナー センターでコマーシャル マーケットプレース プログラムのサポートを受ける
description: パートナー センターでのコマーシャル マーケットプレース プログラムのサポート オプションについて説明します。サポート リクエストの提出方法も記載します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 01/19/2020
ms.openlocfilehash: a1726b29c153bf680d29fe821ac34aa958064335
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879243"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>パートナー センターでのコマーシャル マーケットプレース プログラムのサポート

マイクロソフトでは、さまざまな製品やサービスについてサポートを提供します。 適切でタイムリーな回答を得るには、正しいサポート チームを見つけることが重要です。 次のシナリオを検討して、お問い合わせを適切なチームに送るのに役立ててください。

- あなたが発行元であり、お客様から質問を受けた場合、お客様には  [Azure portal](https://portal.azure.com/) のサポート リンクからサポートを依頼するようにお願いしてください。
- 発行元の方で、Azure で実行されているアプリケーションでセキュリティの問題を検出した場合は、「[セキュリティ イベント サポート チケットをログに記録する方法](../security/fundamentals/event-support-ticket.md)」を参照してください。 発行元は、Azure Marketplace で提供しているソフトウェアやサービス オファリングのセキュリティ インシデントや脆弱性を含め、疑わしいセキュリティ イベントをできるだけ早い機会に報告しなければなりません。
- あなたが発行元であり、ご自分のアプリやサービスに関する質問がある場合は、次のサポート オプションを確認してください。

## <a name="get-help-or-open-a-support-ticket"></a>ヘルプを使用する、またはサポート チケットを開く

1. 職場アカウントでサインインします。 (まだ[パートナー センター アカウントを作成](partner-center-portal/create-account.md)していない場合は、作成する必要があります)。

1. ページの右上にあるメニューで **[サポート]** アイコンを選択します。 ページの右側に **[ヘルプとサポート]** ウィンドウが表示されます。

1. コマーシャル マーケットプレースに関するヘルプの場合は、 **[Commercial Marketplace]\(コマーシャル マーケットプレース\)** を選択します。

   ![サポート ドロップダウン メニュー](./media/support/commercial-marketplace-support-pane.png)

1. **[Problem summary]\(問題の概要\)** ボックスに、問題の簡単な説明を入力します。

1. **[問題の種類]** ボックスで、次のいずれかの操作を行います。

    - **オプション 1**:次のようなキーワードを入力します。Marketplace、Azure アプリ、SaaS オファー、アカウント管理、見込み客管理、デプロイの問題、支払い、共同販売オファーの移行など。 次に、表示される推奨リストから問題の種類を選択します。

    - **オプション 2**: **[カテゴリ]** の一覧から **[トピックの参照]** を選択し、 **[Commercial Marketplace]\(コマーシャル マーケットプレース\)** を選択します。 次に、適切な **トピック** と **サブトピック** を選択します。

1. 目的のトピックを見つけた後、 **[ソリューションの確認]** を選択します。

    ![次のステップ](./media/support/next-step.png)

次のオプションが表示されます。

- 別のトピックを選択するには、 **[Select a different issue]\(別の問題を選択する\)** をクリックします。
- 問題の解決に役立てるために、推奨される手順とドキュメントがあれば確認します。

    ![推奨されるソリューション](./media/support/recommended-solutions.png)

セルフ ヘルプで回答が見つからない場合は、 **[問題の詳細を指定]** を選択します。 解決プロセスを高速化するためにすべての必須フィールドに入力して、 **[送信]** を選択します。

>[!Note]
>パートナー センターにサインインしていない場合は、チケットを作成する前にサインインが必要になることがあります。

## <a name="track-your-existing-support-requests"></a>既存のサポート リクエストを追跡する

オープン状態とクローズ状態のチケットを確認するには、左側のナビゲーション メニューで、 **[Commercial Marketplace]\(コマーシャル マーケットプレース\)**  >  **[サポート]** を選択します。

## <a name="record-issue-details-with-a-har-file"></a>HAR ファイルに問題の詳細を記録する

サポート エージェントがお客様の問題をトラブルシューティングできるように、HTTP アーカイブ形式 (HAR) ファイルをサポート チケットに添付することを検討してください。 HAR ファイルは、Web ブラウザーのネットワーク要求のログです。

> [!WARNING]
> HAR ファイルには、パートナー センター アカウントに関する機密データが記録される場合があります。

### <a name="microsoft-edge-and-google-chrome"></a>Microsoft Edge と Google Chrome

**Microsoft Edge** または **Google Chrome** を使用して HAR ファイルを生成するには:

1. 問題が発生している Web ページに移動します。
2. ウィンドウの右上隅にある省略記号アイコンを選択し、 **[その他のツール]**  >  **[開発者ツール]** を選択します。 ショートカットとして F12 キーを押す方法もあります。
3. [開発者ツール] ペインで、 **[ネットワーク]** タブを選択します。
4. **[Stop recording network log]\(ネットワーク ログの記録を停止する\)** と **[クリア]** を選択して既存のログを削除します。 [記録] アイコンは灰色に変わります。

    ![Microsoft Edge または Google Chrome で既存のログを削除する方法](media/support/chromium-stop-clear-session.png)

5. **[Record network log]\(ネットワーク ログの記録\)** を選択して記録を開始します。 記録を開始すると、[記録] アイコンが赤に変わります。

    ![Microsoft Edge または Google Chrome で記録を開始する方法](media/support/chromium-start-session.png)

6. トラブルシューティングする問題を再現します。
7. 問題を再現したら、 **[Stop recording network log]\(ネットワーク ログの記録を停止する\)** を選択します。
8. 下向き矢印アイコンが付いた **[Export HAR]\(HAR のエクスポート\)** を選択し、ファイルを保存します。

    ![Microsoft Edge または Google Chrome で HAR ファイルをエクスポートする方法](media/support/chromium-network-export-har.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

**Mozilla Firefox** を使用して HAR ファイルを生成するには:

1. 問題が発生している Web ページに移動します。
1. ウィンドウの右上隅にある省略記号アイコンを選択し、 **[Web Developer]**  >  **[Toggle Tools]\(ツールの切り替え\)** を選択します。 ショートカットとして F12 キーを押す方法もあります。
1. **[ネットワーク]** タブを選択し、次に **[クリア]** を選択して既存のログを削除します。

    ![Mozilla Firefox で既存のログを削除する方法](media/support/firefox-clear-session.png)

1. トラブルシューティングする問題を再現します。
1. 問題を再現したら、 **[HAR Export/Import]\(HAR のエクスポートまたはインポート\)**  >  **[Save All As HAR]\(すべてを HAR として保存\)** を選択します。

    ![Mozilla Firefox で HAR ファイルをエクスポートする方法](media/support/firefox-network-export-har.png)

### <a name="apple-safari"></a>Apple Safari

**Safari** を使用して HAR ファイルを生成するには:

1. Safari で開発者ツールを有効にします。 **[Safari]**  >  **[Preferences]\(基本設定\)** を選択します。 **[Advanced]\(詳細\)** タブに移動し、 **[Show Develop menu in menu bar]\(メニュー バーに開発メニューを表示する\)** を選択します。
1. 問題が発生している Web ページに移動します。
1. **[Develop]\(開発\)** を選択し、 **[Show Web Inspector]\(Web Inspector を表示\)** を選択します。
1. **[Network]\(ネットワーク\)** タブを選択し、次に **[Clear Network Items]\(ネットワーク項目のクリア\)** を選択して既存のログを削除します。

    ![Safari で既存のログを削除する方法](media/support/safari-clear-session.png)

1. トラブルシューティングする問題を再現します。
1. 問題を再現した後、 **[Export]\(エクスポート\)** を選択し、ファイルを保存します。

    ![Safari で HAR ファイルをエクスポートする方法](media/support/safari-network-export-har.png)

## <a name="next-steps"></a>次のステップ

- [商業マーケットプレースで既存のオファーを更新する](partner-center-portal/update-existing-offer.md)
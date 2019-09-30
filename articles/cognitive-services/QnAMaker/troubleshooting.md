---
title: トラブルシューティング - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker サービスについてきわめて多く寄せられる質問を精選した一覧です。サービスの採用にかかる期間を短縮し、成果を上げるためにお役立てください。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 95b4978b66accce0182e58493cb9d600dbe5e35f
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277481"
---
# <a name="troubleshooting-for-qna-maker"></a>QnA Maker のトラブルシューティング

QnA Maker サービスについてきわめて多く寄せられる質問を精選した一覧です。サービスの採用にかかる期間を短縮し、成果を上げるためにお役立てください。


## <a name="how-to-get-the-qnamaker-service-hostname"></a>QnA Maker サービスのホスト名を取得する方法

QnA Maker サービスのホスト名は、デバッグ目的で QnA Maker サポートや UserVoice に問い合わせる場合に役立ちます。 ホスト名は、 https:// *{ホスト名}* .azurewebsites.net という形式の URL です。
    
1. [Azure portal](https://portal.azure.com) で QnA Maker サービス (リソース グループ) に移動します。

    ![QnAMaker の Azure リソース グループ (Azure portal)](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. QnA Maker リソースに関連付けられている App Service を選択します。 通常、名前は同じです。

     ![QnAMaker の App Service を選択する](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. ホスト名 URL は [概要] セクションで確認できます。

    ![QnA Maker のホスト名](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>QnA Maker ポータルでヘルプ ボットを使用する

QnA Maker では、QnA Maker ポータルで役に立つ**ヘルプ** ボットが提供されています。 ヘルプ ボットは、すべての Web ページで利用できます。 ボットでは、QnA Maker を使用して回答が提供されます。ボットには [C# Bot Framework コード プロジェクト](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support)が用意されているので、独自の応答ボットをすばやく稼働させることができます。 

![![QnA Maker では、QnA Maker ポータルで役に立つ**ヘルプ** ボットが提供されています。](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>ナレッジ ベースの管理

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>自分の QnA Maker の一部を誤って削除しました。どうしたらいいでしょうか。 

質問と回答のペア、ファイル、URL、カスタムの質問と回答、ナレッジ ベース、Azure リソースを含め、削除はすべて永続的です。 ナレッジ ベースの一部を削除する前に、 **[設定]** ページから確実にナレッジ ベースをエクスポートしてください。 

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>指定した URL/ファイルから質問とその回答のペアが抽出されません。なぜでしょうか。

QnA Maker は、有効な FAQ URL であっても、一部の Q&A (QnA) コンテンツを自動抽出できない場合があります。 そのような場合は、QnA コンテンツを .txt ファイルに貼り付けて、ツールによる取り込みが可能かどうかを確認できます。 または、[QnA Maker ポータル](https://qnamaker.ai)経由でナレッジ ベースに編集という形でコンテンツを追加することもできます。

### <a name="how-large-a-knowledge-base-can-i-create"></a>作成できるナレッジ ベースの最大サイズはどれくらいですか。

ナレッジ ベースのサイズは、QnA Maker サービスの作成時に選択した Azure Search の SKU により異なります。 詳細については、[こちら](./Tutorials/choosing-capacity-qnamaker-deployment.md)を参照してください。

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>新しいナレッジ ベースを作成しようとしたときに、ドロップダウンに選択肢が表示されません。なぜでしょうか?

まだ Azure に QnA Maker サービスが作成されていません。 その方法については、[こちら](./How-To/set-up-qnamaker-service-azure.md)を参照してください。

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>複数人でナレッジ ベースを共有するにはどうすればよいですか?

共有は、QnA Maker サービス レベルで行われます。つまり、サービス内のすべてのナレッジ ベースが共有されます。 ナレッジ ベースの共同作業の方法については、[こちら](./How-To/collaborate-knowledge-base.md)を参照してください。

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>ナレッジ ベースを変更するために、同じ AAD テナントに含まれない共同作成者とナレッジ ベースを共有できますか。 

共有は Azure ロールベースのアクセス制御 (RBAC) に基づきます。 Azure で他のユーザーと _任意の_ リソースを共有できる場合、QnA Maker を共有することもできます。

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>5 つの QnAMaker ナレッジ ベースがある 1 つの App Service プランがある場合、 5 人のユーザーに、それぞれが 1 つだけの QnAMaker ナレッジ ベースにアクセスできるように、読み取り/書き込み権限を割り当てることができますか。

個々のナレッジ ベースではなく、QnAMaker サービス全体を共有できます。

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>適切な一致が見つからなかったときの既定のメッセージを変更するにはどうすればよいですか?

既定のメッセージは、App Service の設定に含まれています。
- Azure portal の App Service リソースに移動します

![QnA Maker App Service](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- **[設定]** オプションをクリックします。

![QnA Maker App Service の設定](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- **[DefaultAnswer]** という設定の値を変更します。
- App Service を再起動します。

![QnA Maker App Service の再起動](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>SharePoint リンクが抽出されません。なぜでしょうか?

詳細については、「[Data source locations (データ ソースの場所)](./Concepts/data-sources-supported.md#data-source-locations)」を参照してください。

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>ナレッジ ベースに対して行った更新が発行時に反映されません。 なぜでしょうか?

編集内容を公開するには、テーブルの更新、テスト、設定のいずれであってもすべて保存する必要があります。 編集操作後は、必ず  **[Save and train]** \(保存してトレーニング\) ボタンをクリックしてください。

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>ナレッジ ベースは、リッチ データやマルチメディアに対応していますか?

ナレッジ ベースは Markdown に対応しています。 ただし、URL からの自動抽出では、HTML から Markdown への変換機能が制限されています。 Markdown の機能をすべて使用するには、テーブル内のコンテンツを直接編集するか、リッチ コンテンツと共にナレッジ ベースをアップロードしてください。

画像やビデオなどのマルチメディアには、現時点では対応していません。

### <a name="does-qna-maker-support-non-english-languages"></a>QnA Maker では英語以外の言語もサポートされますか。

詳細については、[サポートされている言語](./Overview/languages-supported.md)に関するページを参照してください。

多言語コンテンツが含まれる場合は、言語ごとにサービスを作成してください。

## <a name="manage-service"></a>サービスの管理

### <a name="when-should-i-restart-my-app-service"></a>いつアプリ サービスを再起動するべきですか。 

**[ユーザー設定]** [ページ](https://www.qnamaker.ai/UserSettings)の **[エンドポイント キー]** で、ナレッジ ベースのバージョン値の隣に注意のアイコンが表示されているときは、アプリ サービスを更新してください。

### <a name="i-deleted-my-existing-search-service-how-can-i-fix-this"></a>既存の検索サービスを削除してしまいました。 どうしたらいいですか。

Azure Search インデックスを削除した場合、この操作は最終的なものであり、インデックスを復旧することはできません。 

### <a name="i-deleted-my-testkb-index-in-my-search-service-how-can-i-fix-this"></a>検索サービスの `testkb` インデックスを削除してしまいました。 どうしたらいいですか。 

古いデータを回復することはできません。 新しい QnA Maker リソースを作成し、ナレッジ ベースを再度作成してください。

### <a name="when-should-i-refresh-my-endpoint-keys"></a>エンドポイント キーは、どのようなときに更新すべきでしょうか?

エンドポイント キーの更新は、鍵漏えいのおそれがある場合に行ってください。

### <a name="can-i-use-the-same-azure-search-resource-for-knowledge-bases-using-multiple-languages"></a>複数の言語を使用したナレッジ ベースに同じ Azure Search リソースを使用できますか。

複数の言語と複数のナレッジ ベースを使用するには、言語ごとに QnA Maker リソースを作成する必要があります。 これにより、言語ごとに個別の Azure Search サービスが作成されます。 1 つの Azure Search サービスに異なる言語のナレッジ ベースを混在させると、結果の関連性が低下します。

### <a name="how-can-i-change-the-name-of-the-azure-search-resource-used-by-qna-maker"></a>QnA Maker で使用される Azure Search リソースの名前を変更するには、どうすればよいですか。

Azure Search リソースの名前は、QnA Maker リソース名の後にいくつかのランダムな文字が追加されたものです。 そのため、QnA Maker の複数の Search リソースを識別することが難しくなります。 別の Azure Search サービスを作成し (任意の方法で名前を付け)、それを QnA サービスに接続します。 手順は、[Azure Search をアップグレードする](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-search-service)ために必要な手順と同様です。

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>QnA Maker から `Runtime core is not initialized,` が返された場合は、どのように解決すればよいですか?

App Service のディスク領域がいっぱいになっている可能性があります。 ディスク領域を修正する手順は次のとおりです。

1. [Azure portal](https://portal.azure.com) で、QnA Maker の App Service を選択し、サービスを停止します。
1. この App Service で、 **[開発ツール]** 、 **[高度なツール]** 、 **[移動]** の順に選択します。 これで新しいブラウザー ウィンドウが開きます。
1. **[デバッグ コンソール]** 、 **[CMD]** の順に選択してコマンドライン ツールを開きます。 
1. _site/wwwroot/Data/QnAMaker/_ ディレクトリに移動します。
1. 名前が `rd` で始まるフォルダーをすべて削除します。 

    次の項目は**削除しないでください**。

    * KbIdToRankerMappings.txt ファイル
    * EndpointSettings.json ファイル
    * EndpointKeys フォルダー 

1. App Service を開始します。
1. ナレッジ ベースにアクセスして動作していることを確認します。 


## <a name="integrate-with-other-services-including-bots"></a>ボットなどの他のサービスとの統合

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>QnA Maker を利用するには Bot Framework を使う必要がありますか?

いいえ。QnA Maker に関して [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) を使用する必要はありません。 ただし、QnA Maker は  [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) に用意されているいくつかのテンプレートの 1 つとして提供されています。 Bot Service では、Microsoft Bot Framework を使用してインテリジェントなボットを迅速に開発し、サーバーレス環境で実行することができます。

### <a name="how-can-i-create-a-new-bot-with-qna-maker"></a>QnA Maker で新しいボットを作成するにはどうすればよいですか?

Azure Bot Service でボットを作成するには、[こちら](./Tutorials/create-qna-bot.md)のドキュメントの手順に従ってください。

### <a name="how-do-i-use-a-different-knowledge-base-with-an-existing-azure-bot-service"></a>既存の Azure Bot Service で別のナレッジ ベースを使用する方法

次のナレッジ ベースに関する情報が必要です。

* ナレッジ ベース ID。
* ナレッジ ベースの公開されたエンドポイント ホスト名 - 公開後に **[設定]** ページで見つかります。
* ナレッジ ベースの公開されたエンドポイント キー - 公開後に **[設定]** ページで見つかります。 

この情報を使用して、Azure portal でご利用のボットのアプリ サービスにアクセスします。 **[設定] -> [構成] -> [アプリケーション設定]** で、これらの値を変更します。  

ナレッジ ベースのエンドポイント キーは、ABS サービスで `QnAAuthkey` としてラベルが付けられます。 

### <a name="can-two-or-more-client-applications-share-a-knowledge-base"></a>2 つ以上のクライアント アプリケーションでナレッジ ベースを共有できますか? 

はい。ナレッジ ベースには、任意の数のクライアントからクエリを実行できます。 ナレッジ ベースからの応答が低速であるか、タイムアウトになっている場合は、ナレッジ ベースに関連付けられているアプリ サービスのサービス レベルをアップグレードすることを検討してください。

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>QnA Maker サービスを Web サイトに埋め込むにはどうすればよいですか?

Web サイトに QnA Maker サービスを Web チャット コントロールとして埋め込むには、次の手順に従ってください。

1. [こちら](./Tutorials/create-qna-bot.md)の手順に従って、FAQ ボットを作成します。
2. [こちら](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)の手順に従って、Web チャットを有効にします。

## <a name="data-storage"></a>データ ストレージ

### <a name="what-data-is-stored-and-where-is-it-stored"></a>どのようなデータが、どこに保存されますか。 

QnA Maker サービスを作成するときに、Azure リージョンを選択しました。 ナレッジ ベースとログ ファイルが、このリージョンに格納されます。 

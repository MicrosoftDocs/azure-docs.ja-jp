---
title: FAQ - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker サービスについてきわめて多く寄せられる質問を精選した一覧です。サービスの採用にかかる期間を短縮し、成果を上げるためにお役立てください。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/11/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 8104497a1808aa4e92d62f45e37525dba2f47742
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607171"
---
# <a name="frequently-asked-questions-for-qna-maker"></a>QnA Maker に関してよく寄せられる質問

QnA Maker サービスについてきわめて多く寄せられる質問を精選した一覧です。サービスの採用にかかる期間を短縮し、成果を上げるためにお役立てください。

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>QnA Maker ポータルでヘルプ ボットを使用する

QnA Maker では、QnA Maker ポータルで役に立つ**ヘルプ** ボットが提供されています。 ヘルプ ボットは、すべての Web ページで利用できます。 ボットでは、QnA Maker を使用して回答が提供されます。ボットには [C# Bot Framework コード プロジェクト](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot)が用意されているので、独自の応答ボットをすばやく稼働させることができます。 

![![QnA Maker では、QnA Maker ポータルで役に立つ**ヘルプ** ボットが提供されています。](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>ナレッジ ベースの管理

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>自分の QnA Maker の一部を誤って削除しました。どうしたらいいでしょうか。 

質問と回答のペア、ファイル、URL、カスタムの質問と回答、ナレッジ ベース、Azure リソースを含め、削除はすべて永続的です。 ナレッジ ベースの一部を削除する前に、**[設定]** ページから確実にナレッジ ベースをエクスポートしてください。 

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

### <a name="when-should-i-refresh-my-endpoint-keys"></a>エンドポイント キーは、どのようなときに更新すべきでしょうか?

エンドポイント キーの更新は、鍵漏えいのおそれがある場合に行ってください。

### <a name="can-i-use-the-same-azure-search-resource-for-knowledge-bases-using-multiple-languages"></a>複数の言語を使用したナレッジ ベースに同じ Azure Search リソースを使用できますか。

複数の言語と複数のナレッジ ベースを使用するには、言語ごとに QnA Maker リソースを作成する必要があります。 これにより、言語ごとに個別の Azure Search サービスが作成されます。 1 つの Azure Search サービスに異なる言語のナレッジ ベースを混在させると、結果の関連性が低下します。

### <a name="how-can-i-change-the-name-of-the-azure-search-resource-used-by-qna-maker"></a>QnA Maker で使用される Azure Search リソースの名前を変更するには、どうすればよいですか。

Azure Search リソースの名前は、QnA Maker リソース名の後にいくつかのランダムな文字が追加されたものです。 そのため、QnA Maker の複数の Search リソースを識別することが難しくなります。 別の Azure Search サービスを作成し (任意の方法で名前を付け)、それを QnA サービスに接続します。 手順は、[Azure Search をアップグレードする](How-To/upgrade-qnamaker-service.md#upgrade-azure-search-service)ために必要な手順と同様です。

## <a name="integrate-with-other-services-including-bots"></a>ボットなどの他のサービスとの統合

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>QnA Maker を利用するには Bot Framework を使う必要がありますか?

いいえ。QnA Maker に関して Bot Framework を使用する必要はありません。 ただし、QnA Maker は Azure Bot Service に用意されているいくつかのテンプレートの 1 つとして提供されています。 Bot Service では、Microsoft Bot Framework を使用してインテリジェントなボットを迅速に開発し、サーバーレス環境で実行することができます。

### <a name="how-can-i-create-a-bot-with-qna-maker"></a>QnA Maker でボットを作成するにはどうすればよいですか?

Azure Bot Service でボットを作成するには、[こちら](./Tutorials/create-qna-bot.md)のドキュメントの手順に従ってください。

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>QnA Maker サービスを Web サイトに埋め込むにはどうすればよいですか?

Web サイトに QnA Maker サービスを Web チャット コントロールとして埋め込むには、次の手順に従ってください。

1. [こちら](./Tutorials/create-qna-bot.md)の手順に従って、FAQ ボットを作成します。
2. [こちら](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)の手順に従って、Web チャットを有効にします。

## <a name="data-storage"></a>データ ストレージ

### <a name="what-data-is-stored-and-where-is-it-stored"></a>どのようなデータが、どこに保存されますか。 

QnA Maker サービスを作成するときに、Azure リージョンを選択しました。 ナレッジ ベースとログ ファイルが、このリージョンに格納されます。 

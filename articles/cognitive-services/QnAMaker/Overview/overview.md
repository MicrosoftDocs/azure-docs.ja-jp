---
title: QnA Maker サービスとは
description: QnA Maker は、データに自然な会話レイヤーを簡単に作成するクラウドベースの NLP サービスです。 これは、特定の自然言語入力に対して、カスタム ナレッジ ベース (KB) の情報から最も適切な回答を見つけるのに使用できます。
ms.topic: overview
ms.date: 05/26/2020
ms.openlocfilehash: d2ff2d789f2ea1ae6018d95ef1d880da87b4ff74
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994869"
---
# <a name="what-is-the-qna-maker-service"></a>QnA Maker サービスとは

[!INCLUDE [TLS 1.2 enforcement](../../../../includes/cognitive-services-tls-announcement.md)]

QnA Maker は、データに自然な会話レイヤーを簡単に作成するクラウドベースの自然言語処理 (NLP) サービスです。 これは、特定の自然言語入力に対して、カスタム ナレッジ ベース (KB) の情報から最も適切な回答を見つけるのに使用できます。

QnA Maker 用のクライアント アプリケーションは、自然言語でユーザーと通信して質問に回答する任意の会話アプリケーションです。 クライアント アプリケーションの例として、ソーシャル メディア アプリ、チャットボット、音声対応デスクトップ アプリケーションがあります。

## <a name="when-to-use-qna-maker"></a>QnA Maker を使用する場合

* **静的な情報がある場合** - 回答のナレッジ ベースに静的な情報がある場合に、QnA Maker を使用します。 このナレッジ ベースは、[PDF や URL](../concepts/content-types.md) などのドキュメントで作成したニーズに合わせてカスタマイズされています。
* **要求、質問、またはコマンドに同じ回答を提供する場合** - 異なるユーザーが同じ質問を送信すると、同じ回答が返されます。
* **メタ情報に基づいて静的情報をフィルター処理する場合** - [メタデータ](../how-to/metadata-generateanswer-usage.md) タグを追加して、クライアント アプリケーションのユーザーと情報に関連する追加のフィルター オプションを提供します。 一般的なメタデータ情報には、[chit-chat](../how-to/chit-chat-knowledge-base.md)、コンテンツの種類または形式、コンテンツの目的、コンテンツの鮮度が含まれます。
* **静的な情報を含むボットの会話を管理する場合** - ご利用のナレッジ ベースは、ユーザーの会話テキストまたはコマンドを取得して、それに応答します。 回答が、ナレッジ ベースで[複数ターンのコンテキスト](../how-to/multiturn-conversation.md)で表される、事前に決定された会話フローの一部である場合、ボットはこのフローを簡単に提供できます。

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>チャット ボットで QnA Maker ナレッジ ベースを使用する

QnA Maker のナレッジ ベースが公開されると、クライアント アプリケーションは、ナレッジ ベースのエンドポイントに質問を送信し、結果を JSON 応答として受信します。 QnA Maker の一般的なクライアント アプリケーションはチャット ボットです。

![ボットに質問をして、ナレッジ ベースのコンテンツから回答を得る](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|手順|アクション|
|:--|:--|
|1|クライアント アプリケーションは、ユーザーの_質問_ (自分の言葉で表現されたテキスト) "プログラムによってナレッジ ベースを更新するにはどうすればよいですか?" を、 ご利用のナレッジ ベース エンドポイントに送信します。|
|2|QnA Maker は、トレーニング済みのナレッジ ベースを使用して、正しい回答と、最適な回答の検索を絞り込むために使用できる任意のフォローアップ プロンプトを提供します。 QnA Maker では、JSON 形式で応答を返します。|
|3|クライアント アプリケーションは、JSON 応答を使用して、会話を継続する方法を判断します。 この判断には、上位の回答を表示することや、最適な回答を得るためにより多くの選択肢を提示することが挙げられます。 |
|||

## <a name="what-is-a-knowledge-base"></a>ナレッジ ベースとは

QnA Maker では、質問と回答がペアになったナレッジ ベースに[コンテンツをインポート](../concepts/knowledge-base.md)します。 インポート プロセスでは、質問と回答のペア間のリレーションシップを意味する、構造化コンテンツと半構造化コンテンツのパーツ間のリレーションシップに関する情報を抽出します。 これらの質問と回答のペアを編集したり、新しいペアを追加したりできます。

質問と回答のペアのコンテンツには、次のものが含まれます。
* 質問のすべての代替形式
* 検索時に回答の選択肢をフィルター処理するために使用されるメタデータ タグ
* 検索の絞り込みを続行するためのフォローアップ プロンプト

![メタデータを使用した質問と回答の例](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

ナレッジ ベースを公開すると、クライアント アプリケーションによってユーザーの質問がエンドポイントに送信されます。 QnA Maker サービスは、質問を処理して、最適な回答で応答します。

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>コードを使用せずにボットを作成、管理、公開する

QnA Maker ポータルでは、完全なナレッジ ベースの作成エクスペリエンスが提供されます。 ドキュメントをそのままの形式でナレッジ ベースにインポートできます。 これらのドキュメント (FAQ、製品マニュアル、スプレッドシート、Web ページなど) は、質問と回答のペアに変換されます。 各ペアは、フォローアップ プロンプト用に分析され、他のペアに接続されます。 最終的な "_マークダウン_" 形式は、画像やリンクを含む豊富なプレゼンテーションをサポートします。

ナレッジ ベースを編集したら、作業中の [Azure Web アプリ ボット](https://azure.microsoft.com/services/bot-service/)にそのナレッジ ベースを公開します。コードを記述する必要はありません。 [Azure portal](https://portal.azure.com) でボットをテストするか、ダウンロードして開発を続行します。

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>最適な回答を提供する検索品質とランク付け

QnA Maker のシステムは、階層化されたランク付けアプローチです。 データは Azure Search に格納されます。これは、第 1 のランク付けレイヤーとしても機能します。 次に、Azure Search からの上位の結果が QnA Maker の NLP 再順位付けモデルを通じて渡され、最終的な結果と信頼度スコアが生成されます。

## <a name="qna-maker-improves-the-conversation-process"></a>会話プロセスを改善する QnA Maker

QnA Maker では、基本的な質問と回答のペアを改善するために、複数ターンのプロンプトとアクティブ ラーニングが提供されています。

**複数ターンのプロンプト**により、質問と回答のペアを結び付ける機会が与えられます。 この結び付けにより、クライアント アプリケーションは上位の回答を提供し、最終的な回答の検索を絞り込むためにより多くの質問を提供することができます。

ナレッジ ベースが公開されたエンドポイントでユーザーからの質問を受け取ると、QnA Maker はこれらの実際の質問に**アクティブ ラーニング**を適用して、品質向上のためにナレッジ ベースへの変更を提案します。

## <a name="development-lifecycle"></a>開発ライフサイクル

QnA Maker では、完全な開発ライフサイクルに統合するために、コラボレーションと共に、作成、トレーニング、および発行のアクセス許可を提供します。

> [!div class="mx-imgBorder"]
> ![開発サイクルの概念図](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="how-do-i-start"></a>開始方法

**手順 1.** :[Azure portal](https://portal.azure.com) で QnA Maker リソースを作成します。

**手順 2**:[QnA Maker](https://www.qnamaker.ai) ポータルでナレッジ ベースを作成します。 ナレッジ ベースを作成するための[ファイルと URL](../concepts/content-types.md) を追加します。

**手順 3**:[cURL または Postman](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) を使用して、カスタム エンドポイントからナレッジ ベースを公開してテストします。

**手順 4**:クライアント アプリケーションから、プログラムを使用してナレッジ ベースのエンドポイントを呼び出します。 クライアント アプリケーションで JSON 応答が処理され、ユーザーに最適な回答が表示されます。

## <a name="next-steps"></a>次のステップ
QnA Maker には、カスタム ナレッジ ベースのビルド、管理、およびデプロイに必要なすべてのものが用意されています。

> [!div class="nextstepaction"]
> [最新の変更を確認する](../whats-new.md)

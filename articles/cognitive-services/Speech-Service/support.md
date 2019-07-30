---
title: サポート オプションとヘルプ オプション
titlesuffix: Azure Cognitive Services
description: Speech Service と統合するアプリケーションを作成するときの疑問や問題に対するヘルプやサポートを入手する方法について説明します
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 981851ab9c4bbc39a2bd5df9f97a080b4f6ea388
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404807"
---
# <a name="support-and-help-options"></a>サポート オプションとヘルプ オプション

Speech Services の機能を調べ始めたばかりですか。 アプリケーションに新しい機能を実装しているのですか。 ここでは、開発者がヘルプを入手するためのヒントを示します。

> [!div class="checklist"]
> * *Azure Cognitive Services* の新たな開発情報を随時確認し、*Speech Service* に関連する最新ニュースを検索します。
> * リリース ノートには、すべてのリリースの情報が含まれています。
> * 問題がコミュニティで話題に上がっていないかどうかや、実装しようとしている機能が既存のドキュメントで既に説明されていないかどうかを、検索して確認します。
> * 十分な答えが見つからない場合は、*Stack Overflow* で質問を投稿します。
> * GitHub のサンプルに関する問題の場合は、*GitHub* で問題を提起します。
> * *UserVoice フォーラム*で解決策を検索します。

## <a name="stay-informed"></a>最新情報を入手

Cognitive Services に関するニュースは、[Cognitive Services ブログ](https://azure.microsoft.com/blog/topics/cognitive-services/)から収集できます。 Speech Services 関連の最新情報については、[Speech Services ブログ](https://azure.microsoft.com/blog/tag/speech-service/)をチェックしてください。

## <a name="release-notes"></a>リリース ノート

[リリース ノート](https://aka.ms/csspeech/whatsnew)は、新しいリリースが使用可能になるとすぐに更新されます。 ノートには、新機能、改善、バグ修正に関する情報が含まれます。

## <a name="search"></a>Search

必要な回答が、ドキュメント、サンプル、または [Stack Overflow](https://www.stackoverflow.com) の質問への回答で見つかるかもしれません。

### <a name="scoped-search"></a>範囲指定の検索

検索速度を高めるには、[好みの検索エンジン](https://bing.com)で次のクエリを使って、検索範囲を Stack Overflow、ドキュメント、およびコード サンプルに設定できます。

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples)
```

ここで、 *{Your Search Terms}* は検索するキーワードです。

## <a name="create-an-azure-support-request"></a>Azure サポート要求を作成する

Azure ユーザーは、Azure portal でサポート要求を作成し、管理できます。

* [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
* [米国政府機関向け Azure portal](https://portal.azure.us)

## <a name="post-a-question-to-stack-overflow"></a>Stack Overflow に質問を投稿する

Stack Overflow は、開発関連の質問を投稿するのに適したチャネルです。 コミュニティのメンバーや Microsoft チームのメンバーから、問題の解決方法について直接意見を聞くことができます。

検索で問題に対する回答が見つからない場合は、Stack Overflow に新しい質問を提出します。 質問を作成するときは、次のタグのいずれかを使います。

|コンポーネント/区分  |Tags  |
|---------|---------|
|音声認識 |[[microsoft-cognitive+speech-to-text]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+speech-to-text)|
|音声合成 |[[microsoft-cognitive+text-to-speech]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+text-to-speech)|
|音声翻訳 |[[microsoft-cognitive+translation]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+translation)|
|音声意図 |[[microsoft-cognitive+luis]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+luis)|
|General Speech SDK |[[microsoft-cognitive+microsoft-speech-api]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+microsoft-speech-api)|
| 音声優先仮想アシスタント (プレビュー) | [[microsoft-cognitive+virtual-assistant+botframework]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+virtual-assistant+botframework) |

> [!TIP]
> Stack Overflow の次の投稿では、質問の作成方法やソース コードの追加方法に関するヒントが示されています。 これらのガイドラインに従うと、コミュニティ メンバーからの評価や回答がより早く返ってくる可能性があります。  
> * [よい質問をする方法](https://stackoverflow.com/help/how-to-ask)
> * [最小限の例、完全な例、実証可能な例を作成する方法](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>GitHub の issue を作成する

多くの場合、サンプルはオープン ソースとして投稿されます。 質問や問題の場合は、それぞれの GitHub リポジトリで "*問題*" を作成します。 pull request を送信することもできます。 次の一覧には、サンプル リポジトリへのリンクが含まれます。

* [Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues)
* [Speech Devices SDK](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/issues)

バグ レポート、機能要求、または一般的な質問を作成し、ベスト プラクティスを共有できます。 バグ レポートの場合は、提供されているテンプレートに従ってください。

**バグの説明**

バグの明確で簡潔な説明。

**再現方法**

動作を再現する手順:
1. ...
2. ...

**正しい動作**

正しい反応の明確で簡潔な説明。

**Cognitive Services Speech SDK のバージョン**

使用している SDK のバージョン。

**プラットフォーム、オペレーティング システム、プログラミング言語**

 - OS: (例: Windows、Linux、Android、iOS、...) - 特定してください
 - ハードウェア - x64、x86、ARM、...
 - ブラウザー (例: Chrome、Safari) (該当する場) - 特定してください

**追加コンテキスト**

 - エラー メッセージ、ログ情報、スタック トレース、...
 - 特定のサービス操作のエラーを報告する場合は、報告するインシデントのセッション ID と時刻 (タイムゾーンを含む) を報告してください。 セッション ID は、すべてのコールバック/受け取ったイベントで報告されます。
 - 他の追加情報


## <a name="uservoice-forum"></a>UserVoice フォーラム

開発しているアプリケーションで Cognitive Services と関連 API をいっそう適切に動作させるためのアイデアを共有します。 拡大し続けるナレッジ ベースを使って、よくある質問に対する回答を探してください。

[UserVoice](https://cognitive.uservoice.com/)

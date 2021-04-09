---
title: 回答範囲検出を使用した正確な回答 - QnA Maker
description: QnA Maker マネージドにある正確な回答機能について理解します。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 9c7b265118e395dde2b4b5f3959ba397d75ddac5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232190"
---
# <a name="precise-answering"></a>正確な回答

QnA Maker マネージド (プレビュー) で導入された正確な回答機能を使用すると、ナレッジベースにある一番適した回答文章の候補から、ユーザーのすべてのクエリに対する正しい短い回答を得ることができます。 この機能では、実行時にユーザー クエリの意図を理解し、回答文章にファクトとして短い回答がある場合に短い回答を回答文章から検出する、ディープ ラーニング モデルを使用しています。 

この機能は、テスト ウィンドウで既定でオンになっているため、自分のシナリオに固有の機能をテストできます。 この機能は、コンテンツ開発者とエンドユーザーの両方に非常に有益です。 コンテンツ開発者は、ナレッジベースにあるすべてのファクトに対して、特定の質問と回答のペアを手動でキュレートする必要がなくなりました。また、エンドユーザーは、サービスから返された回答全体を確認して、ユーザーのクエリの回答である実際のファクトを探す必要がなくなりました。 [GenerateAnswer API を使用して、正確な回答](How-To/metadata-generateanswer-usage.md#get-precise-answers-with-generateanswer-api)をフェッチできます。

## <a name="precise-answering-on-qna-maker-portal"></a>QnA Maker ポータルでの正確な回答

QnA Maker ポータルで [テスト] ウィンドウを開くと、上部に **[Display short answer]** \(短い回答を表示する\) オプションが表示されます。 このオプションは既定でオンになっています。 [テスト] ウィンドウにクエリを入力すると、回答文章に短い回答がある場合、回答文章と共に短い回答が表示されます。
 
![マネージドが有効な [テスト] ウィンドウ](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

[テスト] ウィンドウに回答文章のみを表示する場合は、 **[Display short answer]** \(短い回答を表示する\) オプションをオフにします。 

このサービスでは、正確な回答の信頼度スコアも **回答範囲スコア** として返されます。これは、[テスト] ウィンドウのクエリのすぐ下の **[検査]** オプションを選択して確認できます。

![管理された回答範囲スコア](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>QnA Maker ボットを発行する

ボットを発行すると、自分のアプリケーションで既定で正確な回答を、回答文章と共に短い回答を表示して提供できるようになります。 応答で正確な回答を使用する (AnswerSpan) 方法については、API リファレンスの「[回答を生成する](/rest/api/cognitiveservices/qnamakerv5.0-preview.1/knowledgebase/generateanswer#answerspan)」を参照してください。 Bot アプリ サービスを使用してテンプレートを更新すると、ユーザーは他のエクスペリエンスも柔軟に選択できるようになります。 

## <a name="language-support"></a>言語のサポート

現時点では、正確な回答機能は、英語のみでサポートされています。

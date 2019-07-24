---
title: データ ストレージ
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS では、キーによって指定された領域に対応する Azure のデータ ストアに、データが暗号化されて格納されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: diberry
ms.openlocfilehash: a1093c2a6303b453a17a52058303913de5ecfa8d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58893199"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Language Understanding (LUIS) Cognitive Services でのデータの格納と削除
LUIS では、キーによって指定された領域に対応する Azure のデータ ストアに、データが暗号化されて格納されます。 このデータは 30 日間保存されます。 

## <a name="export-and-delete-app"></a>アプリのエクスポートと削除
ユーザーは、アプリの[エクスポート](luis-how-to-start-new-app.md#export-app)と[削除](luis-how-to-start-new-app.md#delete-app)を完全に制御できます。 

## <a name="utterances"></a>発話

発話は 2 つの異なる場所に格納できます。 

* 発話は**作成プロセス**中に作成され、意図に格納されます。 意図内の発話は、LUIS アプリの成功に必要です。 アプリが発行され、エンドポイントでクエリが受け取られると、エンドポイント要求のクエリ文字列 `log=false` によって、エンドポイントの発話が格納されるかどうかが決定されます。 格納されたエンドポイントは、**[エンドポイントの発言の確認]** セクション内の、ポータルの **[ビルド]** セクションにあるアクティブ ラーニング発話の一部になります。 
* **エンドポイントの発話を確認**し、発話を意図に追加すると、その発話は確認されるべきエンドポイントの発話の一部として格納されなくなります。 これはアプリの意図に追加されます。 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>意図からの発話例の削除
[LUIS](luis-reference-regions.md) のトレーニングに使用された発話の例を削除します。 LUIS アプリから発話の例を削除すると、LUIS Web サービスから削除されて、エクスポートに使用できなくなります。

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>アクティブ ラーニングからの確認中の発話の削除

**[[Review endpoint utterances]\(エンドポイントの発話の確認\)](luis-how-to-review-endpoint-utterances.md)** ページで LUIS が提案するユーザー発話のリストから、発話を削除できます。 このリストから削除した発話は提案されなくなりますが、ログからは削除されません。

アクティブ ラーニング発話が不要な場合、[アクティブ ラーニングを無効にする](luis-how-to-review-endpoint-utterances.md#disable-active-learning)ことができます。 アクティブ ラーニングを無効にすると、ログ記録も無効になります。

### <a name="disable-logging-utterances"></a>発話のログ記録の無効化
[アクティブ ラーニングを無効にする](luis-how-to-review-endpoint-utterances.md#disable-active-learning)と、ログ記録が無効になります。


<a name="accounts"></a>

## <a name="delete-an-account"></a>アカウントの削除
アカウントを削除すると、発話例およびログと共に、すべてのアプリが削除されます。 データが 60 日間保持された後、アカウントとデータは完全に削除されます。

アカウントの削除は、**[設定]** ページで実行できます。 右上部のナビゲーション バーにあるアカウント名を選択して、**[設定]** ページに移動します。

## <a name="data-inactivity-as-an-expired-subscription"></a>有効期限が切れたサブスクリプションでの非アクティブなデータ
データの保有と削除の目的で、非アクティブの LUIS アプリは、"_Microsoft の裁量_" で有効期限が切れたサブスクリプションとして扱われることがあります。 過去 90 日間に次の条件を満たすアプリは、非アクティブと見なされます。 

* 呼び出しが行われて**いない**
* 変更されていない
* 最新のキーが割り当てられていない
* ユーザーがサインインしていない

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリのエクスポートと削除について確認します](luis-how-to-start-new-app.md)

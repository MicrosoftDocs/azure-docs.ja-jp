---
title: カスタム コマンド アプリケーションの作成時にエラーをデバッグする (プレビュー)
titleSuffix: Azure Cognitive Services
description: この記事では、カスタム コマンド アプリケーションの作成時にエラーをデバッグする方法について説明します。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: aeb90e8e064c44f4d17f920261ed58310f0e55f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95025703"
---
# <a name="debug-errors-when-authoring-a-custom-commands-application"></a>カスタム コマンド アプリケーションの作成時にエラーをデバッグする

この記事では、カスタム コマンド アプリケーションの作成中にエラーが発生した場合のデバッグ方法について説明します。 

## <a name="errors-when-creating-an-application"></a>アプリケーションを作成する際のエラー
カスタム コマンドでは、カスタム コマンド アプリケーションを作成するとき、アプリケーションが [LUIS](https://www.luis.ai/) でも作成されます。 

[LUIS には作成リソースごとに 500 のアプリケーションという制限があります](../luis/luis-limits.md)。 既に 500 のアプリケーションが存在する作成リソースを使用している場合、LUIS アプリケーションの作成が失敗する可能性があります。 

選択した LUIS 作成リソースに含まれるアプリケーションが 500 未満であることを確認してください。 そうでない場合は、新しい LUIS 作成リソースを作成してそれに切り替えるか、またはご利用の LUIS アプリケーションのクリーンアップを試みます。  

## <a name="errors-when-deleting-an-application"></a>アプリケーションを削除する際のエラー
### <a name="cant-delete-luis-application"></a>LUIS アプリケーションを削除できない
カスタム コマンドでは、カスタム コマンド アプリケーションの削除時に、カスタム コマンド アプリケーションに関連付けられている LUIS アプリケーションの削除も試みられる場合があります。

LUIS アプリケーションの削除に失敗した場合は、ご利用の [LUIS](https://www.luis.ai/) アカウントにアクセスして、手動で削除してください。

### <a name="toomanyrequests"></a>TooManyRequests
多数のアプリケーションを一度にすべて削除しようとすると、"TooManyRequests" エラーが表示される可能性があります。 これらのエラーは、削除要求が Azure によってスロットルされることを意味します。 

ページを更新してから、削除するアプリケーションの数を減らしてみてください。

## <a name="errors-when-modifying-an-application"></a>アプリケーションを変更する際のエラー

### <a name="cant-delete-a-parameter-or-a-web-endpoint"></a>パラメーターまたは Web エンドポイントを削除できない
パラメーターが使用中である場合、それを削除することはできません。 音声応答、例文、条件、アクション内のパラメーター参照を削除してから、もう一度やり直してください。

### <a name="cant-delete-a-web-endpoint"></a>Web エンドポイントを削除できない
Web エンドポイントが使用中である場合、それを削除することはできません。 Web エンドポイントを削除するには、この Web エンドポイントを使用する **[Call Web Endpoint]\(Web エンドポイントの呼び出し\)** アクションを事前に削除してください。

## <a name="errors-when-training-an-application"></a>アプリケーションをトレーニングする際のエラー
### <a name="built-in-intents"></a>組み込みの意図
LUIS には、肯定または否定の意図が組み込まれています。 "yes" のみを含む例文を使用すると、"no" の場合にトレーニングは失敗します。 

| Keyword | バリエーション | 
| ------- | --------- | 
| はい | Sure、OK |
| いいえ | Nope、Not | 

### <a name="common-sample-sentences"></a>共通の例文
カスタム コマンドでは、共通の例文が異なるコマンド間で共有されることは許可されません。 あるコマンド内のいくつかの例文が既に別のコマンドで定義されている場合、アプリケーションのトレーニングは失敗する可能性があります。 

異なるコマンド間で共有される共通の例文がないことを確認してください。 

異なるコマンド間で例文のバランスを取るベスト プラクティスについては、[LUIS のベスト プラクティス](../luis/luis-concept-best-practices.md)に関するページを参照してください。

### <a name="empty-sample-sentences"></a>空の例文
各コマンドには、少なくとも 1 つの例文が必要です。

### <a name="undefined-parameter-in-sample-sentences"></a>例文内に未定義のパラメーターがある
例文では 1 つまたは複数のパラメーターが使用されていますが、定義されていません。

### <a name="training-takes-too-long"></a>トレーニングに時間がかかりすぎる
LUIS トレーニングは、より少ない例をすばやく学習するように作られています。 追加する例文の数が多すぎないようにしてください。 

類似した例文が多数ある場合は、パラメーターを定義し、それらをパターンに抽象化し、それを例文に追加します。

たとえば、以下の例文に対してパラメーター {車両} を定義すると、"{車両} を予約する" を例文に追加するだけで済みます。

| 例文 | Pattern | 
| ------- | ------- | 
| 車を予約する | {車両} を予約する | 
| Book a flight | {車両} を予約する |
| タクシーを予約する | {車両} を予約する |

LUIS トレーニングのベスト プラクティスについては、[LUIS のベスト プラクティス](../luis/luis-concept-best-practices.md)に関するページを参照してください。

## <a name="cant-update-luis-key"></a>LUIS キーを更新できない
### <a name="reassign-to-e0-authoring-resource"></a>E0 作成リソースへの再割り当て
LUIS では、E0 作成リソースへの LUIS アプリケーションの再割り当てはサポートされていません。

作成リソースを F0 から E0 に変更する必要がある場合、または別の E0 リソースに変更する必要がある場合は、アプリケーションを再作成してください。 

既存のアプリケーションをすばやくエクスポートし、新しいアプリケーションにインポートするには、「[Azure DevOps による継続的配置](./how-to-custom-commands-deploy-cicd.md)」を参照してください。

### <a name="save-button-is-disabled"></a>[保存] ボタンが無効になる
LUIS 予測リソースをご利用のアプリケーションに割り当てていない場合、予測リソースを追加せずに作成リソースを変更しようとすると、[保存] ボタンが無効になります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [GitHub のサンプルを参照](https://aka.ms/speech/cc-samples)
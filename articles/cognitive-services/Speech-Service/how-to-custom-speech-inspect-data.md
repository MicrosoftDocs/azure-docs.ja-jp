---
title: Custom Speech のデータ品質を検査する - Speech Services
titlesuffix: Azure Cognitive Services
description: Custom Speech には、オーディオ データを対応する認識結果と比較することによって、モデルの認識品質を視覚的に検査するツールがあります。 Custom Speech ポータルから、アップロードされたオーディオを再生し、提供された認識結果が正しいかどうかを判断できます。  このツールを使用すると、オーディオ データを文字起こしすることなく、Microsoft のベースラインの音声変換モデルまたはトレーニング済みのカスタム モデルの品質をすばやく検査できます。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 900d9b032b4ed121589d904a8ad18059b3283661
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603137"
---
# <a name="inspect-custom-speech-data"></a>Custom Speech データを検査する

> [!NOTE]
> このページは、「[Prepare test data for Custom Speech (Custom Speech 用のテスト データを準備する)](how-to-custom-speech-test-data.md)」を読んでおり、検査用のデータセットをアップロードしていることを前提としています。

Custom Speech には、オーディオ データを対応する認識結果と比較することによって、モデルの認識品質を視覚的に検査するツールがあります。 Custom Speech ポータルから、アップロードされたオーディオを再生し、提供された認識結果が正しいかどうかを判断できます。 このツールを使用すると、オーディオ データを文字起こしすることなく、Microsoft のベースラインの音声変換モデルまたはトレーニング済みのカスタム モデルの品質をすばやく検査できます。

このドキュメントでは、以前にアップロードしたトレーニング データを使用してモデルの品質を視覚的に検証する方法について説明します。

このページでは、Microsoft のベースラインの音声変換モデルやトレーニングしたカスタム モデルの品質を視覚的に検査する方法について説明します。 テストには **[データ]** タブにアップロードしたデータを使用します。

## <a name="create-a-test"></a>テストを作成する

テストを作成するには、以下の手順を実行します。

1. **[音声変換]、[Custom Speech]、[Testing]\(テスト\)** の順に移動します。
2. **[テストの追加]** をクリックします。
3. **[Inspect quality (Audio-only data)]\(品質の検査 (オーディオのみのデータ)\)** を選択します。 テストの名前と説明を設定し、オーディオ データセットを選択します。
4. テストするモデルを最大で 2 つ選択します。
5. **Create** をクリックしてください。

テストが正常に作成されたら、モデルを並べて比較できます。

## <a name="side-by-side-model-comparisons"></a>モデルを並べて比較する

テスト状態が *[成功]* の場合は、テスト項目名をクリックしてテストの詳細を表示します。 この詳細ページには、データセット内のすべての発話が一覧表示され、送信されたデータセットからの文字起こしと共に、2 つのモデルの認識結果が示されます。

並べた比較を検査しやすくするために、挿入、削除、置換を含むさまざまなエラーの種類を切り替えることができます。 オーディオを聞きながら ("人間" とラベルが付いた文字起こしと 2 つの音声変換モデルの結果が表示される) 各列の認識結果を比較すると、どちらのモデルがニーズに合い、改善が必要な部分を判断できます。

品質テストの検査は、音声認識エンドポイントの品質がアプリケーションに十分かどうかを検証するために役立ちます。  文字起こしされたオーディオが必要な正確性の客観的な尺度を得るには、[正確性の評価](how-to-custom-speech-evaluate-data.md)に関するページの手順に従ってください。

## <a name="next-steps"></a>次の手順

* [データを評価する](how-to-custom-speech-evaluate-data.md)
* [モデルをトレーニングする](how-to-custom-speech-train-model.md)
* [モデルをデプロイする](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>その他のリソース

* [Custom Speech 用のテスト データを準備する](how-to-custom-speech-test-data.md)

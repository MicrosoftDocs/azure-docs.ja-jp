---
title: Custom Speech のデータ品質を検査する - Speech Services
titleSuffix: Azure Cognitive Services
description: Custom Speech には、オーディオ データを対応する認識結果と比較することによって、モデルの認識品質を視覚的に検査するツールがあります。 アップロードされたオーディオを再生し、提供された認識結果が正しいかどうかを判断できます。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: erhopf
ms.openlocfilehash: 9ce0d3a06846cbc3aa37ab836564150e6f2c34ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100362813"
---
# <a name="inspect-custom-speech-data"></a>Custom Speech データを検査する

> [!NOTE]
> このページは、「[Prepare test data for Custom Speech (Custom Speech 用のテスト データを準備する)](./how-to-custom-speech-test-and-train.md)」を読んでおり、検査用のデータセットをアップロードしていることを前提としています。

Custom Speech には、オーディオ データを対応する認識結果と比較することによって、モデルの認識品質を視覚的に検査するツールがあります。 [Speech Studio](https://speech.microsoft.com/customspeech) から、アップロードされたオーディオを再生し、提供された認識結果が正しいかどうかを判断できます。 このツールは、Microsoft のベースライン音声テキスト変換モデルの品質検査、トレーニング済みのカスタム モデルの検査、2 つのモデルでの文字起こしの比較に役立ちます。

このドキュメントでは、Microsoft のベースラインの音声テキスト変換モデルやトレーニングしたカスタム モデルの品質を視覚的に検査する方法について説明します。 また、オンライン文字起こしエディターを使用して、ラベル付けされたオーディオ データセットを作成および調整する方法についても説明します。

## <a name="create-a-test"></a>テストを作成する

テストを作成するには、以下の手順を実行します。

1. [Speech Studio](https://speech.microsoft.com/customspeech) にサインインします。
2. **[音声テキスト変換]、[Custom Speech]、[<プロジェクト名>]、[テスト]** の順に移動します。
3. **[テストの追加]** をクリックします。
4. **[Inspect quality (Audio-only data)]\(品質の検査 (オーディオのみのデータ)\)** を選択します。 テストの名前と説明を設定し、オーディオ データセットを選択します。
5. テストするモデルを最大で 2 つ選択します。
6. **Create** をクリックしてください。

テストが正常に作成されたら、モデルによって、指定したオーディオ データセットがどのように書き起こされたかを確認したり、2 つのモデルの結果を並べて比較したりできます。

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>モデルを並べて比較する

テスト状態が _[成功]_ の場合は、テスト項目名をクリックしてテストの詳細を表示します。 この詳細ページには、データセット内のすべての発話が一覧表示され、比較する 2 つのモデルの認識結果が表示されます。

並べた比較を検査しやすくするために、挿入、削除、置換を含むさまざまなエラーの種類を切り替えることができます。 オーディオを聞きながら ("人間" とラベルが付いた文字起こしと 2 つの音声変換モデルの結果が表示される) 各列の認識結果を比較すると、どちらのモデルがニーズに合い、改善が必要な部分を判断できます。

モデルを並べたテストは、アプリケーションに最適な音声認識モデルを検証するのに役立ちます。 文字起こしされたオーディオが必要な正確性の客観的な尺度を得るには、[正確性の評価](how-to-custom-speech-evaluate-data.md)に関するページの手順に従ってください。

## <a name="online-transcription-editor"></a>オンライン文字起こしエディター

オンライン文字起こしエディターを使用すると、Custom Speech で音声の文字起こしを簡単に操作できます。 エディターの主なユース ケースは次のとおりです。 

* 音声データしかないが、モデルのトレーニングで使用するために、正確な音声 + "人間" とラベルが付いたデータセットをゼロから作成する必要がある場合。
* 音声 + "人間" とラベルが付いたデータセットは既に存在するが、文字起こしでエラーや不具合が生じている場合。 このエディターでは、文字起こしを簡単に変更して、トレーニングの精度を最大限に高めることができます。

文字起こしエディターを使用するための唯一の要件は、音声データをアップロードしていることです (音声のみ、または音声 + 文字起こし)。

### <a name="import-datasets-to-editor"></a>データセットをエディターにインポートする

エディターにデータをインポートするには、最初に **[Custom Speech] > [Your project]\(自分のプロジェクト\) > [エディター]** の順に移動します。

![[編集] タブ](media/custom-speech/custom-speech-editor-detail.png)

次に、以下の手順を使用してデータをインポートします。

1. **[データのインポート]** をクリックします
1. 新しいデータセットを作成して説明を入力します
1. データセットを選択します。 複数の選択肢がサポートされており、音声データだけでなく、音声 + "人間" のラベルが付いたデータも選択できます。
1. 音声のみのデータの場合は、必要に応じて既定のモデルを使用して、エディターにインポートした後で自動的に機械文字起こしを生成することができます
1. **[インポート]** をクリックします。

データを正常にインポートした後、データセットをクリックして、編集を開始できます。

> [!TIP]
> データセットを選択し、 **[エディターにエクスポート]** をクリックして、データセットをエディターに直接インポートすることもできます

### <a name="edit-transcription-by-listening-to-audio"></a>音声をリッスンして文字起こしを編集する

データのアップロードが成功した後、各項目名をクリックすると、データの詳細が表示されます。 また、 **[前へ]** と **[次へ]** を使用して各ファイル間を移動することもできます。

詳細ページには、各音声ファイル内のすべてのセグメントが一覧表示され、目的の発話をクリックできます。 発話ごとに、音声を再生して、文字起こしを確認でき、挿入、削除、または置換のエラーが見つかった場合は文字起こしを編集できます。 エラーの種類の詳細については、[データの評価方法](how-to-custom-speech-evaluate-data.md)に関するページをご覧ください。

![エディター ページ](media/custom-speech/custom-speech-editor.png)

編集が完了したら、 **[保存]** ボタンをクリックします。

### <a name="export-datasets-from-the-editor"></a>エディターからデータセットをエクスポートする

データセットを **[データ]** タブにエクスポートするには、データの詳細ページに移動し、 **[エクスポート]** ボタンをクリックして、すべてのファイルを新しいデータセットとしてエクスポートします。 最後に編集された時間、音声の長さなどでファイルをフィルター処理して、目的のファイルを部分的に選択することもできます。 

![データのエクスポート](media/custom-speech/custom-speech-editor-export.png)

[データ] にエクスポートされたファイルは、新しいデータセットとして使用され、既存のデータ/トレーニング/テスト エンティティには影響しません。

## <a name="next-steps"></a>次のステップ

- [データを評価する](how-to-custom-speech-evaluate-data.md)
- [モデルをトレーニングする](how-to-custom-speech-train-model.md)
- [モデルを改良する](./how-to-custom-speech-evaluate-data.md)
- [モデルをデプロイする](./how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>その他のリソース

- [Custom Speech 用のテスト データを準備する](./how-to-custom-speech-test-and-train.md)
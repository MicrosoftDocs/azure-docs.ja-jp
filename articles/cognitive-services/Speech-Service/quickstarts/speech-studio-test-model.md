---
title: クイック スタート:オーディオ ファイルを使用してモデルをテストする - Speech Studio
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Speech Studio を使用してオーディオ ファイルで音声認識をテストします。
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: v-demjoh
ms.openlocfilehash: 76fb933893b624e9b289ddf51d2a0373b51e58d1
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391538"
---
# <a name="quickstart-test-a-model-using-an-audio-file-in-speech-studio"></a>クイック スタート:Speech Studio でオーディオ ファイルを使用してモデルをテストする

このクイックスタートでは、Speech Studio を使用して音声をオーディオ ファイルからテキストに変換します。 Speech Studio を使用すると、関連するテキスト、人間とラベルが付いた文字起こしデータを含むオーディオ、および指定した発音ガイダンスを使用して、音声認識モデルのテスト、比較、改善、デプロイを行うことができます。

## <a name="prerequisites"></a>前提条件

Speech ポータルを使用する前に、[次の手順に従って、Azure アカウントを作成し、Speech サービスにサブスクライブします](../how-to-custom-speech.md#set-up-your-azure-account)。 この統合されたサブスクリプションにより、音声テキスト変換、テキスト読み上げ、音声翻訳、および Custom Speech ポータルにアクセスできるようになります。

## <a name="download-an-audio-file"></a>オーディオ ファイルをダウンロードする

音声を含むオーディオ ファイルをダウンロードし、zip ファイルにパッケージ化するには、次の手順に従います。

1. リンクを右クリックし、 **[リンクの保存]** を選択して、 **[このリンクからサンプルの wav ファイル](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)** をダウンロードします。 **[保存]** をクリックして、`whatstheweatherlike.wav` ファイルをダウンロードします。
2. zip ツールでファイル エクスプローラーまたはターミナルウィンドウを使用して、ダウンロードした `whatstheweatherlike.wav` ファイルを含む `whatstheweatherlike.zip` という名前の zip ファイルを作成します。 Windows では、Windows エクスプローラーを開き、`Downloads` フォルダーに移動して `whatstheweatherliike.wav` を右クリックし、 **[送信先]** をクリックして、 **[圧縮 (zip 形式) フォルダー]** をクリックし、Enter キーを押して既定のファイル名をそのまま使用することができます。

## <a name="create-a-project-in-the-custom-speech-portal"></a>Custom Speech ポータルでプロジェクトを作成する

1 つのオーディオ ファイルの zip を含むプロジェクトを作成するには、次の手順に従います。

1. [Speech Studio](https://speech.microsoft.com/) を開き、 **[新しいプロジェクト]** をクリックします。 このプロジェクトの名前を入力し、 **[作成]** クリックします。 プロジェクトが Custom Speech の一覧に表示されます。
2. プロジェクトの名前をクリックします。 [データ] タブで、 **[データのアップロード]** をクリックします。
3. 音声データ型は、既定で **[オーディオのみ]** に設定されているので、 **[次へ]** をクリックします。
4. 新しい音声データセットに `MyZipOfAudio` という名前を指定し、 **[次へ]** をクリックします。
5. **[ファイルの参照]** をクリックし、`whatstheweatherlike.zip` ファイルに移動して、 **[開く]** をクリックします。
6. **[アップロード]** ボタンをクリックします。 ブラウザーで zip ファイルが Speech Studio にアップロードされ、Speech Studio でコンテンツが処理されます。

## <a name="test-a-model"></a>モデルをテストする

Speech Studio で zip ファイルの内容が処理されたら、ソース オーディオを再生して、文字起こしデータにエラーやミスがないか確認することができます。 ブラウザーで文字起こしの品質を確認するには、次の手順に従います。

1. **[テスト中]** タブをクリックし、 **[テストの追加]** をクリックします。
2. このテストでは、オーディオのみのデータの品質を調査しているので、 **[次へ]** をクリックしてこのテストの種類を受け入れます。
3. このテストに `MyModelTest` という名前を指定し、 **[次へ]** をクリックします。
4. `MyZipOfAudio` の左側にあるラジオ ボタンをクリックし、 **[次へ]** をクリックします。
5. **[モデル 1]** ドロップダウンは、既定で最新の認識モデルに設定されているので、 **[作成]** をクリックします。 オーディオ データセットの内容が処理されたら、テストの状態は **[成功]** に変わります。
6. **[MyModelTest]** をクリックします。 音声認識の結果が表示されます。 円の中の右向きの三角形をクリックすると、オーディオが再生され、その内容を円の横のテキストと比較できます。

## <a name="download-detailed-results"></a>詳細な結果をダウンロードする

文字起こしについて説明するファイルを、はるかに詳細な形でダウンロードできます。 これらのファイルには、オーディオ ファイル内の語彙形式の音声と、各単語に関するオフセット、期間、文字起こしの信頼度の詳細を含む JSON ファイルが含まれます。 これらのファイルを表示するには、次の手順に従います。

1. **[Download]** をクリックします。
2. [ダウンロード] ダイアログで **[オーディオ]** を選択解除し、 **[ダウンロード]** をクリックします。
3. ダウンロードした zip ファイルを解凍し、抽出したファイルを確認します。

## <a name="next-steps"></a>次のステップ

[カスタム モデルをトレーニングする](../how-to-custom-speech-test-and-train.md)ことにより、音声認識の精度を向上させる方法について説明します。
---
title: Windows ML で ONNX モデルを使用する - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services からエクスポートされた ONNX モデルを使用する Windows UWP アプリの作成方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: pafarley
ms.openlocfilehash: 58ced0c45d66223ac3e40112126e92a4539db32d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94616078"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Custom Vision からエクスポートされた ONNX モデルを Windows ML (プレビュー) で使用する

Custom Vision Service からエクスポートされた ONNX モデルを Windows ML (プレビュー) で使用する方法について説明します。

このガイドでは、Custom Vision Service からエクスポートされた ONNX ファイルを Windows ML で使用する方法について説明します。 トレーニングされた独自の画像分類器で、サンプルの UWP アプリケーションを使用します。

## <a name="prerequisites"></a>前提条件

* Windows 10 バージョン 1809 以降
* ビルド 17763 以降の Windows SDK
* Visual Studio 2017 バージョン 15.7 以降がインストールされ、 __ユニバーサル Windows プラットフォーム開発__ ワークロードが有効にされている。
* PC の開発者モードが有効にされている。 詳細については、「[デバイスを開発用に有効にする](/windows/uwp/get-started/enable-your-device-for-development)」を参照してください。

## <a name="about-the-example-app"></a>サンプル アプリについて

含まれているアプリケーションは汎用 Windows UWP アプリケーションです。 これにより、コンピューターから画像を選択し、ローカルに保存された分類モデルで処理できるようになります。 モデルから返されたタグとスコアは、イメージの横に表示されます。

## <a name="get-the-example-code"></a>サンプル コードを入手する

このサンプル アプリケーションは、GitHub の [Cognitive Services ONNX Custom Vision サンプル](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) リポジトリで入手できます。 それをローカル コンピューターに複製し、Visual Studio で *SampleOnnxEvaluationApp.sln* を開きます。

## <a name="test-the-application"></a>アプリケーションをテストする

1. Visual Studio で `F5` キーを押すと、アプリケーションが起動します。 開発者モードを有効にするよう求めるメッセージが表示される場合があります。
1. アプリケーションの起動時に、ボタンを使用してスコア付け用のイメージを選択します。 既定の ONNX モデルは、さまざまな種類のプランクトンを分類するようトレーニングされています。

## <a name="use-your-own-model"></a>自分で用意したモデルを使用する

独自の画像分類モデルを使用するには、次の手順を実行します。

1. Custom Vision Service を使用して分類器を作成してトレーニングします。 この方法の手順については、[分類器を作成してトレーニングする](./getting-started-build-a-classifier.md)方法に関する記事を参照してください。 **[全般 (コンパクト)]** などの **コンパクト** ドメインのいずれかを使用します。 
   * 別のドメインを使用する分類器が既に存在する場合は、プロジェクト設定で **コンパクト** に変換できます。 次に、プロジェクトを再トレーニングしてから続行します。
1. モデルをエクスポートします。 [パフォーマンス] タブに切り替えて、**コンパクト** ドメインでトレーニングされたイテレーションを選択します。 表示される **[エクスポート]** ボタンを選択します。 次に、 **[ONNX]** を選択してから **[エクスポート]** を選択します。 ファイルの準備ができたら、 **[ダウンロード]** ボタンを選択します。 エクスポート オプションの詳細については、[モデルのエクスポート](./export-your-model.md)に関する記事を参照してください。
1. ダウンロードした *.zip* ファイルを開き、そこから *model.onnx* ファイルを抽出します。 このファイルには、独自の分類モデルが含まれています。
1. Visual Studio のソリューション エクスプローラーで、 **[Assets]** フォルダーを右クリックし、 __[既存項目の追加]__ を選択します。 ONNX ファイルを選択します。
1. ソリューション エクスプローラーで、ONNX ファイルを右クリックし、 **[プロパティ]** を選択します。 ファイルの以下のプロパティを変更します。
   * __[ビルド アクション]__  ->  __[コンテンツ]__
   * __[出力ディレクトリにコピー]__  ->  __[新しい場合はコピーする]__
1. 次に、_MainPage.xaml.cs_ を開き、`_ourOnnxFileName` の値を ONNX ファイルの名前に変更します。
1. プロジェクトをビルドして実行するには、`F5` を使用します。
1. ボタンをクリックして、評価するイメージを選択します。

## <a name="next-steps"></a>次のステップ

Custom Vision モデルをエクスポートして使用する別の方法については、次のドキュメントを参照してください。

* [モデルをエクスポートする](./export-your-model.md)
* [エクスポートされた Tensorflow モデルを Android アプリケーションで使用する](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [エクスポートされた CoreML モデルを Swift iOS アプリケーションで使用する](https://go.microsoft.com/fwlink/?linkid=857726)
* [エクスポートされた CoreML モデルを Xamarin を使って iOS アプリケーションで使用する](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Windows ML で ONNX モデルを使用する方法については、「[Windows ML でモデルをアプリに統合する](/windows/ai/windows-ml/integrate-model)」を参照してください。
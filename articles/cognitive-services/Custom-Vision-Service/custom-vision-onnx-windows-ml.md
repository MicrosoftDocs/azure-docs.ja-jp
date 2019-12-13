---
title: チュートリアル:Windows ML で ONNX モデルを使用する - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services からエクスポートされた ONNX モデルを使用する Windows UWP アプリの作成方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 3b56cd12aec21cffd98a0143f5cf3f083ec55e94
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74960555"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>チュートリアル:Custom Vision からエクスポートされた ONNX モデルを Windows ML (プレビュー) で使用する

Custom Vision Service からエクスポートされた ONNX モデルを Windows ML (プレビュー) で使用する方法について説明します。

このドキュメントでは、Custom Vision Service からエクスポートされた ONNX ファイルを Windows ML で 使用する方法について例を挙げて説明します。 サンプルとして Windows UWP アプリケーションが用意されています。 このサンプルには、認識できるトレーニング済みモデルが含まれています。 また、自分で用意したモデルをこのサンプルで使用するための手順も記載されています。

> [!div class="checklist"]
> * サンプル アプリについて
> * サンプル コードを入手する
> * 例を実行する
> * 自分で用意したモデルを使用する

## <a name="prerequisites"></a>前提条件

* Windows 10 バージョン 1809 以降

* ビルド 17763 以降の Windows SDK

* Visual Studio 2017 バージョン 15.7 以降がインストールされ、 __ユニバーサル Windows プラットフォーム開発__ ワークロードが有効にされている。

* 開発者モードが有効にされている。 詳細については、ドキュメント「[デバイスを開発用に有効にする](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)」を参照してください。

## <a name="about-the-example-app"></a>サンプル アプリについて

このアプリケーションは汎用 Windows UWP アプリケーションです。 使用しているコンピューターのイメージを選択することができます。その後、このイメージがモデルに送信されます。 モデルから返されたタグとスコアは、イメージの横に表示されます。

## <a name="get-the-example-code"></a>サンプル コードを入手する

サンプル アプリケーションは [https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) で入手できます。

## <a name="run-the-example"></a>例を実行する

1. Visual Studio で `F5` キーを押すと、アプリケーションが起動します。 開発者モードを有効にするよう求めるメッセージが表示される場合があります。 詳細については、ドキュメント「[デバイスを開発用に有効にする](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)」を参照してください。

1. アプリケーションの起動時に、ボタンを使用してスコア付け用のイメージを選択します。

## <a name="use-your-own-model"></a>自分で用意したモデルを使用する

自分で用意したモデルを使用するには、次の手順を実行します。

1. Custom Vision Service を使用して分類器を[作成およびトレーニング](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)します。 モデルをエクスポートするには、 **[General (compact)]\(全般 (コンパクト)\)** など、__コンパクト__ なドメインを選択します。 既存の分類器をエクスポートするには、右上にある歯車アイコンを選択して、ドメインをコンパクトに変換します。 __[設定]__ でコンパクト モデルを選択し、保存してからプロジェクトをトレーニングします。  

1. [パフォーマンス] タブに移動して、[モデルをエクスポート](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)します。コンパクト ドメインによるトレーニングの反復を選択すると、[エクスポート] ボタンが表示されます。 *[エクスポート]* 、 *[ONNX]* 、 *[エクスポート]* の順に選択します。 ファイルの準備ができたら、 *[ダウンロード]* ボタンを選択します。

1. ONNX ファイルをプロジェクトの __[Assets]__ フォルダーにドロップします。 

1. ソリューション エクスプローラーで [Assets] フォルダーを右クリックし、 __[既存項目の追加]__ を選択します。 ONNX ファイルを選択します。

1. ソリューション エクスプローラーで、[Assets] フォルダーの ONNX ファイルを選択します。 ファイルの以下のプロパティを変更します。

    * __[ビルド アクション]__  ->  __[コンテンツ]__
    * __[出力ディレクトリにコピー]__  ->  __[新しい場合はコピーする]__

1. `_onnxFileNames` 変数を ONNX ファイルの名前に変更します。 `ClassLabel` も、モデルに含まれているラベルの数に変更します。

1. ビルドして実行します。

1. ボタンをクリックして、評価するイメージを選択します。

## <a name="next-steps"></a>次の手順

Custom Vision モデルをエクスポートして使用する別の方法については、次のドキュメントを参照してください。

* [モデルをエクスポートする](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [エクスポートされた Tensorflow モデルを Android アプリケーションで使用する](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [エクスポートされた CoreML モデルを Swift iOS アプリケーションで使用する](https://go.microsoft.com/fwlink/?linkid=857726)
* [エクスポートされた CoreML モデルを Xamarin を使って iOS アプリケーションで使用する](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Windows ML で ONNX モデルを使用する方法については、ドキュメント「[Windows ML でモデルをアプリに統合する](/windows/ai/windows-ml/integrate-model)」を参照してください。

---
title: モデルをモバイルにエクスポートする - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: この記事では、モバイル アプリケーションの作成に使用するため、またはローカルで実行してリアルタイム分類を行うために、モデルをエクスポートする方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: be28a10f8a96d21d64d08cfacee4543c065168ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "85391759"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>モバイル デバイスで使用するためにモデルをエクスポートする

Custom Vision Service では、分類子をエクスポートし、オフラインで実行できます。 エクスポートした分類子をアプリケーションに組み込み、デバイス上でローカル実行すると、リアルタイムの分類が可能になります。

## <a name="export-options"></a>エクスポート オプション

Custom Vision Service は次のエクスポートに対応しています。

* __Android__ 向け __Tensorflow__。
* __iOS11__ 向け __CoreML__。
* __Windows ML__ 向け __ONNX__。
* __[Vision AI Developer Kit](https://azure.github.io/Vision-AI-DevKit-Pages/)__ 。
* Windows、Linux、または ARM アーキテクチャ用の __Docker コンテナー__。 コンテナーには、Tensorflow モデルと、Custom Vision API を使用するためのサービス コードが含まれています。

> [!IMPORTANT]
> Custom Vision Service は __コンパクト__ ドメインのみをエクスポートします。 コンパクト ドメインで生成されたモデルは、モバイル デバイスのリアルタイム分類の制約に合わせて最適化されています。 コンパクト ドメインでビルドされた分類子は、トレーニング データの量が同じ標準ドメインより、わずかに正確性に欠けることがあります。
>
> 分類子を改善する方法については、[分類子の改善](getting-started-improving-your-classifier.md)に関するドキュメントを参照してください。

## <a name="convert-to-a-compact-domain"></a>コンパクト ドメインに変換する

> [!NOTE]
> このセクションの手順は、コンパクト ドメインに設定されていない既存のモデルがある場合にのみ適用されます。

既存のモデルのドメインは次の手順で変換します。

1. [Custom Vision の Web サイト](https://customvision.ai)で、__[ホーム]__ アイコンを選択し、プロジェクトを一覧表示します。

    ![ホーム アイコンとプロジェクト一覧の画像](./media/export-your-model/projects-list.png)

1. プロジェクトを選択し、ページ右上にある __歯車__ アイコンを選択します。

    ![歯車アイコンの画像](./media/export-your-model/gear-icon.png)

1. __[ドメイン]__ セクションで、__コンパクト__ ドメインの 1 つを選択します。 __[変更の保存]__ を選択して変更を保存します。 

    > [!NOTE]
    > Vision AI Dev Kit の場合は、プロジェクトを __[General (Compact)]\(汎用 (コンパクト)\)__ ドメインで作成する必要があります。また、**[Export Capabilities]\(エクスポート機能\)** セクションの **[Vision AI Dev Kit]** オプションを指定する必要があります。

    ![ドメイン選択の画像](./media/export-your-model/domains.png)

1. ページの上部から、__[Train]\(トレーニングする\)__ を選択し、新しいドメインを使用して再トレーニングします。

## <a name="export-your-model"></a>モデルをエクスポートする

再トレーニング後にモデルをエクスポートするには、次の手順を使用します。

1. **[パフォーマンス]** タブに移動し、__[エクスポート]__ を選択します。 

    ![エクスポート アイコンの画像](./media/export-your-model/export.png)

    > [!TIP]
    > __[エクスポート]__ エントリがない場合、選択されているイテレーションでコンパクト ドメインが使用されていません。 このページの __[イテレーション]__ セクションを利用し、コンパクト ドメインを使用しているイテレーションを選択し、__[エクスポート]__ を選択してください。

1. 目的のエクスポート形式を選択し、__[エクスポート]__ を選択してモデルをダウンロードします。

## <a name="next-steps"></a>次のステップ

以下のいずれかの記事またはサンプルを調べて、エクスポートしたモデルをアプリケーションに統合します。

* [Python での TensorFlow モデルの使用](export-model-python.md)
* [Windows Machine Learning での ONNX モデルの使用](custom-vision-onnx-windows-ml.md)
* Swift によって画像をリアルタイム分類するために、[iOS アプリケーションでの CoreML モデル](https://go.microsoft.com/fwlink/?linkid=857726)のサンプルを確認してください。
* Android 上で画像をリアルタイム分類するために、[Android アプリケーションでの Tensorflow モデル](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)のサンプルを確認してください。
* Xamarin iOS アプリで画像をリアルタイム分類するために、[Xamarin を利用した CoreML モデル](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)のサンプルを確認してください。

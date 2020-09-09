---
title: Custom Speech 用のモデルをデプロイする - Speech Service
titleSuffix: Azure Cognitive Services
description: このドキュメントでは、Custom Speech ポータルを使用してエンドポイントを作成およびデプロイする方法について説明します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: c7f03027abf7f3c5e330e5cd95075cce1152a7d9
ms.sourcegitcommit: 666303748238dfdf9da30d49d89b915af73b0468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2020
ms.locfileid: "85130417"
---
# <a name="deploy-a-custom-model"></a>カスタム モデルをデプロイする

データをアップロードして検査し、正確性を評価し、カスタム モデルをトレーニングした後は、アプリ、ツール、および製品で使用するカスタム エンドポイントをデプロイできます。 このドキュメントでは、[Custom Speech ポータル](https://speech.microsoft.com/customspeech)を使用してエンドポイントを作成およびデプロイする方法について説明します。

## <a name="create-a-custom-endpoint"></a>カスタム エンドポイントを作成する

新しいカスタム エンドポイントを作成するには、[Custom Speech ポータル](https://speech.microsoft.com/customspeech)にサインインし、ページの一番上の [Custom Speech] メニューから **[デプロイ]** を選択します。 これが最初の実行の場合は、表に記載されているエンドポイントがないことがわかります。 エンドポイントを作成したら、このページを使用して、デプロイされた各エンドポイントを追跡します。

次に、 **[エンドポイントの追加]** を選択し、カスタム エンドポイントの **[名前]** と **[説明]** を入力します。 このエンドポイントに関連付けるカスタム モデルを選択します。 このページから、ログを有効にすることもできます。 ログを使用すると、エンドポイントのトラフィックを監視できます。 無効にすると、トラフィックは保存されません。

![モデルをデプロイする方法](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> 必ず使用条件と価格の詳細に同意します。

次に、 **[作成]** を選択します。 この操作によって、 **[デプロイ]** ページに戻ります。 表には、カスタム エンドポイントに対応するエントリが含まれています。 エンドポイントの状態は現在の状態を示しています。 カスタム モデルを使用して新しいエンドポイントをインスタンス化するには、最大で 30 分かかることがあります。 デプロイの状態が **[完了]** に変わると、エンドポイントは使用できる状態です。

ご自分のエンドポイントがデプロイされると、エンドポイント名はリンクとして表示されます。 リンクをクリックすると、エンドポイント キー、エンドポイントの URL、サンプル コードなど、ご自分のエンドポイントに固有の情報が表示されます。

## <a name="view-logging-data"></a>ログ データを表示する

ログ データは **[エンドポイント] > [詳細]** からダウンロードできます。
> [!NOTE]
>ログ データは、Microsoft が所有するストレージで 30 日間使用できます。その後は削除されます。 お客様が所有するストレージ アカウントが Cognitive Services サブスクリプションにリンクされている場合は、ログ データは自動的に削除されません。

## <a name="next-steps"></a>次のステップ

* [こちら](how-to-specify-source-language.md)でカスタム モデルの使用方法を学びます。

## <a name="additional-resources"></a>その他のリソース

* [データを準備してテストする](how-to-custom-speech-test-data.md)
* [データを検査する](how-to-custom-speech-inspect-data.md)
* [データを評価する](how-to-custom-speech-evaluate-data.md)
* [モデルをトレーニングする](how-to-custom-speech-train-model.md)
* [モデルをデプロイする](how-to-custom-speech-deploy-model.md)

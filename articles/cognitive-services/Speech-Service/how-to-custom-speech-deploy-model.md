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
ms.openlocfilehash: fcd3e5fd6db0d7c18a5f9bfba030cd3d809b452f
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802523"
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

エンドポイントがデプロイされると、エンドポイント名はリンクとして表示されます。 リンクをクリックすると、エンドポイント キー、エンドポイントの URL、サンプル コードなど、エンドポイントに固有の情報が表示されます。

## <a name="view-logging-data"></a>ログ データを表示する

ログ データは **[エンドポイント] > [詳細]** からダウンロードできます。

## <a name="next-steps"></a>次の手順

* カスタム エンドポイントを [Speech SDK](speech-sdk.md) と共に使用します

## <a name="additional-resources"></a>その他のリソース

* [データを準備してテストする](how-to-custom-speech-test-data.md)
* [データを検査する](how-to-custom-speech-inspect-data.md)
* [データを評価する](how-to-custom-speech-evaluate-data.md)
* [モデルをトレーニングする](how-to-custom-speech-train-model.md)
* [モデルをデプロイする](how-to-custom-speech-deploy-model.md)

---
title: カスタム モデル - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Form Recognizer API カスタム モデルに関連する概念 - 使用法と制限について説明します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 235e868952bb742b082492e2e388170a921c1929
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467976"
---
# <a name="form-recognizer-custom-models"></a>Form Recognizer カスタム モデル

Form Recognizer により、高度な機械学習テクノロジを使用して、フォームやドキュメントのデータを分析し、抽出します。 Form Recognizer モデルは、特定のコンテンツを分析するための参照として使用される抽出データの表現です。 Form Recognizer モデルには、次の 2 つの種類があります。

* **カスタム モデル**。 Form Recognizer カスタム モデルは、ビジネスに固有の "_フォーム_" から抽出されたデータを表します。 カスタム モデルは、個別のフォーム データを分析するようにトレーニングする必要があります。

* **事前構築済みのモデル**。 Form Recognizer は、現在、_領収書、名刺、身分証明書_、および _請求書_ の事前構築済みのモデルをサポートしています。 事前構築済みのモデルにより、ドキュメント イメージから情報が検出および抽出され、抽出されたデータは構造化された JSON 出力で返されます。

## <a name="what-does-a-custom-model-do"></a>カスタム モデルの機能

Form Recognizer を使用すると、ユース ケースに関連するフォームから情報を抽出するモデルをトレーニングできます。 始めるために必要な同じフォームの種類の例は 5 つのみです。 カスタム モデルは、ラベル付きデータセットの有無にかかわらずトレーニングできます。

## <a name="create-use-and-manage-your-custom-model"></a>カスタム モデルの作成、使用、管理

大まかに説明すると、カスタム モデルを構築、トレーニング、および使用するための手順は次のとおりです。

> [!div class="nextstepaction"]
> [1.トレーニング データセットをアセンブルする](build-training-data-set.md#custom-model-input-requirements)

カスタム モデルの構築は、トレーニング データセットの確立から始まります。 サンプル データセットには、同じ種類の完成したフォームが少なくとも 5 つ必要です。 異なるファイルの種類にして、テキストと手書きの両方を含めることができます。 フォームは同じ種類のドキュメントで、Form Recognizer の[入力要件](build-training-data-set.md#custom-model-input-requirements)に従う必要があります。  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;

> [!div class="nextstepaction"]
> [2.トレーニング データセットをアップロードする](build-training-data-set.md#upload-your-training-data)

トレーニング データを Azure BLOB ストレージ コンテナーにアップロードする必要があります。 コンテナーを含む Azure Storage アカウントを作成する方法がわからない場合は、[Azure portal の Azure Storage に関するクイックスタート](../../storage/blobs/storage-quickstart-blobs-portal.md)を *参照してください*。 Free 価格レベル (F0) を使ってサービスを試用し、後から運用環境用の有料レベルにアップグレードします。  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;
> [!div class="nextstepaction"]
> [3.カスタム モデルをトレーニングする](quickstarts/client-library.md#train-a-custom-model)

ラベル付きデータ セットの[ない](quickstarts/client-library.md#train-a-model-without-labels)、または[ある](quickstarts/client-library.md#train-a-model-with-labels)モデルをトレーニングできます。 ラベルのないデータセットの場合、Layout API のみに依存し、ユーザーの入力を追加することなく重要な情報を検出および特定できます。 ラベル付きデータセットの場合も、Layout API をに依存していますが、特定のラベルやフィールドの場所など、補助的なユーザーの入力が含まれています。 ラベル付きデータとラベルなしデータの両方を使用するには、ラベル付きトレーニング データに同じ種類の少なくとも 5 つの完成したフォームから始めて、ラベルなしデータを必要なデータ セットに追加します。  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;  

>[!div class="nextstepaction"]
> [4.カスタム モデルを使用してドキュメントを分析する](quickstarts/client-library.md#analyze-forms-with-a-custom-model)

トレーニング データセットの一部ではないフォームを使用して、新しくトレーニングしたモデルをテストします。 カスタム モデルのパフォーマンスを向上させるために、さらにトレーニングを続けることができます。  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;

> [!div class="nextstepaction"]
> [5.カスタム モデルを管理する](quickstarts/client-library.md#manage-custom-models)

いつでも、サブスクリプションに属するすべてのカスタム モデルの一覧を表示すること、特定のカスタム モデルに関する情報を取得すること、アカウントからカスタム モデルを削除することができます。

## <a name="next-steps"></a>次のステップ

詳細については、 **[Form Recognizer API リファレンス](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)** のドキュメントを参照してください。
>

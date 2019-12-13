---
title: クイック スタート:カスタム モデルを構築、デプロイ、および使用する - カスタム翻訳ツール
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Custom Translator を使用して翻訳システムを構築する詳細なプロセスを実行します。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: swmachan
ms.topic: quickstart
ms.openlocfilehash: d93c6333b811885eec37e8401bee27afeedf1475
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975756"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>クイック スタート:翻訳のためのカスタム モデルを構築、デプロイ、および使用する

この記事では、Custom Translator を使用して翻訳システムを構築する詳細な手順が示されています。

## <a name="prerequisites"></a>前提条件

1. [Custom Translator](https://portal.customtranslator.azure.ai) ポータルを使用するには、サインインするための [Microsoft アカウント](https://signup.live.com)または [Azure AD アカウント](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure でホストされている組織アカウント) が必要になります。

2. Azure portal を通じた Translator Text API へのサブスクリプション。 Custom Translator 内のお客様のワークスペースに関連付ける Translator Text API サブスクリプション キーが必要です。 「[Translator Text API にサインアップする方法](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup)」を参照してください。

3. 上記の両方がある場合は、[Custom Translator](https://portal.customtranslator.azure.ai) ポータルにサインインします。 Custom Translator ポータルで [設定] ページに移動します。ここでは、お客様の Microsoft Translator Text API サブスクリプション キーをお客様のワークスペースに関連付けることができます。

## <a name="create-a-project"></a>プロジェクトの作成

Custom Translator ポータルのランディング ページで、[新しいプロジェクト] をクリックします。 ダイアログで、お客様が望むプロジェクト名、言語ペア、カテゴリ、その他の関連フィールドを入力することができます。 その後、お客様のプロジェクトを保存します。 詳細については、[プロジェクトの作成](how-to-create-project.md)に関するページを参照してください。

![Create project](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>ドキュメントのアップロード

次に、[トレーニング](training-and-model.md#training-dataset-for-custom-translator)、[チューニング](training-and-model.md#tuning-dataset-for-custom-translator)、および[テスト](training-and-model.md#testing-dataset-for-custom-translator)のドキュメント セットをアップロードします。 [並列](what-are-parallel-documents.md)ドキュメントと複合ドキュメントの両方をアップロードすることができます。 [辞書](what-is-dictionary.md)をアップロードすることもできます。

ドキュメントのアップロードは、ドキュメント タブからも、特定のプロジェクトのページからも行うことができます。

![ドキュメントのアップロード](media/quickstart/ct-how-to-upload.png)

ドキュメントをアップロードするときに、ドキュメントの種類 (トレーニング、チューニング、またはテスト) と言語ペアを選択します。 並列ドキュメントをアップロードする場合は、ドキュメント名をさらに指定する必要があります。 詳細については、[ドキュメントのアップロード](how-to-upload-document.md)に関するページを参照してください。

## <a name="create-a-model"></a>モデルの作成

お客様にとって必要なドキュメントをすべてアップロードしたら、次の手順は、お客様のモデルの構築になります。

作成したプロジェクトを選択します。 お客様がアップロードしたすべてのドキュメントが表示されます。これらでは、このプロジェクトと言語ペアが共有されます。 お客様のモデルに含めるドキュメントを選択します。 [トレーニング](training-and-model.md#training-dataset-for-custom-translator) データ、[チューニング](training-and-model.md#tuning-dataset-for-custom-translator) データ、および[テスト](training-and-model.md#testing-dataset-for-custom-translator) データを選択できます。または、トレーニング データだけを選択して、お客様のモデル用のチューニング セットおよびテスト セットが Custom Translator によって自動的に構築されるようにすることができます。

![モデルの作成](media/quickstart/ct-how-to-train.png)

お客様が望むドキュメントを選択したら、[モデルの作成] ボタンをクリックして、お客様のモデルを作成し、トレーニングを開始します。 お客様のトレーニングの状態と、トレーニングしたすべてのモデルの詳細は、[モデル] タブで確認できます。

詳細については、[モデルの作成](how-to-train-model.md)に関するページを参照してください。

## <a name="analyze-your-model"></a>モデルの分析

お客様のトレーニングが正常に完了したら、結果を検査します。 BLEU スコアは、お客様の翻訳の品質を示す 1 つのメトリックです。 [テスト] タブに移動し、[System Results]\(システム結果\) をクリックすると、お客様のカスタム モデルで作成された翻訳とお客様のテスト セットで提供された翻訳を手動で比較することもできます。 これらの翻訳のいくつかを手動で検査すると、お客様のシステムによって生成された翻訳の品質についてよく理解できます。 詳細については、[システム テスト結果](how-to-view-system-test-results.md)に関するページを参照してください。

## <a name="deploy-a-trained-model"></a>トレーニング済みモデルのデプロイ

お客様のトレーニング済みモデルをデプロイする準備ができたら、[デプロイ] ボタンをクリックします。 プロジェクトごとに 1 つのモデルをデプロイすることができ、[状態] 列でお客様のデプロイの状態を表示できます。 詳細については、[モデルのデプロイ](how-to-view-system-test-results.md#deploy-a-model)に関するセクションを参照してください。

![トレーニング済みモデルのデプロイ](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>デプロイしたモデルの使用

デプロイしたモデルには、Microsoft Translator [Text API V3 を通じて CategoryID を指定することによって](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)アクセスすることができます。 Translator Text API の詳細については、[API リファレンス](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) Web ページを参照してください。

## <a name="next-steps"></a>次の手順

- [Custom Translator ワークスペース内を移動し、お客様のプロジェクトを管理する](workspace-and-project.md)方法について学習します。

---
title: 'クイック スタート: カスタム モデルを構築、デプロイ、および使用する - Custom Translator'
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Custom Translator を使用して翻訳システムを構築する詳細なプロセスを実行します。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: lajanuar
ms.topic: quickstart
ms.openlocfilehash: ae417006903c8d2478126f928f05647361bad1b9
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896208"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>クイック スタート: 翻訳のためのカスタム モデルを構築、デプロイ、および使用する

この記事では、Custom Translator を使用して翻訳システムを構築する詳細な手順が示されています。

## <a name="prerequisites"></a>前提条件

1. [Custom Translator](https://portal.customtranslator.azure.ai) ポータルを使用するには、サインインするための [Microsoft アカウント](https://signup.live.com)または [Azure AD アカウント](../../../active-directory/fundamentals/active-directory-whatis.md) (Azure でホストされている組織アカウント) が必要になります。

2. Azure portal を通じた Translator Text API へのサブスクリプション。 Custom Translator 内のお客様のワークスペースに関連付ける Translator Text API サブスクリプション キーが必要です。 「[Translator Text API にサインアップする方法](../translator-how-to-signup.md)」を参照してください。

3. 上記の両方がある場合は、[カスタム翻訳ツール](https://portal.customtranslator.azure.ai) ポータルにサインインしてワークスペースとプロジェクトを作成し、ファイルをアップロードしてモデルを作成、デプロイします。

>[!Note]
>[対応 VNET](../../../api-management/api-management-using-with-vnet.md) 内に作成された Translator Text API リソース用のワークスペースをカスタム翻訳ツールで作成することはできません。

## <a name="create-a-workspace"></a>ワークスペースの作成

初回使用時は、ワークスペースを作成して Microsoft Translator Text API サブスクリプションに関連付ける際に、サービス利用規約への同意を求められます。

![ワークスペースの作成](media/quickstart/terms-of-service.png)
![ワークスペースの作成の画像 1](media/quickstart/create-workspace-1.png)
![ワークスペースの作成の画像 2](media/quickstart/create-workspace-2.png)
![ワークスペースの作成の画像 3](media/quickstart/create-workspace-3.png)
![ワークスペースの作成の画像 4](media/quickstart/create-workspace-4.png)
![ワークスペースの作成の画像 5](media/quickstart/create-workspace-5.png)
![ワークスペースの作成の画像 6](media/quickstart/create-workspace-6.png)

2 回目以降は、Custom Translator ポータルにアクセスし、[設定] ページに移動してワークスペースを管理できます。ワークスペースを作成したり、Microsoft Translator Text API のサブスクリプション キーをワークスペースに関連付けたり、共同所有者を追加したり、サブスクリプション キーを変更したりすることが可能です。

## <a name="create-a-project"></a>プロジェクトの作成

Custom Translator ポータルのランディング ページで、[新しいプロジェクト] をクリックします。 ダイアログで、お客様が望むプロジェクト名、言語ペア、カテゴリ、その他の関連フィールドを入力することができます。 その後、お客様のプロジェクトを保存します。 詳細については、[プロジェクトの作成](how-to-create-project.md)に関するページを参照してください。

![Create project](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>ドキュメントのアップロード

次に、[トレーニング](training-and-model.md#training-document-type-for-custom-translator)、[チューニング](training-and-model.md#tuning-document-type-for-custom-translator)、および[テスト](training-and-model.md#testing-dataset-for-custom-translator)のドキュメント セットをアップロードします。 [並列](what-are-parallel-documents.md)ドキュメントと複合ドキュメントの両方をアップロードすることができます。 [辞書](what-is-dictionary.md)をアップロードすることもできます。

ドキュメントのアップロードは、ドキュメント タブからも、特定のプロジェクトのページからも行うことができます。

![ドキュメントのアップロード](media/quickstart/ct-how-to-upload.png)

ドキュメントをアップロードするときに、ドキュメントの種類 (トレーニング、チューニング、またはテスト) と言語ペアを選択します。 並列ドキュメントをアップロードする場合は、ドキュメント名をさらに指定する必要があります。 詳細については、[ドキュメントのアップロード](how-to-upload-document.md)に関するページを参照してください。

## <a name="create-a-model"></a>モデルを作成する

お客様にとって必要なドキュメントをすべてアップロードしたら、次の手順は、お客様のモデルの構築になります。

作成したプロジェクトを選択します。 お客様がアップロードしたすべてのドキュメントが表示されます。これらでは、このプロジェクトと言語ペアが共有されます。 お客様のモデルに含めるドキュメントを選択します。 [トレーニング](training-and-model.md#training-document-type-for-custom-translator) データ、[チューニング](training-and-model.md#tuning-document-type-for-custom-translator) データ、および[テスト](training-and-model.md#testing-dataset-for-custom-translator) データを選択できます。または、トレーニング データだけを選択して、お客様のモデル用のチューニング セットおよびテスト セットが Custom Translator によって自動的に構築されるようにすることができます。

![モデルを作成する](media/quickstart/ct-how-to-train.png)

お客様が望むドキュメントを選択したら、[モデルの作成] ボタンをクリックして、お客様のモデルを作成し、トレーニングを開始します。 お客様のトレーニングの状態と、トレーニングしたすべてのモデルの詳細は、[モデル] タブで確認できます。

詳細については、[モデルの作成](how-to-train-model.md)に関するページを参照してください。

## <a name="analyze-your-model"></a>モデルの分析

お客様のトレーニングが正常に完了したら、結果を検査します。 BLEU スコアは、お客様の翻訳の品質を示す 1 つのメトリックです。 [テスト] タブに移動し、[System Results]\(システム結果\) をクリックすると、お客様のカスタム モデルで作成された翻訳とお客様のテスト セットで提供された翻訳を手動で比較することもできます。 これらの翻訳のいくつかを手動で検査すると、お客様のシステムによって生成された翻訳の品質についてよく理解できます。 詳細については、[システム テスト結果](how-to-view-system-test-results.md)に関するページを参照してください。

## <a name="deploy-a-trained-model"></a>トレーニング済みモデルのデプロイ

お客様のトレーニング済みモデルをデプロイする準備ができたら、[デプロイ] ボタンをクリックします。 プロジェクトごとに 1 つのモデルをデプロイすることができ、[状態] 列でお客様のデプロイの状態を表示できます。 詳細については、[モデルのデプロイ](how-to-view-system-test-results.md#deploy-a-model)に関するセクションを参照してください。

![トレーニング済みモデルのデプロイ](media/quickstart/ct-how-to-deploy.png)

## <a name="swap-deployed-model"></a>デプロイしたモデルを入れ替える

デプロイしたモデルをプロジェクト内の別のものと入れ替えるには、目的のモデルの横に表示されている [スワップ] ボタンをクリックします。 スワップ処理中、デプロイしたモデルは引き続き翻訳要求の処理に使用できます。 

![デプロイしたモデルを入れ替える](media/quickstart/ct-how-to-swap-model.png)

## <a name="use-a-deployed-model"></a>デプロイしたモデルの使用

デプロイしたモデルには、Microsoft Translator [Text API V3 を通じて CategoryID を指定することによって](../reference/v3-0-translate.md?tabs=curl)アクセスすることができます。 Translator Text API の詳細については、[API リファレンス](../reference/v3-0-reference.md) Web ページを参照してください。

## <a name="next-steps"></a>次のステップ

- [Custom Translator ワークスペース内を移動し、お客様のプロジェクトを管理する](workspace-and-project.md)方法について学習します。
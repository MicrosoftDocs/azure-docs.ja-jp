---
title: 例:Azure Machine Learning を使用してカスタム スキルを作成およびデプロイする
titleSuffix: Azure Cognitive Search
description: この例では、Azure Machine Learning を使用して、Azure Cognitive Search の AI エンリッチメント パイプラインのカスタム スキルを作成してデプロイする方法について説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 98d8395236bf955eed88f36c03c96981fa0e4b6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745636"
---
# <a name="example-build-and-deploy-a-custom-skill-with-azure-machine-learning"></a>例:Azure Machine Learning を使用してカスタム スキルを作成およびデプロイする 

この例では、[ホテルのレビューのデータセット](https://www.kaggle.com/datafiniti/hotel-reviews) (クリエイティブ コモンズの [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.txt) ライセンス下で配布) を使用して、Azure Machine Learning を使用してレビューからアスペクトベースのセンチメントを抽出する[カスタム スキル](./cognitive-search-aml-skill.md)を作成します。 これにより、同じレビュー内の肯定的および否定的なセンチメントを、スタッフ、部屋、ロビー、プールなどの識別されたエンティティに正しく割り当てることができます。

Azure Machine Learning でアスペクトベースのセンチメント モデルをトレーニングするには、[nlp レシピ リポジトリ](https://github.com/microsoft/nlp-recipes/tree/master/examples/sentiment_analysis/absa)を使用します。 その後、そのモデルは Azure Kubernetes クラスター上にエンドポイントとしてデプロイされます。 デプロイされたエンドポイントは、Cognitive Search サービスで使用するための AML スキルとしてエンリッチメント パイプラインに追加されます。

提供されるデータセットは 2 つあります。 モデルを自分でトレーニングする場合は、hotel_reviews_1000.csv ファイルが必要です。 トレーニング手順をスキップする場合は、 hotel_reviews_100.csv をダウンロードします。

> [!div class="checklist"]
> * Azure Cognitive Search インスタンスを作成する
> * Azure Machine Learning ワークスペースを作成する (検索サービスとワークスペースが同じサブスクリプションに存在する必要があります)
> * モデルをトレーニングして Azure Kubernetes クラスターにデプロイする
> * デプロイされたモデルに AI エンリッチメント パイプラインをリンクする
> * デプロイされたモデルからの出力をカスタム スキルとして取り込む

> [!IMPORTANT] 
> このスキルは現在、パブリック プレビューの段階です。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 現在、.NET SDK によるサポートはありません。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料のサブスクリプション](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を入手できます。
* [Cognitive Search サービス](./search-get-started-arm.md)
* [Cognitive Services リソース](../cognitive-services/cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows)
* [Azure Storage アカウント](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure Machine Learning ワークスペース](../machine-learning/how-to-manage-workspace.md)

## <a name="setup"></a>セットアップ

* [サンプル リポジトリ](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)のコンテンツをクローンまたはダウンロードします。
* ZIP ファイルをダウンロードしたら、コンテンツを展開します。 ファイルが読み取り/書き込み可能であることを確認します。
* Azure のアカウントとサービスを設定する際、簡単にアクセスできるテキスト ファイルにそれらの名前とキーをコピーします。 名前とキーは、ノートブックの最初のセルに追加されます。ここでは、Azure サービスにアクセスするための変数が定義されています。
* Azure Machine Learning とその要件に詳しくない場合は、作業を開始する前に次のドキュメントを確認してください。
 * [Azure Machine Learning のための開発環境を構成する](../machine-learning/how-to-configure-environment.md)
 * [Azure portal 内で Azure Machine Learning ワークスペースを作成および管理する](../machine-learning/how-to-manage-workspace.md)
 * Azure Machine Learning の開発環境を構成する場合は、[クラウドベースのコンピューティング インスタンス](../machine-learning/how-to-configure-environment.md#compute-instance)を使用して開始時の速度と使いやすさを向上させることを検討してください。
* ストレージ アカウント内のコンテナーにデータセット ファイルをアップロードします。 ノートブックでトレーニング手順を実行するには、大きなファイルが必要です。 トレーニング手順をスキップする場合は、ファイルのサイズを小さくすることをお勧めします。

## <a name="open-notebook-and-connect-to-azure-services"></a>ノートブックを開いて Azure サービスに接続する

1. Azure サービスへのアクセスを許可する変数に関する必要な情報を最初のセル内に入力し、そのセルを実行します。
1. 2 番目のセルを実行すると、お使いのサブスクリプションの検索サービスに接続済みであることが確認されます。
1. セクション 1.1 から 1.5 では、検索サービスのデータストア、スキルセット、インデックス、インデクサーが作成されます。

この時点では、Azure Machine Learning でトレーニング データ セットと実験を作成する手順をスキップし、GitHub リポジトリのモデル用フォルダーに用意されている 2 つのモデルの登録に直接進むことができます。 これらの手順をスキップする場合は、ノートブックのセクション 3.5 のスコアリング スクリプトの記述に進みます。 その結果、時間が節約されます。データのダウンロードとアップロードの手順の完了には、最大 30 分かかる場合があります。

## <a name="creating-and-training-the-models"></a>モデルを作成してトレーニングする

セクション 2 には、nlp レシピ リポジトリから glove 埋め込みファイルをダウンロードする 6 つのセルがあります。 ダウンロード後、このファイルは Azure Machine Learning データストアにアップロードされます。 この .zip ファイルのサイズは約 2G あるため、これらのタスクを実行するには時間がかかります。 アップロードが完了すると、トレーニング データが抽出され、セクション 3 に進むことができます。

## <a name="train-the-aspect-based-sentiment-model-and-deploy-your-endpoint"></a>アスペクトベースのセンチメント モデルをトレーニングしてエンドポイントをデプロイする

ノートブックのセクション 3 では、セクション 2 で作成したモデルをトレーニングして登録し、Azure Kubernetes クラスターにエンドポイントとしてデプロイします。 Azure Kubernetes に詳しくない場合は、推論クラスターを作成する前に次の記事を確認することを強くお勧めします。

* [Azure Kubernetes サービスの概要](../aks/intro-kubernetes.md)
* [Azure Kubernetes Services (AKS) における Kubernetes の中心概念](../aks/concepts-clusters-workloads.md)
* [Azure Kubernetes Service (AKS) のクォータ、仮想マシンのサイズの制限、およびリージョンの可用性](../aks/quotas-skus-regions.md)

推論クラスターの作成とデプロイには、最大 30 分かかる場合があります。 最後の手順に進む前に Web サービスをテストし、スキルセットを更新して、インデクサーを実行することをお勧めします。

## <a name="update-the-skillset"></a>スキルセットを更新する

ノートブックのセクション 4 には、スキルセットとインデクサーを更新する 4 つのセルがあります。 また、ポータルを使用して、新しいスキルを選択してスキルセットに適用してから、インデクサーを実行して検索サービスを更新することもできます。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Active-Learning-with-Azure-Cognitive-Search/player#time=19m35s:paused/03/player]

ポータルで [スキルセット] に移動し、[スキルセットの定義 (JSON)] リンクを選択します。 ポータルには、ノートブックの最初のセルで作成されたスキルセットの JSON が表示されます。 画面の右側には、スキル定義テンプレートを選択できるドロップダウン メニューがあります。 Azure Machine Learning (AML) テンプレートを選択します。 Azure ML ワークスペースの名前と、推論クラスターにデプロイされたモデルのエンドポイントを指定します。 このテンプレートはエンドポイントの URI とキーで更新されます。

> :::image type="content" source="media/cognitive-search-aml-skill/portal-aml-skillset-definition.png" alt-text="スキルセット定義テンプレート":::

このウィンドウからスキルセット テンプレートをコピーし、左側のスキルセット定義に貼り付けます。 テンプレートを編集して、不足している次の値を指定します。

* 名前
* 説明
* Context
* "inputs" の名前とソース
* "outputs" の名前とターゲット名

スキルセットを保存します。

スキルセットを保存したら、インデクサーに移動し、[インデクサー定義 (JSON)] リンクを選択します。 ポータルには、ノートブックの最初のセルで作成されたインデクサーの JSON が表示されます。 出力フィールドのマッピングは、インデクサーが適切に処理して渡すことができるように、追加のフィールド マッピングを使用して更新する必要があります。 変更を保存し、[実行] を選択します。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[すべてのリソース]** または **[リソース グループ]** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム スキルの Web API を確認する](./cognitive-search-custom-skill-web-api.md)
> [エンリッチメント パイプラインへのカスタム スキルの追加の詳細を確認する](./cognitive-search-custom-skill-interface.md)
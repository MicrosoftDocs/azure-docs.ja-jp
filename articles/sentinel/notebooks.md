---
title: Azure Sentinel でセキュリティ ハンティングにノートブックを使用する
description: この記事では、Azure Sentinel 検出機能でノートブックを使用する方法について説明します。
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 43d7a697b3cb013a73a0b14db8ec1758244ae3b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97092195"
---
# <a name="use-jupyter-notebook-to-hunt-for-security-threats"></a>Jupyter Notebook を使用してセキュリティの脅威を検出する

Azure Sentinel の基盤となるのは、ハイ パフォーマンス クエリと動的スキーマを併用して大規模なデータ ボリュームまでスケーリングするデータ ストアです。 Azure portal とすべての Azure Sentinel ツールでは、このデータ ストアにアクセスする際に、共通の API が使用されます。 同じ API を [Jupyter](https://jupyter.org/) Notebook や Python などの外部ツールでも使用できます。 ポータルでは多くの一般的なタスクを実行できますが、Jupyter では、データに対して、より幅広い処理が可能です。 高度なプログラミング性と膨大なライブラリ コレクションを組み合わせて、機械学習、視覚化、データ解析を行えます。 こうした特性から、Jupyter は、セキュリティの調査や検出にうってつけのツールとなっています。

![ノートブックの例](./media/notebooks/sentinel-notebooks-map.png)

Jupyter のエクスペリエンスが Azure portal に統合されているため、簡単にノートブックを作成して実行し、データを解析できます。 *Kqlmagic* ライブラリは、Azure Sentinel からクエリを取得して、ノートブック内で直接実行できるようにする接着剤の働きをします。 クエリでは、[Kusto クエリ言語](https://kusto.azurewebsites.net/docs/kusto/query/index.html)が使用されます。 Azure Sentinel には、Microsoft のセキュリティ アナリストが開発した複数のノートブックがパッケージされています。 これらのノートブックの中には、特定のシナリオ向けに作成され、そのまま使用できるようになっているものがあります。 一方、技術や機能を説明するサンプルとして作成されたものもあります。これをコピーまたは改造して、独自のノートブック内で使用することができます。 Azure Sentinel コミュニティの GitHub からインポートできるノートブックもあります。

統合された Jupyter エクスペリエンスでは、[Azure Notebooks](https://notebooks.azure.com/) を使用して、ノートブックが保存、共有、および実行されます。 これらのノートブックは、ローカルで実行することも (コンピューター上に Python 環境と Jupyter がある場合)、他の JupterHub 環境 (Azure Databricks など) 内で実行することもできます。

ノートブックは、次の 2 つのコンポーネントから構成されています。

- ブラウザーベースのインターフェイス。クエリやコードを入力して実行すると、実行結果が表示されます。
- "*カーネル*"。コード自体を解析して実行する役割を果たします。

Azure Sentinel ノートブックのカーネルは、Azure 仮想マシン (VM) で実行されます。 ノートブックに複雑な機械学習モデルが含まれている場合は、より強力な仮想マシンを活用するためのライセンス オプションがいくつか存在します。

Azure Sentinel ノートブックでは、pandas、matplotlib、bokeh など、多くのポピュラーな Python ライブラリが使用されています。 次の分野に対応したその他の多数の Python パッケージから選択することもできます。

- 視覚化とグラフィックス
- データ処理と解析
- 統計と数値計算
- 機械学習とディープ ラーニング

オープンソースの Jupyter セキュリティ ツールも [msticpy](https://github.com/Microsoft/msticpy/) という名前のパッケージでリリースされています。 このパッケージは、組み込みのノートブックの多くで使用されています。 Msticpy ツールは、特に検出や調査用のノートブックの作成に役立つように設計されており、新機能の開発や改良が鋭意進められています。

[Azure Sentinel コミュニティの GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel)は、将来 Microsoft が作成した Azure Sentinel ノートブックやコミュニティから投稿された Azure Sentinel ノートブックを保管するための場所です。

ノートブックを使用するには、まず、Azure Machine Learning (ML) ワークスペースを作成する必要があります。

## <a name="create-an-azure-ml-workspace"></a>Azure ML ワークスペースを作成する

1. Azure portal から **[Azure Sentinel]**  >  **[脅威の管理]**  >  **[ノートブック]** に移動し、 **[ノートブックの起動]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![ノートブックを起動して Azure ML ワークスペースを起動する](./media/notebooks/sentinel-notebooks-launch.png)

1. **[AzureML ワークスペース]** で **[新規作成]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![ワークスペースを作成する](./media/notebooks/sentinel-notebooks-azureml-create.png)

1. **[機械学習]** ページに次の情報を入力し、 **[確認と作成]** を選択します。

    |フィールド|説明|
    |--|--|
    |サブスクリプション|使用する Azure サブスクリプションを選択します。|
    |Resource group|サブスクリプションの既存のリソース グループを使用するか、任意の名前を入力して新しいリソース グループを作成します。 リソース グループは、Azure ソリューションの関連するリソースを保持します。 この例では、**AzureMLRG** を使用します。|
    |ワークスペース名|ワークスペースを識別する一意の名前を入力します。 この例では、**testworkspace1** を使用します。 名前は、リソース グループ全体で一意である必要があります。 覚えやすく、他のユーザーが作成したワークスペースと区別しやすい名前を使用します。|
    |Region|ユーザーとデータ リソースに最も近い場所を選択し、ワークスペースを作成します。|
    |ワークスペースのエディション|この例では、ワークスペースの種類として **[Basic]** を選択します。 利用できる機能と価格は、ワークスペースの種類 (Basic および Enterprise) によって決まります。|

    > [!div class="mx-imgBorder"]
    > ![ワークスペースの詳細を指定する](./media/notebooks/sentinel-notebooks-azureml-basics.png)

1. 情報を見直して、それが正しいことを確認してから、 **[作成]** を選択してワークスペースのデプロイを開始します。

    > [!div class="mx-imgBorder"]
    > ![ワークスペースの詳細を見直す](./media/notebooks/sentinel-notebooks-azureml-review.png)

    クラウドにワークスペースを作成するのに数分かかる場合があります。その間、 **[概要]** ページに現在のデプロイの状態が表示されます。

    > [!div class="mx-imgBorder"]
    > ![ワークスペースのデプロイ](./media/notebooks/sentinel-notebooks-azureml-deploy.png)

デプロイが完了したら、新しい Azure ML ワークスペース内でノートブックを起動できます。

> [!div class="mx-imgBorder"]
> ![ワークスペースのデプロイが成功した](./media/notebooks/sentinel-notebooks-azureml-complete.png)

## <a name="launch-a-notebook-using-your-azure-ml-workspace"></a>Azure ML ワークスペースを使用してノートブックを起動する

1. Azure portal から **[Azure Sentinel]**  >  **[Threat management]\(脅威の管理\)**  >  **[Notebooks]\(ノートブック\)** に移動します。ここで、Azure Sentinel によって提供されるノートブックを確認できます。

    > [!TIP]
    > **[ガイドとフィードバック]** を選択して、ノートブックに関する追加のヘルプとガイダンスが表示されるウィンドウを開きます。
    > ![ノートブックのガイドを表示する](./media/notebooks/sentinel-azure-notebooks-guides.png)

1. 個々のノートブックを選択し、それらの説明、必要なデータの種類、およびデータ ソースを表示します。

    > [!div class="mx-imgBorder"]
    > ![ノートブックの詳細を表示する](./media/notebooks/sentinel-azure-notebooks-view.png)

1. 使用するノートブックを選択してから、 **[ノートブックの起動]** を選択して、Azure Sentinel ワークスペースに接続する新しい Azure Notebooks プロジェクトにノートブックを複製し、構成します。 プロセスが完了すると、Azure Notebooks 内でノートブックが開き、実行できるようになります。

    > [!div class="mx-imgBorder"]
    > ![ノートブックを選択する](./media/notebooks/sentinel-azure-notebooks-select.png)

1. [AzureML ワークスペース] で Azure ML ワークスペースを選択してから、 **[起動]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![ノートブックの起動](./media/notebooks/sentinel-azure-notebooks-launch.png)

1. コンピューティング インスタンスを選択します。 コンピューティング インスタンスがない場合は、以下を実行します。
    1. プラス記号 (+) を選択して **[新しいコンピューティング インスタンス]** ウィザードを開始します。

        > [!div class="mx-imgBorder"]
        > ![コンピューティング インスタンスのウィザードを開始する](./media/notebooks/sentinel-azure-notebooks-compute-wizard.png)

    1. **[新しいコンピューティング インスタンス]** ページで、必要な情報を指定してから、 **[作成]** を選択します。

        > [!div class="mx-imgBorder"]
        > ![コンピューティング インスタンスを作成する](./media/notebooks/sentinel-azure-notebooks-compute-create.png)

1. ノートブック サーバーが作成されたら、各セル内で実行アイコンを選択し、ノートブック内のコードを実行します。

    > [!div class="mx-imgBorder"]
    > ![ノートブックを実行する](./media/notebooks/sentinel-azure-notebooks-run.png)

推奨事項:

- Azure Sentinel のデータのクエリを行うことの概要の紹介については、[Azure ML Notebooks と Azure Sentinel の使用開始](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb)ガイドを参照してください。

- GitHub の [**Sample-Notebooks**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) サブフォルダーには、追加のサンプル ノートブックがあります。 これらのサンプルのノートブックは、データと共に保存されているため、所定の出力を簡単に確認できます。 これらのノートブックは、[nbviewer](https://nbviewer.jupyter.org/) で閲覧することをお勧めします。

- GitHub の [**HowTos**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) フォルダーに含まれるノートブックには、Python の既定バージョンの設定、DSVM の構成、ノートブックからの Azure Sentinel ブックマークの作成、その他の件名などが記述されています。

これらのノートブックは、便利なツールとして利用できるだけではなく、自分でノートブックを開発するときに使う説明やコード サンプルとしても利用できます。

ご提案、機能の要求、投稿されたノートブック、バグ レポート、既存のノートブックへの改善や追加など、フィードバックがございましたら、ぜひお寄せください。 問題またはフォークを作成して、投稿をアップロードするには、[Azure Sentinel コミュニティの GitHub](https://github.com/Azure/Azure-Sentinel) にアクセスしてください。

## <a name="next-steps"></a>次の手順

この記事では、Azure Sentinel 内で Jupyter Notebook を使い始める方法を学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [脅威を事前に検出する](hunting.md)
- [ブックマークを使用して検出中に関心のある情報を保存する](bookmarks.md)

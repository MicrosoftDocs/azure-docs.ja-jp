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
ms.date: 11/25/2019
ms.openlocfilehash: bf63d5c8cb46fd791508af40dcefd7b39d4ba9de
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652032"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Jupyter のノートブックを使用してセキュリティの脅威を検出する

Azure Sentinel の基盤となるのは、ハイ パフォーマンス クエリと動的スキーマを併用し、大量のデータ ボリュームにスケーリングできるデータ ストアです。 Azure portal とすべての Azure Sentinel ツールでは、このデータ ストアにアクセスする際に、共通の API が使用されます。 同じ API を [Jupyter](https://jupyter.org/) Notebook や Python などの外部ツールでも使用できます。 ポータルでは多くの一般的なタスクを実行できますが、Jupyter では、データに対して、より幅広い処理が可能です。 高度なプログラミング性と膨大なライブラリ コレクションを組み合わせて、機械学習、視覚化、データ解析を行えます。 こうした特性から、Jupyter は、セキュリティの調査や検出にうってつけのツールとなっています。

![ノートブックの例](./media/notebooks/sentinel-notebooks-map.png)

Jupyter のエクスペリエンスが Azure portal に統合されているため、簡単にノートブックを作成して実行し、データを解析できます。 *Kqlmagic* ライブラリは、Azure Sentinel からクエリを取得して、ノートブック内で直接実行できるようにする接着剤の働きをします。 クエリでは、[Kusto クエリ言語](https://kusto.azurewebsites.net/docs/query/index.html)が使用されます。 Azure Sentinel には、Microsoft のセキュリティ アナリストが開発した複数のノートブックがパッケージされています。 これらのノートブックの中には、特定のシナリオ向けに作成され、そのまま使用できるようになっているものがあります。 一方、技術や機能を説明するサンプルとして作成されたものもあります。これをコピーまたは改造して、独自のノートブック内で使用することができます。 Azure Sentinel コミュニティの GitHub からインポートできるノートブックもあります。

統合された Jupyter エクスペリエンスでは、[Azure Notebooks](https://notebooks.azure.com/) を使用して、ノートブックが保存、共有、および実行されます。 これらのノートブックは、ローカルで実行することも (コンピューター上に Python 環境と Jupyter がある場合)、他の JupterHub 環境 (Azure Databricks など) 内で実行することもできます。

ノートブックは、次の 2 つのコンポーネントから構成されています。

- ブラウザーベースのインターフェイス。クエリやコードを入力して実行すると、実行結果が表示されます。
- "*カーネル*"。コード自体を解析して実行する役割を果たします。 

Azure Notebooks の場合、このカーネルは既定で Azure の "*無料のクラウド コンピューティングおよびストレージ*" 上で実行されます。 ノートブックに含まれる機械学習モデルや視覚化が複雑である場合は、[Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM) などのより強力な専用のコンピューティング リソースを使用することを検討してください。 アカウント内のノートブックは、共有を選択しない限り、プライベートで保持されます。

Azure Sentinel ノートブックでは、pandas、matplotlib、bokeh など、多くのポピュラーな Python ライブラリが使用されています。 次の分野に対応したその他の多数の Python パッケージから選択することもできます。

- 視覚化とグラフィックス
- データ処理と解析
- 統計と数値計算
- 機械学習とディープ ラーニング

オープンソースの Jupyter セキュリティ ツールも [msticpy](https://github.com/Microsoft/msticpy/) という名前のパッケージでリリースされています。 このパッケージは、組み込みのノートブックの多くで使用されています。 Msticpy ツールは、特に検出や調査用のノートブックの作成に役立つように設計されており、新機能の開発や改良が鋭意進められています。

最初のノートブックには、次のものが含まれております。

- **ガイド付き調査 - プロセス アラート**:影響を受けるホスト上のアクティビティを分析することにより、アラートを迅速にトリアージできます。
- **ガイド付き検出 - Windows ホスト エクスプローラー**:ホスト上のアカウント アクティビティ、プロセス実行、ネットワーク アクティビティ、およびその他のイベントを参照できます。
- **ガイド付き検出 - Office365 の探索**:複数の Office 365 データ セット内で不審な Office 365 アクティビティを検出します。

[Azure Sentinel コミュニティの GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel)は、将来 Microsoft が作成した Azure Sentinel ノートブックやコミュニティから投稿された Azure Sentinel ノートブックを保管するための場所です。

ノートブックを使用するには、Azure Notebooks アカウントが必要です。 詳細については、「[クイック スタート: サインインとユーザー ID の設定](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks)」(Azure Notebooks のドキュメント) を参照してください。 このアカウントを作成するには、 **[Azure Sentinel - Notebooks]\(Azure Sentinel - ノートブック\)** のコマンド バーにある **[Azure Notebooks にサインアップ]** オプションを使用できます。

> [!div class="mx-imgBorder"]
>![[Azure Notebooks にサインアップ] オプション](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

Azure Sentinel から直接ノートブックを実行することも、すべての Azure Sentinel ノートブックを新しい Azure Notebooks プロジェクトにクローンすることもできます。

## <a name="run-a-notebook-from-azure-sentinel"></a>Azure Sentinel からノートブックを実行する
 
1. Azure portal から **[Azure Sentinel]**  >  **[Threat management]\(脅威の管理\)**  >  **[Notebooks]\(ノートブック\)** に移動します。ここで、Azure Sentinel によって提供されるノートブックを確認できます。 

2. 個々のノートブックを選択し、それらの説明、必要なデータの種類、データ ソースを確認します。 次に例を示します。
    
    > [!div class="mx-imgBorder"]
    > ![ノートブックの起動](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. 使用するノートブックを選択してから、 **[Launch Notebook (Preview)]\(ノートブックの起動 (プレビュー)\)** を選択して、Azure Sentinel ワークスペースに接続する新しい Azure Notebooks プロジェクトにノートブックをクローンし、構成します。 プロセスが完了すると、Azure Notebooks 内でノートブックが開き、実行できるようになります。

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Azure Sentinel のノートブックを新しい Azure Notebooks プロジェクトにクローンする

この手順では、Azure Sentinel のノートブックを含む Azure Notebooks プロジェクトを作成します。 その後、そのノートブックをそのまま実行したり、変更を加えて実行したりすることができます。

1. Azure portal から **[Azure Sentinel]**  >  **[Threat management]\(脅威の管理\)**  >  **[Notebooks]\(ノートブック\)** に移動し、コマンド バーから **[Clone Notebooks]\(ノートブックのクローン\)** を選択します。
  
    > [!div class="mx-imgBorder"]
    >![[Clone Notebooks]\(ノートブックのクローン\) オプション](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. 次のダイアログが表示されたら、 **[インポート]** をクリックして、GitHub リポジトリを自身の Azure Notebooks プロジェクトにクローンします。 既存の Azure Notebooks アカウントを持っていない場合は、アカウントを作成してサインインするように求められます。

   ![ノートブックをインポートする](./media/notebooks/sentinel-notebooks-clone.png)

3. **[Upload GitHub Repository]\(GitHub リポジトリのアップロード\)** ダイアログ ボックスで、 **[Clone recursively]\(再帰的にクローン\)** を選択しないでください。このオプションは、リンクされた GitHub リポジトリを参照するためです。 プロジェクト名には、既定の名前を使用するか、新しい名前を入力します。 その後 **[インポート]** をクリックすると、GitHub コンテンツのクローンが開始されます。クローンが完了するまでに数分かかる場合があります。

   ![ノートブックをインポートする](./media/notebooks/sentinel-create-project.png)

4. 先ほど作成したプロジェクトを開き、**Notebooks** フォルダーを開いてノートブックを表示します。 次に例を示します。

   ![リポジトリをインポートする](./media/notebooks/sentinel-open-notebook1.png)

これで、Azure Notebooks からノートブックを実行できます。 Azure Sentinel からこれらのノートブックに戻るには、 **[Azure Sentinel - Notebooks]\(Azure Sentinel - ノートブック\)** のコマンド バーから **[Go to your Notebooks]\(ノートブックに移動する\)** を選択します。

> [!div class="mx-imgBorder"]
>![[Go to your Notebooks]\(ノートブックに移動する\) オプション](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>ノートブックを使用した検出

各ノートブックでは、検出や調査を実行するための手順が紹介されます。 ノートブックで必要なライブラリやその他の依存関係ファイルは、Notebook 自体からインストールすることも、簡単な構成手順を介してインストールすることもできます。 ノートブック プロジェクトを Azure Sentinel サブスクリプションに関連付ける構成は、前の手順で自動的にプロビジョニングされています。

1. まだ Azure Notebooks に移動していない場合は、 **[Azure Sentinel - Notebooks]\(Azure Sentinel - ノートブック\)** のコマンド バーにある **[Go to your Notebooks]\(ノートブックに移動する\)** オプションを使用できます。
    
    > [!div class="mx-imgBorder"]
    >![[Go to your Notebooks]\(ノートブックに移動する\) オプション](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    Azure Notebooks で、 **[マイ プロジェクト]** を選択し、Azure Sentinel のノートブックを含むプロジェクトを選択して、最後に **Notebooks** フォルダーを選択します。
    
2. ノートブックを開く前に、ノートブックが無料のコンピューティングで実行されるよう既定で選択されていることに注意してください。
    
   ![ノートブックを選択する](./media/notebooks/sentinel-open-notebook2.png)
    
    導入部で説明したように、使用する Data Science Virtual Machines (DSVM) を構成した場合は、最初のノートブックを選択する前に DSVM を選択して認証します。 

3. ノートブックをクリックして開きます。
    
    初めてノートブックを開くと、カーネルのバージョンを選択するよう求められることがあります。 プロンプトが表示されない場合は、 **[カーネル]**  >   **[Change kernel]\(カーネルの変更\)** で 3.6 以上のバージョンのカーネルを選択します。 選択したカーネルのバージョンは、ノートブックのウィンドウの右上隅表示されます。
    
   ![ノートブックを選択する](./media/notebooks/sentinel-select-kernel.png)

4. ダウンロードしたノートブックに変更を加える前に、元のノートブックのコピーを作成して作業することをお勧めします。 これを行うには、 **[ファイル]**  >  **[コピーの作成]** を選択します。 コピーで作業すると、データを上書きせずに将来のバージョンのノートブックへと更新できるので安全です。
    
    これで、選択したノートブックを実行または編集する準備ができました。

推奨事項:

- Azure Sentinel でのデータのクエリに関する簡単な説明を確認するには、メインの **Notebooks** フォルダー内にある [[GetStarted]\(はじめに\)](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/345cf9f7c8f6137f5af4593a3f9d7568acd6cbc2/DeprecatedNotebooks/Get%20Started.ipynb) ノートブックを参照してください。 

- **Sample-Notebooks** サブフォルダー内には、別のサンプルのノートブックが用意されています。 これらのサンプルのノートブックは、データと共に保存されているため、所定の出力を簡単に確認できます。 これらのノートブックは、[nbviewer](https://nbviewer.jupyter.org/) で閲覧することをお勧めします。 

- **Howto** フォルダーに含まれるノートブックには、Python の既定バージョンの設定、DSVM の構成、ノートブックからの Azure Sentinel ブックマークの作成、その他の件名などが記述されています。

これらのノートブックは、便利なツールとして利用できるだけではなく、自分でノートブックを開発するときに使う説明やコード サンプルとしても利用できます。

ご提案、機能の要求、投稿されたノートブック、バグ レポート、既存のノートブックへの改善や追加など、フィードバックがございましたら、ぜひお寄せください。 問題またはフォークを作成して、投稿をアップロードするには、[Azure Sentinel コミュニティの GitHub](https://github.com/Azure/Azure-Sentinel) にアクセスしてください。

## <a name="next-steps"></a>次のステップ

この記事では、Azure Sentinel 内で Jupyter ノートブックを使い始める方法を学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [脅威を事前に検出する](hunting.md)
- [ブックマークを使用して検出中に関心のある情報を保存する](bookmarks.md)

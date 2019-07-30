---
title: Azure Sentinel プレビューのノートブックを使用した検出機能 | Microsoft Docs
description: この記事では、Azure Sentinel 検出機能でノートブックを使用する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: cc84db4bd3dc06d4e2dbecb3be8ceee37d554ae7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619849"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Jupyter Notebook を使用してセキュリティの脅威を検出する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Sentinel の基盤となるのは、ハイ パフォーマンス クエリと動的スキーマを併用し、大量のデータ ボリュームにスケーリングできるデータ ストアです。 Azure Sentinel ポータルとすべての Azure Sentinel ツールでは、このデータ ストアにアクセスする際、共通の API が使用されます。 同じ API を [Jupyter](https://jupyter.org/) Notebook や Python などの外部ツールでも使用できます。 ポータルでは多くの一般的なタスクを実行できますが、Jupyter では、データに対して、より幅広い処理が可能です。 高度なプログラミング性と膨大なライブラリ コレクションを組み合わせて、機械学習、視覚化、データ解析を行えます。 こうした特性から、Jupyter は、セキュリティの調査や検出にうってつけのツールとなっています。

![ノートブックの例](./media/notebooks/sentinel-nb-mapandtimeline.png)

Jupyter のエクスペリエンスが Azure Sentinel ポータルに統合されているため、簡単にノートブックを作成して実行し、データを解析できます。 *Kqlmagic* ライブラリは、Azure Sentinel からクエリを取得して、ノートブック内で直接実行できるようにする接着剤の働きをします。 クエリでは、[Kusto クエリ言語](https://kusto.azurewebsites.net/docs/query/index.html)が使用されます。 Azure Sentinel には、Microsoft のセキュリティ アナリストが開発した複数のノートブックがパッケージされています。 これらのノートブックの中には、特定のシナリオ向けに作成され、そのまま使用できるようになっているものがあります。 一方、技術や機能を説明するサンプルとして作成されたものもあります。これをコピーまたは改造して、独自のノートブック内で使用することができます。 Azure Sentinel コミュニティの GitHub からインポートできるノートブックもあります。

統合された Jupyter エクスペリエンスでは、[Azure Notebooks](https://notebooks.azure.com/) を使用して、ノートブックが保存、共有、および実行されます。 これらのノートブックは、ローカルで実行することも (コンピューター上に Python 環境と Jupyter がある場合)、他の JupterHub 環境 (Azure Databricks など) 内で実行することもできます。

ノートブックは、次の 2 つのコンポーネントから構成されています。

- ブラウザーベースのインターフェイス。クエリやコードを入力して実行すると、実行結果が表示されます。
- "*カーネル*"。コード自体を解析して実行する役割を果たします。 

Azure Notebooks の場合、このカーネルは、既定では Azure の*無料のクラウド コンピューティングおよびストレージ*上で実行されます。 ノートブックに含まれる機械学習モデルや視覚化が複雑である場合は、[Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM) などのより強力な専用のコンピューティング リソースを使用することを検討してください。 アカウント内のノートブックは、共有を選択しない限り、プライベートで保持されます。

Azure Sentinel ノートブックでは、pandas、matplotlib、bokeh など、多くのポピュラーな Python ライブラリが使用されています。 次の分野に対応したその他の多数の Python パッケージから選択することもできます。

- 視覚化およびグラフィックス
- データ処理および解析
- 統計および数値計算
- 機械学習およびディープ ラーニング

オープンソースの Jupyter セキュリティ ツールも [msticpy](https://github.com/Microsoft/msticpy/) という名前のパッケージでリリースされています。 このパッケージは、組み込みのノートブックの多くで使用されています。 Msticpy ツールは、特に検出や調査用のノートブックの作成に役立つように設計されており、新機能の開発や改良が鋭意進められています。

最初のノートブックには、次のものが含まれております。

- **ガイド付き調査 - プロセス アラート**:影響を受けるホスト上のアクティビティを分析することにより、アラートを迅速にトリアージできます。
- **ガイド付き検出 - Windows ホスト エクスプローラー**:ホスト上のアカウント アクティビティ、プロセス実行、ネットワーク アクティビティ、およびその他のイベントを参照できます。  
- **ガイド付き検出 - Office365 の探索**:複数の O365 データ セット内で不審な Office 365 アクティビティを検出します。

[Azure Sentinel コミュニティの GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel)は、将来 Microsoft が作成した Azure Sentinel ノートブックやコミュニティから投稿された Azure Sentinel ノートブックを保管するための場所です。

## <a name="run-a-notebook"></a>ノートブックの実行

次の例では Azure Sentinel ポータルから Azure Notebooks プロジェクトを作成し、このプロジェクトにノートブックを設定します。 これらのノートブックは、使用する前にコピーを作成し、そのコピーで作業することをお勧めします。 コピーで作業すると、データを上書きせずに将来のバージョンのノートブックへと更新できるので安全です。

1. Azure Sentinel ポータル内のナビゲーション メニューにある **[ノートブック]** をクリックします。 新しい Azure Notebooks プロジェクトを作成するには、 **[Clone Azure Sentinel Notebooks]\(Azure Sentinel Notebooks の複製\)** をクリックします。既存のノートブック プロジェクトを開くには、 **[Go to your Notebooks]\(ノートブックに移動する\)** をクリックします。
  
   ![ノートブックを選択する](./media/notebooks/sentinel-az-notebooks-home.png)

2. 前の手順で **[Clone Azure Sentinel Notebooks]\(Azure Sentinel Notebooks の複製\)** を選択すると、次のダイアログが表示されます。 **[インポート]** をクリックして、GitHub リポジトリの複製を自分の Azure Notebooks プロジェクトに作成します。 既存の Azure Notebooks アカウントを持っていない場合は、アカウントを作成してサインインするように求められます。

   ![ノートブックをインポートする](./media/notebooks/sentinel-nb-signin-and-clone.png)

3. 新しいプロジェクトを作成するときは、プロジェクト名を設定する必要があります (既定の名前を使用するか、または新しい名前を入力します)。 **[サブモジュールを再帰的に複製]** オプションはオンにしないでください。このオプションでは、リンクされた GitHub リポジトリが参照されます。 **[インポート]** をクリックすると、GitHub コンテンツの複製が開始されます。複製が完了するまでに数分かかる場合があります。

   ![ノートブックをインポートする](./media/notebooks/sentinel-create-nb-project.png)

4. **[Notebooks]** フォルダーを開くと、ノートブックが表示されます。 各ノートブックでは、検出や調査を実行するための手順が紹介されます。 ノートブックで必要なライブラリやその他の依存関係ファイルは、Notebook 自体からインストールすることも、簡単な構成手順を介してインストールすることもできます。 ノートブック プロジェクトを Azure Sentinel サブスクリプションに関連付ける構成は、前の手順で自動的にプロビジョニングされています。 Azure Sentinel Log Analytics ワークスペースに対してノートブックを実行する準備ができました。

   ![リポジトリをインポートする](./media/notebooks/sentinel-open-notebook1.png)

5. ノートブックを開きます。 既定では、ノートブックを実行する際に [Free Compute]\(無料コンピューティング\) が選択されています (強調表示されています)。 DSVM を使用する構成 (上記を参照) になっている場合は、[DSVM] を選択し、最初のノートブックを開く前に認証を行います。 ノートブックをクリックして開きます。

   ![ノートブックを選択する](./media/notebooks/sentinel-open-notebook2.png)

6. Python のバージョンを選択します。 最初にノートブックを開くと、カーネルのバージョンを選択するように求められる場合があります。 それ以外の場合は、次のように使用するカーネルを選択します。 ([ノートブック] ウィンドウの右上隅で) Python 3.6 以降をカーネルとして選択する必要があります。

   ![ノートブックを選択する](./media/notebooks/sentinel-select-kernel.png)

Azure Sentinel でのデータのクエリに関する簡単な説明を確認するには、メインの [Notebooks] フォルダー内にある [[GetStarted]\(はじめに\)](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) ノートブックを参照してください。 **[Sample-Notebooks]** サブフォルダー内には、別のサンプルのノートブックが用意されています。 これらのサンプルのノートブックは、データと共に保存されているため、所定の出力を簡単に確認できます (これらのノートブックは、[nbviewer](https://nbviewer.jupyter.org/) で表示することをお勧めします)。 **[HowTos]** フォルダーに含まれるノートブックには、Python の既定バージョンの設定、DSVM の構成、ノートブックからの Azure Sentinel ブックマークの作成、その他の件名などが記述されています。

これらのノートブックは、便利なツールとして利用できるだけではなく、自分でノートブックを開発するときに使う説明やコード サンプルとしても利用できます。

ご提案、機能の要求、投稿されたノートブック、バグ レポート、既存のノートブックへの改善や追加など、フィードバックがございましたら、ぜひお寄せください。 問題またはフォークを作成して、投稿をアップロードするには、[Azure Sentinel コミュニティの GitHub](https://github.com/Azure/Azure-Sentinel) にアクセスしてください。

## <a name="next-steps"></a>次の手順

この記事では、Azure Sentinel 内で Jupyter ノートブックを使い始める方法を学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [脅威を事前に検出する](hunting.md)
- [ブックマークを使用して検出中に関心のある情報を保存する](bookmarks.md)

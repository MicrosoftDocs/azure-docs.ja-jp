---
title: Apache Spark 用の Python ライブラリを管理する
description: Azure Synapse Analytics で、Apache Spark によって使用される Python ライブラリを追加および管理する方法について説明します。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 4bb323e0e8f72456b6a522ede9a98d193e1c3c7e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098776"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Azure Synapse Analytics で Apache Spark 用の Python ライブラリを管理する

ライブラリでは、プログラムまたはプロジェクトに含めることができる再利用可能なコードが提供されます。 

さまざまな理由から、サーバーレス Apache Spark プール環境を更新する必要がある場合があります。 たとえば、次のようなことが考えられます。
- コア依存関係の 1 つで新しいバージョンがリリースされたばかりである。
- 機械学習モデルのトレーニングやデータの準備のために追加のパッケージが必要になった。
- より優れたパッケージが見つかり、前のパッケージが不要になった。

サード パーティのコードまたはローカル環境でビルドされたコードをアプリケーションで使用できるようにするには、いずれかのサーバーレス Apache Spark プールまたはノートブック セッションにライブラリをインストールすることができます。 この記事では、サーバーレスの Apache Spark プール全体で Python ライブラリを管理する方法について説明します。

## <a name="default-installation"></a>既定のインストール
Azure Synapse Analytics の Apache Spark には、一般的なデータ エンジニアリング、データ準備、機械学習、データ視覚化の各タスク用のライブラリ一式が用意されています。 完全なライブラリの一覧については、[Apache Spark のバージョンのサポート](apache-spark-version-support.md)に関するページを参照してください。 

これらのライブラリは、Spark インスタンスが起動されるときに自動的に組み込まれます。 追加の Python およびカスタム ビルドのパッケージを、Spark プールおよびセッション レベルで追加できます。

## <a name="pool-libraries"></a>プール ライブラリ
Spark アプリケーションで使用したい Python ライブラリを見つけたら、Spark プールにインストールすることができます。 プール レベルのライブラリは、プールで実行されているすべてのノートブックおよびジョブで使用できます。

クラスターにライブラリをインストールするには、主に次の 2 つの方法があります。
-  ワークスペース パッケージとしてアップロードされたワークスペース ライブラリをインストールします。
-  環境仕様 *requirements.txt* または *Conda environment.yml* を指定して、PyPI、Conda-Forge などのリポジトリからパッケージをインストールします。

> [!IMPORTANT]
> - インストールするパッケージが大きいか、インストールに時間がかかる場合、これは Spark インスタンスのアップタイムに影響します。
> - PySpark、Python、Scala/Java、.NET、または Spark のバージョンの変更はサポートされていません。
> - PyPI、Conda-Forge、または既定の Conda チャネルなどの外部リポジトリからパッケージをインストールすることは、DEP 対応のワークスペースではサポートされていません。

### <a name="install-python-packages"></a>Python パッケージのインストール
環境仕様ファイルを指定して、PyPI や Conda-Forge などのリポジトリから Python パッケージをインストールできます。 

#### <a name="environment-specification-formats"></a>環境仕様の形式

##### <a name="pip-requirementstxt"></a>PIP requirements.txt
*requirements.txt* ファイル (`pip freeze` コマンドからの出力) を使用して、環境をアップグレードできます。 プールが更新されると、このファイルに記載されているパッケージが PyPI からダウンロードされます。 その後、完全な依存関係がキャッシュされ、後でプールを再利用するために保存されます。 

次のスニペットは、要件ファイルの形式を示しています。 PyPI パッケージ名が正確なバージョンと共にリストされます。 このファイルは、[pip freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) のリファレンス ドキュメントで説明されている形式に従います。 

この例では、特定のバージョンを固定しています。 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>YML 形式 (プレビュー)
さらに、*environment.yml* ファイルを指定してプール環境を更新することもできます。 このファイルに記載されているパッケージが、既定の Conda チャネル、Conda-Forge、PyPI からダウンロードされます。 構成オプションを使用して、他のチャネルを指定したり、既定のチャネルを削除したりできます。

この例では、チャネルと Conda/PyPI の依存関係を指定します。 

```
name: stats2
channels:
  - defaults
dependencies:
  - bokeh=0.9.2
  - numpy=1.9.*
  - flask
  - pip:
    - matplotlib
```
この environment.yml ファイルから環境を作成する方法の詳細については、[environment.yml ファイルからの環境の作成](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually)に関するページを参照してください。

#### <a name="update-python-packages"></a>Python パッケージを更新する
Spark プールにインストールする環境仕様ファイルまたはライブラリのセットを特定したら、Azure Synapse Studio または Azure portal に移動して Spark プール ライブラリを更新できます。 ここでは、環境仕様を指定し、インストールするワークスペース ライブラリを選択することができます。 

変更が保存されると、Spark ジョブによってインストールが実行され、結果として得られた環境は後で再利用するためにキャッシュされます。 ジョブが完了すると、新しい Spark ジョブまたはノートブック セッションで、更新されたプール ライブラリが使用されます。 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Azure Synapse Studio または Azure portal からパッケージを管理する
Spark プール ライブラリは、Azure Synapse Studio または Azure portal から管理できます。 

Spark プールのライブラリを更新または追加するには:
1. Azure portal から Azure Synapse Analytics ワークスペースに移動します。

    **Azure portal** から更新する場合:

    - **[Synapse resources]\(Synapse リソース\)** セクションで **[Apache Spark プール]** タブを選択し、一覧から Spark プールを選択します。
     
    - Spark プールの **[設定]** セクションから **[パッケージ]** を選択します。
  
    ![環境構成ファイルのアップロード ボタンが強調表示されていることを示すスクリーンショット。](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Python ライブラリを追加する")
   
    **Synapse Studio** から更新する場合:
    - メイン ナビゲーション パネルから **[管理]** を選択し、 **[Apache Spark プール]** を選択します。

    - 特定の Spark プールの **[パッケージ]** セクションを選択します。
    ![Studio から環境構成をアップロードするオプションを強調したスクリーンショット。](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Studio から Python ライブラリを追加する")
   
2. ページの **[パッケージ]** セクションにあるファイル セレクターを使用して、環境構成ファイルをアップロードします。
3. プールに追加する追加の **ワークスペース パッケージ** を選択することもできます。 
4. 変更内容を保存すると、システム ジョブがトリガーされ、指定したライブラリがインストールおよびキャッシュされます。 このプロセスにより、セッションの全体的な起動時間を短縮できます。 
5. ジョブが正常に完了すると、すべての新しいセッションで、更新されたプール ライブラリが取得されます。

> [!IMPORTANT]
> **[Force new settings]\(新しい設定を強制\)** オプションを選択すると、選択した Spark プールの現在のすべてのセッションが終了します。 セッションが終了したら、プールが再起動するまで待機する必要があります。 
>
> この設定をオフにした場合は、現在の Spark セッションが終了するまで待つか、手動で停止する必要があります。 セッションが終了したら、プールが再起動されるようにする必要があります。


##### <a name="track-installation-progress-preview"></a>インストールの進行状況の追跡 (プレビュー)
システム予約 Spark ジョブは、新しい一連のライブラリを使用してプールが更新されるたびに開始されます。 この Spark ジョブは、ライブラリのインストールの状態を監視するために役立ちます。 ライブラリの競合またはその他の問題のためインストールが失敗した場合、Spark プールは以前の、または既定の状態に戻ります。 

さらに、ユーザーはインストール ログを調査することで、依存関係の競合を特定したり、プールの更新中にインストールされたライブラリを確認したりすることもできます。

これらのログを表示するには:
1. **[監視]** タブで、Spark アプリケーションの一覧に移動します。 
2. プールの更新に対応するシステム Spark アプリケーション ジョブを選択します。 これらのシステム ジョブは、*SystemReservedJob-LibraryManagement* というタイトルの下で実行されています。
   ![システム予約済みライブラリ ジョブが強調表示されているスクリーンショット。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "システム ライブラリ ジョブの表示")
3. **driver** および **stdout** ログを表示するように切り替えます。 
4. この結果の中に、依存関係のインストールに関連するログが表示されます。
    ![システム予約済みライブラリ ジョブの結果が強調表示されているスクリーンショット。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "システム ライブラリ ジョブの進行状況の表示")

## <a name="install-wheel-files"></a>wheel ファイルをインストールする
Python の wheel ファイルは、Python ライブラリをパッケージ化するための一般的な方法です。 Azure Synapse Analytics 内で、ユーザーは自分の wheel ファイルを Azure Data Lake Storage アカウントの既知の場所にアップロードしたり、Azure Synapse のワークスペース パッケージ インターフェイスを使用してアップロードしたりできます。

### <a name="workspace-packages-preview"></a>ワークスペース パッケージ (プレビュー)
ワークスペース パッケージは、カスタムまたはプライベートの wheel ファイルにすることができます。 これらのパッケージをワークスペースにアップロードし、後で特定の Spark プールに割り当てることができます。

ワークスペース パッケージを追加するには:
1. **[管理]**  >  **[ワークスペース パッケージ]** タブに移動します。
2. ファイル セレクターを使用して wheel ファイルをアップロードします。
3. ファイルが Azure Synapse ワークスペースにアップロードされたら、これらのパッケージを特定の Apache Spark プールに追加できます。

![ワークスペース パッケージを強調したスクリーンショット。](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "ワークスペース パッケージを表示する")

### <a name="storage-account"></a>ストレージ アカウント
Synapse ワークスペースにリンクされている Azure Data Lake Storage (Gen2) アカウントにすべての wheel ファイルをアップロードすることで、カスタム ビルドの wheel パッケージを Apache Spark プールにインストールできます。 

ストレージ アカウントの既定のコンテナーの次のパスに、ファイルをアップロードする必要があります。 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

```libraries``` フォルダー内に ```python``` フォルダーがまだ存在しない場合は、これを追加する必要があります。

> [!IMPORTANT]
> Azure DataLake Storage メソッドを使用してカスタム ライブラリをインストールするには、Azure Synapse Analytics ワークスペースにリンクされているプライマリ Gen2 ストレージ アカウントに対して、**ストレージ BLOB データ共同作成者** または **ストレージ BLOB データ所有者** のアクセス許可を持っている必要があります。

>[!WARNING]
> カスタム wheel ファイルを指定する場合、ユーザーはストレージ アカウントとワークスペース ライブラリ インターフェイスの両方で wheel ファイルを指定することはできません。 両方を指定した場合は、ワークスペース パッケージの一覧で指定した wheel ファイルのみがインストールされます。 

## <a name="session-scoped-packages-preview"></a>セッション スコープのパッケージ (プレビュー)
プール レベルのパッケージに加えて、セッション スコープのライブラリをノートブック セッションの開始時に指定することもできます。  セッション スコープのライブラリを使用すると、ノートブック セッション内でカスタム Python 環境を指定して使用できます。 

セッション スコープのライブラリを使用する場合は、以下の点に留意することが重要です。
   - セッション スコープのライブラリをインストールすると、現在のノートブックだけが、指定されたライブラリにアクセスできます。 
   - これらのライブラリは、同じ Spark プールを使用する他のセッションまたはジョブには影響しません。 
   - これらのライブラリは、基本ランタイムおよびプール レベルのライブラリの上にインストールされます。 
   - ノートブック ライブラリの優先順位が最も高くなります。

セッション スコープのパッケージを指定するには:
1.  選択した Spark プールに移動し、セッション レベルのライブラリが有効になっていることを確認します。  この設定を有効にするには、 **[管理]**  >  **[Apache Spark プール]**  >  **[パッケージ]** タブに移動します。![セッション パッケージを有効にします。](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "セッション パッケージを有効にする")
2.  設定が適用されたら、ノートブックを開いて **[セッションの構成]** >  **[パッケージ]** を選択できます。
  ![セッション パッケージを指定します。](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "セッションの構成を更新する")
3.  ここで、Conda の *environment.yml* ファイルをアップロードして、セッション内でパッケージをインストールまたはアップグレードできます。 セッションを開始すると、指定したライブラリがインストールされます。 セッションが終了すると、これらのライブラリはセッションに固有のものであるため、使用できなくなります。

## <a name="verify-installed-libraries"></a>インストールされているライブラリの検証
正しいライブラリの正しいバージョンが PyPI からインストールされているかどうかを検証するには、次のコードを実行します。
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
場合によっては、Conda からのパッケージのバージョンを確認するために、パッケージのバージョンを個別に検査する必要があります。

## <a name="next-steps"></a>次のステップ
- 既定のライブラリを確認します: [Apache Spark バージョンのサポート](apache-spark-version-support.md)
- ライブラリのインストール エラーのトラブルシューティング: [ライブラリのエラーのトラブルシューティング](apache-spark-troubleshoot-library-errors.md)
- Azure Data Lake Storage アカウントを使用してプライベート Conda チャネルを作成する: [Conda プライベート チャネル](./spark/../apache-spark-custom-conda-channel.md)

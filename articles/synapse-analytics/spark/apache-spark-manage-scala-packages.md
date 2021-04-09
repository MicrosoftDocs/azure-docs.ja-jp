---
title: Apache Spark 用の Scala および Java ライブラリを管理する
description: Azure Synapse Analytics で Scala および Java ライブラリを追加および管理する方法について説明します。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: c70ecc4fc5469d728bc12d47024585ccf00ff98e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098708"
---
# <a name="manage-scala-and-java-packages-for-apache-spark-in-azure-synapse-analytics"></a>Azure Synapse Analytics で Apache Spark 用の Scala および Java パッケージを管理する

ライブラリでは、プログラムまたはプロジェクトに含めることができる再利用可能なコードが提供されます。 

いくつかの理由により、サーバーレス Apache Spark プール環境を更新することが必要な場合があります。 たとえば、次のようなことが考えられます。
- コア依存関係の 1 つで新しいバージョンがリリースされた。
- 機械学習モデルのトレーニングやデータの準備のために追加のパッケージが必要になった。
- より優れたパッケージが見つかり、前のパッケージが不要になった。

サード パーティのコードまたはローカル環境でビルドされたコードをアプリケーションで使用できるようにするには、いずれかのサーバーレス Apache Spark プールまたはノートブック セッションにライブラリをインストールすることができます。 この記事では、Scala および Java パッケージを管理する方法について説明します。

## <a name="default-installation"></a>既定のインストール
Azure Synapse Analytics の Apache Spark には、一般的なデータ エンジニアリング、データ準備、機械学習、データ視覚化の各タスク用のライブラリ一式が用意されています。 完全なライブラリの一覧については、[Apache Spark のバージョンのサポート](apache-spark-version-support.md)に関するページを参照してください。 

これらのライブラリは、Spark インスタンスが起動されるときに自動的に組み込まれます。 追加の Scala/Java パッケージを、Spark プールおよびセッション レベルで追加できます。

## <a name="workspace-packages"></a>ワークスペース パッケージ
ワークスペース パッケージは、カスタムまたはプライベートの jar ファイルにすることができます。 これらのパッケージをワークスペースにアップロードし、後で特定の Spark プールに割り当てることができます。

ワークスペース パッケージを追加するには:
1. **[管理]**  >  **[ワークスペース パッケージ]** タブに移動します。
2. ファイル セレクターを使用して jar ファイルをアップロードします。
3. ファイルが Azure Synapse ワークスペースにアップロードされたら、これらの jar ファイルを特定の Apache Spark プールに追加できます。

![ワークスペース パッケージを強調したスクリーンショット。](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "ワークスペース パッケージを表示する")

## <a name="pool-libraries"></a>プール ライブラリ
Spark アプリケーションで使用したい Scala および Java パッケージを見つけたら、Spark プールにインストールすることができます。 プール レベルのライブラリは、プールで実行されているすべてのノートブックおよびジョブで使用できます。

Spark プール ライブラリを更新するには、Azure Synapse Studio または Azure portal に移動します。 ここでは、インストールするワークスペース ライブラリを選択できます。 

変更が保存された後、Spark ジョブによってインストールが実行され、結果として得られた環境は後で再利用するためにキャッシュされます。 ジョブが完了すると、新しい Spark ジョブまたはノートブック セッションで、更新されたプール ライブラリが使用されます。 

> [!IMPORTANT]
> - インストールするパッケージが大きいか、インストールに時間がかかる場合、これは Spark インスタンスのアップタイムに影響します。
> - PySpark、Python、Scala/Java、.NET、または Spark のバージョンの変更はサポートされていません。

#### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Azure Synapse Studio または Azure portal からパッケージを管理する
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
   
2. Jar ファイルを追加するには、 **[ワークスペース パッケージ]** セクションに移動してプールに追加します。 
3. 変更内容を保存すると、システム ジョブがトリガーされ、指定したライブラリがインストールおよびキャッシュされます。 このプロセスにより、セッションの全体的な起動時間を短縮できます。 
4. ジョブが正常に完了すると、すべての新しいセッションで、更新されたプール ライブラリが取得されます。

> [!IMPORTANT]
> **[Force new settings]\(新しい設定を強制\)** オプションを選択すると、選択した Spark プールの現在のすべてのセッションが終了します。 セッションが終了したら、プールが再起動するまで待機する必要があります。 
>
> この設定をオフにした場合は、現在の Spark セッションが終了するまで待つか、手動で停止する必要があります。 セッションが終了したら、プールが再起動されるようにする必要があります。

#### <a name="track-installation-progress-preview"></a>インストールの進行状況の追跡 (プレビュー)
システム予約 Spark ジョブは、新しい一連のライブラリを使用してプールが更新されるたびに開始されます。 この Spark ジョブは、ライブラリのインストールの状態を監視するために役立ちます。 ライブラリの競合またはその他の問題のためインストールが失敗した場合、Spark プールは以前の、または既定の状態に戻ります。 

さらに、ユーザーはインストール ログを調査することで、依存関係の競合を特定したり、プールの更新中にインストールされたライブラリを確認したりすることもできます。

これらのログを表示するには:
1. **[監視]** タブで、Spark アプリケーションの一覧に移動します。 
2. プールの更新に対応するシステム Spark アプリケーション ジョブを選択します。 これらのシステム ジョブは、*SystemReservedJob-LibraryManagement* というタイトルの下で実行されています。
   ![システム予約済みライブラリ ジョブが強調表示されているスクリーンショット。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "システム ライブラリ ジョブの表示")
3. **driver** および **stdout** ログを表示するように切り替えます。 
4. この結果の中に、パッケージのインストールに関連するログが表示されます。
    ![システム予約済みライブラリ ジョブの結果が強調表示されているスクリーンショット。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "システム ライブラリ ジョブの進行状況の表示")

## <a name="session-scoped-libraries"></a>セッション スコープのライブラリ 
プール レベルのライブラリに加えて、セッション スコープのライブラリをノートブック セッションの開始時に指定することもできます。  セッション スコープのライブラリを使用すると、ノートブック セッション内のみで jar パッケージを指定して使用できます。 

セッション スコープのライブラリを使用する場合は、以下の点に留意することが重要です。
   - セッション スコープのライブラリをインストールすると、現在のノートブックだけが、指定されたライブラリにアクセスできます。 
   - これらのライブラリは、同じ Spark プールを使用する他のセッションまたはジョブには影響しません。 
   - これらのライブラリは、基本ランタイムおよびプール レベルのライブラリの上にインストールされます。 
   - ノートブック ライブラリの優先順位が最も高くなります。

セッション スコープの Java または Scala パッケージは、```%%configure``` オプションを使用して指定できます。

```scala
%%configure -f
{
    "conf": {
        "spark.jars": "abfss://<<file system>>@<<storage account>.dfs.core.windows.net/<<path to JAR file>>",
    }
}
```

%%configure はノートブックの先頭で実行することをお勧めします。 すべての有効なパラメーターの一覧については、この[ドキュメント](https://github.com/cloudera/livy#request-body)を参照してください。

## <a name="next-steps"></a>次のステップ
- 既定のライブラリを確認します: [Apache Spark バージョンのサポート](apache-spark-version-support.md)
- ライブラリのインストール エラーのトラブルシューティング: [ライブラリのエラーのトラブルシューティング](apache-spark-troubleshoot-library-errors.md)

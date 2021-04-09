---
title: ライブラリのインストール エラーのトラブルシューティング
description: このチュートリアルでは、ライブラリのインストール エラーをトラブルシューティングする方法の概要を説明します。
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 57e9d0c584600a8fac90499d72cfac1620052603
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694922"
---
# <a name="troubleshoot-library-installation-errors"></a>ライブラリのインストール エラーのトラブルシューティング 
サード パーティまたはローカル環境でビルドされたコードをアプリケーションで使用できるようにするには、いずれかのサーバーレス Apache Spark プールにライブラリをインストールできます。 requirements.txt ファイルにリストされているパッケージは、プールの開始時に PyPi からダウンロードされます。 この要件ファイルは、その Spark プールから Spark インスタンスが作成されるたびに使用されます。 Spark プールにインストールされたライブラリは、同じプールを使用するすべてのセッションで使用できるようになります。 

場合によっては、ライブラリが Apache Spark プールに表示されないことがあります。 このケースは、提供された requirements.txt または指定されたライブラリにエラーがある場合によく発生します。 ライブラリのインストール プロセスでエラーが発生すると、Apache Spark プールは、Synapse 基本ランタイムに指定されたライブラリに戻ります。

このドキュメントの目的は、一般的な問題について説明し、ライブラリのインストール エラーのデバッグに役立ててもらうことです。

## <a name="force-update-your-apache-spark-pool"></a>Apache Spark プールを強制的に更新する
Apache Spark プールでライブラリを更新した場合、プールを再起動するとそれらの変更が取得されます。 アクティブなジョブがある場合、それらのジョブは引き続き Spark プールの元のバージョンで実行されます。

**[Force new settings]\(新しい設定を強制\)** オプションを選択すると、強制的に変更を適用できます。 この設定により、選択した Spark プールの現在のセッションがすべて終了します。 セッションが終了したら、プールが再起動するまで待機する必要があります。 

![Python ライブラリを追加する](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Python ライブラリを追加する")

## <a name="track-installation-progress"></a>インストールの進行状況の追跡
システム予約 Spark ジョブは、新しい一連のライブラリを使用してプールが更新されるたびに開始されます。 この Spark ジョブは、ライブラリのインストールの状態を監視するために役立ちます。 ライブラリの競合またはその他の問題が原因でインストールが失敗した場合、Spark プールは以前の、または既定の状態に戻ります。 

さらに、ユーザーはインストール ログを調査することで、依存関係の競合を特定したり、プールの更新中にインストールされたライブラリを確認したりすることもできます。

これらのログを表示するには:
1. **[監視]** タブで、Spark アプリケーションの一覧に移動します。 
2. プールの更新に対応するシステム Spark アプリケーション ジョブを選択します。 これらのシステム ジョブは、*SystemReservedJob-LibraryManagement* というタイトルの下で実行されています。
   ![システム予約済みライブラリ ジョブが強調表示されているスクリーンショット。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "システム ライブラリ ジョブの表示")
3. **driver** および **stdout** ログを表示するように切り替えます。 
4. この結果の中に、パッケージのインストールに関連するログが表示されます。
    ![システム予約済みライブラリ ジョブの結果が強調表示されているスクリーンショット。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "システム ライブラリ ジョブの進行状況の表示")

## <a name="validate-your-permissions"></a>アクセス許可を検証する
ライブラリをインストールおよび更新するには、Azure Synapse Analytics ワークスペースにリンクされているプライマリ Azure Data Lake Storage Gen2 ストレージ アカウントに対して、**ストレージ BLOB データ共同作成者** または **ストレージ BLOB データ所有者** のアクセス許可を持っている必要があります。

これらのアクセス許可があることを検証するには、次のコードを実行します。

```python
from pyspark.sql.types import StructType,StructField, StringType, IntegerType
data2 = [("James","Smith","Joe","4355","M",3000),
    ("Michael","Rose","Edward","40288","F",4000)
  ]

schema = StructType([ \
    StructField("firstname",StringType(),True), \
    StructField("middlename",StringType(),True), \
    StructField("lastname",StringType(),True), \
    StructField("id", StringType(), True), \
    StructField("gender", StringType(), True), \
    StructField("salary", IntegerType(), True) \
  ])
 
df = spark.createDataFrame(data=data2,schema=schema)

df.write.csv("abfss://<<ENTER NAME OF FILE SYSTEM>>@<<ENTER NAME OF PRIMARY STORAGE ACCOUNT>>.dfs.core.windows.net/validate_permissions.csv")

```
エラーが表示された場合は、必要なアクセス許可が不足している可能性があります。 必要なアクセス許可を取得する方法については、次のドキュメントを参照してください: [ストレージ BLOB データ共同作成者またはストレージ BLOB データ所有者のアクセス許可を割り当てる](../../storage/common/storage-auth-aad-rbac-portal.md#assign-an-azure-built-in-role)。

また、パイプラインを実行している場合は、ワークスペース MSI にもストレージ BLOB データ所有者またはストレージ BLOB データ共同作成者のアクセス許可が必要です。 ワークスペース ID にこのアクセス許可を付与する方法については、次を参照してください: [ワークスペースのマネージド ID にアクセス許可を付与する](../security/how-to-grant-workspace-managed-identity-permissions.md)。

## <a name="check-the-environment-configuration-file"></a>環境構成ファイルの確認
環境構成ファイルを使用して、Conda 環境をアップグレードすることができます。 Python プール管理に使用できるこのファイル形式の一覧は、[こちら](./apache-spark-manage-python-packages.md)に掲載されています。

次の制限事項に注意してください。
   -  要件ファイルの内容には、余分な空白行または文字を含めないでください。 
   -  [Synapse ランタイム](apache-spark-version-support.md)には、すべてのサーバーレス Apache Spark プールにプレインストールされているライブラリのセットが含まれています。 基本ランタイムにプレインストールされているパッケージを削除またはアンインストールすることはできません。
   -  PySpark、Python、Scala/Java、.NET、または Spark のバージョンの変更はサポートされていません。
   -  Python セッション スコープ ライブラリは、YML 拡張子を持つファイルのみを受け入れます。

## <a name="validate-wheel-files"></a>Wheel ファイルを検証する
Synapse サーバーレス Apache Spark プールは、Linux ディストリビューションに基づいています。 PyPI から Wheel ファイルを直接ダウンロードしてインストールする場合は、必ず、Linux 上で構築されており、Spark プールと同じ Python バージョンで実行されるバージョンを選択するようにしてください。

>[!IMPORTANT]
>セッションとセッションの間に、カスタム パッケージを追加または変更できます。 ただし、更新されたパッケージを表示するには、プールとセッションが再起動するまで待つ必要があります。

## <a name="check-for-dependency-conflicts"></a>依存関係の競合を確認する
 一般に、Python の依存関係の解決は、管理が難しい場合があります。 依存関係の競合をローカルでデバッグできるように、Synapse ランタイムに基づいて独自の仮想環境を作成し、変更を検証できます。

環境を再作成し、更新を検証するには、次のようにします。
 1. Synapse ランタイムをローカルに再作成するためのテンプレートを[ダウンロード](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml)します。 テンプレートと実際の Synapse 環境の間には若干の違いがある場合があります。
   
 2. [次の手順](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html)を使用して仮想環境を作成します。 この環境を使用すると、指定したライブラリの一覧を使用して、分離された Python インストールを作成できます。 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. ``pip install -r <provide your req.txt file>`` を使用して、指定したパッケージで仮想環境を更新します。 インストールでエラーが発生した場合は、Synapse 基本ランタイムにプレインストールされているものと、指定された要件ファイルに指定されているものとの間に競合が存在する可能性があります。 サーバーレス Apache Spark プール上に更新されたライブラリを取得するには、これらの依存関係の競合を解決する必要があります。

>[!IMPORTANT]
>pip と conda を一緒に使用すると、問題が発生するおそれがあります。 pip と conda を組み合わせる場合は、次の[推奨されるベスト プラクティス](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#using-pip-in-an-environment)に従うことをお勧めします。

## <a name="next-steps"></a>次のステップ
- 既定のライブラリを確認します: [Apache Spark バージョンのサポート](apache-spark-version-support.md)
---
title: Databricks Notebook でデータを変換する
description: Databricks Notebook を実行してデータを処理または変換する方法を説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
manager: shwang
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c7a2aec35511ef066033c3d6462143ac31660e76
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923058"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Databricks Notebook を実行してデータを変換する

[Data Factory パイプライン](concepts-pipelines-activities.md)の Azure Databricks Notebook アクティビティは、Azure Databricks ワークスペースで Databricks Notebook を実行します。 この記事は、データ変換の概要とサポートされる変換アクティビティを説明している [データ変換アクティビティ](transform-data.md) に関する記事に基づいています。 Azure Databricks は、Apache Spark を実行するための管理されたプラットフォームです。

## <a name="databricks-notebook-activity-definition"></a>Databricks Notebook アクティビティの定義

Databricks Notebook アクティビティのサンプルの JSON 定義を次に示します。

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Databricks Notebook アクティビティのプロパティ

次の表で、JSON 定義で使用される JSON プロパティについて説明します。

|プロパティ|説明|必須|
|---|---|---|
|名前|パイプラインのアクティビティの名前。|はい|
|description|アクティビティの動作を説明するテキスト。|いいえ|
|type|Databricks Notebook アクティビティでは、アクティビティの種類は DatabricksNotebook です。|はい|
|linkedServiceName|Databricks Notebook が実行されている Databricks リンク サービスの名前です。 このリンクされたサービスの詳細については、 [コンピューティングのリンクされたサービス](compute-linked-services.md) に関する記事をご覧ください。|はい|
|notebookPath|Databricks ワークスペースで実行するノートブックの絶対パスです。 このパスはスラッシュで始まる必要があります。|はい|
|baseParameters|キーと値ペアの配列です。 基本パラメーターは、各アクティビティの実行に使うことができます。 指定されていないパラメーターをノートブックが受け取った場合は、ノートブックの既定値が使われます。 パラメーターについて詳しくは、[Databricks Notebook](https://docs.databricks.com/api/latest/jobs.html#jobsparampair) に関する記事をご覧ください。|いいえ|
|libraries|ジョブを実行するクラスターにインストールされるライブラリのリスト。 \<文字列, オブジェクト> の配列を指定できます。|いいえ|


## <a name="supported-libraries-for-databricks-activities"></a>Databricks アクティビティでサポートされるライブラリ

前述の Databricks アクティビティ定義では、*jar*、*egg*、*whl*、*maven*、*pypi*、*cran* というライブラリの種類を指定しています。

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
    {
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

ライブラリの種類の詳細については、[Databricks のドキュメント](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary)を参照してください。

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>ノートブックと Data Factory の間でパラメーターを渡す

Databricks アクティビティの *baseParameters* プロパティを使用して、Data Factory のパラメーターをノートブックに渡すことができます。 

場合によっては、ノートブックから Data Factory に特定の値を戻すことが必要になる場合があります。これは、Data Factory の制御フロー (条件チェック) に使用したり、ダウンストリームのアクティビティで使用したりできます (サイズの上限は 2 MB)。 

1. ノートブックでは、[dbutils.notebook.exit("returnValue")](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) を呼び出すことができ、対応する "returnValue" が Data Factory に返されます。

2. `'@activity('databricks notebook activity name').output.runOutput'` などの式を使用して、Data Factory で出力を使用できます。 

   > [!IMPORTANT]
   > JSON オブジェクトを渡す場合は、プロパティ名を追加することによって値を取得できます。 例: `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>Databricks でライブラリをアップロードする方法

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Databricks ワークスペース UI の使用](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

UI を使用して追加されたライブラリの dbfs パスを取得するには、[Databricks CLI (インストール)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli) を使用します。 

UI を使用する場合、通常、Jar ライブラリは dbfs:/FileStore/jars に保存されます。 CLI *databricks fs ls dbfs:/FileStore/jars* を使用してすべてを一覧表示することができます。



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Databricks CLI を使用したライブラリのコピー](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

例: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*

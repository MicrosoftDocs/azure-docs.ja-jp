---
title: Databricks Python でデータを変換する
description: Azure Data Factory パイプラインで Databricks Python アクティビティを実行して、データを処理または変換する方法について説明します。
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/15/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 49dfe11ceb01471e3b5afadd30259dcd63e7b82a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373948"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Azure Databricks で Python アクティビティを実行してデータを変換する
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Data Factory パイプライン](concepts-pipelines-activities.md) の Azure Databricks Python アクティビティは、Azure Databricks クラスターで Python ファイルを実行します。 この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、 [データ変換アクティビティ](transform-data.md) に関する記事に基づいています。  Azure Databricks は、Apache Spark を実行するための管理されたプラットフォームです。

この機能の概要とデモンストレーションについては、以下の 11 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Databricks Python アクティビティの定義

Databricks Python アクティビティのサンプルの JSON 定義を次に示します。

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Databricks Python アクティビティのプロパティ

次の表で、JSON 定義で使用される JSON プロパティについて説明します。

|プロパティ|説明|必須|
|---|---|---|
|name|パイプラインのアクティビティの名前。|はい|
|description|アクティビティの動作を説明するテキスト。|いいえ|
|type|Databricks Python アクティビティでは、アクティビティの種類は DatabricksSparkPython です。|はい|
|linkedServiceName|Python アクティビティが実行されている Databricks リンク サービスの名前です。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。|はい|
|pythonFile|実行される Python ファイルの URI。 DBFS パスのみがサポートされています。|はい|
|parameters|Python ファイルに渡されるコマンド ライン パラメーター。 文字列の配列です。|いいえ|
|libraries|ジョブを実行するクラスターにインストールされるライブラリのリスト。 <文字列, オブジェクト> の配列を指定できます。|いいえ|

## <a name="supported-libraries-for-databricks-activities"></a>databricks アクティビティでサポートされるライブラリ

前述の Databricks アクティビティ定義では、*jar*、*egg*、*maven*、*pypi*、*cran* というライブラリの種類を指定しています。

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

ライブラリの種類の詳細については、[Databricks のドキュメント](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary)を参照してください。

## <a name="how-to-upload-a-library-in-databricks"></a>Databricks でライブラリをアップロードする方法

### <a name="you-can-use-the-workspace-ui"></a>ワークスペース UI を使用できます。

1. [Databricks ワークスペース UI を使用する](/azure/databricks/libraries/#create-a-library)

2. UI を使用して追加されたライブラリの dbfs パスを取得するには、[Databricks CLI](/azure/databricks/dev-tools/cli/#install-the-cli) を使用します。

   UI を使用する場合、通常、Jar ライブラリは dbfs:/FileStore/jars に保存されます。 CLI *databricks fs ls dbfs:/FileStore/job-jars* を使用してすべてを一覧表示することができます

### <a name="or-you-can-use-the-databricks-cli"></a>または、Databricks CLI を使用できます。

1. [Databricks CLI を使用したライブラリのコピー](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)に関するページの手順を行います

2. Databricks CLI を使用します [(インストール手順)](/azure/databricks/dev-tools/cli/#install-the-cli)。

   たとえば、JAR を dbfs にコピーする場合: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`

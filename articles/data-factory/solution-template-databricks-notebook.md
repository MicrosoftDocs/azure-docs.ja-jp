---
title: Azure Data Factory で Databricks を使用してデータを変換する | Microsoft Docs
description: Azure Data Factory で Databricks ノートブックを使用することにより、ソリューション テンプレートを使用してデータを変換する方法について説明します。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 562ce675acc43002ce468d60f8a8c412410be86c
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630805"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Azure Data Factory で Databricks を使用してデータを変換する

このチュートリアルでは、Data Factory の**ルックアップ**、**コピー**、および **Databricks ノートブック** アクティビティが含まれる、エンド ツー エンドのパイプラインを作成します。

-   **ルックアップ**または GetMetadata アクティビティは、コピーおよび分析ジョブをトリガーする前に、ソース データセットをダウンストリームで使用できる状態にするために使用されます。

-   **コピー アクティビティ**では、ソース ファイル/データセットがシンク ストレージにコピーされます。 Spark でデータセットを直接使用できるように、シンク ストレージは Databricks ノートブックに DBFS としてマウントされています。

-   **Databricks ノートブック アクティビティ**では、データセットを変換する Databricks ノートブックがトリガーされて、処理済みのフォルダー/SQL DW に追加されます。

このテンプレートを簡潔にするため、テンプレートではスケジュールされたトリガーは作成されません。 必要に応じて追加できます。

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>前提条件

1.  **BLOB ストレージ アカウント**と、**シンク** として使用される `sinkdata` という名前のコンテナーを作成します。 **ストレージ アカウントの名前**、**コンテナーの名前**、および**アクセス キー**を記録しておきます。後で、テンプレートにおいてこれらを参照します。

2.  **Azure Databricks ワークスペース**があることを確認します。または、新しく作成します。

1.  **ETL 用のノートブックをインポート**します。 以下の Transformations ノートブックを Databricks ワークスペースにインポートします。 (以下と同じ場所でなくてもかまいませんが、後でわかるように選択したパスを憶えておいてください。)URL フィールドに次の URL を入力して、そこからノートブックをインポートします: `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`。 **[インポート]** を選択します。

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  それでは、**Transformations** ノートブックを実際の**ストレージ接続情報** (名前とアクセス キー) で更新しましょう。 上でインポートしたノートブックの **command 5** に移動し、それを次のコード スニペットで置き換えます。置き換える前に、強調表示されている値を変更しておきます。 このアカウントが先ほど作成したものと同じストレージ アカウントであり、`sinkdata` コンテナーが含まれることを確認します。

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1.  Data Factory で Databricks にアクセスするための **Databricks アクセス トークン**を生成します。 後で Databricks のリンクされたサービスを作成するときに使用するので、**アクセス トークンを保存します**。トークンは、"dapi32db32cbb4w6eee18b7d87e45exxxxxx" のような値です。

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>リンクされたサービスとデータセットを作成する

1.  Data Factory UI で *[Connections Linked services + new]\(接続リンクされたサービス + 新規\)* に移動して、新しい**リンクされたサービス**を作成します

    1.  **ソース** – ソース データへのアクセス用。 このサンプルのソース ファイルが含まれるパブリック BLOB ストレージを使用できます。

        **Blob Storage** を選択し、以下の **SAS URI** を使用して、ソース ストレージに接続します (読み取り専用アクセス)。

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **シンク** – データ コピー先用。

        シンクのリンクされたサービスで、前提条件 1 で作成したストレージを選択します。

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks** – Databricks クラスターへの接続用。

        前提条件 2.c で生成したアクセス キーを使用して、Databricks のリンクされたサービスを作成します。 "*対話型クラスター*" がある場合は、それを選択してもかまいません。 (この例では、*[New job cluster]\(新しいジョブ クラスター\)* オプションを使用します。)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  **データセット**を作成します

    1.  ソース データがあるかどうかを確認するために、"**sourceAvailability_Dataset**" を作成します

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **ソース データセット** – ソース データのコピー用 (バイナリ コピーを使用)

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **シンク データセット** – シンク/コピー先の場所へのコピー用

        1.  リンクされたサービス - 1.b で作成した "sinkBlob_LS" を選択します

        2.  ファイル パス - "sinkdata/staged_sink"

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>アクティビティを作成する

1.  ソース使用可能性チェックを行うためのルックアップ アクティビティ "**Availability flag**" を作成します (ルックアップまたは GetMetadata を使用できます)。 2.a で作成した "sourceAvailability_Dataset" を選択します。

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  ソースからシンクにデータセットをコピーするためのコピー アクティビティ "**file-to-blob**" を作成します。 この場合、データはバイナリ ファイルです。 コピー アクティビティのソースとシンクの構成については、以下のスクリーンショットを参照してください。

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  **パイプラインのパラメーター**を定義します

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  **Databricks アクティビティ**を作成します

    前のステップで作成したリンクされたサービスを選択します。

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    **設定**を構成します。 スクリーンショットで示されているように **[Base Parameters]\(基本パラメーター\)** を作成し、Data Factory から Databricks ノートブックに渡されるパラメーターを作成します。 **前提条件 2** でアップロードした**正しいノートブック パス**を参照して**選択**します。

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **パイプラインを実行します**。 より詳細な Spark のログについては、Databricks のログへのリンクを検索できます。

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    ストレージ エクスプローラーを使用してデータ ファイルを確認することもできます。 (Data Factory のパイプラインの実行と関連付けるため、この例では Data Factory からのパイプライン実行 ID を出力フォルダーに追加しています。 このようにすると、各実行で生成されたファイルに戻って追跡できます。)

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>次の手順

- [Azure Data Factory の概要](introduction.md)

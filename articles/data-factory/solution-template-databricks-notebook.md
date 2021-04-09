---
title: Azure Databricks による変換
description: Azure Data Factory で Databricks ノートブックを使用することにより、ソリューション テンプレートを使用してデータを変換する方法について説明します。
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/27/2020
ms.openlocfilehash: ee663423071458605f37f07293693dbc91f592bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100362116"
---
# <a name="transformation-with-azure-databricks"></a>Azure Databricks による変換

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

このチュートリアルでは、Azure Data Factory の **検証**、**データ コピー**、および **ノートブック** アクティビティが含まれる、エンド ツー エンドのパイプラインを作成します。

- **検証** は、コピーおよび分析ジョブをトリガーする前に、ソース データセットがダウンストリームで使用できる状態にします。

- **データ コピー** は、ソース データセットを Azure Databricks Notebook に DBFS としてマウントされているシンク ストレージにコピーします。 このようにして、データセットを Spark で直接使用することができます。

- **ノートブック** によって、データセットを変換する Databricks Notebook がトリガーされます。 また、処理されたフォルダーまたは Azure Synapse Analytics にデータセットが追加されます。

わかりやすくするために、このチュートリアルのテンプレートではスケジュールされたトリガーを作成しません。 必要に応じて、1 つを追加できます。

![パイプラインの図](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>前提条件

- シンクとして使用するための `sinkdata` という名前のコンテナーを持つ Azure Blob ストレージ アカウント。

  ストレージ アカウント名、コンテナー名、アクセス キーをメモしておきます。 これらの値は、後でテンプレートの中で必要になります。

- Azure Databricks ワークスペース。

## <a name="import-a-notebook-for-transformation"></a>変換するノートブックをインポートする

**変換** ノートブックを Databricks ワークスペースにインポートするには、次のようにします。

1. Azure Databricks ワークスペースにサインインし、 **[インポート]** を選択します。
       ![ワークスペースをインポートするメニュー コマンド ](media/solution-template-Databricks-notebook/import-notebook.png)ワークスペース パスは、表示されているものとは異なる場合がありますが、後で使用できるように注意してください。
1. **インポート元:URL** を選択します。 テキスト ボックスに、「`https://adflabstaging1.blob.core.windows.net/share/Transformations.html`」と入力します。

   ![ノートブックをインポートするための選択](media/solution-template-Databricks-notebook/import-from-url.png)

1. それでは、 **[変換]** ノートブックを実際のストレージ接続情報で更新しましょう。

   インポートしたノートブックで、次のコード スニペットに示すように **コマンド 5** にアクセスします。

   - `<storage name>` と `<access key>` を実際のストレージ接続情報に置き換えます。
   - `sinkdata` コンテナーでストレージ アカウントを使用します。

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. Data Factory で Databricks にアクセスするための **Databricks アクセス トークン** を生成します。
   1. Databricks ワークスペースで、右上にあるユーザー プロファイル アイコンを選択します。
   1. **[ユーザー設定]** を選択します。
    ![ユーザー設定のメニュー コマンド](media/solution-template-Databricks-notebook/user-setting.png)
   1. **[アクセス トークン]** タブの **[新しいトークンの生成]** を選択します。
   1. **[Generate] \(生成)** を選択します。

    ![[生成] ボタン](media/solution-template-Databricks-notebook/generate-new-token.png)

   Databricks のリンクされたサービスの作成に後で使用するために *アクセス トークンを保存します。* アクセス トークンは、`dapi32db32cbb4w6eee18b7d87e45exxxxxx` のようになります。

## <a name="how-to-use-this-template"></a>このテンプレートの使用方法

1. **Azure Databricks による変換** テンプレートに移動し、次の接続用の新しいリンクされたサービスを作成します。

   ![接続の設定](media/solution-template-Databricks-notebook/connections-preview.png)

    - **ソース BLOB 接続** - ソース データにアクセスするため。

       この演習では、ソース ファイルが格納されているパブリック BLOB ストレージを使用できます。 構成については、次のスクリーンショットを参照してください。 次の **[SAS URL]** を使用して、ソース ストレージに接続します (読み取り専用アクセス)。

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![認証方法と SAS URL の選択](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **コピー先 BLOB 接続** – コピーしたデータの保管先。

       **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、シンク ストレージ BLOB を選択します。

       ![新しいリンクされたサービスとしてのシンク ストレージ BLOB](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** - Databricks クラスターに接続する。

        前に生成したアクセス キーを使用して、Databricks にリンクされたサービスを作成します。 ある場合は、*対話型クラスター* を選択することもできます。 この例では、 **[New job cluster]\(新しいジョブ クラスター\)** オプションを使用します。

        ![クラスターに接続するための選択](media/solution-template-Databricks-notebook/databricks-connection.png)

1. **[このテンプレートを使用]** を選択します。 作成されたパイプラインが表示されます。

    ![パイプラインを作成する](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>パイプラインの概要と構成

新しいパイプラインのほとんどの設定は、既定値で自動的に構成されています。 パイプラインの構成を確認し、必要に応じて変更します。

1. **[検証]** アクティビティの **[Availability flag]\(可用性フラグ\)** で、ソース **データセット** の値が、先ほど作成した `SourceAvailabilityDataset` に設定されていることを確認します。

   ![ソース データセットの値](media/solution-template-Databricks-notebook/validation-settings.png)

1. **データ コピー** アクティビティの **[file-to-blob]** で、 **[ソース]** タブと **[シンク]** タブを確認します。 必要に応じて設定を変更します。

   - **[ソース]![ タブ** ソース タブ](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - **[シンク]** タブ ![シンク タブ](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. **ノートブック** アクティビティの **変換** で、必要に応じてパスと設定を確認し、更新します。

   **Databricks のリンクされたサービス** には、前の手順の値が事前に設定されます。次に例を示します。![Databricks のリンクされたサービスで入力されている値](media/solution-template-Databricks-notebook/notebook-activity.png)

   **ノートブック** 設定を確認するには、次のようにします。
  
    1. **[設定]** タブを選択します。**ノートブック パス** については、既定のパスが正しいことを確認します。 正しいノートブック パスを参照して、選択する必要がある場合があります。

       ![ノートブック パス](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. **[基本パラメーター]** セレクターを展開し、パラメーターが次のスクリーンショットに示されている内容と一致していることを確認します。 これらのパラメーターは Data Factory から Databricks Notebook に渡されます。

       ![基本パラメーター](media/solution-template-Databricks-notebook/base-parameters.png)

1. **パイプラインのパラメーター** が、次のスクリーンショットに示されている内容と一致していることを確認します。![パイプラインのパラメーター](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. データセットに接続します。

    >[!NOTE]
    >以下のデータ セットでは、ファイルのパスはテンプレートで自動的に指定されています。 変更が必要な場合は、接続エラーが発生した場合に備えて、**container** と **directory** の両方のパスを指定してください。

   - **SourceAvailabilityDataset** - ソース データが使用可能であることを確認します。

     ![SourceAvailabilityDataset のリンクされたサービスとファイル パスの選択](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** - ソース データにアクセスします。

       ![SourceFilesDataset のリンクされたサービスとファイル パスの選択](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** - シンクのターゲットの場所にデータをコピーします。 次の値を使用します。

     - **リンクされたサービス** - `sinkBlob_LS`前のステップで作成済み。

     - **ファイル パス** - `sinkdata/staged_sink`。

       ![DestinationFilesDataset のリンクされたサービスとファイル パスの選択](media/solution-template-Databricks-notebook/destination-dataset.png)

1. **[デバッグ]** を選択してパイプラインを実行します。 より詳細な Spark のログについては、Databricks のログへのリンクを検索できます。

    ![出力から Databricks ログへのリンク](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Azure Storage Explorer を使用してデータ ファイルを確認することもできます。

    > [!NOTE]
    > Data Factory のパイプラインの実行と関連付けるため、この例では Data Factory からのパイプライン実行 ID を出力フォルダーに追加しています。 これにより、実行ごとに生成されたファイルを追跡することができます。
    > ![追加されたパイプラインの実行 ID](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>次のステップ

- [Azure Data Factory の概要](introduction.md)

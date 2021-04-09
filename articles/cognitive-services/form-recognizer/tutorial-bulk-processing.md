---
title: チュートリアル:Azure Data Factory を使用したフォーム データの一括抽出 - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Form Recognizer モデルのトレーニングと実行をトリガーし、ドキュメントの大きなバックログをデジタル化するために Azure Data Factory アクティビティを設定します。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: lajanuar
ms.openlocfilehash: 0c009a87a5834997cdc489efc75ebb16f9459754
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467104"
---
# <a name="tutorial-extract-form-data-in-bulk-by-using-azure-data-factory"></a>チュートリアル: Azure Data Factory を使用してフォーム データを一括で抽出する

このチュートリアルでは、各 Azure サービスを使用して大量のフォームをデジタル メディアに取り込む方法を確認します。 このチュートリアルでは、ドキュメントの Azure データ レイク から Azure SQL データベースへのデータ インジェストを自動化する方法について説明します。 数回クリックするだけで、すばやくモデルをトレーニングして新しいドキュメントを処理できるようになります。

## <a name="business-need"></a>ビジネス ニーズ

ほとんどの組織が今では、さまざまな形式 (PDF、画像、ビデオ) で所有しているデータの価値を認識するようになっています。 このような資産をデジタル化するためのベスト プラクティスおよびもっともコスト効果の高い方法が求められています。

また、多くの場合、Microsoft のお客様は多数のクライアントや顧客から送信されるさまざまな種類のフォームを使用しています。 [クイックスタート](./quickstarts/client-library.md)とは異なり、このチュートリアルではメタデータ駆動型のアプローチを使用して、新しいさまざまな種類のフォームでモデルを自動的にトレーニングする方法について説明します。 特定の種類のフォームに対して既存のモデルがない場合は、システムによって作成され、モデル ID が付与されます。 

フォームからデータを抽出し、既存の運用システムやデータ ウェアハウスと組み合わせることにより、ビジネスでは分析情報を得たり、顧客やビジネス ユーザーに価値を提供したりすることができます。

Azure Form Recognizer を使用すると、組織におけるデータの使用、プロセス (請求書の支払い、税の処理など) の自動化、コストと時間の節約、およびデータの精度の向上に役立ちます。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * フォームを格納するための Azure Data Lake を設定する。
> * Azure データベースを使用してパラメーター化テーブルを作成する。
> * Azure Key Vault を使用して機密性の高い資格情報を格納する。
> * Azure Databricks ノートブックで Form Recognizer モデルをトレーニングする。
> * Databricks ノートブックを使用してフォーム データを抽出する。
> * Azure Data Factory を使用してフォームのトレーニングと抽出を自動化する。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [無料で作成できます](https://azure.microsoft.com/free/cognitive-services/)。
* Azure サブスクリプションを用意したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer リソースを作成"  target="_blank">Form Recognizer リソースを作成</a>し、自分のキーとエンドポイントを取得します。 リソースがデプロイされた後、**[リソースに移動]** を選択します。
    * 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 キーとエンドポイントは、このクイックスタートの後半でコードに貼り付けます。
    * Free 価格レベル (F0) を利用して、サービスを試すことができます。 その後、運用環境用の有料レベルにアップグレードできます。
* 同じ種類の少なくとも 5 つのフォームのセット。 このワークフローは、大量のドキュメントをサポートするためのものであることが理想的です。 トレーニング データ ットをまとめるためのヒントとオプションについては、[トレーニング データセットの作成](./build-training-data-set.md)に関するページを参照してください。 このチュートリアルでは、[サンプル データセット](https://go.microsoft.com/fwlink/?linkid=2128080)の Train フォルダーにあるファイルを使用できます。


## <a name="project-architecture"></a>プロジェクト アーキテクチャ 

このプロジェクトでは、Azure Data Lake ストレージ アカウント内のドキュメントのデータをトレーニング、分析、および抽出する Python ノートブックをトリガーするための、一連の Azure Data Factory パイプラインを設定します。

Form Recognizer REST API には、入力としていくつかのパラメーターが必要です。 セキュリティ上の理由から、これらのパラメーターの一部は Azure キー コンテナーに格納されます。 ストレージ BLOB フォルダー名など、機密性が低いその他のパラメーターは、Azure SQL データベース内のパラメーター化テーブルに格納されます。

分析するフォームの種類ごとに、データ エンジニアまたはデータ科学者がパラメーター テーブルの行を設定します。 次に、彼らは Azure Data Factory を使用して、検出されたフォームの種類のリストを反復処理し、関連するパラメーターを Databricks ノートブックに渡して、Form Recognizer モデルをトレーニングまたは再トレーニングします。 ここでは、Azure 関数を使用することもできます。

その後、Azure Databricks ノートブックでは、トレーニング済みのモデルを使用してフォーム データを抽出します。 そのデータは Azure SQL データベースにエクスポートされます。

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="プロジェクト アーキテクチャを示す図。":::


## <a name="set-up-azure-data-lake"></a>Azure Data Lake の設定

フォームのバックログは、オンプレミスの環境内、または (s)FTP サーバー上に存在する場合があります。 このチュートリアルでは、Azure Data Lake Storage Gen2 アカウントにあるフォームを使用します。 そこにファイルを転送するには、Azure Data Factory、Azure Storage Explorer、または AzCopy を使用します。 トレーニングとスコアリングのデータセットは異なるコンテナーに配置できますが、すべてのフォームの種類のトレーニング データセットは同じコンテナー内に配置する必要があります。 (それらを異なるフォルダーに配置することはできます。)

新しいデータ レイクを作成するには、「[Azure Data Lake Storage Gen2 で使用するストレージ アカウントを作成する](../../storage/blobs/create-data-lake-storage-account.md)」の手順に従います。

## <a name="create-a-parameterization-table"></a>パラメーター化テーブルを作成する

次に、Azure SQL データベースにメタデータ テーブルを作成します。 このテーブルには、Form Recognizer REST API に必要な機密性の低いデータを含めます。 データセットに新しい種類のフォームがある場合は常に、このテーブルに新しいレコードを挿入し、トレーニングおよびスコアリングのパイプラインをトリガーします。 (これらのパイプラインは後で実装します。)

このテーブルでは、次のフィールドが使用されます。

* **form_description**。 トレーニングの一部として必須ではありません。 このフィールドには、モデルをトレーニングするフォームの種類の説明 ("client A forms"、"Hotel B forms" など) を指定します。
* **training_container_name**。 トレーニング データセットを格納したストレージ アカウント コンテナーの名前。 **scoring_container_name** と同じコンテナーにすることができます。
* **training_blob_root_folder**。 モデルのトレーニング用のファイルを格納するストレージ アカウント内のフォルダー。
* **scoring_container_name**。 キーと値のペアを抽出するファイルを格納したストレージ アカウント コンテナーの名前。 **training_container_name** と同じコンテナーにすることができます。
* **scoring_input_blob_folder**。 データの抽出元のファイルを格納するストレージ アカウント内のフォルダー。
* **model_id**。 再トレーニングするモデルの ID。 最初の実行では、値を -1 に設定する必要があります。これにより、トレーニング ノートブックでトレーニングする新しいカスタム モデルが作成されます。 トレーニング ノートブックによって、Azure Data Factory インスタンスに新しいモデル ID が返されます。 ストアド プロシージャ アクティビティを使用して、Azure SQL データベースでこの値を更新します。

  新しいフォームの種類を取り込む場合は常に、モデルをトレーニングする前に、モデル ID を -1 に手動でリセットする必要があります。

* **file_type**。 サポートされているフォームの種類は、`application/pdf`、`image/jpeg`、`image/png`、および `image/tif` です。

  他のファイルの種類のフォームがある場合は、新しいフォームの種類をトレーニングするときに、この値と **model_id** を変更する必要があります。
* **form_batch_group_id**。 時間の経過と共に、同じモデルに対して複数のフォームの種類をトレーニングすることがあります。 **form_batch_group_id** フィールドを使用すると、特定のモデルでトレーニングしてきたすべてのフォームの種類を指定できます。

### <a name="create-the-table"></a>テーブルの作成


[Azure SQL データベースを作成](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase)した後、[クエリ エディター](../../azure-sql/database/connect-query-portal.md)でこの SQL スクリプトを実行して、必要なテーブルを作成します。


```sql
CREATE TABLE dbo.ParamFormRecogniser(
    form_description varchar(50) NULL,
  training_container_name varchar(50) NOT NULL,
    training_blob_root_folder varchar(50) NULL,
    scoring_container_name varchar(50) NOT NULL,
    scoring_input_blob_folder varchar(50) NOT NULL,
    scoring_output_blob_folder varchar(50) NOT NULL,
    model_id varchar(50) NULL,
    file_type varchar(50) NULL
) ON PRIMARY
GO
```

このスクリプトを実行して、トレーニング後に **model_id** を自動的に更新するプロシージャを作成します。

```SQL
CREATE PROCEDURE [dbo].[update_model_id] ( @form_batch_group_id  varchar(50),@model_id varchar(50))
AS
BEGIN 
    UPDATE [dbo].[ParamFormRecogniser]   
        SET [model_id] = @model_id  
    WHERE form_batch_group_id =@form_batch_group_id
END
```

## <a name="use-azure-key-vault-to-store-sensitive-credentials"></a>Azure Key Vault を使用して機密性の高い資格情報を保存する

セキュリティ上の理由から、特定の機密性の高い情報は Azure SQL データベース内のパラメーター化テーブルに格納したくありません。 機密性の高いパラメーターは Azure Key Vault シークレットとして格納します。

### <a name="create-an-azure-key-vault"></a>Azure Key Vault を作成する

[Key Vault リソースを作成します](https://ms.portal.azure.com/#create/Microsoft.KeyVault)。 次に、作成された Key Vault リソースに移動し、 **[設定]** セクションで、 **[シークレット]** を選択してパラメーターを追加します。

新しいウィンドウが表示されます。 **[生成/インポート]** を選択します。 パラメーターの名前とその値を入力し、 **[作成]** を選択します。 次のパラメーターに対して、この手順を完了します。

* **CognitiveServiceEndpoint。** Form Recognizer API のエンドポイント URL。
* **CognitiveServiceSubscriptionKey**。 Form Recognizer サービスのアクセス キー。 
* **StorageAccountName**。 トレーニング データセットと、キーと値のペアを抽出するフォームが格納されているストレージ アカウント。 これらの項目が異なるアカウントにある場合は、各アカウント名を個別のシークレットとして入力します。 すべてのフォームの種類のトレーニング データセットは同じコンテナーに配置する必要があることを忘れないでください。 それらを異なるフォルダーに配置することはできます。
* **StorageAccountSasKey**。 ストレージ アカウントの Shared Access Signature (SAS)。 SAS URL を取得するには、ストレージ リソースにアクセスします。 **[ストレージ エクスプローラー]** タブでお使いのコンテナーに移動して右クリックし、 **[Shared Access Signature の取得]** を選択します。 ストレージ アカウント自体ではなく、コンテナー用の SAS を取得することが重要です。 アクセス許可の **[読み取り]** と **[リスト]** が選択されていることを確認し、 **[作成]** を選択します。 次に、その値を **URL** セクションにコピーします。 これは `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` の形式である必要があります。

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>Databricks ノートブックで Form Recognizer モデルをトレーニングする

Form Recognizer サービスと対話する Python コードを格納して実行するために、Azure Databricks を使用します。

### <a name="create-a-notebook-in-databricks"></a>Databricks でノートブックを作成する

Azure portal で [Azure Databricks リソースを作成します](https://ms.portal.azure.com/#create/Microsoft.Databricks)。 作成したリソースに移動し、ワークスペースを開きます。

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>Azure Key Vault によってサポートされるシークレットのスコープを作成する


上記で作成した Azure キー コンテナーのシークレットを参照するには、Databricks でシークレットのスコープを作成する必要があります。 「[Azure Key Vault を利用するシークレットのスコープを作成する](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)」の手順に従ってください。

### <a name="create-a-databricks-cluster"></a>Databricks クラスターを作成する

クラスターは、Databricks コンピュテーション リソースのコレクションです。 クラスターを作成するには:

1. 左側のペインで **[クラスター]** ボタンを選択します。
1. **[クラスター]** ページで、 **[クラスターの作成]** を選択します。
1. **[クラスターの作成]** ページでクラスター名を指定し、 **[Databricks Runtime Version]\(Databricks Runtime のバージョン\)** リストで **[7.2 (Scala 2.12, Spark 3.0.0)]** を選択します。
1. **[クラスターの作成]** を選択します。

### <a name="write-a-settings-notebook"></a>設定ノートブックを作成する

Python ノートブックを追加する準備ができました。 まず、「**Settings**」という名前のノートブックを作成します。 このノートブックにより、パラメーター化テーブル内の値がスクリプト内の変数に代入されます。 この値は、後でAzure Data Factory によってパラメーターとして渡されます。 また、キー コンテナー内のシークレットの値も変数に代入します。 

1. **Settings** ノートブックを作成するには、 **[ワークスペース]** ボタンを選択します。 新しいタブで、ドロップダウン リストを選択し、 **[作成]** 、 **[ノートブック]** の順に選択します。
1. ポップアップ ウィンドウでノートブックの名前を入力し、既定の言語として **[Python]** を選択します。 Databricks クラスターを選択し、 **[作成]** を選択します。
1. 最初のノートブック セルでは、Azure Data Factory によって渡されたパラメーターを取得します。

    ```python 
    dbutils.widgets.text("form_batch_group_id", "","")
    dbutils.widgets.get("form_batch_group_id")
    form_batch_group_id = getArgument("form_batch_group_id")
    
    dbutils.widgets.text("model_id", "","")
    dbutils.widgets.get("model_id")
    model_id = getArgument("model_id")
    
    dbutils.widgets.text("training_container_name", "","")
    dbutils.widgets.get("training_container_name")
    training_container_name = getArgument("training_container_name")
    
    dbutils.widgets.text("training_blob_root_folder", "","")
    dbutils.widgets.get("training_blob_root_folder")
    training_blob_root_folder=  getArgument("training_blob_root_folder")
    
    dbutils.widgets.text("scoring_container_name", "","")
    dbutils.widgets.get("scoring_container_name")
    scoring_container_name=  getArgument("scoring_container_name")
    
    dbutils.widgets.text("scoring_input_blob_folder", "","")
    dbutils.widgets.get("scoring_input_blob_folder")
    scoring_input_blob_folder=  getArgument("scoring_input_blob_folder")
    
    
    dbutils.widgets.text("file_type", "","")
    dbutils.widgets.get("file_type")
    file_type = getArgument("file_type")
    
    dbutils.widgets.text("file_to_score_name", "","")
    dbutils.widgets.get("file_to_score_name")
    file_to_score_name=  getArgument("file_to_score_name")
    ```

1. 2 番目のセルでは、Key Vault からシークレットを取得し、変数に代入します。

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>トレーニング ノートブックを作成する

**Settings** ノートブックが完成したので、モデルをトレーニングするためのノートブックを作成できます。 前述のように、Azure Data Lake Storage Gen2 アカウント内のフォルダー (**training_blob_root_folder**) に格納されているファイルを使用します。 フォルダー名は変数として渡されています。 フォームの種類の各セットは、同じフォルダーにあります。 パラメーター テーブルをループ処理しながら、そのフォームの種類すべてを使用してモデルをトレーニングします。 

1. 「**TrainFormRecognizer**」という名前のノートブックを作成します。 
1. 最初のセルで、**Settings** ノートブックを実行します。

    ```python
    %run "./Settings"
    ```

1. 次のセルでは、Settings ファイルから変数を代入し、[REST のクイックスタート](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20)に記載されているコードを応用して、フォームの種類ごとにモデルを動的にトレーニングします。

    ```python
    import json
    import time
    from requests import get, post
    
    post_url = cognitive_service_endpoint + r"/formrecognizer/v2.0/custom/models"
    source = training_storage_account_url
    prefix= training_blob_root_folder
    
    includeSubFolders=True
    useLabelFile=False
    headers = {
        # Request headers.
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    body =     {
        "source": source
        ,"sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
       },
    }
    if model_id=="-1": # If you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key/value pairs.
      try:
          resp = post(url = post_url, json = body, headers = headers)
          if resp.status_code != 201:
              print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
              quit()
          print("POST model succeeded:\n%s" % resp.headers)
          get_url = resp.headers["location"]
          model_id=get_url[get_url.index('models/')+len('models/'):]     
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    else :# If you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. トレーニング プロセスの最後の手順は、JSON 形式でトレーニング結果を取得することです。

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = headers)
            resp_json = resp.json()
            print (resp.status_code)
            if resp.status_code != 200:
                print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
                n_try += 1
                quit()
            model_status = resp_json["modelInfo"]["status"]
            print (model_status)
            if model_status == "ready":
                print("Training succeeded:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            if model_status == "invalid":
                print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            # Training still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1
            wait_sec = min(2*wait_sec, max_wait_sec)     
            print (n_try)
        except Exception as e:
            msg = "GET model failed:\n%s" % str(e)
            print(msg)
            quit()
    print("Train operation did not complete within the allocated time.")
    ```

## <a name="extract-form-data-by-using-a-notebook"></a>ノートブックを使用してフォーム データを抽出する

### <a name="mount-the-azure-data-lake-storage"></a>Azure Data Lake ストレージをマウントする

次の手順は、トレーニング済みのモデルを使用して、所有するさまざまなフォームをスコア付けすることです。 Azure Data Lake Storage アカウントを Databricks にマウントし、取り込みプロセス中にそのマウントを参照します。

トレーニング段階で行ったように、Azure Data Factory を使用して、フォームからのキーと値のペアの抽出を呼び出します。 パラメーター テーブルで指定されているフォルダー内のフォームに対してループ処理を行います。

1. Databricks にストレージ アカウントをマウントするノートブックを作成します。 これは「**MountDataLake**」という名前にします。 
1. 最初に **Settings** ノートブックを呼び出す必要があります。

    ```python
    %run "./Settings"
    ```

1. 2 番目のセルでは、機密性の高いパラメーター用の変数を定義します。これは Key Vault シークレットから取得します。

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. 以前にマウントされていた場合に備えて、ストレージ アカウントのマウントを解除します。

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. ストレージ アカウントをマウントします。


    ```python
    try: 
      dbutils.fs.mount( 
        source = scoring_source_str, 
        mount_point = scoring_mount_point, 
        extra_configs = {scoring_conf_key: scoring_storage_account_sas_key} 
      ) 
    except Exception as e: 
      print("ERROR: {} already mounted. Run previous cells to unmount first".format(scoring_mount_point))
    
    ```

    > [!NOTE]
    > マウントしたのは、トレーニング ストレージ アカウントのみです。 この場合、トレーニング ファイルと、キーと値のペアを抽出するファイルは同じストレージ アカウント内にあります。 スコアリングとトレーニングのストレージ アカウントが異なる場合は、両方のストレージ アカウントをマウントする必要があります。 

### <a name="write-the-scoring-notebook"></a>スコアリング ノートブックを作成する

これでスコアリング ノートブックを作成できます。 トレーニング ノートブックで行った内容と同様のことを行います。先ほどマウントした Azure Data Lake Storage アカウント内のフォルダーに格納されているファイルを使用します。 フォルダー名は変数として渡されます。 指定されたフォルダー内のすべてのフォームに対してループ処理を行い、それらからキーと値のペアを抽出します。 

1. ノートブックを作成し、「**ScoreFormRecognizer**」という名前を付けます。 
1. **Settings** および **MountDataLake** ノートブックを実行します。

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. [Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) API を呼び出すコードを追加します。

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post 
    
    
    #prefix= TrainingBlobFolder
    post_url = cognitive_service_endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"/dbfs/mnt/"+scoring_container_name+"/"+scoring_input_blob_folder+"/"+file_to_score_name
    output = r"/dbfs/mnt/"+scoring_container_name+"/scoringforms/ExtractionResult/"+os.path.splitext(os.path.basename(source))[0]+"_output.json"
    
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers.
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. 次のセルで、キーと値のペアの抽出の結果を取得します。 このセルによって結果が出力されます。 Azure SQL Database または Azure Cosmos DB でさらに処理できるように、結果は JSON 形式にする必要があります。 そのため、結果を .json ファイルに書き込みます。 出力ファイル名は、スコアリングされたファイルの名前に "_output.json" を連結したものになります。 ファイルは、ソース ファイルと同じフォルダーに格納されます。

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
       try:
           resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": cognitive_service_subscription_key})
           resp_json = resp.json()
           if resp.status_code != 200:
               print("GET analyze results failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           status = resp_json["status"]
           if status == "succeeded":
               print("Analysis succeeded:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           if status == "failed":
               print("Analysis failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           # Analysis still running. Wait and retry.
           time.sleep(wait_sec)
           n_try += 1
           wait_sec = min(2*wait_sec, max_wait_sec)     
       except Exception as e:
           msg = "GET analyze results failed:\n%s" % str(e)
           print(msg)
           n_try += 1
           print("Analyze operation did not complete within the allocated time.")
           quit()
    
    ```
1. 新しいセルでファイルの書き込み手順を実行します。

    ```python
    import requests
    file = open(output, "w")
    file.write(str(resp_json))
    file.close()
    ```

## <a name="automate-training-and-scoring-by-using-azure-data-factory"></a>Azure Data Factory を使用してトレーニングとスコアリングを自動化する

残っている唯一の手順は、トレーニングとスコアリングのプロセスを自動化するために Azure Data Factory サービスを設定することです。 まず、「[Data Factory の作成](../../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)」の手順に従います。 Azure Data Factory リソースを作成したら、3 つのパイプライン (トレーニング用に 1 つ、スコアリング用に 2 つ) を作成する必要があります。 (後で説明します。)

### <a name="training-pipeline"></a>トレーニング パイプライン

トレーニング パイプラインの最初のアクティビティは、Azure SQL データベース内のパラメーター化テーブルの値を読み取って返すためのルックアップです。 トレーニング データセットはすべて同じストレージ アカウントとコンテナーにあります (ただし、異なるフォルダーにある可能性はあります)。 そのため、Lookup アクティビティ設定では既定の属性値 **[First row only]\(先頭行のみ\)** のままにします。 モデルをトレーニングするフォームの種類ごとに、**training_blob_root_folder** 内のすべてのファイルを使用してモデルをトレーニングします。

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="Data Factory のトレーニング パイプラインを示すスクリーンショット。":::

ストアド プロシージャは、2 つのパラメーター **model_id** と **form_batch_group_id** を受け取ります。 Databricks ノートブックからモデル ID を返すコードは `dbutils.notebook.exit(model_id)` です。 Data Factory のストアド プロシージャ アクティビティのコードを読み取るコードは `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id` です。

### <a name="scoring-pipelines"></a>スコアリング パイプライン

キーと値のペアを抽出するには、パラメーター化テーブル内のすべてのフォルダーをスキャンします。 フォルダーごとに、その中にあるすべてのファイルのキーと値のペアを抽出します。 Azure Data Factory では、入れ子になった ForEach ループは現在サポートされていません。 代わりに、2 つのパイプラインを作成します。 最初のパイプラインでは、パラメーター化テーブルから参照を行い、フォルダーの一覧をパラメーターとして 2 番目のパイプラインに渡します。

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="Data Factory の最初のスコアリング パイプラインを示すスクリーンショット。":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="Data Factory の最初のスコアリング パイプラインの詳細を示すスクリーンショット。":::

2 番目のパイプラインでは、GetMeta アクティビティを使用してフォルダー内のファイルの一覧を取得し、それをパラメーターとして Databricks スコアリング ノートブックに渡します。

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="Data Factory の 2 番目のスコアリング パイプラインを示すスクリーンショット。":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="Data Factory の 2 番目のスコアリング パイプラインの詳細を示すスクリーンショット。":::

### <a name="specify-a-degree-of-parallelism"></a>並列処理の次数を指定する

トレーニングとスコアリングの両方のパイプラインで並列処理の次数を指定できるため、複数のフォームを同時に処理できます。

Azure Data Factory パイプラインで並列処理の次数を設定するには、次のようにします。

1. **ForEach** アクティビティを選択します。
1. **[シーケンシャル]** ボックスをオフにします。
1. **[バッチ カウント]** ボックスで並列処理の次数を設定します。 スコアリングには、最大バッチ カウントを 15 にすることをお勧めします。

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="Azure Data Factory のスコアリング アクティビティの並列処理構成を示すスクリーンショット。":::

## <a name="how-to-use-the-pipeline"></a>パイプラインの使用方法

これで、フォームのバックログをデジタル化し、さらにいくつかの分析を実行する、自動化されたパイプラインが完成しました。 既存のストレージ フォルダーに使い慣れた種類の新しいフォームを追加するときは、スコアリング パイプラインを再度実行するだけです。 それにより、新しいフォームの出力ファイルを含むすべての出力ファイルが更新されます。 

新しい種類の新しいフォームを追加する場合は、適切なコンテナーにトレーニング データセットをアップロードする必要もあります。 次に、パラメーター化テーブルに新しい行を追加し、新しいドキュメントとそのトレーニング データセットの場所を入力します。 フォームに対して新しいモデルをトレーニングする必要があることを示すために、**model_ID** に値 -1 を入力します。 その後、Azure Data Factory でトレーニングパイプラインを実行します。 パイプラインにより、テーブルからの読み取り、モデルのトレーニング、テーブル内のモデル ID の上書きが行われます。 その後、スコアリング パイプラインを呼び出して、出力ファイルの書き込みを開始できます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Form Recognizer モデルのトレーニングと実行をトリガーし、ファイルの大きなバックログをデジタル化するために、Azure Data Factory パイプラインを設定しました。 次に、Form Recognizer API を調べて、他に何ができるかを確認します。

* [Form Recognizer REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync)

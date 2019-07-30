---
title: '読み込み: Data Prep Python SDK'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Data Prep SDK を使用してデータを読み込む方法について説明します。 さまざまな種類の入力データを読み込むことができ、データ ファイルの種類やパラメーターを指定することも、SDK のスマート読み取り機能を使用してファイルの種類を自動検出することもできます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 07/12/2019
ms.custom: seodec18
ms.openlocfilehash: bd60d9f9bee55ef1342fe344e8b4f2f64e313331
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360979"
---
# <a name="load-and-read-data-with-the-azure-machine-learning-data-prep-sdk"></a>Azure Machine Learning Data Prep SDK でのデータの読み込みと読み取り
この記事では、Azure Machine Learning Data Prep SDK を使用してデータを読み込むさまざまな方法について説明します。  SDK では、次のような複数のデータ インジェスト機能がサポートされます。

* パラメーターの推論 (エンコード、区切り、ヘッダー) を解析してさまざまな種類のファイルから読み込む
* ファイルの読み込み中に推論を使用した型変換
* MS SQL Server と Azure Data Lake Storage の接続のサポート

> [!Important]
> 新しいソリューションを構築している場合は、データの探索と準備に [Azure Machine Learning Datasets](how-to-explore-prepare-data.md) (プレビュー) をお試しください。 Datasets は、次のバージョンのデータデータ準備 SDK であり、AI ソリューションでデータセットを管理するための拡張機能が提供されます。


次の表は、一般的なファイル タイプからのデータの読み込みに使用される関数の選択を示します。

| ファイルの種類 | Function | 参照リンク |
|-------|-------|-------|
|任意|`auto_read_file()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#auto-read-file-path--filepath--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|Text|`read_lines()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-lines-path--filepath--header--azureml-dataprep-api-dataflow-promoteheadersmode----promoteheadersmode-none--0---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---skip-rows--int---0--skip-mode--azureml-dataprep-api-dataflow-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|CSV|`read_csv()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-csv-path--filepath--separator--str--------header--azureml-dataprep-api-dataflow-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---quoting--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-dataflow-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false--archive-options--azureml-dataprep-api--archiveoption-archiveoptions---none--infer-column-types--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|Excel|`read_excel()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-excel-path--filepath--sheet-name--str---none--use-column-headers--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--include-path--bool---false--infer-column-types--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|固定長|`read_fwf()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-fwf-path--filepath--offsets--typing-list-int---header--azureml-dataprep-api-dataflow-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-dataflow-skipmode----skipmode-none--0---include-path--bool---false--infer-column-types--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|JSON|`read_json()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-json-path--filepath--encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---flatten-nested-arrays--bool---false--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|

## <a name="load-data-automatically"></a>データを自動的に読み込む

ファイルの種類を指定せずに自動的にデータを読み込むには、`auto_read_file()` 関数を使用します。 ファイルの種類と読み取りに必要な引数は、自動的に推測されます。

```python
import azureml.dataprep as dprep

dflow = dprep.auto_read_file(path='./data/any-file.txt')
```

この関数は、ファイルの種類、エンコード、およびその他の解析の引数を、すべて 1 つの便利なエントリ ポイントから自動的に検出するので便利です。 この関数は、区切られたデータの読み込み時によく実行される以下の手順も自動的に実行します。

* 区切り記号の推測と設定
* ファイル最上部にある空のレコードのスキップ
* ヘッダー行の推測と設定

または、事前にファイルの種類がわかっていて、解析方法を明示的に制御したい場合は、ファイル固有の関数を使用してください。

## <a name="load-text-line-data"></a>テキスト行データを読み込む

単純なテキスト データをデータフローに読み取るには、省略可能なパラメーターを指定しないで `read_lines()` を使用します。

```python
dflow = dprep.read_lines(path='./data/text_lines.txt')
dflow.head(5)
```

||Line|
|----|-----|
|0|日付 \|\|  最低気温 \|\|  最大温度|
|1|2015-07-1 \|\|  -4.1 \|\|  10.0|
|2|2015-07-2 \|\|  -0.8 \|\|  10.8|


データの取り込み後に、次のコードを実行して、データフロー オブジェクトを Pandas データフレームに変換します。

```python
pandas_df = dflow.to_pandas_dataframe()
```

## <a name="load-csv-data"></a>CSV データを読み込む

区切り形式のファイルを読み取る場合は、解析パラメーター (区切り、エンコード、ヘッダーを使用するかどうかなど) を基礎ランタイムに推測させることができます。 次のコードを実行して、場所だけを指定して CSV ファイルの読み取りを試行します。

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Hale County|10171002158| |
|2|ALABAMA|1|101710|Hale County|10171002162| |


読み込み中に行を除外するには、`skip_rows` パラメーターを定義します。 このパラメーターでは、CSV ファイル内で行の読み込みを降順にスキップします (1 から始まるインデックスを使用)。

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                       skip_rows=1)
dflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|ALABAMA|1|101710|Hale County|10171002158|29|
|1|ALABAMA|1|101710|Hale County|10171002162|40 |

列のデータ型を表示するには次のコードを実行します。

```python
dflow.dtypes
```
出力:

    stnam                     object
    fipst                     object
    leaid                     object
    leanm10                   object
    ncessch                   object
    schnam10                  object
    MAM_MTH00numvalid_1011    object
    dtype: object

既定では、Azure Machine Learning Data Prep SDK ではデータ型は変更されません。 ここで読み込むデータ ソースはテキスト ファイルなので、SDK ではすべての値が文字列として読み取られます。 この例では、数値列を数値として解析する必要があります。 `inference_arguments` パラメーターを `InferenceArguments.current_culture()` に設定して、ファイルの読み取り中に列の型を自動的に推測し、変換します。

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dflow.dtypes
```
出力:

    stnam                      object
    fipst                     float64
    leaid                     float64
    leanm10                    object
    ncessch                   float64
    schnam10                   object
    ALL_MTH00numvalid_1011    float64
    dtype: object


複数の列が数値として正しく検出され、型が `float64` に設定されました。

## <a name="use-excel-data"></a>Excel データを使用する

SDK には、Excel ファイルを読み込むための `read_excel()` 関数が含まれています。 既定では、関数によってブック内の最初のシートが読み込まれます。 読み込む特定のシートを定義するには、`sheet_name` パラメーターにシート名の文字列値を定義します。

```python
dflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dflow.head(5)
```

| |Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8| | |
|-|-------|-------|-------|-------|-------|-------|-------|-------|-|-|
|0|なし|なし|なし|なし|なし|なし|なし|なし|なし| |
|1|なし|なし|なし|なし|なし|なし|なし|なし|なし| |
|2|なし|なし|なし|なし|なし|なし|なし|なし|なし| |
|3|RANK|タイトル|スタジオ|世界全域|国内 / %|列 1|海外 / %|列 2|年 ^| |
|4|1|Avatar|Fox|2788|760.5|0.273|2027.5|0.727|2009^|5|

出力には、2 番目のシート内のデータに、ヘッダーより前に 3 つの空の行があったことが示されています。 `read_excel()` 関数には、行のスキップとヘッダーの使用のための省略可能なパラメーターが含まれています。 次のコードを実行して最初の 3 行をスキップし、4 行目をヘッダーとして使用します。

```python
dflow = dprep.read_excel(path='./data/excel.xlsx',
                         sheet_name='Sheet2', use_column_headers=True, skip_rows=3)
```

||RANK|タイトル|スタジオ|世界全域|国内 / %|列 1|海外 / %|列 2|年 ^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Avatar|Fox|2788|760.5|0.273|2027.5|0.727|2009^|
|1|2|Titanic|Par.|2186.8|658.7|0.301|1528.1|0.699|1997^|

## <a name="load-fixed-width-data-files"></a>固定幅データ ファイルを読み込む

固定幅のファイルを読み込むには、文字のオフセットの一覧を指定します。 最初の列は、常にオフセット 0 から始まると想定されます。

```python
dflow = dprep.read_fwf('./data/fixed_width_file.txt',
                       offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dflow.head(5)
```

||010000|99999|BOGUS NORWAY|NO|NO_1|ENRS|列 7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|BOGUS NORWAY|NO|NO|ENSO||||
|1|010010|99999|JAN MAYEN|NO|JN|ENJA|+70933|-008667|+00090|


ヘッダーの検出を回避し、正しいデータを解析するには、`header` パラメーターに `PromoteHeadersMode.NONE` を渡します。

```python
dflow = dprep.read_fwf('./data/fixed_width_file.txt',
                       offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                       header=dprep.PromoteHeadersMode.NONE)
```

||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|BOGUS NORWAY|NO|NO_1|ENRS|列 7|Column8|Column9|
|1|010003|99999|BOGUS NORWAY|NO|NO|ENSO||||


## <a name="load-sql-data"></a>SQL データの読み込み

SDK では、SQL ソースからデータを読み込むこともできます。 現在は、Microsoft SQL Server のみサポートされています。 SQL サーバーからデータを読み込むには、接続パラメーターを含む [`MSSQLDataSource`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.mssqldatasource?view=azure-dataprep-py) オブジェクトを作成します。 `MSSQLDataSource` のパスワード パラメーターは [`Secret`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#register-secret-value--str--id--str---none-----azureml-dataprep-api-engineapi-typedefinitions-secret) オブジェクトを受け入れます。 シークレット オブジェクトは、2 つの方法で作成できます。

* 実行エンジンに、シークレットとその値を登録します。
* `dprep.create_secret("[SECRET-ID]")` を使用して、`id` のみを指定してシークレットを作成します (実行環境にシークレット値が既に登録されている場合)。

```python
secret = dprep.register_secret(value="[SECRET-PASSWORD]", id="[SECRET-ID]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=secret)
```

データ ソース オブジェクトを作成したら、クエリ出力からのデータの読み取りに進むことができます。

```python
dflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dflow.head(5)
```

| |ProductID|EnableAdfsAuthentication|ProductNumber|色|StandardCost|ListPrice|Size|Weight|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|rowguid|ModifiedDate| |
|-|---------|----|-------------|-----|------------|---------|----|------|-----------------|--------------|-------------|-----------|----------------|--------------|----------------------|-------|------------|-|
|0|680|HL Road Frame - Black, 58|FR-R92B-58|黒|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|なし|なし|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000+00:00|
|1|706|HL Road Frame - Red, 58|FR-R92R-58|赤|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|なし|なし|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000+00:00|
|2|707|Sport-100 Helmet, Red|HL-U509-R|赤|13.0863|34.99|なし|なし|35|33|2005-07-01 00:00:00+00:00|なし|なし|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000+00:00|


## <a name="use-azure-data-lake-storage"></a>Azure Data Lake Storage を使用する

SDK が Azure Data Lake Storage にアクセスするために必要な OAuth トークンを取得するには、2 つの方法があります。

* ユーザーの Azure CLI ログインの最近のセッションからアクセス トークンを取得する
* サービス プリンシパル (SP) と証明書をシークレットとして使用する

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>最近の Azure CLI セッションからアクセス トークンを使用する

ローカル コンピューター上で、次のコマンドを実行します。

```azurecli
az login
az account show --query tenantId
dflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dflow.head(5) head
```

> [!NOTE]
> ユーザー アカウントが複数の Azure テナントのメンバーである場合は、AAD URL ホスト名の形式でテナントを指定する必要があります。

### <a name="create-a-service-principal-with-the-azure-cli"></a>Azure CLI でサービス プリンシパルを作成する

Azure CLI を使用してサービス プリンシパルと対応する証明書を作成します。 この特定のサービス プリンシパルは `reader` ロールを使用して構成され、その範囲は Azure Data Lake Storage アカウントの 'dpreptestfiles' のみに縮小されています。

```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```

このコマンドは、`appId` と証明書ファイルへのパス (通常はホーム フォルダー内) を発行します。 .crt ファイルには、パブリック証明書と秘密キーの両方が PEM 形式で格納されています。

```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Azure Data Lake Storage ファイル システム用の ACL を構成するには、ユーザーの objectId を使用します。 この例では、サービス プリンシパルが使用されます。

```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Azure Data Lake Storage ファイル システムに対する `Read` および `Execute` アクセスを構成するには、フォルダーとファイルの ACL を個別に構成します。 これは、基礎となる HDFS ACL モデルが継承をサポートしていないためです。

```azurecli
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```

```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```

### <a name="acquire-an-oauth-access-token"></a>OAuth アクセス トークンを取得する

MSFT テナント上で認証コンテキストを作成し、OAuth アクセス トークンを取得するには、`adal` パッケージ (`pip install adal`) を使用します。 ADLS の場合、トークン要求のリソースは、他の多くの Azure リソースとは異なり、'https:\//datalake.azure.net' 用でなければなりません。

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext(
    'https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate(
    'https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dflow = dprep.read_csv(path=DataLakeDataSource(
    path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Web サイト|street|city|County|
|----|------|-----|----|----|----|----|
|0|1012063|Caledonia Farmers Market Association - Danville|https://sites.google.com/site/caledoniafarmers.. ||Danville|Caledonia|
|1|1011871|Stearns Homestead Farmers' Market|http://Stearnshomestead.com |6975 Ridge Road|Parma|Cuyahoga|
|2|1011878|100 Mile Market|https://www.pfcmarkets.com |507 Harrison St|Kalamazoo|Kalamazoo|
|3|1009364|106 S. Main Street Farmers Market|http://thetownofsixmile.wordpress.com/ |106 S. Main Street|Six Mile|||
|4|1010691|10th Street Community Farmers Market|https://agrimissouri.com/.. |10th Street and Poplar|Lamar|Barton|

## <a name="next-steps"></a>次の手順

* 特定のシナリオを解決するサンプルについては Azure Machine Learning Data Prep SDK の [チュートリアル](tutorial-data-prep.md) をご覧ください。

---
title: Azure Data Factory のトラブルシューティング | Microsoft Docs
description: Azure Data Factory における外部の制御アクティビティのトラブルシューティング方法について説明します。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: ed466b072a771c3aa288a96fa4a0037c31b875f9
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091988"
---
# <a name="troubleshoot-azure-data-factory"></a>Azure Data Factory のトラブルシューティング

この記事では、Azure Data Factory における外部の制御アクティビティの一般的なトラブルシューティング方法について説明します。

## <a name="connector-and-copy-activity"></a>コネクタとコピー アクティビティ

コピー アクティビティの使用時にエラーが発生したなど、コネクタに関する問題については、「[Azure Data Factory コネクタの問題を解決する](connector-troubleshoot-guide.md)」を参照してください。

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>エラー コード:3200

- **メッセージ**:エラー 403。

- **原因**: `The Databricks access token has expired.`

- **推奨事項**:既定では、Azure Databricks のアクセス トークンは 90 日間有効です。 新しいトークンを作成し、リンクされたサービスを更新してください。


### <a name="error-code--3201"></a>エラー コード:3201

- **メッセージ**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **原因**: `Bad authoring: Notebook path not specified correctly.`

- **推奨事項**:Databricks アクティビティに Notebook のパスを指定してください。

<br/>

- **メッセージ**: `Cluster   ... does not exist.`

- **原因**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **推奨事項**:Databricks クラスターが存在することを確認してください。

<br/>

- **メッセージ**: `Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **原因**: `Bad authoring.`

- **推奨事項**:ワークスペース アドレス指定スキームの絶対パス (Databricks ファイル システムに格納されているファイルの場合は `dbfs:/folder/subfolder/foo.py`) を指定してください。

<br/>

- **メッセージ**: `{0} LinkedService should have domain and accessToken as required properties.`

- **原因**: `Bad authoring.`

- **推奨事項**:[リンクされたサービスの定義](compute-linked-services.md#azure-databricks-linked-service)を確認してください。

<br/>

- **メッセージ**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **原因**: `Bad authoring.`

- **推奨事項**:[リンクされたサービスの定義](compute-linked-services.md#azure-databricks-linked-service)を確認してください。

<br/>

- **メッセージ**: `Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **原因**: `Bad authoring.`

- **推奨事項**:エラー メッセージを参照してください。 

<br/>

### <a name="error-code--3202"></a>エラー コード:3202

- **メッセージ**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **原因**: `Too many Databricks runs in an hour.`

- **推奨事項**:この Databricks ワークスペースを使用しているすべてのパイプラインでジョブの作成速度を確認します。  パイプラインで起動された Databricks の実行数が全体として多すぎる場合は、一部のパイプラインを新しいワークスペースに移行してください。

<br/>

- **メッセージ**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **原因**: `Authoring error: No value provided for the parameter.`

- **推奨事項**:パイプラインの JSON を検査して、baseParameters ノートブックのすべてのパラメーターに空以外の値が指定されていることを確認してください。

<br/>

- **メッセージ**:`User: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` is not   authorized to access cluster.`

- **原因**:アクセス トークンを生成したユーザーは、リンクされたサービスに指定されている Databricks クラスターへのアクセスを許可されていません。

- **推奨事項**:ワークスペースで必要なアクセス許可がユーザーにあることを確認します。


### <a name="error-code--3203"></a>エラー コード:3203

- **メッセージ**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **原因**:クラスターは終了されました。 対話型クラスターの場合、競合状態になる可能性があります。

- **推奨事項**:このエラーを回避する最善の方法は、ジョブ クラスターを使用することです。


### <a name="error-code--3204"></a>エラー コード:3204

- **メッセージ**: `Job execution failed.`

- **原因**:エラー メッセージは、予期しないクラスター状態や特定のアクティビティなど、さまざまな問題を示します。 ほとんどの場合、エラー メッセージがまったく表示されません。 

- **推奨事項**:該当なし


## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

次の表は、U-SQL について記載したものです。


### <a name="error-code--2709"></a>エラー コード:2709

- **メッセージ**: `The access token is from the wrong tenant.`

- **原因**:Azure Active Directory (Azure AD) テナントが正しくありません。

- **推奨事項**:Azure Active Directory (Azure AD) テナントが正しくありません。

<br/>

- **メッセージ**: `We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **原因**:このエラーは、Data Lake Analytics のスロットリングに起因しています。

- **推奨事項**:アクティビティに対する Data Factory のトリガーとコンカレンシーの設定を変更して、Data Lake Analytics に送信されるジョブの数を減らしてください。 または、Data Lake Analytics の上限を引き上げます。

<br/>

- **メッセージ**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **原因**:このエラーは、Data Lake Analytics のスロットリングに起因しています。 

- **推奨事項**:アクティビティに対する Data Factory のトリガーとコンカレンシーの設定を変更して、Data Lake Analytics に送信されるジョブの数を減らしてください。 または、Data Lake Analytics の上限を引き上げます。


### <a name="error-code--2705"></a>エラー コード:2705

- **メッセージ**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **原因**:サービス プリンシパルまたは証明書には、ストレージ内のファイルへのアクセス権がありません。

- **推奨事項**:Data Lake Analytics ジョブ用にユーザーが指定したサービス プリンシパルまたは証明書が、Data Lake Analytics アカウントにアクセスできること、また、ルート フォルダーから既定の Data Lake Storage インスタンスにアクセスできることを確認します。


### <a name="error-code--2711"></a>エラー コード:2711

- **メッセージ**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **原因**:サービス プリンシパルまたは証明書には、ストレージ内のファイルへのアクセス権がありません。

- **推奨事項**:Data Lake Analytics ジョブ用にユーザーが指定したサービス プリンシパルまたは証明書が、Data Lake Analytics アカウントにアクセスできること、また、ルート フォルダーから既定の Data Lake Storage インスタンスにアクセスできることを確認します。

<br/>

- **メッセージ**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **原因**:U-SQL ファイルのパスが間違っているか、リンクされたサービスの資格情報がアクセス権を持っていません。

- **推奨事項**:パスと、リンクされたサービスに指定された資格情報を確認してください。


### <a name="error-code--2704"></a>エラー コード:2704

- **メッセージ**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **原因**:サービス プリンシパルまたは証明書には、ストレージ内のファイルへのアクセス権がありません。

- **推奨事項**:Data Lake Analytics ジョブ用にユーザーが指定したサービス プリンシパルまたは証明書が、Data Lake Analytics アカウントにアクセスできること、また、ルート フォルダーから既定の Data Lake Storage インスタンスにアクセスできることを確認します。


### <a name="error-code--2707"></a>エラー コード:2707

- **メッセージ**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **原因**:リンクされたサービスの Data Lake Analytics アカウントに誤りがあります。

- **推奨事項**:正しいアカウントが指定されていることを確認してください。


### <a name="error-code--2703"></a>エラー コード:2703

- **メッセージ**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **原因**:Data Lake Analytics からのエラーです。 

- **推奨事項**:この例のようなエラーは、Data Lake Analytics にジョブが送信されたものの、そこでスクリプトが失敗したことを意味します。 Data Lake Analytics 内を調査してください。 ポータルから Data Lake Analytics アカウントに移動し、Data Factory の (パイプラインの実行 ID ではなく) アクティビティの実行 IDを使用して該当するジョブを探します。 見つかったジョブから、エラーに関する詳細な情報を入手し、トラブルシューティングに役立てることができます。 解決方法が明確でない場合は、Data Lake Analytics サポート チームに連絡し、お使いのアカウント名とジョブ ID を含むジョブの URL を伝えてください。



## <a name="azure-functions"></a>Azure 関数

### <a name="error-code--3602"></a>エラー コード:3602

- **メッセージ**: `Invalid HttpMethod: {method}.`

- **原因**:アクティビティのペイロードに指定された HTTP メソッドが Azure 関数アクティビティでサポートされていません。 

- **推奨事項**:サポートされている HTTP メソッドは、PUT、POST、GET、DELETE、OPTIONS、HEAD、TRACE です。


### <a name="error-code--3603"></a>エラー コード:3603

- **メッセージ**: `Response content is not a valid JObject.`

- **原因**:呼び出した Azure 関数の応答で JSON ペイロードが返されませんでした。 Data Factory の Azure 関数アクティビティでサポートされるのは JSON 応答コンテンツだけです。 

- **推奨事項**:有効な JSON ペイロードを返すように Azure 関数を更新してください。 たとえば、C# の関数から `(ActionResult)new<OkObjectResult("{`\"Id\":\"123\"`}");` を返すことが考えられます。


### <a name="error-code--3606"></a>エラー コード:3606

- **メッセージ**: `Azure function activity missing function key.`

- **原因**:Azure 関数アクティビティの定義が完全ではありません。 

- **推奨事項**:入力 AzureFunction アクティビティの JSON 定義に、"functionKey" という名前のプロパティがあることを確認してください。


### <a name="error-code--3607"></a>エラー コード:3607

- **メッセージ**: `Azure function activity missing function name.`

- **原因**:Azure 関数アクティビティの定義が完全ではありません。 

- **推奨事項**:入力 AzureFunction アクティビティの JSON 定義に、"functionName" という名前のプロパティがあることを確認してください。


### <a name="error-code--3608"></a>エラー コード:3608

- **メッセージ**: `Call to provided Azure function '{FunctionName}' failed with status-'{statusCode}' and message - '{message}'.` 

- **原因**:アクティビティ定義での Azure 関数の詳細が正しくない可能性があります。 

- **推奨事項**:Azure 関数の詳細を修正してから、もう一度やり直してください。


### <a name="error-code--3609"></a>エラー コード:3609

- **メッセージ**: `Azure function activity missing functionAppUrl.` 

- **原因**:Azure 関数アクティビティの定義が完全ではありません。 

- **推奨事項**:入力 AzureFunction アクティビティの JSON 定義に、"functionAppUrl" という名前のプロパティがあることを確認してください。


### <a name="error-code--3610"></a>エラー コード:3610

- **メッセージ**: `There was an error while calling endpoint.`

- **原因**:関数の URL が正しくない可能性があります。

- **推奨事項**:アクティビティ JSON の "functionAppUrl" の値が正しいことを確認してから、もう一度やり直してください。


### <a name="error-code--3611"></a>エラー コード:3611

- **メッセージ**: `Azure function activity missing Method in JSON.` 

- **原因**:Azure 関数アクティビティの定義が完全ではありません。

- **推奨事項**:入力 AzureFunction アクティビティの JSON 定義に、"method" という名前のプロパティがあることを確認してください。


### <a name="error-code--3612"></a>エラー コード:3612

- **メッセージ**: `Azure function activity missing LinkedService definition in JSON.`

- **原因**:Azure 関数アクティビティの定義が完全ではない可能性があります。

- **推奨事項**:入力 AzureFunction アクティビティの JSON 定義に、リンクされたサービスの詳細があることを確認してください。


## <a name="azure-machine-learning"></a>Azure Machine Learning


### <a name="error-code--4101"></a>エラー コード:4101

- **メッセージ**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **原因**:プロパティの形式が正しくないか、定義がありません。

- **推奨事項**:正しいデータを使用してアクティビティが定義されているかどうかを確認してください。


### <a name="error-code--4110"></a>エラー コード:4110

- **メッセージ**:JSON の AzureMLExecutePipeline アクティビティに LinkedService の定義がありません。

- **原因**:AzureMLExecutePipeline アクティビティの定義が完全ではありません。

- **推奨事項**:入力 AzureMLExecutePipeline アクティビティの JSON 定義に、リンクされたサービスの詳細があることを確認してください。


### <a name="error-code--4111"></a>エラー コード:4111

- **メッセージ**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **原因**:アクティビティの定義が正しくありません。

- **推奨事項**:入力 AzureMLExecutePipeline アクティビティの JSON 定義に、正しいリンクされたサービスの詳細があるかどうかを確認してください。


### <a name="error-code--4112"></a>エラー コード:4112

- **メッセージ**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **原因**:プロパティの形式が正しくないか、定義がありません。

- **推奨事項**:リンクされたサービスの定義に正しいデータがあるかどうかを確認してください。


### <a name="error-code--4121"></a>エラー コード:4121

- **メッセージ**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **原因**:Azure ML サービスへのアクセスに使用された資格情報の有効期限が切れています。

- **推奨事項**:資格情報が有効であることを確認してから、再試行してください


### <a name="error-code--4122"></a>エラー コード:4122

- **メッセージ**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **原因**:AzureML サービスのリンクされたサービスで指定された資格情報が無効であるか、操作に対するアクセス許可がありません。

- **推奨事項**:リンクされたサービスの資格情報が有効で、AzureML サービスにアクセスするためのアクセス許可があることを確認してください。


### <a name="error-code--4123"></a>エラー コード:4123

- **メッセージ**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **原因**: `Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **推奨事項**:アクティビティのプロパティの値が、リンクされたサービスで指定されている公開済みの Azure ML パイプラインの予想されるペイロードと一致することを確認してください。


### <a name="error-code--4124"></a>エラー コード:4124

- **メッセージ**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **原因**:公開された Azure ML パイプライン エンドポイントが存在しません。

- **推奨事項**:リンクされたサービスで指定された公開済みの Azure ML パイプライン エンドポイントが Azure ML サービスに存在することを確認してください。


### <a name="error-code--4125"></a>エラー コード:4125

- **メッセージ**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **原因**:Azure ML サービスでサーバー エラーが発生しています。

- **推奨事項**:後で再試行してください。 問題が解決しない場合は、Azure ML サービス チームにお問い合わせください。


### <a name="error-code--4126"></a>エラー コード:4126

- **メッセージ**: `AzureML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in AzureMLService for more error loggings.`

- **原因**:AzureML パイプラインを実行できませんでした。

- **推奨事項**:AzureMLService でエラー ログの詳細を確認し、ML パイプラインを修正してください


## <a name="custom"></a>カスタム

次の表は、Azure Batch に当てはまります。


### <a name="error-code--2500"></a>エラー コード:2500

- **メッセージ**: `Hit unexpected exception and execution failed.`

- **原因**: `Can't launch command, or the program returned an error code.`

- **推奨事項**:実行可能ファイルが存在することを確認してください。 プログラムが起動している場合は、*stdout.txt* と *stderr.txt* がストレージ アカウントにアップロードされていることを確認します。 コードでは、デバッグ用に詳しいログを出力することをお勧めします。


### <a name="error-code--2501"></a>エラー コード:2501

- **メッセージ**: `Cannot access user batch account; please check batch account settings.`

- **原因**:Batch アクセス キーまたはプール名が正しくありません。

- **推奨事項**:リンクされたサービスでプール名と Batch アクセス キーを確認してください。


### <a name="error-code--2502"></a>エラー コード:2502

- **メッセージ**: `Cannot access user storage account; please check storage account settings.`

- **原因**:ストレージ アカウント名またはアクセス キーが正しくありません。

- **推奨事項**:リンクされたサービスでストレージ アカウント名とアクセス キーを確認してください。


### <a name="error-code--2504"></a>エラー コード:2504

- **メッセージ**: `Operation returned an invalid status code 'BadRequest'.` 

- **原因**:カスタム アクティビティの folderPath に含まれるファイルが多すぎます。 resourceFiles の合計サイズは 32,768 文字を超えないようにします。

- **推奨事項**:不要なファイルを削除してください。 または、それらを zip 圧縮したうえで、展開するための解凍コマンドを追加してください。 たとえば、`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe` を使用します。


### <a name="error-code--2505"></a>エラー コード:2505

- **メッセージ**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **原因**:カスタム アクティビティでは、アクセス キーを使用するストレージ アカウントのみがサポートされます。

- **推奨事項**:エラーの説明を参照してください。


### <a name="error-code--2507"></a>エラー コード:2507

- **メッセージ**: `The folder path does not exist or is empty: ....`

- **原因**:指定されたパスのストレージ アカウントにファイルがありません。

- **推奨事項**:このフォルダー パスには、実行したい実行可能ファイルが含まれている必要があります。


### <a name="error-code--2508"></a>エラー コード:2508

- **メッセージ**: `There are duplicate files in the resource folder.`

- **原因**:folderPath の異なるサブフォルダーに、同じ名前のファイルが複数あります。

- **推奨事項**:カスタム アクティビティでは、folderPath の下でフォルダー構造がフラット化されます。  フォルダー構造を維持する必要がある場合は、ファイルを zip 圧縮し、Azure Batch で解凍コマンドを使用してそれらを展開します。 たとえば、`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe` を使用します。


### <a name="error-code--2509"></a>エラー コード:2509

- **メッセージ**: `Batch   url ... is invalid; it must be in Uri format.` 

- **原因**:Batch URL は次のようになります: `https://mybatchaccount.eastus.batch.azure.com`

- **推奨事項**:エラーの説明を参照してください。


### <a name="error-code--2510"></a>エラー コード:2510

- **メッセージ**: `An   error occurred while sending the request.`

- **原因**:Batch URL が無効です。 

- **推奨事項**:Batch URL を確認してください。


## <a name="hdinsight"></a>HDInsight

次の表は、Spark、Hive、MapReduce、Pig、Hadoop Streaming に当てはまります。


### <a name="error-code--2300"></a>エラー コード:2300

- **メッセージ**: `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **原因**:指定されたクラスター URI が無効です。 

- **推奨事項**:クラスターが削除されていないことと指定した URI が正しいことを確認してください。 ブラウザーでこの URI を開くと、Ambari UI が表示されます。 クラスターが仮想ネットワークに存在する場合、この URI はプライベート URI になります。 それを開くには、同じ仮想ネットワークに属している VM を使用します。 詳細については、「[Apache Hadoop サービスへの直接接続](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)」を参照してください。

<br/>

- **メッセージ**: `Hadoop job submission failed. Job: …, Cluster: …/. Error: A task was canceled.`

- **原因**:ジョブの送信がタイムアウトしました。 

- **推奨事項**:一般的な HDInsight の接続またはネットワーク接続の問題が考えられます。 まず、HDInsight Ambari UI がどのブラウザーからも使用可能であることを確認します。 ご自分の資格情報がまだ有効であることを確認してください。 セルフホステッド統合ランタイム (IR) を使用している場合は、必ずセルフホステッド IR がインストールされている VM またはマシンからこれを実行してください。 その後、Data Factory から再びジョブの送信を試みてください。 それでも失敗する場合は、Data Factory チームに連絡してサポートを依頼してください。


- **メッセージ**: `Unauthorized: Ambari user name or password is incorrect  <br/><br/>Unauthorized: User admin is locked out in Ambari.   <br/><br/>403 - Forbidden: Access is denied.`

- **原因**:HDInsight の資格情報が正しくないか、有効期限が切れています。

- **推奨事項**:資格情報を訂正して、リンクされたサービスを再デプロイしてください。 任意のブラウザーでクラスター URI を開いてサインインを試行することで、まず HDInsight で資格情報が機能することを確認します。 資格情報が機能しない場合は、Azure portal からリセットできます。

<br/>

- **メッセージ**: `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **原因**:HDInsight からのエラーです。

- **推奨事項**:このエラーは HDInsight クラスターからのものです。 詳細については、「[Ambari UI 502 エラー](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)」、[Spark Thrift サーバーへの接続時の 502 エラー](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)に関するページ、[Spark Thrift サーバーへの接続時の 502 エラー](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)に関するページ、「[Application Gateway での無効なゲートウェイによるエラーのトラブルシューティング](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502)」を参照してください。

<br/>

- **メッセージ**: `Hadoop job submission failed. Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. <br/><br/>Hadoop job submission failed. Job: xx, Cluster: name.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application:   application_1561147195099_3730\`

- **原因**:HDInsight に同時に送信されているジョブの数が多すぎます。

- **推奨事項**:HDInsight に送信されているコンカレント ジョブの数を制限することを検討してください。 ジョブが同じアクティビティによって送信されている場合は、Data Factory アクティビティのコンカレンシーを参照してください。 同時実行のパイプラインが時間を分散して実行されるようにトリガーを変更してください。 HDInsight のドキュメントを参照し、エラーの提案に従って `templeton.parallellism.job.submit` を調整してください。


### <a name="error-code--2303"></a>エラー コード:2303

- **メッセージ**: `Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **原因**:ジョブが HDInsight に送信され、HDInsight で失敗しました。

- **推奨事項**:ジョブは HDInsight に正常に送信されました。 クラスターで失敗しました。 HDInsight Ambari UI でジョブとログを開くか、エラー メッセージに示されているファイルをストレージから開いてください。 そのファイルにエラーの詳細が表示されます。


### <a name="error-code--2310"></a>エラー コード:2310

- **メッセージ**: `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **原因**:指定されたクラスター URI が無効です。 

- **推奨事項**:クラスターが削除されていないことと指定した URI が正しいことを確認してください。 ブラウザーでこの URI を開くと、Ambari UI が表示されます。 クラスターが仮想ネットワークに存在する場合、この URI はプライベート URI になります。 それを開くには、同じ仮想ネットワークに属している VM を使用します。 詳細については、「[Apache Hadoop サービスへの直接接続](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)」を参照してください。

<br/>

- **メッセージ**: `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **原因**:HDInsight からのエラーです。

- **推奨事項**:このエラーは HDInsight クラスターからのものです。 詳細については、「[Ambari UI 502 エラー](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)」、[Spark Thrift サーバーへの接続時の 502 エラー](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)に関するページ、[Spark Thrift サーバーへの接続時の 502 エラー](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)に関するページ、「[Application Gateway での無効なゲートウェイによるエラーのトラブルシューティング](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502)」を参照してください。

<br/>

- **メッセージ**: `java.lang.NullPointerException`

- **原因**:このエラーは、Spark クラスターにジョブが送信されたときに発生します。 

- **推奨事項**:この例外は HDInsight から発生します。 実際の問題が隠されています。 HDInsight チームに連絡してサポートを依頼してください。 クラスター名とアクティビティの実行時間の範囲を伝えてください。


### <a name="error-code--2347"></a>エラー コード:2347

- **メッセージ**: `Hadoop job failed with exit code '5'. See 'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **原因**:ジョブが HDInsight に送信され、HDInsight で失敗しました。

- **推奨事項**:ジョブは HDInsight に正常に送信されました。 クラスターで失敗しました。 HDInsight Ambari UI でジョブとログを開くか、エラー メッセージに示されているファイルをストレージから開いてください。 そのファイルにエラーの詳細が表示されます。


### <a name="error-code--2328"></a>エラー コード:2328

- **メッセージ**: `Internal server error occurred while processing the request. Please retry the request or contact support. `

- **原因**:このエラーは、HDInsight のオンデマンドで発生します。

- **推奨事項**:このエラーは、HDInsight のプロビジョニングが失敗したときに HDInsight サービスによって生成されます。 HDInsight チームに連絡して、オンデマンド クラスターの名前を伝えてください。



## <a name="web-activity"></a>Web アクティビティ

### <a name="error-code--2108"></a>エラー コード:2108

- **メッセージ**: `Invalid HttpMethod: '...'.`

- **原因**:アクティビティのペイロードで指定された HTTP メソッドが Web アクティビティでサポートされていません。

- **推奨事項**:サポート対象の HTTP メソッドは PUT、POST、GET、DELETE です。

<br/>

- **メッセージ**: `Invalid Server Error 500.`

- **原因**:エンドポイントの内部エラー。

- **推奨事項**:該当 URL に対する機能を Fiddler または Postman を使用してチェックしてください。

<br/>

- **メッセージ**: `Unauthorized 401.`

- **原因**:有効な認証が要求にありません。

- **推奨事項**:トークンの有効期限が切れている可能性があります。 有効な認証方法を指定してください。 該当 URL に対する機能を Fiddler または Postman を使用してチェックしてください。

<br/>

- **メッセージ**: `Forbidden 403.`

- **原因**:必要なアクセス許可がありません。

- **推奨事項**:アクセスしたリソースでユーザーのアクセス許可を確認してください。 該当 URL に対する機能を Fiddler または Postman を使用してチェックしてください。

<br/>

- **メッセージ**: `Bad Request 400.`

- **原因**:HTTP 要求が無効です。

- **推奨事項**: 要求の URL、動詞、本文を確認してください。 Fiddler または Postman を使って要求を確認してください。

<br/>

- **メッセージ**: `Not found 404.` 

- **原因**:リソースが見つかりませんでした。   

- **推奨事項**:Fiddler または Postman を使って要求を確認してください。

<br/>

- **メッセージ**: `Service unavailable.`

- **原因**:サービスを利用できません。

- **推奨事項**:Fiddler または Postman を使って要求を確認してください。

<br/>

- **メッセージ**: `Unsupported Media Type.`

- **原因**:コンテンツ タイプと Web アクティビティの本文が一致していません。

- **推奨事項**:ペイロードの形式と一致するコンテンツ タイプを指定してください。 Fiddler または Postman を使って要求を確認してください。

<br/>

- **メッセージ**: `The resource you are looking for has been removed, has had its name changed, or is temporarily unavailable.`

- **原因**:このリソースは利用できません。 

- **推奨事項**:Fiddler または Postman を使ってエンドポイントを確認してください。

<br/>

- **メッセージ**: `The page you are looking for cannot be displayed because an invalid method (HTTP verb) is being used.`

- **原因**:誤った Web アクティビティ メソッドが要求に指定されました。

- **推奨事項**:Fiddler または Postman を使ってエンドポイントを確認してください。

<br/>

- **メッセージ**: `invalid_payload`

- **原因**:Web アクティビティの本文が正しくありません。

- **推奨事項**:Fiddler または Postman を使ってエンドポイントを確認してください。


### <a name="error-code--2208"></a>エラー コード:2208

- **メッセージ**: `Invoking Web Activity failed with HttpStatusCode - {0}.`

- **原因**:ターゲット サービスからエラー状態が返されました。

- **推奨事項**:Fiddler/Postman を使用して要求を検証してください。


### <a name="error-code--2308"></a>エラー コード:2308

- **メッセージ**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **原因**:ネットワーク接続、DNS エラー、サーバー証明書の検証、タイムアウトなど、このエラーには複数の理由が考えられます。

- **推奨事項**:Fiddler/Postman を使用して要求を検証してください。


Fiddler を使用して監視対象 Web アプリケーションの HTTP セッションを作成するには:

1. [Fiddler](https://www.telerik.com/download/fiddler) をダウンロードし、インストールして開きます。

1. Web アプリケーションで HTTPS が使用されている場合は、 **[Tools]\(ツール\)**  >  **[Fiddler Options]\(Fiddler オプション\)**  >  **[HTTPS]** の順に選択します。 **[Capture HTTPS CONNECTs]\(HTTPS 接続をキャプチャする\)** と **[Decrypt HTTPS traffic]\(HTTPS トラフィックの暗号化を解除する\)** をオンにします。 
   
   ![Fiddler オプション](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. お使いのアプリケーションで SSL 証明書が使用されている場合は、Fiddler の証明書をデバイスに追加します。 **[Tools]\(ツール\)**  >  **[Fiddler Options]\(Fiddler オプション\)**  >  **[HTTPS]**  >  **[Actions]\(アクション\)**  >  **[Export Root Certificate to Desktop]\(ルート証明書をデスクトップにエクスポート\)** に移動します。

1. **[File]\(ファイル\)**  >  **[Capture Traffic]\(トラフィックのキャプチャ\)** に移動して、キャプチャをオフにします。 または **F12** キーを押します。

1. キャッシュされたすべての項目が削除されて、再ダウンロードが必要になるように、ブラウザーのキャッシュをクリアします。

1. 要求を作成します。 

   a. **[Composer]\(コンポーザー\)** タブを選択します。

   b. HTTP メソッドと URL を設定します。

   c. 必要に応じてヘッダーと要求本文を追加します。

   d. **[実行]** を選択します。

9. トラフィックのキャプチャを再び有効にし、ページで問題のあるトランザクションを完了します。

10. **[File]\(ファイル\)**  >  **[Save]\(保存\)**  >  **[All Sessions]\(すべてのセッション\)** に移動します。

詳細については、「[Fiddler の基本](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)」を参照してください。

## <a name="next-steps"></a>次の手順

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)




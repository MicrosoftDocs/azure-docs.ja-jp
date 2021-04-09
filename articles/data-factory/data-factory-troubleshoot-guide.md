---
title: Azure Data Factory のトラブルシューティング | Microsoft Docs
description: Azure Data Factory における外部の制御アクティビティのトラブルシューティング方法について説明します。
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/30/2020
ms.author: abnarain
ms.openlocfilehash: 101e55188b8021040e2fd6bd573c1c6330241e72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382805"
---
# <a name="troubleshoot-azure-data-factory"></a>Azure Data Factory のトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory における外部の制御アクティビティの一般的なトラブルシューティング方法について説明します。

## <a name="connector-and-copy-activity"></a>コネクタとコピー アクティビティ

コピー アクティビティの使用時にエラーが発生したなど、コネクタに関する問題については、「[Azure Data Factory コネクタのトラブルシューティング](connector-troubleshoot-guide.md)」を参照してください。

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code-3200"></a>エラー コード:3200

- **メッセージ**:エラー 403。

- **原因**: `The Databricks access token has expired.`

- **推奨事項**:既定では、Azure Databricks のアクセス トークンは 90 日間有効です。 新しいトークンを作成し、リンクされたサービスを更新してください。

### <a name="error-code-3201"></a>エラー コード:3201

- **メッセージ**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **原因**: `Bad authoring: Notebook path not specified correctly.`

- **推奨事項**:Databricks アクティビティに Notebook のパスを指定してください。

<br/> 

- **メッセージ**: `Cluster... does not exist.`

- **原因**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **推奨事項**:Databricks クラスターが存在することを確認してください。

<br/> 

- **メッセージ**: `Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **原因**: `Bad authoring.`

- **推奨事項**:ワークスペース アドレス指定スキームの絶対パス (Databricks ファイル システム (DFS) に格納されているファイルの場合は `dbfs:/folder/subfolder/foo.py`) を指定します。

<br/> 

- **メッセージ**: `{0} LinkedService should have domain and accessToken as required properties.`

- **原因**: `Bad authoring.`

- **推奨事項**:[リンクされたサービスの定義](compute-linked-services.md#azure-databricks-linked-service)を確認してください。

<br/> 

- **メッセージ**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **原因**: `Bad authoring.`

- **推奨事項**:[リンクされたサービスの定義](compute-linked-services.md#azure-databricks-linked-service)を確認してください。

<br/> 

- **メッセージ**: `Node type Standard_D16S_v3 is not supported. Supported node types: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2.`

- **原因**: `Bad authoring.`

- **推奨事項**:エラー メッセージを参照してください。

<br/>

### <a name="error-code-3202"></a>エラー コード:3202

- **メッセージ**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit: 1000 job creations per 3600 seconds.`

- **原因**: `Too many Databricks runs in an hour.`

- **推奨事項**:この Databricks ワークスペースを使用しているすべてのパイプラインでジョブの作成速度を確認します。 パイプラインで起動された Databricks の実行数が全体として多すぎる場合は、一部のパイプラインを新しいワークスペースに移行してください。

<br/> 

- **メッセージ**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **原因**: `Authoring error: No value provided for the parameter.`

- **推奨事項**:パイプラインの JSON を検査して、baseParameters ノートブックのすべてのパラメーターに空以外の値が指定されていることを確認してください。

<br/> 

- **メッセージ**:`User: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` is not authorized to access cluster.`

- **原因**:アクセス トークンを生成したユーザーは、リンクされたサービスに指定されている Databricks クラスターへのアクセスを許可されていません。

- **推奨事項**:ワークスペースで必要なアクセス許可がユーザーにあることを確認します。

### <a name="error-code-3203"></a>エラー コード:3203

- **メッセージ**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **原因**:クラスターは終了されました。 対話型クラスターの場合、この問題は競合状態である可能性があります。

- **推奨事項**:このエラーを回避するには、ジョブ クラスターを使用します。

### <a name="error-code-3204"></a>エラー コード:3204

- **メッセージ**: `Job execution failed.`

- **原因**:エラー メッセージは、予期しないクラスター状態や特定のアクティビティなど、さまざまな問題を示します。 多くの場合、エラー メッセージは表示されません。

- **推奨事項**:該当なし

### <a name="error-code-3208"></a>エラー コード:3208

- **メッセージ**: `An error occurred while sending the request.`

- **原因**:Databricks サービスへのネットワーク接続が中断されました。

- **推奨事項**:セルフホステッド統合ランタイムを使用している場合は、統合ランタイム ノードからのネットワーク接続が信頼できることを確認してください。 Azure 統合ランタイムを使用している場合、通常は再試行すると機能します。
 
## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

次の表は、U-SQL について記載したものです。
 
### <a name="error-code-2709"></a>エラー コード:2709

- **メッセージ**: `The access token is from the wrong tenant.`

- **原因**:Azure Active Directory (Azure AD) テナントが正しくありません。

- **推奨事項**:Azure Active Directory (Azure AD) テナントが正しくありません。

<br/>

- **メッセージ**: `We cannot accept your job at this moment. The maximum number of queued jobs for your account is 200. `

- **原因**:このエラーは、Data Lake Analytics のスロットリングに起因しています。

- **推奨事項**:Data Lake Analytics に送信されるジョブの数を減らします。 アクティビティに対する Data Factory のトリガーとコンカレンシーの設定を変更するか、Data Lake Analytics の上限を引き上げます。

<br/> 

- **メッセージ**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **原因**:このエラーは、Data Lake Analytics のスロットリングに起因しています。

- **推奨事項**:Data Lake Analytics に送信されるジョブの数を減らします。 アクティビティに対する Data Factory のトリガーとコンカレンシーの設定を変更するか、Data Lake Analytics の上限を引き上げます。

### <a name="error-code-2705"></a>エラー コード:2705

- **メッセージ**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **原因**:サービス プリンシパルまたは証明書には、ストレージ内のファイルへのアクセス権がありません。

- **推奨事項**:Data Lake Analytics ジョブ用にユーザーが指定したサービス プリンシパルまたは証明書が、Data Lake Analytics アカウントと、ルート フォルダーからの既定の Data Lake Storage インスタンスの両方にアクセスできることを確認します。

### <a name="error-code-2711"></a>エラー コード:2711

- **メッセージ**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **原因**:サービス プリンシパルまたは証明書には、ストレージ内のファイルへのアクセス権がありません。

- **推奨事項**:Data Lake Analytics ジョブ用にユーザーが指定したサービス プリンシパルまたは証明書が、Data Lake Analytics アカウントと、ルート フォルダーからの既定の Data Lake Storage インスタンスの両方にアクセスできることを確認します。

<br/> 

- **メッセージ**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **原因**:U-SQL ファイルのパスが間違っているか、リンクされたサービスの資格情報がアクセス権を持っていません。

- **推奨事項**:パスと、リンクされたサービスに指定された資格情報を確認してください。

### <a name="error-code-2704"></a>エラー コード:2704

- **メッセージ**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **原因**:サービス プリンシパルまたは証明書には、ストレージ内のファイルへのアクセス権がありません。

- **推奨事項**:Data Lake Analytics ジョブ用にユーザーが指定したサービス プリンシパルまたは証明書が、Data Lake Analytics アカウントと、ルート フォルダーからの既定の Data Lake Storage インスタンスの両方にアクセスできることを確認します。

### <a name="error-code-2707"></a>エラー コード:2707

- **メッセージ**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and 'DataLakeAnalyticsUri'.`

- **原因**:リンクされたサービスの Data Lake Analytics アカウントに誤りがあります。

- **推奨事項**:正しいアカウントが指定されていることを確認してください。

### <a name="error-code-2703"></a>エラー コード:2703

- **メッセージ**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error Id:").`

- **原因**:Data Lake Analytics からのエラーです。

- **推奨事項**:Data Lake Analytics にジョブが送信され、そこでスクリプトとジョブの両方が失敗しました。 Data Lake Analytics 内を調査してください。 ポータルから Data Lake Analytics アカウントに移動し、Data Factory のアクティビティの実行 ID を使用して該当するジョブを探します (パイプラインの実行 ID は使用しません)。 見つかったジョブから、エラーに関する詳細な情報を入手し、トラブルシューティングに役立てることができます。

   解決方法が明確でない場合は、Data Lake Analytics サポート チームに連絡し、お使いのアカウント名とジョブ ID を含むジョブの Universal Resource Locator (URL) を伝えてください。
 
## <a name="azure-functions"></a>Azure 関数

### <a name="error-code-3602"></a>エラー コード:3602

- **メッセージ**: `Invalid HttpMethod: '%method;'.`

- **原因**:アクティビティのペイロードに指定された Httpmethod が Azure 関数アクティビティでサポートされていません。

- **推奨事項**:サポートされている Httpmethod は次のとおりです: PUT、POST、GET、DELETE、OPTIONS、HEAD、および TRACE。

### <a name="error-code-3603"></a>エラー コード:3603

- **メッセージ**: `Response Content is not a valid JObject.`

- **原因**:呼び出した Azure 関数の応答で JSON ペイロードが返されませんでした。 Azure Data Factory (ADF) Azure 関数アクティビティでは、JSON 応答コンテンツのみがサポートされます。

- **推奨事項**:有効な JSON ペイロードを返すように Azure 関数を更新します。たとえば、C# 関数は `(ActionResult)new OkObjectResult("{\"Id\":\"123\"}");` を返すことができます

### <a name="error-code-3606"></a>エラー コード:3606

- **メッセージ**:Azure 関数アクティビティに関数キーがありません。

- **原因**:Azure 関数アクティビティの定義が完全ではありません。

- **推奨事項**:入力 Azure 関数アクティビティの JSON 定義に、`functionKey` という名前のプロパティがあることを確認します。

### <a name="error-code-3607"></a>エラー コード:3607

- **メッセージ**: `Azure function activity missing function name.`

- **原因**:Azure 関数アクティビティの定義が完全ではありません。

- **推奨事項**:入力 Azure 関数アクティビティの JSON 定義に、`functionName` という名前のプロパティがあることを確認します。

### <a name="error-code-3608"></a>エラー コード:3608

- **メッセージ**: `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **原因**:アクティビティ定義における Azure 関数の詳細が正しくない可能性があります。

- **推奨事項**:Azure 関数の詳細を修正してから、再試行します。

### <a name="error-code-3609"></a>エラー コード:3609

- **メッセージ**: `Azure function activity missing functionAppUrl.`

- **原因**:Azure 関数アクティビティの定義が完全ではありません。

- **推奨事項**:入力 Azure 関数アクティビティの JSON 定義に、`functionAppUrl` という名前のプロパティがあることを確認します。

### <a name="error-code-3610"></a>エラー コード:3610

- **メッセージ**: `There was an error while calling endpoint.`

- **原因**:関数の URL が正しくない可能性があります。

- **推奨事項**:アクティビティ JSON の `functionAppUrl` の値が正しいことを確認してから、再試行します。

### <a name="error-code-3611"></a>エラー コード:3611

- **メッセージ**: `Azure function activity missing Method in JSON.`

- **原因**:Azure 関数アクティビティの定義が完全ではありません。

- **推奨事項**:入力 Azure 関数アクティビティの JSON 定義に、`method` という名前のプロパティがあることを確認します。

### <a name="error-code-3612"></a>エラー コード:3612

- **メッセージ**: `Azure function activity missing LinkedService definition in JSON.`

- **原因**:Azure 関数アクティビティの定義が完全ではありません。

- **推奨事項**:入力 Azure 関数アクティビティの JSON 定義に、リンクされたサービスの詳細があることを確認します。

## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code-4101"></a>エラー コード:4101

- **メッセージ**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **原因**:プロパティ `%propertyName;` の形式が正しくないか、定義がありません。

- **推奨事項**:アクティビティ `%activityName;` に適切なデータが定義されたプロパティ `%propertyName;` があるかどうかを確認します。

### <a name="error-code-4110"></a>エラー コード:4110

- **メッセージ**: `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **原因**:AzureMLExecutePipeline アクティビティの定義が完全ではありません。

- **推奨事項**:入力 AzureMLExecutePipeline アクティビティの JSON 定義に、正しくリンクされたサービスの詳細があることを確認します。

### <a name="error-code-4111"></a>エラー コード:4111

- **メッセージ**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **原因**:アクティビティの定義が正しくありません。

- **推奨事項**:入力 AzureMLExecutePipeline アクティビティの JSON 定義に、正しくリンクされたサービスの詳細があることを確認します。

### <a name="error-code-4112"></a>エラー コード:4112

- **メッセージ**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **原因**:プロパティ '%propertyName;' の形式が正しくないか、定義がありません。

- **推奨事項**:リンクされたサービスに適切なデータが定義されたプロパティ `%propertyName;` があるかどうかを確認します。

### <a name="error-code-4121"></a>エラー コード:4121

- **メッセージ**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**:Azure Machine Learning へのアクセスに使用された資格情報の有効期限が切れています。

- **推奨事項**:資格情報が有効であることを確認してから、再試行します。

### <a name="error-code-4122"></a>エラー コード:4122

- **メッセージ**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**:Azure Machine Learning のリンクされたサービスで指定された資格情報が無効であるか、操作に対するアクセス許可がありません。

- **推奨事項**:リンクされたサービスの資格情報が有効で、Azure Machine Learning にアクセスするためのアクセス許可があることを確認します。

### <a name="error-code-4123"></a>エラー コード:4123

- **メッセージ**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**:Azure Machine Learning (ML) パイプラインには、`pipelineParameters` などのアクティビティのプロパティは無効です。

- **推奨事項**:アクティビティのプロパティの値が、リンクされたサービスで指定されている公開済みの Azure ML パイプラインの予想されるペイロードと一致することを確認します。

### <a name="error-code-4124"></a>エラー コード:4124

- **メッセージ**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**:公開された Azure ML パイプライン エンドポイントが存在しません。

- **推奨事項**:リンクされたサービスで指定された公開済みの Azure Machine Learning パイプライン エンドポイントが、Azure Machine Learning に存在することを確認します。

### <a name="error-code-4125"></a>エラー コード:4125

- **メッセージ**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**:Azure Machine Learning でサーバー エラーが発生しています。

- **推奨事項**:後で再試行してください。 問題が解決しない場合は、Azure Machine Learning チームにお問い合わせください。

### <a name="error-code-4126"></a>エラー コード:4126

- **メッセージ**: `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **原因**:Azure ML パイプラインを実行できませんでした。

- **推奨事項**:Azure Machine Learning でその他のエラー ログを確認し、ML パイプラインを修正します。

## <a name="common"></a>共通

### <a name="error-code-2103"></a>エラー コード:2103

- **メッセージ**: `Please provide value for the required property '%propertyName;'.`

- **原因**:プロパティに必要な値が指定されていません。

- **推奨事項**:メッセージの値を指定してから、再試行してください。

### <a name="error-code-2104"></a>エラー コード:2104

- **メッセージ**: `The type of the property '%propertyName;' is incorrect.`

- **原因**:指定されたプロパティの型が正しくありません。

- **推奨事項**:プロパティの型を修正してから、再試行します。

### <a name="error-code-2105"></a>エラー コード:2105

- **メッセージ**: `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **原因**:プロパティの値が無効であるか、正しい形式ではありません。

- **推奨事項**:プロパティのドキュメントを参照して、指定された値の形式と型が正しいことを確認します。

### <a name="error-code-2106"></a>エラー コード:2106

- **メッセージ**: `The storage connection string is invalid. %errorMessage;`

- **原因**:ストレージの接続文字列が無効であるか、その形式が間違っています。

- **推奨事項**:Azure portal に進み、自分のストレージを見つけ、接続文字列をコピーしてリンクされたサービスに貼り付けてから再試行します。

### <a name="error-code-2108"></a>エラー コード:2108

- **メッセージ**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **原因**:ネットワーク接続、DNS エラー、サーバー証明書の検証、タイムアウトなどの根本的な問題が原因で要求が失敗しました。

- **推奨事項**:Fiddler/Postman を使用して要求を検証してください。

### <a name="error-code-2110"></a>エラー コード:2110

- **メッセージ**: `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **原因**:アクティビティに指定されているリンクされたサービスが正しくありません。

- **推奨事項**:リンクされたサービスの種類がアクティビティでサポートされているいずれかの種類であることを確認します。 たとえば、HDI アクティビティの場合、リンクされたサービスの種類は HDInsight または HDInsightOnDemand になります。

### <a name="error-code-2111"></a>エラー コード:2111

- **メッセージ**: `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **原因**:指定されたプロパティの型が正しくありません。

- **推奨事項**:プロパティの型を修正してから、再試行します。

### <a name="error-code-2112"></a>エラー コード:2112

- **メッセージ**: `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **原因**:クラウドの種類がサポートされていないか、EndpointSuffix からストレージ用のものを決定できませんでした。

- **推奨事項**:別のクラウドのストレージを使用し、再試行します。

### <a name="error-code-2128"></a>エラー コード:2128

- **メッセージ**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **原因**:ネットワーク接続、DNS エラー、サーバー証明書の検証、またはタイムアウト。

- **推奨事項**:要求を試しているエンドポイントが要求に応答していることを確認します。 Fiddler/Postman などのツールを使用できます。

## <a name="custom"></a>Custom

次の表は、Azure Batch に当てはまります。
 
### <a name="error-code-2500"></a>エラー コード:2500

- **メッセージ**: `Hit unexpected exception and execution failed.`

- **原因**: `Can't launch command, or the program returned an error code.`

- **推奨事項**:実行可能ファイルが存在することを確認してください。 プログラムが起動している場合は、*stdout.txt* と *stderr.txt* がストレージ アカウントにアップロードされていることを確認します。 デバッグのためにコードにログを含めることをお勧めします。

### <a name="error-code-2501"></a>エラー コード:2501

- **メッセージ**: `Cannot access user batch account; please check batch account settings.`

- **原因**:Batch アクセス キーまたはプール名が正しくありません。

- **推奨事項**:リンクされたサービスでプール名と Batch アクセス キーを確認してください。

### <a name="error-code-2502"></a>エラー コード:2502

- **メッセージ**: `Cannot access user storage account; please check storage account settings.`

- **原因**:ストレージ アカウント名またはアクセス キーが正しくありません。

- **推奨事項**:リンクされたサービスでストレージ アカウント名とアクセス キーを確認してください。

### <a name="error-code-2504"></a>エラー コード:2504

- **メッセージ**: `Operation returned an invalid status code 'BadRequest'.`

- **原因**:カスタム アクティビティの `folderPath` 内のファイルが多すぎます。 `resourceFiles` の合計サイズが 32,768 文字を超えることはできません。

- **推奨事項**:不要なファイルを削除するか、それらを zip 圧縮したうえで、展開するための解凍コマンドを追加します。
   
   たとえば、`powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe` を使用します

### <a name="error-code-2505"></a>エラー コード:2505

- **メッセージ**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **原因**:カスタム アクティビティでは、アクセス キーを使用するストレージ アカウントのみがサポートされます。

- **推奨事項**:エラーの説明を参照してください。

### <a name="error-code-2507"></a>エラー コード:2507

- **メッセージ**: `The folder path does not exist or is empty: ...`

- **原因**:指定されたパスのストレージ アカウントにファイルがありません。

- **推奨事項**:このフォルダー パスには、実行したい実行可能ファイルが含まれている必要があります。

### <a name="error-code-2508"></a>エラー コード:2508

- **メッセージ**: `There are duplicate files in the resource folder.`

- **原因**:folderPath の異なるサブフォルダーに、同じ名前のファイルが複数あります。

- **推奨事項**:カスタム アクティビティでは、folderPath の下でフォルダー構造がフラット化されます。 フォルダー構造を維持する必要がある場合は、ファイルを zip 圧縮し、Azure Batch で解凍コマンドを使用してそれらを展開します。
   
   たとえば、`powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe` を使用します

### <a name="error-code-2509"></a>エラー コード:2509

- **メッセージ**: `Batch url ... is invalid; it must be in Uri format.`

- **原因**:Batch URL は次のようになります: `https://mybatchaccount.eastus.batch.azure.com`

- **推奨事項**:エラーの説明を参照してください。

### <a name="error-code-2510"></a>エラー コード:2510

- **メッセージ**: `An error occurred while sending the request.`

- **原因**:Batch URL が無効です。

- **推奨事項**:Batch URL を確認してください。
 
## <a name="hdinsight"></a>HDInsight

### <a name="error-code-206"></a>エラー コード:206

- **メッセージ**: `The batch ID for Spark job is invalid. Please retry your job.`

- **原因**:このエラーの原因となったサービスに内部的な問題がありました。

- **推奨事項**:この問題は一時的なものである可能性があります。 しばらくしてから、ジョブを再試行してください。

### <a name="error-code-207"></a>エラー コード:207

- **メッセージ**: `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI.`

- **原因**:プライマリ ストレージ アカウントからリージョンを特定しているときに内部エラーが発生しました。

- **推奨事項**:別のストレージを試してください。 

### <a name="error-code-208"></a>エラー コード:208

- **メッセージ**: `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again.`

- **原因**:サービス プリンシパルを読み取ろうとしたときか MSI 認証をインスタンス化しようとしたとき、内部エラーが発生しました。

- **推奨事項**:指定のサブスクリプションで HDInsight クラスターを作成するアクセス許可を持っているサービス プリンシパルの指定を検討し、再試行します。 [管理 ID が正しく設定されていること](../hdinsight/hdinsight-managed-identities.md)を確認します。

### <a name="error-code-2300"></a>エラー コード:2300

- **メッセージ**: `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **原因**:エラー メッセージに `The remote name could not be resolved.` のようなメッセージが含まれています。 指定されたクラスター URI が無効である可能性があります。

- **推奨事項**:クラスターが削除されていないこと、および指定した URI が正しいことを確認します。 ブラウザーでこの URI を開くと、Ambari UI が表示されます。 クラスターが仮想ネットワークに存在する場合、この URI はプライベート URI になります。 それを開くには、同じ仮想ネットワークに属している仮想マシン (VM) を使用します。

   詳細については、「[Apache Hadoop サービスへの直接接続](../hdinsight/hdinsight-plan-virtual-network-deployment.md#directly-connect-to-apache-hadoop-services)」を参照してください。
 
 </br>

- **原因**:エラー メッセージに `A task was canceled.` のようなメッセージが含まれている場合は、ジョブの送信がタイムアウトしました。

- **推奨事項**:一般的な HDInsight の接続またはネットワーク接続の問題が考えられます。 まず、HDInsight Ambari UI がどのブラウザーからも使用可能であることを確認します。 次に、ご自分の資格情報がまだ有効であることを確認してください。
   
   セルフホステッド統合ランタイム (IR) を使用している場合は、セルフホステッド IR がインストールされている VM またはマシンからこの手順を実行します。 その後、Data Factory から再びジョブの送信を試みてください。

   詳細については、「[Ambari Web UI](../hdinsight/hdinsight-hadoop-manage-ambari.md#ambari-web-ui)」を参照してください。

 </br>

- **原因**:エラー メッセージに `User admin is locked out in Ambari` や `Unauthorized: Ambari user name or password is incorrect` のようなメッセージが含まれている場合は、HDInsight の資格情報が正しくないか、期限切れになっています。

- **推奨事項**:資格情報を訂正して、リンクされたサービスを再デプロイしてください。 任意のブラウザーでクラスター URI を開いてサインインを試行することで、まず HDInsight で資格情報が機能することを確認します。 資格情報が機能しない場合は、Azure portal からリセットできます。

   ESP クラスターでは、[セルフ サービスのパスワード リセット](../active-directory/user-help/active-directory-passwords-update-your-own-password.md)を使用してパスワードをリセットすることができます。

 </br>

- **原因**:エラー メッセージに `502 - Web server received an invalid response while acting as a gateway or proxy server` のようなメッセージが含まれている場合、このエラーは HDInsight サービスによって返されています。

- **推奨事項**:502 エラーは、多くの場合、Ambari サーバー プロセスがシャットダウンされたときに発生します。 ヘッド ノードを再起動することで、Ambari サービスを再起動できます。

    1. SSH を使用して、HDInsight 上のいずれかのノードに接続します。
    1. `ping headnodehost` を実行して、アクティブなヘッド ノード ホストを特定します。
    1. Ambari サーバーはアクティブなヘッド ノード上に配置されるため、SSH を使用してアクティブなヘッド ノードに接続します。 
    1. アクティブなヘッド ノードを再起動します。

       詳細については、Azure HDInsight のトラブルシューティングに関するドキュメントを参照してください。 次に例を示します。

       * [Ambari UI の 502 エラー](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)
       * [Apache Spark Thrift サーバーの RpcTimeoutException](../hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception.md)
       * [Application Gateway での無効なゲートウェイによるエラーのトラブルシューティング](../application-gateway/application-gateway-troubleshooting-502.md)。

 </br>

- **原因**:エラー メッセージに `Unable to service the submit job request as templeton service is busy with too many submit job requests` や `Queue root.joblauncher already has 500 applications, cannot accept submission of application` のようなメッセージが含まれている場合は、同時に HDInsight に送信されているジョブの数が多すぎます。

- **推奨事項**:HDInsight に送信されるコンカレント ジョブの数を制限します。 ジョブが同じアクティビティによって送信されている場合は、Data Factory アクティビティのコンカレンシーを参照してください。 同時実行のパイプラインが時間を分散して実行されるようにトリガーを変更してください。

   [HDInsight のドキュメント](../hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors.md)を参照し、エラーの提案に従って `templeton.parallellism.job.submit` を調整してください。

### <a name="error-code-2301"></a>エラー コード:2301

- **メッセージ**: `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **原因**:HDInsight のクラスターまたはサービスに問題があります。

- **推奨事項**:このエラーは、実行中のジョブの状態を要求しようとしたときに、ADF が HDInsight クラスターから応答を受信しない場合に発生します。 この問題はクラスター自体に存在する可能性があります。または、HDInsight サービスが停止している可能性があります。

   HDInsight のトラブルシューティング ドキュメント (https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide ) を参照するか、サポートに連絡してさらに支援を求めてください。

### <a name="error-code-2302"></a>エラー コード:2302

- **メッセージ**: `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **原因**:ジョブは HDI クラスターに送信されましたが、そこでエラーが発生しました。

- **推奨事項**: 

 1. Ambari UI を確認します。
    1. すべてのサービスがまだ実行されていることを確認します。
    1. Ambari UI で、ダッシュボードのアラート セクションを確認します。
       1. アラートとアラートの解決方法の詳細については、「[クラスターの管理と監視](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)」を参照してください。
    1. YARN メモリを確認します。 YARN メモリの量が多い場合は、ジョブの処理が遅れている可能性があります。 Spark アプリケーション/ジョブに対応できるだけの十分なリソースがない場合は、クラスターに十分なメモリとコアが確保されるようにクラスターをスケールアップしてください。 
 1. サンプル テスト ジョブを実行します。
    1. HDInsight バックエンドで同じジョブを実行する場合は、ジョブが成功したことを確認します。 サンプル実行の例については、「[HDInsight に含まれる MapReduce サンプルを実行する](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md)」を参照してください 
 1. ジョブが引き続き HDInsight で失敗している場合は、アプリケーションのログと情報を確認して、サポートに提供します。
    1. ジョブが YARN に送信されたかどうかを確認します。 ジョブが yarn に送信されていない場合は、`--master yarn` を使用します。
    1. アプリケーションの実行が終了している場合は、YARN アプリケーションの開始時刻と終了時刻を収集します。 アプリケーションの実行が完了しなかった場合は、開始時刻または起動時刻を収集します。
    1. `yarn logs -applicationId <Insert_Your_Application_ID>` を使用して、アプリケーション ログを確認し、収集します。
    1. `/var/log/hadoop-yarn/yarn` ディレクトリの下の yarn Resource Manager ログを確認し、収集します。
    1. これらの手順が問題を解決するのに十分でない場合は、Azure HDInsight チームにサポートを依頼し、上記のログとタイムスタンプを提供してください。

### <a name="error-code-2303"></a>エラー コード:2303

- **メッセージ**: `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **原因**:ジョブは HDI クラスターに送信されましたが、そこでエラーが発生しました。

- **推奨事項**: 

 1. Ambari UI を確認します。
    1. すべてのサービスがまだ実行されていることを確認します。
    1. Ambari UI で、ダッシュボードのアラート セクションを確認します。
       1. アラートとアラートの解決方法の詳細については、「[クラスターの管理と監視](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)」を参照してください。
    1. YARN メモリを確認します。 YARN メモリの量が多い場合は、ジョブの処理が遅れている可能性があります。 Spark アプリケーション/ジョブに対応できるだけの十分なリソースがない場合は、クラスターに十分なメモリとコアが確保されるようにクラスターをスケールアップしてください。 
 1. サンプル テスト ジョブを実行します。
    1. HDInsight バックエンドで同じジョブを実行する場合は、ジョブが成功したことを確認します。 サンプル実行の例については、「[HDInsight に含まれる MapReduce サンプルを実行する](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md)」を参照してください 
 1. ジョブが引き続き HDInsight で失敗している場合は、アプリケーションのログと情報を確認して、サポートに提供します。
    1. ジョブが YARN に送信されたかどうかを確認します。 ジョブが yarn に送信されていない場合は、`--master yarn` を使用します。
    1. アプリケーションの実行が終了している場合は、YARN アプリケーションの開始時刻と終了時刻を収集します。 アプリケーションの実行が完了しなかった場合は、開始時刻または起動時刻を収集します。
    1. `yarn logs -applicationId <Insert_Your_Application_ID>` を使用して、アプリケーション ログを確認し、収集します。
    1. `/var/log/hadoop-yarn/yarn` ディレクトリの下の yarn Resource Manager ログを確認し、収集します。
    1. これらの手順が問題を解決するのに十分でない場合は、Azure HDInsight チームにサポートを依頼し、上記のログとタイムスタンプを提供してください。

### <a name="error-code-2304"></a>エラー コード:2304

- **メッセージ**: `MSI authentication is not supported on storages for HDI activities.`

- **原因**:HDInsight (HDI) のリンクされたサービスまたは HDI のアクティビティで使用されているストレージのリンクされたサービスが、サポートされていない MSI 認証を使用して構成されています。

- **推奨事項**:HDI のリンクされたサービスまたは HDI のアクティビティで使用されているストレージ アカウントの完全接続文字列を指定します。

### <a name="error-code-2305"></a>エラー コード:2305

- **メッセージ**: `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **原因**:HDI クラスターの接続情報が間違っているか、指定されたユーザーに必要なアクションを実行するアクセス許可がないか、HDInsight サービスに問題があり、ADF からの要求に応答できなかったことが原因です。

- **推奨事項**:ユーザー情報が正しいことを確認します。さらに、HDI クラスターの Ambari UI を、IR がインストールされている VM からブラウザーで開くことができること (セルフホステッド IR の場合)、または任意のマシンから開くことができること (Azure IR の場合) を確認します。

### <a name="error-code-2306"></a>エラー コード:2306

- **メッセージ**: `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **原因**:スクリプト アクションに指定された JSON が無効です。

- **推奨事項**:エラー メッセージは、問題の特定に役立ちます。 json 構成を修正し、再試行します。

   詳細については、「[Azure HDInsight のオンデマンドのリンクされたサービス](./compute-linked-services.md#azure-hdinsight-on-demand-linked-service)」を参照してください。

### <a name="error-code-2310"></a>エラー コード:2310

- **メッセージ**: `Failed to submit Spark job. Error: '%message;'`

- **原因**:ADF で、Livy API (livy/batch) を使用して Spark クラスターにバッチを作成しようとしましたが、エラーが表示されました。

- **推奨事項**:エラー メッセージに従い、問題を解決します。 これを解決できる十分な情報がない場合、HDI チームに連絡し、バッチ ID とジョブ ID を伝えてください。ID は ADF 監視ページのアクティビティ実行 Output で見つかります。 さらにトラブルシューティングを行うには、バッチ ジョブの完全なログを収集します。

   完全なログを収集する方法の詳細については、「[バッチ ジョブの完全なログを取得する](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)」を参照してください。

### <a name="error-code-2312"></a>エラー コード:2312

- **メッセージ**: `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **原因**:HDInsight Spark クラスターでジョブが失敗しました。

- **推奨事項**:ADF 監視ページのアクティビティ実行 Output にあるリンクにアクセスし、HDInsight Spark クラスターでの実行のトラブルシューティングを行います。 さらに支援が必要であれば、HDInsight サポート チームにお問い合わせください。

   完全なログを収集する方法の詳細については、「[バッチ ジョブの完全なログを取得する](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)」を参照してください。

### <a name="error-code-2313"></a>エラー コード:2313

- **メッセージ**: `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **原因**:HDInsight Spark クラスターでバッチが削除されました。

- **推奨事項**:HDInsight Spark クラスターでのバッチをトラブルシューティングしてください。 さらに支援が必要であれば、HDInsight サポートにお問い合わせください。 

   完全なログを収集する方法の詳細については、「[バッチ ジョブの完全なログを取得する](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)」を参照してください。完全なログを HDInsight サポートと共有して、さらに支援を求めてください。

### <a name="error-code-2328"></a>エラー コード:2328

- **メッセージ**: `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **原因**:エラー メッセージに、発生した問題の詳細が表示されているはずです。

- **推奨事項**:エラー メッセージは、問題の解決に役立ちます。

### <a name="error-code-2329"></a>エラー コード:2329

- **メッセージ**: `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **原因**:エラー メッセージに、発生した問題の詳細が表示されているはずです。

- **推奨事項**:エラー メッセージは、問題の解決に役立ちます。

### <a name="error-code-2331"></a>エラー コード:2331

- **メッセージ**: `The file path should not be null or empty.`

- **原因**:指定されたファイル パスが空です。

- **推奨事項**:存在するファイルのパスを指定します。

### <a name="error-code-2340"></a>エラー コード:2340

- **メッセージ**: `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **原因**:HDInsightOnDemand のリンクされたサービスでは、セルフホステッド IR 経由の実行がサポートされていません。

- **推奨事項**:Azure IR を選択してから、再試行します。

### <a name="error-code-2341"></a>エラー コード:2341

- **メッセージ**: `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **原因**:指定した URL の形式が正しくありません。

- **推奨事項**:クラスター URL を修正し、再試行します。

### <a name="error-code-2342"></a>エラー コード:2342

- **メッセージ**: `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **原因**:クラスターに指定された資格情報が間違っているか、ネットワークの構成または接続に問題があるか、または IR とクラスターの接続に問題があります。

- **推奨事項**: 
    1. ブラウザーで HDInsight クラスターの Ambari UI を開いて、資格情報が正しいことを確認します。
    1. クラスターが仮想ネットワーク (VNet) 内にあり、セルフホステッド IR が使用されている場合、HDI URL は VNet のプライベート URL である必要があり、クラスター名の後に "-int" が指定されている必要があります。
    
       たとえば、`https://mycluster.azurehdinsight.net/` を `https://mycluster-int.azurehdinsight.net/` に変更します。 `-int` は `mycluster` の後かつ `.azurehdinsight.net` の前にあることに注意してください
    1. クラスターが VNet 内にあり、セルフホステッド IR を使用し、プライベート URL を使用しても接続に失敗する場合は、IR がインストールされている VM に、HDI に接続できない問題があります。 
    
       IR がインストールされている VM に接続し、ブラウザーで Ambari UI を開きます。 クラスターにプライベート URL を使用します。 この接続はブラウザーから動作するはずです。 動作しない場合、HDInsight サポートにお問い合わせください。
    1. セルフホステッド IR が使用されていない場合、HDI クラスターにパブリック アクセスできるはずです。 ブラウザーで Ambari UI を開き、それが開くことを確認します。 クラスターやクラスター上のサービスに問題がある場合、HDInsight サポート チームに連絡して支援を求めてください。

       テスト接続に合格し、実行が機能するには、ADF のリンクされたサービスで使用されている HDI クラスター URL に ADF IR (セルフホステッドまたは Azure) でアクセスできる必要があります。 この状態は、VM または任意のパブリック マシンでブラウザーからその URL を開くことで確認できます。

### <a name="error-code-2343"></a>エラー コード:2343

- **メッセージ**: `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **原因**:ユーザー名またはパスワードが空です。

- **推奨事項**:HDI に接続するための正しい資格情報を指定し、再試行します。

### <a name="error-code-2345"></a>エラー コード:2345

- **メッセージ**: `Failed to read the content of the hive script. Error: '%message;'`

- **原因**:スクリプト ファイルが存在しないか、ADF でスクリプトの場所に接続できませんでした。

- **推奨事項**:スクリプトが存在し、関連するリンクされたサービスに接続のための適切な資格情報があることを確認します。

### <a name="error-code-2346"></a>エラー コード:2346

- **メッセージ**: `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **原因**:ADF で HDI クラスターへの Open Database Connectivity (ODBC) 接続を確立しようとしましたが、エラーが発生して失敗しました。

- **推奨事項**: 

   1. ODBC または Java Database Connectivity (JDBC) 接続が正しく設定されていることを確認します。
      1. JDBC の場合、同じ仮想ネットワークを使用しているときは、以下からこの接続を取得できます。<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. 適切な JDBC が設定されていることを確認するには、「[HDInsight 上で JDBC ドライバーを使用して Apache Hive のクエリを実行する](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md)」を参照してください。
      1. Open Database (ODB) の場合は、「[チュートリアル: ODBC と PowerShell による Apache Hive のクエリの実行](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md)」を参照して、設定が正しいことを確認します。 
   1. Hiveserver2、Hive メタストア、および Hiveserver2 Interactive がアクティブで動作していることを確認します。 
   1. Ambari ユーザー インターフェイス (UI) を確認します。
      1. すべてのサービスがまだ実行されていることを確認します。
      1. Ambari UI で、ダッシュボードのアラート セクションを確認します。
         1. アラートとアラートの解決方法の詳細については、「[クラスターの管理と監視](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)」を参照してください。
   1. これらの手順が問題を解決するのに十分でない場合は、Azure HDInsight チームにお問い合わせください。

### <a name="error-code-2347"></a>エラー コード:2347

- **メッセージ**: `Hive execution through ODBC failed with error message '%message;'.`

- **原因**:ADF から ODBC 接続経由で HDI クラスターに実行のための hive スクリプトが送信され、そのスクリプトが HDI 上で失敗しました。

- **推奨事項**: 

   1. ODBC または Java Database Connectivity (JDBC) 接続が正しく設定されていることを確認します。
      1. JDBC の場合、同じ仮想ネットワークを使用しているときは、以下からこの接続を取得できます。<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. 適切な JDBC が設定されていることを確認するには、「[HDInsight 上で JDBC ドライバーを使用して Apache Hive のクエリを実行する](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md)」を参照してください。
      1. Open Database (ODB) の場合は、「[チュートリアル: ODBC と PowerShell による Apache Hive のクエリの実行](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md)」を参照して、設定が正しいことを確認します。 
   1. Hiveserver2、Hive メタストア、および Hiveserver2 Interactive がアクティブで動作していることを確認します。 
   1. Ambari ユーザー インターフェイス (UI) を確認します。
      1. すべてのサービスがまだ実行されていることを確認します。
      1. Ambari UI で、ダッシュボードのアラート セクションを確認します。
         1. アラートとアラートの解決方法の詳細については、「[クラスターの管理と監視](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)」を参照してください。
   1. これらの手順が問題を解決するのに十分でない場合は、Azure HDInsight チームにお問い合わせください。

### <a name="error-code-2348"></a>エラー コード:2348

- **メッセージ**: `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **原因**:ストレージのリンクされたサービスのプロパティが正しく設定されていません。

- **推奨事項**:HDI アクティビティのメイン ストレージのリンクされたサービスでは、完全接続文字列のみサポートされています。 MSI 認証またはアプリケーションを使用していないことを確認します。

### <a name="error-code-2350"></a>エラー コード:2350

- **メッセージ**: `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **原因**:ファイルがあるはずのストレージに接続するための資格情報が間違っているか、ファイルがそこにありません。

- **推奨事項**:このエラーは、ADF が HDI アクティビティに備えて、ジョブを HDI に送信する前にファイルをメイン ストレージにコピーしようとしたときに発生します。 指定の場所にファイルが存在すること、およびストレージ接続が正しいことを確認します。 ADF HDI アクティビティでは、HDI アクティビティに関連付けられているストレージ アカウントの MSI 認証がサポートされていません。そのため、これらのリンクされたサービスに完全なキーが指定されていること、または Azure Key Vault が使用されていることを確認します。

### <a name="error-code-2351"></a>エラー コード:2351

- **メッセージ**: `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **原因**:指定されたパスにファイルが存在しません。

- **推奨事項**:ファイルが実際に存在するかどうかを確認し、このファイルを指す接続情報を含むリンクされたサービスに正しい資格情報が指定されていることを確認します。

### <a name="error-code-2352"></a>エラー コード:2352

- **メッセージ**: `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **原因**:ファイル ストレージのリンクされたサービスのプロパティが正しく設定されていません。

- **推奨事項**:ファイル ストレージのリンクされたサービスのプロパティが正しく構成されていることを確認します。

### <a name="error-code-2353"></a>エラー コード:2353

- **メッセージ**: `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **原因**:スクリプト ストレージのリンクされたサービスのプロパティが正しく設定されていません。

- **推奨事項**:スクリプト ストレージのリンクされたサービスのプロパティが正しく構成されていることを確認します。

### <a name="error-code-2354"></a>エラー コード:2354

- **メッセージ**: `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **原因**:ストレージのリンクされたサービスの種類は、このアクティビティではサポートされていません。

- **推奨事項**:選択したリンクされたサービスがアクティビティでサポートされているいずれかの種類であることを確認します。 HDI アクティビティでは、AzureBlobStorage と AzureBlobFSStorage のリンクされたサービスがサポートされています。

   詳細については、「[Azure HDInsight クラスターで使用するストレージ オプションを比較する](../hdinsight/hdinsight-hadoop-compare-storage-options.md)」を参照してください

### <a name="error-code-2355"></a>エラー コード:2355

- **メッセージ**: `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **原因**:`commandEnvironment` に指定された値が正しくありません。

- **推奨事項**:指定された値が次のようになっていることを確認します。
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    また、各変数が一覧内で 1 回だけ出現することを確認します。

### <a name="error-code-2356"></a>エラー コード:2356

- **メッセージ**: `The commandEnvironment already contains a variable named '%variableName;'.`

- **原因**:`commandEnvironment` に指定された値が正しくありません。

- **推奨事項**:指定された値が次のようになっていることを確認します。
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    また、各変数が一覧内で 1 回だけ出現することを確認します。

### <a name="error-code-2357"></a>エラー コード:2357

- **メッセージ**: `The certificate or password is wrong for ADLS Gen 1 storage.`

- **原因**:指定された資格情報が間違っています。

- **推奨事項**:ADLS Gen 1 の接続情報がサービスにリンクされていることを確認し、テスト接続に成功することを確認します。

### <a name="error-code-2358"></a>エラー コード:2358

- **メッセージ**: `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **原因**:必須プロパティ `TimeToLive` に指定された値の形式が無効です。 

- **推奨事項**:値を推奨される範囲に更新し、再試行します。

### <a name="error-code-2359"></a>エラー コード:2359

- **メッセージ**: `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **原因**:プロパティ `roles` に指定された値が無効です。

- **推奨事項**:値をいずれかの推奨値に変更し、再試行します。

### <a name="error-code-2360"></a>エラー コード:2360

- **メッセージ**: `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **原因**:`HCatalogLinkedService` に指定された接続文字列が無効です。

- **推奨事項**:値を正しい Azure SQL 接続文字列に更新し、再試行します。

### <a name="error-code-2361"></a>エラー コード:2361

- **メッセージ**: `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **原因**:クラスター作成に失敗したが、ADF には HDInsight サービスからエラーが返されませんでした。

- **推奨事項**:Azure portal を開き、指定された名前の HDI リソースを見つけて、プロビジョニング状態を確認します。 さらに支援が必要であれば、HDInsight サポート チームにお問い合わせください。

### <a name="error-code-2362"></a>エラー コード:2362

- **メッセージ**: `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **原因**:指定された追加のストレージは Azure Blob Storage ではありませんでした。

- **推奨事項**:HDInsight オンデマンドのリンクされたサービスの追加ストレージとして Azure Blob Storage アカウントを指定します。

### <a name="ssl-error-when-adf-linked-service-using-hdinsight-esp-cluster"></a>HDInsight ESP クラスターを使用している ADF のリンクされたサービスで SSL エラーが発生しました

- **メッセージ**: `Failed to connect to HDInsight cluster: 'ERROR [HY000] [Microsoft][DriverSupport] (1100) SSL certificate verification failed because the certificate is missing or incorrect.`

- **原因**:この問題は、システムの信頼ストアに関連している可能性が最も高いです。

- **解決方法**:パス **Microsoft Integration Runtime\4.0\Shared\ODBC Drivers\Microsoft Hive ODBC Driver\lib** に移動し、DriverConfiguration64.exe を開いて設定を変更できます。

    ![[Use System Trust Store]\(システムの信頼ストアの使用\) をオフにする](./media/connector-troubleshoot-guide/system-trust-store-setting.png)

## <a name="web-activity"></a>Web アクティビティ

### <a name="error-code-2128"></a>エラー コード:2128

- **メッセージ**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **原因**:この問題は、ネットワーク接続、DNS エラー、サーバー証明書の検証、またはタイムアウトが原因で発生します。

- **推奨事項**:要求を試しているエンドポイントが要求に応答していることを確認します。 **Fiddler や Postman** などのツールを使用できます。

### <a name="error-code-2108"></a>エラー コード:2108

- **メッセージ**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **原因**:ネットワーク接続、DNS エラー、サーバー証明書の検証、タイムアウトなどの根本的な問題が原因で要求が失敗しました。

- **推奨事項**:Fiddler/Postman を使用して要求を検証してください。

#### <a name="more-details"></a>詳細
**Fiddler** を使用して監視対象 Web アプリケーションの HTTP セッションを作成するには:

1. [Fiddler](https://www.telerik.com/download/fiddler) をダウンロードし、インストールして開きます。

1. Web アプリケーションで HTTPS が使用されている場合は、 **[Tools]\(ツール\)**  >  **[Fiddler Options]\(Fiddler オプション\)**  >  **[HTTPS]** の順に選択します。

   1. [HTTPS] タブで、 **[Capture HTTPS CONNECTs]\(HTTPS 接続をキャプチャする\)** と **[Decrypt HTTPS traffic]\(HTTPS トラフィックの暗号化を解除する\)** の両方をオンにします。

      ![Fiddler オプション](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. お使いのアプリケーションで TLS/SSL 証明書が使用されている場合は、Fiddler の証明書をデバイスに追加します。

   参照先: **[Tools]\(ツール\)**  >  **[Fiddler Options]\(Fiddler オプション\)**  >  **[HTTPS]**  >  **[Actions]\(アクション\)**  >  **[Export Root Certificate to Desktop]\(ルート証明書をデスクトップにエクスポート\)** 。

1. **[File]\(ファイル\)**  >  **[Capture Traffic]\(トラフィックのキャプチャ\)** に移動して、キャプチャをオフにします。 または **F12** キーを押します。

1. キャッシュされたすべての項目が削除されて、再ダウンロードが必要になるように、ブラウザーのキャッシュをクリアします。

1. 要求を作成します。

1. **[Composer]\(コンポーザー\)** タブを選択します。

   1. HTTP メソッドと URL を設定します。
 
   1. 必要に応じて、ヘッダーと要求本文を追加します。

   1. **[実行]** を選択します。

1. トラフィックのキャプチャを再び有効にし、ページで問題のあるトランザクションを完了します。

1. 参照先: **[File]\(ファイル\)**  >  **[Save]\(保存\)**  >  **[All Sessions]\(すべてのセッション\)** 。

詳細については、「[Fiddler の基本](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)」を参照してください。

## <a name="general"></a>全般

### <a name="activity-stuck-issue"></a>アクティビティの停止に関する問題

アクティビティが通常の実行時間を大幅に超えて実行され、ほとんど進行していない場合、そのアクティビティは停止している可能性があります。 取り消しを試みた後、再試行して問題が解決するかどうかを確認できます。 コピー アクティビティの場合は、パフォーマンスの監視とトラブルシューティングの詳細について「[コピーアクティビティのパフォーマンスのトラブルシューティング](copy-activity-performance-troubleshooting.md)」を参照してください。データ フローの場合は、[マッピング データ フローのパフォーマンス](concepts-data-flow-performance.md)とチューニング ガイドに関するページを参照してください。

### <a name="payload-is-too-large"></a>ペイロードが大きすぎます

**エラー メッセージ:** `The payload including configurations on activity/dataSet/linked service is too large. Please check if you have settings with very large value and try to reduce its size.`

**原因:** 各アクティビティの実行のペイロードには、アクティビティの構成、関連付けられているデータセットとリンクされたサービスの構成 (ある場合)、アクティビティの種類ごとに生成されるシステム プロパティの一部が含まれます。 このようなペイロード サイズの制限は、「[Data Factory の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)」セクションで説明されているように 896 KB です。

**推奨事項:** アップストリーム アクティビティの出力または外部から 1 つ以上の大きなパラメーター値を渡す場合、特に制御フロー内のアクティビティ間で実際のデータを渡す場合、この制限に達する可能性があります。 大きなパラメーター値のサイズを減らせるかどうかを確認するか、パイプライン ロジックを調整して、アクティビティ間で値が渡されずにアクティビティ内で処理されるようにします。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

* [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
* [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
* [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft Q&A 質問ページ](/answers/topics/azure-data-factory.html)

---
title: ML Studio (classic):REST API のエラー コード - Azure
description: Azure Machine Learning Web サービスに対する操作では、これらのエラー コードが返されることがあります。
keywords: ''
services: machine-learning
author: likebupt
ms.author: keli19
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: c5a457a673500c542dd6130d5cfadd73ab677f7b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518353"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Azure Machine Learning Studio (クラシック) REST API のエラー コード

**適用対象:** ![適用対象: ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![適用対象外: ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Azure Machine Learning Studio (クラシック) の Web サービスの操作で、次のエラー コードが返されることがあります。
 
## <a name="badargument-http-status-code-400"></a>BadArgument (HTTP 状態コード 400)
 
指定された引数が無効です。
 
このクラスのエラーは、どこかで指定された引数が無効であることを意味します。 たとえば、Web サービスに渡された Azure Storage の資格情報や場所です。 無効な引数を具体的に特定するには、"details" セクションのエラー "code" フィールドを参照してください。
 
| エラー コード | ユーザー メッセージ |
| ---------- |--------------|
| BadParameterValue | 指定されたパラメーター値がパラメーターの規則に従っていません。 |
| BadSubscriptionId | スコア付けに使用されるサブスクリプション ID がリソースに存在する ID と異なります。 |
| BadVersionCall | API 呼び出し中に無効なバージョン パラメーターが渡されました: {0}。 API のヘルプ ページで正しいバージョンを渡す方法を確認してから、やり直してください。 |
| BatchJobInputsNotSpecified | 次の必須の入力が要求に指定されていませんでした: {0}。 すべての入力データを指定してから、やり直してください。 |
| BatchJobInputsTooManySpecified | サービスで定義されているよりも多くの入力が要求に指定されました。 許容される入力の一覧: {0}。 すべての入力データを正しく指定してから、やり直してください。 |
| BlobNameTooLong | 診断出力に指定された Azure Blob Storage のパスが長すぎます: {0}。 パスを短くしてから、やり直してください。 |
| BlobNotFound | 指定された Azure Blob にアクセスできません: {0}。  Azure のエラー メッセージ: {1}。 |
| ContainerIsEmpty | Azure Storage コンテナー名が指定されませんでした。 有効なコンテナー名を指定してから、やり直してください。 |
| ContainerSegmentInvalid | コンテナーの名前が無効です。 有効なコンテナー名を指定してから、やり直してください。 |
| ContainerValidationFailed | 次のエラーで Blob コンテナーの検証が失敗しました: {0}。 |
| DataTypeNotSupported | 指定されたデータ型はサポートされていません。 有効なデータ型を指定してから、やり直してください。 |
| DuplicateInputInBatchCall | バッチ要求が無効です。 単一の入力と複数の入力の両方を同時に指定することはできません。 要求からどちらかの項目を削除してから、やり直してください。 |
| ExpiryTimeInThePast | 指定された有効期限が過去の時点になっています: {0}。 UTC で未来の有効期限を指定してから、やり直してください。 期限切れにならないようにするには、有効期限を NULL に設定してください。 |
| IncompleteSettings | 診断設定が不完全です。 |
| InputBlobRelativeLocationInvalid | Azure Storage BLOB 名が指定されていません。 有効な BLOB 名を指定してから、やり直してください。 |
| InvalidBlob | Blob の指定が無効です: {0}。 接続文字列と相対パスまたは SAS トークンの仕様が正しいことを確認してから、やり直してください。 |
| InvalidBlobConnectionString | 入出力 BLOB のいずれかに指定された接続文字列が無効です: {0}。 修正してから、やり直してください。 |
| InvalidBlobExtension | Blob の参照: {0} のファイル拡張子が無効か、見つかりません。 この出力の種類でサポートされているファイル拡張子は "{1}" です。 |
| InvalidInputNames | 要求で指定されたサービスの入力名が無効です: {0}。 入力データを正しいサービスの入力にマップしてから、やり直してください。 |
| InvalidOutputOverrideName | 出力のオーバーライドの名前が無効です: {0}。 サービスには、この名前の出力ノードがありません。 オーバーライドする正しい出力ノード名を渡してください (大文字と小文字が区別されます)。 |
| InvalidQueryParameter | クエリ パラメーター '{0}' が無効です。 {1} |
| MissingInputBlobInformation | Azure Storage BLOB の情報が不足しています。 有効な接続文字列と相対パスまたは URI を指定してから、やり直してください。 |
| MissingJobId | ジョブ ID が指定されていません。 初めてジョブを送信すると、ジョブ ID が返されます。 ジョブ ID が正しいことを確認してから、やり直してください。 |
| MissingKeys | キーが指定されていないか、プライマリ キーとセカンダリ キーのいずれかが指定されていません。 |
| MissingModelPackage | モデル パッケージ ID またはモデル パッケージが指定されていません。 有効なモデル パッケージ ID またはモデル パッケージを指定してから、やり直してください。 |
| MissingOutputOverrideSpecification | 要求に出力のオーバーライド {0} の BLOB の指定がありません。 要求で有効な BLOB の場所を指定するか、場所をオーバーライドする必要がない場合は出力の指定を削除してください。 |
| MissingRequestInput | Web サービスには入力が必要ですが、入力が指定されませんでした。 モデルの発行済みの入力ポートに基づいて有効な入力値が指定されていることを確認してから、やり直してください。 |
| MissingRequiredGlobalParameters | Web サービスの必須パラメーターが一部指定されていません。 モジュールに必要なパラメーターが正しいことを確認してから、やり直してください。 |
| MissingRequiredOutputOverrides | 暗号化されたサービス エンドポイントを呼び出す場合は、サービスのすべての出力に対して出力のオーバーライドを渡すことが必須となります。 現時点では、次の出力のオーバーライドがありません: {0} |
| MissingWebServiceGroupId | Web サービス グループ ID が指定されていません。 有効な Web サービス グループ ID を指定してから、やり直してください。 |
| MissingWebServiceId | Web サービス ID が指定されていません。 有効な Web サービス ID を指定してから、やり直してください。 |
| MissingWebServicePackage | Web サービス パッケージが指定されていません。 有効な Web サービス パッケージを指定してから、やり直してください。 |
| MissingWorkspaceId | ワークスペース ID が指定されていません。 有効なワークスペース ID を指定してから、やり直してください。 |
| ModelConfigurationInvalid | モデル パッケージ内のモデル構成が無効です。 モデル構成に、出力エンドポイントの定義、std error エンドポイント、std out エンドポイントが含まれていることを確認してから、やり直してください。 |
| ModelPackageIdInvalid | モデル パッケージ ID が無効です。モデル パッケージ ID が正しいことを確認してから、やり直してください。 |
| RequestBodyInvalid | 要求本文が指定されていないか、要求本文を逆シリアル化中にエラーが発生しました。 |
| RequestIsEmpty | 要求が指定されていません。 有効な要求を指定してから、やり直してください。 |
| UnexpectedParameter | 予期しないパラメーターが指定されました。 すべてのパラメーター名が正しく入力されており、必要なパラメーターのみが渡されていることを確認してから、やり直してください。 |
| UnknownError | 不明なエラー。 |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | {0} Web サービスの同時要求の要件を変更することはできません。 |
| WebServiceIdInvalid | 指定された Web サービス ID が無効です。 Web サービス ID は有効な GUID である必要があります。 |
| WebServiceTooManyConcurrentRequestRequirement | {0} までしか同時要求の要件を設定することはできません。 |
| WebServiceTypeInvalid | 指定された Web サービスの種類が無効です。 Web サービスの種類が有効で正しいことを確認してから、やり直してください。 有効な Web サービスの種類: {0}。 |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (HTTP 状態コード 400)
 
指定されたユーザー引数が無効です。
 
| エラー コード | ユーザー メッセージ |
| ---------- |--------------|
| InputMismatchError | 入力データが入力ポートのスキーマと一致しません。 |
| InputParseError | 入力ベクトルの解析に失敗しました。  入力ベクトルの列数とデータ型が正しいことを確認してください。  追加情報: {0}。 |
| MissingRequiredGlobalParameters | Web サービスに必要なパラメーターが不足しています。 Web サービスの必須パラメーターがすべて正しいことを確認してから、やり直してください。 |
| UnexpectedParameter | Web サービスの必須パラメーターのみが渡されていることを確認してから、やり直してください。 |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (HTTP 状態コード 400)
 
現在のコンテキストでは要求が無効です。
 
| エラー コード | ユーザー メッセージ |
| ---------- |--------------|
| CannotStartJob | 状態が {0} のため、ジョブを開始できません。 |
| IncompatibleModel | モデルは要求バージョンと互換性がありません。 この要求バージョンでは、1 つの datatable 出力モデルしかサポートしていません。 |
| MultipleInputsNotAllowed | このモデルでは、複数の入力が許可されていません。 |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (HTTP 状態コード 400)
 
モジュールの実行中に内部ライブラリ エラーが発生しました。
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (HTTP 状態コード 400)
 
モジュールの実行中にエラーが発生しました。
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (HTTP 状態コード 400)
 
Web サービス パッケージが無効です。 指定された Web サービス パッケージが正しいことを確認してから、やり直してください。
 
| エラー コード | ユーザー メッセージ |
| ---------- |--------------|
| FormatError | Web サービス パッケージの形式が正しくありません。 詳細: {0} |
| RuntimesError | Web サービス パッケージのグラフが無効です。 詳細: {0} |
| ValidationError | Web サービス パッケージのグラフが無効です。 詳細: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Unauthorized (HTTP 状態コード 401)
 
要求にリソースへのアクセスが許可されていません。
 
| エラー コード | ユーザー メッセージ |
| ---------- |--------------|
| AdminRequestUnauthorized | 権限がありません |
| ManagementRequestUnauthorized | 権限がありません |
| ScoreRequestUnauthorized | 指定された資格情報が無効です。 |
 
## <a name="notfound-http-status-code-404"></a>NotFound (HTTP 状態コード 404)
 
リソースが見つかりません。
 
| エラー コード | ユーザー メッセージ |
| ---------- |--------------|
| ModelPackageNotFound | モデル パッケージが見つかりません。 モデル パッケージ ID が正しいことを確認してから、やり直してください。 |
| WebServiceIdNotFoundInWorkspace | このワークスペースに Web サービスが見つかりません。 webServiceId と workspaceId が一致しません。 指定された Web サービスがワークスペースの一部であることを確認してから、やり直してください。 |
| WebServiceNotFound | Web サービスが見つかりません。 Web サービス ID が正しいことを確認してから、やり直してください。 |
| WorkspaceNotFound | ワークスペースが見つかりません。 ワークスペース ID が正しいことを確認してから、やり直してください。 |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (HTTP 状態コード 408)
 
許可された時間内に操作を完了できません。
 
| エラー コード | ユーザー メッセージ |
| ---------- |--------------|
| RequestCanceled | 要求がクライアントによって取り消されました。 |
| ScoreRequestTimeout | 実行要求がタイムアウトしました。 |
 
## <a name="conflict-http-status-code-409"></a>Conflict (HTTP 状態コード 409)
 
リソースは既に存在しています。
 
| エラー コード | ユーザー メッセージ |
| ---------- |--------------|
| ModelOutputMetadataMismatch | 出力パラメーター名が無効です。 メタデータ エディター モジュールを使用して、列名を変更してから、やり直してください。 |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (HTTP 状態コード 413)
 
モデルが割り当てられているメモリのクォータを超えました。
 
| エラー コード | ユーザー メッセージ |
| ---------- |--------------|
| OutOfMemoryLimit | モデルが割り当てられているよりも多くのメモリを消費しました。 モデルの許容される最大メモリは {0} MB です。 モデルに問題がないかどうかを確認してください。 |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (HTTP 状態コード 500)
 
実行中に内部エラーが発生しました。
 
| エラー コード | ユーザー メッセージ |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | システム エラーでコンテナー プロセスがクラッシュしました |
| ContainerProcessTerminatedWithUnknownError | 不明なエラーでコンテナー プロセスがクラッシュしました |
| ContainerValidationFailed | 次のエラーで Blob コンテナーの検証が失敗しました: {0}。 |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | メモリの構成が無効です。構成値: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | 引数が指定されていません。 有効な引数が渡されていることを確認してから、やり直してください。 |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | ポート ID {0} ではサポートされていないデータ型です: {1}。 |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Swagger の生成に失敗しました。詳細: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | 不明なジョブ状態コード: {0}。 |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | Web サービス パッケージが無効です。詳細: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (HTTP 状態コード 500)
 
実行中に内部エラーが発生しました。 システム メモリが不足しています。 再試行してください。
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (HTTP 状態コード 500)
 
モデル パッケージが無効です。 指定されたモデル パッケージが正しいことを確認してから、やり直してください。
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (HTTP 状態コード 500)
 
Web サービス パッケージが無効です。 指定された Web パッケージが正しいことを確認してから、やり直してください。
 
| エラー コード | ユーザー メッセージ |
| ---------- |--------------|
| ModuleError | Web サービス パッケージのグラフが無効です。 詳細: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (HTTP 状態コード 503)
 
コンテナーが初期化中のため、要求を実行できません。
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (HTTP 状態コード 503)
 
サービスが一時的に利用できません。
 
| エラー コード | ユーザー メッセージ |
| ---------- |--------------|
| NoMoreResources | 要求に使用できるリソースがありません。 |
| RequestThrottled | {0} エンドポイントに対して要求が調整されました。 このエンドポイントの最大のコンカレンシー数は {1} です。 |
| TooManyConcurrentRequests | 送信された同時実行要求が多すぎます。 |
| TooManyHostsBeingInitialized | 同時に初期化されるホストが多すぎます。 調整/再試行を検討してください。 |
| TooManyHostsBeingInitializedPerModel | 同時に初期化されるホストが多すぎます。 調整/再試行を検討してください。 |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (HTTP 状態コード: 504)
 
許可された時間内に操作を完了できません。
 
| エラー コード | ユーザー メッセージ |
| ---------- |--------------|
| BackendInitializationTimeout | 許可された時間内に Web サービスの初期化を完了できません。 |
| BackendScoreTimeout | 許可された時間内に Web サービス要求の実行を完了できません。 |

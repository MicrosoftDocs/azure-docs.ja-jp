---
title: Azure Form Recognizer のディザスター リカバリーに関するガイダンス
titleSuffix: Azure Cognitive Services
description: モデルのコピー API を使用して、ご使用の Form Recognizer リソースをバックアップする方法について説明します。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: b5eb776a7807f48ae6c1a0e3c5879da1f6823830
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466917"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>ご使用の Form Recognizer モデルのバックアップと復旧

Azure portal 内で Form Recognizer リソースを作成するときに、リージョンを指定します。 それ以降、ご使用のリソースとそのすべての操作はその特定の Azure サーバー リージョンに関連付けられたままになります。 リージョン全体に影響が及ぶネットワークの問題が発生することはまれですが、まったくないわけではありません。 ご自分のソリューションを常に使用できるようにする必要がある場合は、別のリージョンにフェールオーバーするか、複数のリージョン間でワークロードを分割するように設計する必要があります。 どちらの方法でも、リージョンごとに少なくとも 2 つの Form Recognizer リソースと、リージョン間でカスタム モデルを同期する機能が必要です。

コピー API を使用して、1 つの Form Recognizer アカウントから、またはサポートされている任意の地理的リージョン内に存在できる別のアカウントに、カスタム モデルをコピーできるようにすることで、このシナリオを実現できます。 このガイドでは、cURL と共にコピー REST API を使用する方法について説明します。 Postman のような HTTP 要求サービスを使用して、要求を発行することもできます。

## <a name="business-scenarios"></a>ビジネス シナリオ

ご自分のアプリまたはビジネスが Form Recognizer カスタム モデルの使用に依存している場合は、別のリージョン内の別の Form Recognizer アカウントにご使用のモデルをコピーすることをお勧めします。 地域的な障害が発生した場合は、コピー先のリージョン内のご自分のモデルにアクセスできます。

##  <a name="prerequisites"></a>前提条件

1. 異なる Azure リージョン内にある 2 つの Form Recognizer Azure リソース。 それらがない場合は、Azure portal に移動し、<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="新しい Form Recognizer リソースを作成します" target="_blank">新しい Form Recognizer リソースを作成します </a>。
1. ご使用の Form Recognizer リソースのサブスクリプション キー、エンドポイント URL、およびサブスクリプション ID。 これらの値は Azure portal 上のリソースの **[概要]** タブにあります。


## <a name="copy-api-overview"></a>コピー API の概要

カスタム モデルをコピーするプロセスは、次の手順で構成されています。

1. まず、ターゲット リソース (コピーされたモデルを受け取るリソース) に対してコピー承認要求を発行します。 新しく作成されたターゲット モデルの URL が返されます。この URL により、コピーされたデータを取得します。
1. 次に、ソース リソース (コピーするモデルが含まれているリソース) にコピー要求を送信します。 クエリを実行して操作の進行状況を追跡できる URL が返されます。
1. 操作が成功するまで、ご自分のソース リソースの資格情報を使用して、進行状況の URL に対してクエリを実行します。 また、ターゲット リソース内の新しいモデル ID に対してクエリを実行して、新しいモデルの状態を取得することもできます。

## <a name="generate-copy-authorization-request"></a>コピー承認要求を生成する

次の HTTP 要求では、ターゲット リソースからコピー承認を取得します。 ご自分のターゲット リソースのエンドポイントとキーをヘッダーとして入力する必要があります。

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

本文に `modelId` 値を含む `201\Created` 応答が返されます。 この文字列は、新しく作成された (空の) モデルの ID です。 `accessToken` は API がこのリソースにデータをコピーするために必要です。`expirationDateTimeTicks` の値はトークンの有効期限です。 これら 3 つの値すべてを安全な場所に保存しておきます。

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>コピー操作を開始する

次の HTTP 要求では、ソース リソースのコピー操作を開始します。 ご自分のソース リソースのエンドポイントとキーをヘッダーとして入力する必要があります。 コピーするソース モデルのモデル ID が要求 URL に含まれていることに注目してください。

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/<your model ID>/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

実際の要求の本文は、次の形式である必要があります。 ご自分のターゲット リソースのリソース ID とリージョン名を入力する必要があります。 リソース ID は、Azure portal のリソースの **[プロパティ]** タブにあります。リージョン名は **[キーとエンドポイント]** タブにあります。また、前の手順でコピーしたモデル ID、アクセス トークン、有効期限の値も必要です。

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
}
```

> [!NOTE]
> コピー API では、[AEK/CMK](https://msazure.visualstudio.com/Cognitive%20Services/_wiki/wikis/Cognitive%20Services.wiki/52146/Customer-Managed-Keys) 機能が透過的にサポートされます。 これには特別な処理は必要ありませんが、暗号化されていないリソースと暗号化されたリソースの間でコピーを行う場合は、要求ヘッダー `x-ms-forms-copy-degrade: true` を含める必要があることにご注意ください。 このヘッダーが含まれていない場合、コピー操作は失敗し、`DataProtectionTransformServiceError` が返されます。

Operation-Location ヘッダーを含む `202\Accepted` 応答が返されます。 この値は、操作の進行状況を追跡するために使用する URL です。 次の手順のために、これを一時的な場所に保存します。

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

### <a name="common-errors"></a>一般的なエラー

|エラー|解決方法|
|:--|:--|
| 400 / `"code:" "1002"` が指定された無効な要求 | 検証エラーまたはコピー要求の形式が正しくないことを示します。 一般的な問題には次のようなものがあります。a) 無効または変更された `copyAuthorization` ペイロード。 b) `expirationDateTimeTicks` トークンの期限切れの値 (`copyAuhtorization` ペイロードは 24 時間有効です)。 c) 無効またはサポートされていない `targetResourceRegion`。 d) 無効または形式が正しくない `targetResourceId` 文字列。
|

## <a name="track-copy-progress"></a>コピーの進行状況を追跡する

ソース リソース エンドポイントに対して **Get Copy Model Result** API のクエリを実行して、進行状況を追跡します。

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

実際の応答は、操作の状態によって異なります。 JSON 本文内の `"status"` フィールドを探します。 スクリプト内でこの API 呼び出しを自動化する場合は、1 秒ごとに操作に対してクエリを実行することをお勧めします。

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="common-errors"></a>一般的なエラー

|エラー|解決方法|
|:--|:--|
|"errors":[{"code":"AuthorizationError",<br>"message":"Authorization failure due to <br>missing or invalid authorization claims."}] (承認要求が見つからないか無効であるため承認が失敗しました)   | `copyAuthorization` ペイロードまたは内容が `copyAuthorization` API によって返されたものから変更されたときに発生します。 ペイロードが、前の `copyAuthorization` の呼び出しから返されたものと確実にまったく同じ内容であるようにします。|
|"errors":[{"code":"AuthorizationError",<br>"message":"Could not retrieve authorization <br>metadata. If this issue persists use a different <br>target model to copy into."}] (承認メタデータを取得できませんでした。この問題が解決しない場合は、コピー先として異なるターゲット モデルをお使いください。) | `copyAuthorization` ペイロードがコピー要求で再利用されていることを示します。 コピー要求が成功した場合、同じ `copyAuthorization` ペイロードを使用したそれ以上の要求は許可されません。 次に示すような別のエラーが発生した後に、同じ承認ペイロードでコピーを再試行すると、このエラーが発生します。 この解決策は、新しい `copyAuthorization` ペイロードを生成してから、コピー要求を再発行することです。|
|"errors":[{"code":"DataProtectionTransformServiceError",<br>"message":"Data transfer request is not allowed <br>as it downgrades to a less secure data protection scheme. Refer documentation or contact your service administrator <br>for details."}] (安全性が低いデータ保護スキームにダウングレードされるため、データ転送要求は許可されていません。詳細については、ドキュメントを参照するか、サービス管理者に連絡してください。)    | `AEK` が有効なリソースと `AEK` が有効でないリソース間でコピーするときに発生します。 暗号化されたモデルを暗号化されていない状態でターゲットにコピーできるようにするには、コピー要求で `x-ms-forms-copy-degrade: true` ヘッダーを指定します。|
|"errors":[{"code":"ResourceResolverError",<br>"message":"Could not fetch information for Cognitive resource with Id '...'. Ensure the resource is valid and exists in the specified region 'westus2'.."}] (ID '...' の Cognitive リソースの情報をフェッチできませんでした。確実にリソースが有効であり、指定されたリージョン 'westus2' に存在するようにしてください。) | `targetResourceId` によって示される Azure リソースが有効な Cognitive リソースではないか、存在しないことを示します。 この問題を解決するには、コピー要求を確認して再発行します。|


### <a name="optional-track-the-target-model-id"></a>[省略可能] ターゲット モデル ID を追跡する 

また、**Get Custom Model** API を使用して、ターゲット モデルに対してクエリを実行することによって、操作の状態を追跡することもできます。 最初の手順でコピーしたターゲット モデル ID を使用して、この API を呼び出します。

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

応答本文には、モデルの詳細に関する情報が表示されます。 `"status"` フィールドでモデルの状態を確認します。

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>cURL サンプル コード

次のコード スニペットでは、cURL を使用して、上記の手順で説明した API 呼び出しを行います。 この場合も、ご自分のリソースに固有のモデル ID とサブスクリプション情報を入力する必要があります。

### <a name="generate-copy-authorization-request"></a>コピー承認要求を生成する

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>コピー操作を開始する

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>コピーの進行状況を追跡する

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>次のステップ

このガイドでは、コピー API を使用して、ご使用のカスタム モデルをセカンダリ Form Recognizer リソースにバックアップする方法について説明しました。 次に、API リファレンス ドキュメントを参照して、Form Recognizer を使用して他にできることを確認します。
* [REST API リファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)

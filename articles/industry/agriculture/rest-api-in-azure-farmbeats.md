---
title: Azure FarmBeats API
description: JSON ベースの応答を備えた標準化された RESTful インターフェイスを農業ビジネスに提供する、Azure FarmBeats API について説明します。
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 292374bcce90da042dc6bd672feaad3750759aa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92674510"
---
# <a name="azure-farmbeats-apis"></a>Azure FarmBeats API

この記事では、Azure FarmBeats API について説明します。 Azure FarmBeats API は、JSON ベースの応答を備えた標準化された RESTful インターフェイスを農業ビジネスに提供し、次のような Azure FarmBeats 機能の活用を支援します。

- センサー、カメラ、ドローン、天気、衛星、キュレートされた地上データを取得する API
- 共通データ プロバイダー全体のデータの正規化とコンテキスト化
- すべての取り込まれたデータに対するスキーマ化されたアクセスとクエリ機能
- 作物の特徴に基づいてクエリを実行できるメタデータの自動生成
- 高速なモデル構築のために自動生成される時系列集計
- カスタム データ処理パイプラインを簡単に作成できる統合 Azure Data Factory エンジン

## <a name="application-development"></a>アプリケーション開発

FarmBeats API には、Swagger の技術文書が含まれています。 すべての API とそれに対応する要求と応答の詳細については、[Swagger](https://aka.ms/FarmBeatsDatahubSwagger) に関するページを参照してください。

次の表は、FarmBeats Datahub 内のすべてのオブジェクトとリソースをまとめたものです。

| オブジェクトとリソース | 説明
--- | ---|
Farm | ファームは、FarmBeats システム内の物理的な場所に対応します。 各ファームには、ファーム名と一意のファーム ID があります。 |
Device  | Device は、ファームに存在する物理デバイスに対応します。 各デバイスには一意のデバイス ID があります。 通常、デバイスはファーム ID を持つファームにプロビジョニングされます。
DeviceModel  | DeviceModel は、デバイスのメタデータ (製造元など) およびデバイスの種類 (ゲートウェイまたはノード) に対応します。
センサー  | センサーは、値を記録する物理的なセンサーに対応するものです。 センサーは通常、デバイス ID を持つデバイスに接続されます。
SensorModel  | SensorModel は、センサーのメタデータ (製造元など)、センサーの種類 (アナログまたはデジタル)、センサーのメジャー (周辺温度や圧力) に対応します。
テレメトリ  | テレメトリでは、特定のセンサーと時間範囲のテレメトリ メッセージを読み取ることができます。
ジョブ  | ジョブは、目的の出力を取得するために FarmBeats システムで実行されるアクティビティのワークフローに対応します。 各ジョブは、ジョブ ID とジョブの種類に関連付けられています。
JobType  | JobType は、システムでサポートされているさまざまなジョブの種類に対応します。 システム定義およびユーザー定義のジョブの種類が含まれます。
ExtendedType  | ExtendedType は、システム内でシステムおよびユーザーによって定義される型の一覧に対応しています。 ExtendedType は、FarmBeats システムで新しいセンサー、シーン、またはシーン ファイルの種類を設定するために役立ちます。
Partner  | パートナーは、FarmBeats のセンサーと画像統合パートナーに対応します。
Scene  | シーンは、ファームのコンテキストで生成されるすべての出力に対応します。 各シーンには、シーン ID、シーン ソース、シーンの種類、およびファーム ID が関連付けられています。 各シーン ID には、複数のシーン ファイルを関連付けることができます。
SceneFile |SceneFile は、1 つのシーンに対して生成されるすべてのファイルに対応します。 1 つのシーン ID に複数の SceneFile ID を関連付けることができます。
ルール  |ルールは、アラートをトリガーするファーム関連のデータの条件に対応します。 各ルールは、ファームのデータのコンテキストに含まれています。
アラート:  | アラートは、ルール条件が満たされたときに生成される通知に対応します。 各アラートは、ルールのコンテキストに含まれています。
RoleDefinition  | RoleDefinition は、ロールに対して許可されるアクションと許可されないアクションを定義します。
RoleAssignment  |RoleAssignment は、ユーザーまたはサービス プリンシパルへのロールの割り当てに対応します。

### <a name="data-format"></a>データ形式

JSON は、任意のデータ構造をシンプルなテキストで表現する、言語に依存しない一般的なデータ形式です。 詳細については、[JSON Web サイト](https://www.json.org/)を参照してください。

## <a name="authentication-and-authorization"></a>認証と権限承認

REST API に対する HTTP 要求は Azure Active Directory (Azure AD) によって保護されます。
REST API に対して認証された要求を行うには、クライアント コードで有効な資格情報を使用して認証を行ってから API を呼び出す必要があります。 さまざまなアクター間の認証は、Azure AD によって調整されます。 認証の証拠としてクライアントにアクセス トークンが提供されます。 その後、REST API 要求の HTTP Authorization ヘッダーでトークンが送信されます。 Azure AD 認証に関する詳細については、[Azure Active Directory](https://portal.azure.com) に関する開発者向けページを参照してください。

アクセス トークンは、次のように後続の API 要求のヘッダー セクションに含めて送信する必要があります。

```http
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>HTTP 要求ヘッダー

ここでは、Azure FarmBeats Datahub への API 呼び出しを行うときに指定する必要がある最も一般的な要求ヘッダーを示します。


**ヘッダー** | **説明と例**
--- | ---
Content-Type  | 要求の形式 (Content-Type: application/<format>)。 Azure FarmBeats Datahub API の場合、形式は JSON です。 Content-Type, application/json
承認  | API 呼び出しを行うために必要なアクセス トークンを指定します。 Authorization:Bearer <Access-Token>
Accept | 応答形式。 Azure FarmBeats Datahub API の場合、形式は JSON です。 Accept: application/json

### <a name="api-requests"></a>API 要求

REST API 要求を行うには、HTTP (GET、POST、PUT、または DELETE) メソッド、API サービスの URL、(クエリ、データの送信、更新、または削除対象の) リソースの URI を結合し、1 つ以上の HTTP 要求ヘッダーを追加します。

API サービスの URL はデータ ハブの URL (例: https://\<yourdatahub-website-name>.azurewebsites.net) です。

必要に応じて、GET 呼び出しにクエリ パラメーターを追加して、応答のデータのフィルター処理、サイズ制限、並べ替えを行うことができます。

次の要求の例を使うと、デバイスの一覧を取得できます。

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

ほとんどの GET、POST、PUT 呼び出しには、JSON 要求本文が必要です。

次の要求の例を使うと、デバイスを作成できます。 この要求には、要求本文を含む入力 JSON が含まれています。

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>クエリ パラメーター

REST GET 呼び出しでは、要求 URI に 1 つ以上のクエリ パラメーターを含めることによって、API 応答データのフィルター処理、サイズ制限、および並べ替えを行うことができます。 クエリ パラメーターについては、API ドキュメントおよび個々の GET 呼び出しを参照してください。
たとえば、クエリを実行してデバイスの一覧を取得する場合 (/Device に対して GET 呼び出しを行う場合)、次のクエリ パラメーターを指定できます。

![デバイスの一覧](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>エラー処理

Azure FarmBeats Datahub API からは、標準の HTTP エラーが返されます。 最も一般的なエラー コードは次のとおりです。

 |エラー コード             | 説明 |
 |---                    | --- |
 |200                    | Success |
 |201                    | 作成 (Post) 成功 |
 |400                    | 正しくない要求。 要求にエラーがあります |
 |401                    | 権限がありません。 API の呼び出し元は、リソースにアクセスする特権がありません |
 |404                    | リソースが見つかりません |
 |5XX                    | 内部サーバー エラー。 5XX で始まるエラーコードは、サーバーで何らかのエラーが発生していることを意味します。 詳細については、サーバー ログと次のセクションを参照してください。 |


Azure FarmBeats Datahub API からは、標準の HTTP エラーに加えて、次の形式の内部エラーも返されます。

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

この例では、ファームの作成時に必須フィールド "Name" が入力ペイロードで指定されていませんでした。 結果のエラーメッセージは次のようになります。

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Azure Active Directory にユーザーまたはアプリ登録を追加する

Azure FarmBeats API は、Azure Active Directory のユーザーまたはアプリ登録からアクセスできます。 Azure Active Directory でアプリの登録を作成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) に移動し、 **[Azure Active Directory]**  >  **[アプリの登録]**  >  **[新規登録]** の順に選択します。 または、既存のアカウントを使用することができます。
2. 新しいアカウントの場合は、次の手順を実行します。

    - 名前を入力します。
    - **この組織ディレクトリのみに含まれるアカウント (シングル テナント)** を選択します。
    - その他のフィールドは、既定値のままにします。
    - **[登録]** を選択します。

3. 新規および既存のアプリの登録の **[概要]** ペインで、次の操作を行います。

    - **[クライアント ID]** と **[テナント ID]** を取得します。
    - **[証明書とシークレット]** にアクセスして新しいクライアント シークレットを生成し、 **[Client-Secret] (クライアントシークレット)** を取得します。
    - **[概要]** に戻り、 **[Manage Application in local directory]\(ローカルディレクトリ内のアプリケーションの管理\)** の横にあるリンクを選択します。
    - **[プロパティ]** に移動し、 **[オブジェクト ID]** を取得します。

4. Datahub Swagger (https://<yourdatahub>.azurewebsites.net/swagger/index.html) にアクセスし、次の手順を実行します。
    - **RoleAssignment API** に移動します。
    - POST を実行し、先ほど作成した **[オブジェクト ID]** の **RoleAssignment** オブジェクトを作成します。
 
```json
{
  "roleDefinitionId": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "objectId": "objectId from step 3 above",
  "objectIdType": "ServicePrincipalId",
  "tenantId": "tenant id of your Azure subscription"
}
```

  > [!NOTE]
  > ユーザーと Active Directory 登録の追加方法については、[Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md) に関する記事を参照してください。

前の手順を完了すると、ベアラー認証を介してアクセス トークンを使用することで、アプリの登録 (クライアント) から Azure FarmBeats API を呼び出すことができるようになります。

アクセス トークンは、次のようにして後続の API 要求のヘッダー セクションに含めて送信します。

```http
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
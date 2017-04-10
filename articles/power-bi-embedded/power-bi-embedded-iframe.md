---
title: "REST 経由で Power BI Embedded を使用する方法 | Microsoft Docs"
description: "REST 経由で Power BI Embedded を使用する方法を説明します。 "
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 8bcef780-cca0-4f30-9a9b-9daa1a7ae865
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 02/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 89e16687f858708cdfd1432114c39bd9109dc6ac
ms.openlocfilehash: 31624b9d15772a4f08cf013ac713b3aa636acfca
ms.lasthandoff: 02/07/2017


---
# <a name="how-to-use-power-bi-embedded-with-rest"></a>REST 経由で Power BI Embedded を使用する方法

## <a name="power-bi-embedded-what-it-is-and-what-its-for"></a>Power BI Embedded の概要と目的

Power BI Embedded の概要については、正式な説明を [Power BI Embedded のサイト](https://azure.microsoft.com/services/power-bi-embedded/)で参照できます。ここでは、Power BI Embedded と REST の使用に関する詳細に入る前に、Power BI Embedded について簡単に確認します。

きわめてシンプルというのは事実です。 独自のアプリケーションで [Power BI](https://powerbi.microsoft.com) の動的データ ビジュアライゼーションが必要になることはよくあります。

ほとんどのカスタム アプリケーションでは、組織内のユーザーに限らず、顧客のデータを提供する必要があります。 たとえば、会社 A と会社 B の両方にサービスを提供している場合、会社 A のユーザーには自分の会社 A のデータのみが表示される必要があります。つまり、配信するには、マルチ テナント機能が必要です。

カスタム アプリケーションで、フォーム認証や基本認証などの独自の認証方法が提供されていることもあります。 その場合、埋め込みソリューションが既存の認証方法と安全に連携している必要があります。 また、Power BI サブスクリプションを追加で購入したりライセンスを追加で取得したりしなくても、ユーザーがこれらの ISV アプリケーションを使用できる必要があります。

 **Power BI Embedded** は、まさにこのようなシナリオ向けに設計されています。 簡単な紹介は終わったので、これから詳細について確認しましょう

.NET \(C#) または Node.js SDK を使用すると、Power BI Embedded を使用してアプリケーションを簡単に作成できます。 ただし、この記事では、SDK なしの Power BI の HTTP フロー \(AuthN を含む) の概要について説明します。 このフローを理解すると、**任意のプログラミング言語で**アプリケーションを構築することができ、Power BI Embedded の本質を深く理解できるようになります。

## <a name="create-power-bi-workspace-collection-and-get-access-key-provisioning"></a>Power BI ワークスペース コレクションを作成してアクセス キーを取得する \(プロビジョニング)

Power BI Embedded は Azure のサービスの&1; つです。 Azure Portal を使用している ISV のみに \(1 時間のユーザー セッションごとに) 使用料が課金され、レポートを表示しているユーザーは課金されないだけでなく、Azure サブスクリプションさえ必要ありません。
アプリケーションの開発を開始する前に、Azure ポータルを使用して **Power BI のワークスペース コレクション** を作成する必要があります。

Power BI Embedded の各ワークスペースは、各顧客 (テナント) のワークスペースであり、各ワークスペース コレクションに多数のワークスペースを追加できます。 各ワークスペース コレクションで同じアクセス キーを使用します。 実質的に、ワークスペース コレクションは、Power BI Embedded のセキュリティ境界です。

![](media/power-bi-embedded-iframe/create-workspace.png)

ワークスペース コレクションの作成が完了したら、Azure ポータルからアクセス キーをコピーします。

![](media/power-bi-embedded-iframe/copy-access-key.png)

> [!NOTE]
> ワークスペース コレクションをプロビジョニングし、REST API 経由でアクセス キーを取得することもできます。 詳細については、「 [Power BI Resource Provider APIs (Power BI リソース プロバイダー API)](https://msdn.microsoft.com/library/azure/mt712306.aspx)」を参照してください。

## <a name="create-pbix-file-with-power-bi-desktop"></a>Power BI Desktop を使用して .pbix ファイルを作成する

次に、データ接続と、埋め込むレポートを作成する必要があります。
このタスクでは、プログラミングやコードは必要ありません。 使用するのは Power BI Desktop のみです。
この記事では、Power BI Desktop を使用する方法の詳細は説明しません。 サポートが必要な場合は、「 [Power BI Desktop の概要](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)」を参照してください。 こちらの例では、「 [小売の分析のサンプル](https://powerbi.microsoft.com/documentation/powerbi-sample-datasets/)」のみを使用します。

![](media/power-bi-embedded-iframe/power-bi-desktop-1.png)

## <a name="create-a-power-bi-workspace"></a>Power BI ワークスペースを作成する

プロビジョニングがすべて完了したので、REST API 経由でワークスペース コレクション内に顧客のワークスペースを作成しましょう。 次の HTTP POST 要求 (REST) は、既存のワークスペース コレクションに新しいワークスペースを作成します。 これは [POST ワークスペース API](https://msdn.microsoft.com/library/azure/mt711503.aspx) です。 この例のワークスペース コレクション名は **mypbiapp**です。 以前にコピーしたアクセス キーを、 **AppKey**として設定するだけです。 非常にシンプルな認証です。

**HTTP 要求**

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces
Authorization: AppKey MpaUgrTv5e...
```

**HTTP 応答**

```
HTTP/1.1 201 Created
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces
RequestId: 4220d385-2fb3-406b-8901-4ebe11a5f6da

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/$metadata#workspaces/$entity",
  "workspaceId": "32960a09-6366-4208-a8bb-9e0678cdbb9d",
  "workspaceCollectionName": "mypbiapp"
}
```

返された **workspaceId** は、次に示す後続の API 呼び出しに使用します。 アプリケーションでこの値を保持する必要があります。

## <a name="import-pbix-file-into-the-workspace"></a>.pbix ファイルをワークスペースにインポートする

ワークスペースの各レポートは、データセットを含む&1; つの Power BI Desktop ファイル \(データソースの設定も含まれます) に対応しています。 次のコードに示すように、.pbix ファイルをワークスペースにインポートできます。 ご覧のように、http で MIME マルチパートを使用して、.pbix ファイルのバイナリをアップロードできます。

URI フラグメント **32960a09-6366-4208-a8bb-9e0678cdbb9d** が workspaceId であり、クエリ パラメーター **datasetDisplayName** は作成するデータセットの名前です。 作成したデータセットには、インポートされたデータ、データ ソースを指すポインターなどの .pbix ファイルのすべてのデータ関連アーティファクトが保持されます。

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports?datasetDisplayName=mydataset01
Authorization: AppKey MpaUgrTv5e...
Content-Type: multipart/form-data; boundary="A300testx"

--A300testx
Content-Disposition: form-data

{the content (binary) of .pbix file}
--A300testx--
```

このインポート タスクの実行には、一定時間が必要になる場合があります。 完了すると、インポート ID を使用してアプリケーションからタスクの状態を問い合わせることができます。 この例では、インポート ID は **4eec64dd-533b-47c3-a72c-6508ad854659**です。

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659?tenantId=myorg
RequestId: 658bd6b4-b68d-4ec3-8818-2a94266dc220

{"id":"4eec64dd-533b-47c3-a72c-6508ad854659"}
```

次のようにこのインポート ID を使用して状態を問い合わせます。

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659
Authorization: AppKey MpaUgrTv5e...
```

タスクが完了していない場合、HTTP 応答は次のようになります。

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: 614a13a5-4de7-43e8-83c9-9cd225535136

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Publishing",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "name": "mydataset01"
}
```

タスクが完了した場合、HTTP 応答はおおよそ次のようになります。

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: eb2c5a85-4d7d-4cc2-b0aa-0bafee4b1606

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Succeeded",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "reports": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://app.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c"
    }
  ],
  "datasets": [
    {
      "id": "458e0451-7215-4029-80b3-9627bf3417b0",
      "name": "mydataset01",
      "tables": [
      ],
      "webUrl": "https://app.powerbi.com/datasets/458e0451-7215-4029-80b3-9627bf3417b0"
    }
  ],
  "name": "mydataset01"
}
```

## <a name="data-source-connectivity-and-multi-tenancy-of-data"></a>データ ソース接続 \(とデータのマルチ テナント)

.pbix ファイル内のほぼすべてのアーティファクトがワークスペースにインポートされますが、データ ソースの資格情報はインポートされません。 その結果、 **DirectQuery モード**を使用した場合、埋め込みレポートを正しく表示できません。 ただし、 **Import モード**を使用すると、インポート済みの既存のデータを使用してレポートを表示できます。 このような場合は、次の手順を使用して、REST 呼び出し経由で資格情報を設定する必要があります。

最初に、ゲートウェイのデータ ソースを取得する必要があります。 データセット **ID** とは、以前返された ID をです。

**HTTP 要求**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.GetBoundGatewayDatasources
Authorization: AppKey MpaUgrTv5e...
```

**HTTP 応答**

```
GET HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: 574b0b18-a6fa-46a6-826c-e65840cf6e15

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#gatewayDatasources",
  "value": [
    {
      "id": "5f7ee2e7-4851-44a1-8b75-3eb01309d0ea",
      "gatewayId": "ca17e77f-1b51-429b-b059-6b3e3e9685d1",
      "datasourceType": "Sql",
      "connectionDetails": "{\"server\":\"testserver.database.windows.net\",\"database\":\"testdb01\"}"
    }
  ]
}
```

返されたゲートウェイ ID とデータ ソース ID を使用して \(返された結果にある、以前の **gatewayId** および **id** を参照)、このデータ ソースの資格情報を次のように変更できます。

**HTTP 要求**

```
PATCH https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/gateways/ca17e77f-1b51-429b-b059-6b3e3e9685d1/datasources/5f7ee2e7-4851-44a1-8b75-3eb01309d0ea
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "credentialType": "Basic",
  "basicCredentials": {
    "username": "demouser",
    "password": "P@ssw0rd"
  }
}
```

**HTTP 応答**

```
HTTP/1.1 200 OK
Content-Type: application/octet-stream
RequestId: 0e533c13-266a-4a9d-8718-fdad90391099
```

運用環境では、REST API を使用してワークスペースごとに異なる接続文字列を設定できます。 \(つまり顧客ごとにデータベースを分離できます)。

次のようにして、REST 経由でデータ ソースの接続文字列を変更します。

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.SetAllConnections
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "connectionString": "data source=testserver02.database.windows.net;initial catalog=testdb02;persist security info=True;encrypt=True;trustservercertificate=False"
}
```

または、Power BI Embedded はで行レベルのセキュリティを使用できるため、1 つのレポートでユーザーごとにデータを分離できます。 その結果、同じ .pbix \(UI など) と異なるデータ ソースを使用して、すべての顧客レポートをプロビジョニングすることができます。

> [!NOTE]
> **DirectQuery モード**ではなく **Import モード** を使用している場合は、API 経由でモデルを更新することができません。 また、Power BI ゲートウェイを介するオンプレミスのデータ ソースは、Power BI embedded ではまだサポートされていません。 ただし、新機能と今後のリリースで予定されている機能については、 [Power BI ブログ](https://powerbi.microsoft.com/blog/) にご注目ください。

## <a name="authentication-and-hosting-embedding-reports-in-our-web-page"></a>Web ページでの認証と (埋め込み) レポートのホスティング

以前の REST API では、アクセス キー **AppKey** 自体を承認ヘッダーとして使用できます。 これらの呼び出しは、バックエンド サーバー側で処理されるため、安全です。

ただし、Web ページにレポートを埋め込むときは、JavaScript \(フロント エンド) を使用してこの種のセキュリティ情報を処理します。 この場合は、承認ヘッダーの値をセキュリティで保護する必要があります。 悪意のあるユーザーや悪意のあるコードによってアクセス キーが検出されると、アクセス キーが操作の呼び出しに使用される可能性があります。

Web ページにレポートを埋め込むときは、アクセス キー **AppKey**ではなく、計算されたトークンを使用する必要があります。 アプリケーションでは、要求と計算されたデジタル署名で構成される、OAuth JSON Web トークン \(JWT) を作成する必要があります。 下図のように、この OAuth JWT は、ドットで区切られ、エンコードされた文字列トークンです。

![](media/power-bi-embedded-iframe/oauth-jwt.png)

最初に、入力値を準備する必要があります。 この値は、次の JSON の Base64 でエンコードされた URL (RFC4648) 文字列であり、ドット \(.) 文字で区切られます。 後ほど、レポート ID を取得する方法について説明します。

> [!NOTE]
> Power BI Embedded と行レベル セキュリティ (RLS) を使用する場合、要求で**ユーザー名**と**ロール**も指定する必要があります。

```
{
  "typ":"JWT",
  "alg":"HS256"
}
```

```
{
  "wid":"{workspace id}",
  "rid":"{report id}",
  "wcn":"{workspace collection name}",
  "iss":"PowerBISDK",
  "ver":"0.2.0",
  "aud":"https://analysis.windows.net/powerbi/api",
  "nbf":{start time of token expiration},
  "exp":{end time of token expiration}
}
```

次に、SHA256 アルゴリズムを使用して、HMAC \(シグネチャ) の Base64 でエンコードされた文字列を作成する必要があります。 この署名された入力値が、先ほどの文字列です。

最後に、ピリオド \(.) 文字を使用して入力値と署名文字列を組み合わせる必要があります。 完成した文字列が、レポートに埋め込むためのアプリケーション トークンです。 悪意のあるユーザーがアプリケーション トークンを検出したとしても、元のアクセス キーを取得することはできません。 このアプリケーション トークンの有効期限はすぐに切れます。

これらの手順の PHP の例を次に示します。

```
<?php
// 1. power bi access key
$accesskey = "MpaUgrTv5e...";

// 2. construct input value
$token1 = "{" .
  "\"typ\":\"JWT\"," .
  "\"alg\":\"HS256\"" .
  "}";
$token2 = "{" .
  "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
  "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
  "\"wcn\":\"mypbiapp\"," . // workspace collection name
  "\"iss\":\"PowerBISDK\"," .
  "\"ver\":\"0.2.0\"," .
  "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
  "\"nbf\":" . date("U") . "," .
  "\"exp\":" . date("U" , strtotime("+1 hour")) .
  "}";
$inputval = rfc4648_base64_encode($token1) .
  "." .
  rfc4648_base64_encode($token2);

// 3. get encoded signature
$hash = hash_hmac("sha256",
    $inputval,
    $accesskey,
    true);
$sig = rfc4648_base64_encode($hash);

// 4. show result (which is the apptoken)
$apptoken = $inputval . "." . $sig;
echo($apptoken);

// helper functions
function rfc4648_base64_encode($arg) {
  $res = $arg;
  $res = base64_encode($res);
  $res = str_replace("/", "_", $res);
  $res = str_replace("+", "-", $res);
  $res = rtrim($res, "=");
  return $res;
}
?>
```

## <a name="finally-embed-the-report-into-the-web-page"></a>最後にレポートを Web ページに埋め込む

レポートを埋め込むには、次の REST API を使用して、埋め込み URL とレポート **ID** を取得する必要があります。

**HTTP 要求**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/reports
Authorization: AppKey MpaUgrTv5e...
```

**HTTP 応答**

```
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: d4099022-405b-49d3-b3b7-3c60cf675958

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#reports",
  "value": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c",
      "isFromPbix": false
    }
  ]
}
```

先ほどのアプリケーション トークンを使用して、レポートを Web アプリケーションに埋め込むことができます。
次のサンプル コードは、前半部分が前の例と同じであることがわかります。 後半部分では、このサンプルは Iframe の **embedUrl** \(先ほどの結果を参照) を示しており、アプリケーション トークンを Iframe に転記しています。

> [!NOTE]
> レポート ID の値は独自の値に変更する必要があります。 また、コンテンツ管理システムの問題により、コード サンプルの Iframe タグは文字どおり読み取られます。 このサンプル コードをコピーして貼り付ける場合は、大文字で記述されているテキストをタグから削除してください。

```
    <?php
    // 1. power bi access key
    $accesskey = "MpaUgrTv5e...";

    // 2. construct input value
    $token1 = "{" .
      "\"typ\":\"JWT\"," .
      "\"alg\":\"HS256\"" .
      "}";
    $token2 = "{" .
      "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
      "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
      "\"wcn\":\"mypbiapp\"," . // workspace collection name
      "\"iss\":\"PowerBISDK\"," .
      "\"ver\":\"0.2.0\"," .
      "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
      "\"nbf\":" . date("U") . "," .
      "\"exp\":" . date("U" , strtotime("+1 hour")) .
      "}";
    $inputval = rfc4648_base64_encode($token1) .
      "." .
      rfc4648_base64_encode($token2);

    // 3. get encoded signature value
    $hash = hash_hmac("sha256",
        $inputval,
        $accesskey,
        true);
    $sig = rfc4648_base64_encode($hash);

    // 4. get apptoken
    $apptoken = $inputval . "." . $sig;

    // helper functions
    function rfc4648_base64_encode($arg) {
      $res = $arg;
      $res = base64_encode($res);
      $res = str_replace("/", "_", $res);
      $res = str_replace("+", "-", $res);
      $res = rtrim($res, "=");
      return $res;
    }
    ?>
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>Test page</title>
      <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    <body>
      <button id="btnView">View Report !</button>
      <div id="divView">
        <**REMOVE THIS CAPPED TEXT IF COPIED** iframe id="ifrTile" width="100%" height="400"></iframe>
      </div>
      <script>
        (function () {
          document.getElementById('btnView').onclick = function() {
            var iframe = document.getElementById('ifrTile');
            iframe.src = 'https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c';
            iframe.onload = function() {
              var msgJson = {
                action: "loadReport",
                accessToken: "<?=$apptoken?>",
                height: 500,
                width: 722
              };
              var msgTxt = JSON.stringify(msgJson);
              iframe.contentWindow.postMessage(msgTxt, "*");
            };
          };
        }());
      </script>
    </body>
```

結果を次に示します。

![](media/power-bi-embedded-iframe/view-report.png)

現時点で、Power BI Embedded では、Iframe のレポートのみが表示されます。 ただし、[Power BI ブログ](https://powerbi.microsoft.com/blog/)に今後もご注目ください。 今後の機能強化により、新しいクライアント側の API を使用できます。この API では、Iframe に情報を送信するだけでなく、情報を取得することもできます。 魅力的な機能です。

## <a name="see-also"></a>関連項目
* [Power BI Embedded での認証と承認](power-bi-embedded-app-token-flow.md)

ご質問は、 [Power BI コミュニティ](http://community.powerbi.com/)で尋ねてみてください。



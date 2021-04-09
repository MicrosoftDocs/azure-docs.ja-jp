---
title: Azure Active Directory SMART on FHIR プロキシ
description: このチュートリアルでは、プロキシを使用して Azure API for FHIR で SMART on FHIR アプリケーションを有効にする方法について説明します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 04/02/2019
ms.openlocfilehash: 2e13a9fc32964781dda07e5534e5cab79868ddf0
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019451"
---
# <a name="tutorial-azure-active-directory-smart-on-fhir-proxy"></a>チュートリアル:Azure Active Directory SMART on FHIR プロキシ

[SMART on FHIR](https://docs.smarthealthit.org/) は、パートナー アプリケーションを FHIR サーバーや FHIR インターフェイス搭載の電子医療記録システムと統合するための一連のオープンな仕様です。 この仕様の主な目的の 1 つは、アプリケーションが FHIR サーバーの認証エンドポイントを検出して認証シーケンスを開始する方法を説明することです。 

認証は、OAuth2 に基づいています。 ただし、SMART on FHIR では Azure Active Directory (Azure AD) と直接互換性のないパラメーターの名前付け規則が使用されるため、Azure API for FHIR には SMART on FHIR 起動シーケンスのサブセットを有効にする組み込みの Azure AD SMART on FHIR プロキシが用意されています。 具体的には、このプロキシによって [EHR 起動シーケンス](https://hl7.org/fhir/smart-app-launch/#ehr-launch-sequence)が有効になります。

このチュートリアルでは、このプロキシを使用して Azure API for FHIR で SMART on FHIR アプリケーションを有効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure API for FHIR のインスタンス
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

## <a name="configure-azure-ad-registrations"></a>Azure AD の登録を構成する

SMART on FHIR では、`Audience` の識別子 URI が FHIR サービスの URI と同じである必要があります。 Azure API for FHIR の標準構成では、`Audience` 値として `https://azurehealthcareapis.com` が使用されます。 ただし、FHIR サービスの特定の URL (`https://MYFHIRAPI.azurehealthcareapis.com` など) と一致する値を設定することもできます。 これは、SMART on FHIR プロキシを操作する際に必要になります。

また、クライアント アプリケーションの登録も必要になります。 ほとんどの SMART on FHIR アプリケーションは、シングルページの JavaScript アプリケーションです。 そのため、[パブリック Azure AD クライアント アプリケーション](register-public-azure-ad-client-app.md)を構成するための手順に従う必要があります。

これらの手順を完了すると、次のものが準備できたことになります。

- rge オーディエンスが `https://MYFHIRAPI.azurehealthcareapis.com` に設定された FHIR サーバー。ここで、`MYFHIRAPI` は Azure API for FHIR インスタンスの名前です。
- パブリック クライアント アプリケーションの登録。 このクライアント アプリケーションのアプリケーション ID を書き留めておきます。

## <a name="enable-the-smart-on-fhir-proxy"></a>SMART on FHIR プロキシを有効にする

Azure API for FHIR インスタンスの **[認証]** 設定で SMART on FHIR プロキシを有効にするには、 **[SMART on FHIR proxy]\(SMART on FHIR プロキシ\)** チェック ボックスをオンにします。

![SMART on FHIR プロキシを有効にするための選択](media/tutorial-smart-on-fhir/enable-smart-on-fhir-proxy.png)

## <a name="enable-cors"></a>CORS を有効にする

ほとんどの SMART on FHIR アプリケーションはシングルページの JavaScript アプリであるため、Azure API for FHIR に対して[クロスオリジン リソース共有 (CORS) を有効にする](configure-cross-origin-resource-sharing.md)必要があります。

![CORS を有効にするための選択](media/tutorial-smart-on-fhir/enable-cors.png)

## <a name="configure-the-reply-url"></a>応答 URL を構成する

SMART on FHIR プロキシは、SMART on FHIR アプリと Azure AD の仲介役として機能します。 認証応答 (認証コード) は、アプリ自体ではなく、SMART on FHIR プロキシに送信される必要があります。 その後、応答はプロキシによってアプリに転送されます。 

この 2 段階の認証コードのリレーのため、Azure AD クライアント アプリケーションの応答 URL (コールバック) を、SMART on FHIR プロキシの応答 URL と SMART on FHIR アプリの応答 URL を組み合わせた URL に設定する必要があります。 結合された応答 URL は次の形式になります。

```http
https://MYFHIRAPI.azurehealthcareapis.com/AadSmartOnFhirProxy/callback/aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA
```

この応答で、`aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA` は SMART on FHIR アプリの応答 URL であり、URL セーフな base64 エンコードのバージョンです。 SMART on FHIR アプリ起動ツールの場合、アプリがローカルで実行されていると、応答 URL は `https://localhost:5001/sampleapp/index.html` になります。 

次のようなスクリプトを使用すると、結合された応答 URL を生成できます。

```PowerShell
$replyUrl = "https://localhost:5001/sampleapp/index.html"
$fhirServerUrl = "https://MYFHIRAPI.azurewebsites.net"
$bytes = [System.Text.Encoding]::UTF8.GetBytes($ReplyUrl)
$encodedText = [Convert]::ToBase64String($bytes)
$encodedText = $encodedText.TrimEnd('=');
$encodedText = $encodedText.Replace('/','_');
$encodedText = $encodedText.Replace('+','-');

$newReplyUrl = $FhirServerUrl.TrimEnd('/') + "/AadSmartOnFhirProxy/callback/" + $encodedText
```

Azure AD 用に先ほど作成したパブリック クライアント アプリケーションにこの応答 URL を追加します。

![パブリック クライアント用に構成された応答 URL](media/tutorial-smart-on-fhir/configure-reply-url.png)

## <a name="get-a-test-patient"></a>テスト患者を取得する

Azure API for FHIR と SMART on FHIR プロキシをテストするには、データベースに少なくとも 1 人の患者が必要です。 まだ API を操作したことがなく、データベースにデータがない場合は、[FHIR API Postman チュートリアル](access-fhir-postman-tutorial.md)に従って患者を読み込んでください。 特定の患者の ID を書き留めておきます。

## <a name="download-the-smart-on-fhir-app-launcher"></a>SMART on FHIR アプリ起動ツールをダウンロードする

オープンソースの [FHIR Server for Azure リポジトリ](https://github.com/Microsoft/fhir-server)には、簡単な SMART on FHIR アプリ起動ツールとサンプルの SMART on FHIR アプリが含まれています。 このチュートリアルでは、この SMART on FHIR 起動ツールをローカルで使用して設定をテストします。

次のコマンドを使用して、GitHub リポジトリをクローンし、アプリケーションに移動できます。

```PowerShell
git clone https://github.com/Microsoft/fhir-server
cd fhir-server/samples/apps/SmartLauncher
```

このアプリケーションには、いくつかの構成設定が必要です。それは、`appsettings.json` で設定できます。

```json
{
    "FhirServerUrl": "https://MYFHIRAPI.azurehealthcareapis.com",
    "ClientId": "APP-ID",
    "DefaultSmartAppUrl": "/sampleapp/launch.html"
}
```

`dotnet user-secrets` 機能を使用することをお勧めします。

```PowerShell
dotnet user-secrets set FhirServerUrl https://MYFHIRAPI.azurehealthcareapis.com
dotnet user-secrets set ClientId <APP-ID>
```

次のコマンドを使用してアプリケーションを実行します。

```PowerShell
dotnet run
```

## <a name="test-the-smart-on-fhir-proxy"></a>SMART on FHIR プロキシをテストする

SMART on FHIR アプリ起動ツールを起動した後、ブラウザーで `https://localhost:5001` に移動できます。ここには、次の画面が表示されます。

![SMART on FHIR アプリ起動ツール](media/tutorial-smart-on-fhir/smart-on-fhir-app-launcher.png)

**[Patient]\(患者\)** 、 **[Encounter]\(外来診察\)** 、または **[Practitioner]\(開業医\)** の情報を入力すると、 **[Launch context]\(起動コンテキスト\)** が更新されたことがわかります。 Azure API for FHIR を使用している場合、起動コンテキストは、患者や開業医などの情報を含む JSON ドキュメントにすぎません。 この起動コンテキストは base64 でエンコードされており、`launch` クエリ パラメーターとして SMART on FHIR アプリに渡されます。 SMART on FHIR 仕様によれば、この変数は SMART on FHIR アプリに対して非透過的であり、ID プロバイダーに渡されます。 

SMART on FHIR プロキシでは、この情報を使用して、トークン応答のフィールドが設定されます。 SMART on FHIR アプリでは、これらのフィールドを使用して、データの要求対象となる患者と画面上でのアプリケーションの表示方法を制御 "*できます*"。 SMART on FHIR プロキシでは、以下のフィールドがサポートされています。

* `patient`
* `encounter`
* `practitioner`
* `need_patient_banner`
* `smart_style_url`

これらのフィールドは、アプリにガイダンスを提供することを意図しており、セキュリティ情報を伝達するものではありません。 SMART on FHIR アプリケーションでは、これらを無視できます。

SMART on FHIR アプリ起動ツールによって、ページ下部にある **[Launch URL]\(起動 URL\)** 情報が更新されることに注意してください。 **[Launch]\(起動\)** を選択してサンプル アプリを起動すると、次のサンプルのように表示されるはずです。

![SMART on FHIR アプリ](media/tutorial-smart-on-fhir/smart-on-fhir-app.png)

トークン応答を調べて、起動コンテキスト フィールドがどのようにアプリに渡されたかを確認します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Active Directory SMART on FHIR プロキシを構成しました。 Azure API for FHIR およびオープンソースの FHIR Server for Azure での SMART on FHIR アプリケーションの使用について調べるには、GitHub にある FHIR サーバー サンプルのリポジトリを参照してください。

>[!div class="nextstepaction"]
>[FHIR サーバーのサンプル](https://github.com/Microsoft/fhir-server-samples)

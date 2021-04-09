---
title: Azure Active Directory B2C と Jumio を構成するチュートリアル
titleSuffix: Azure AD B2C
description: このチュートリアルでは、自動 ID 検証を実現し、顧客データを保護するために Azure Active Directory B2C と Jumio を構成します。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 66ec0d4b09dc983eb898d63d45b3dd7cab291c4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96928666"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Jumio と Azure Active Directory B2C を構成するチュートリアル

このサンプル チュートリアルでは、Azure Active Directory B2C (Azure AD B2C) を [Jumio](https://www.jumio.com/) と統合する方法についてのガイダンスを提供します。 Jumio は、顧客データの保護に役立つリアルタイムの自動 ID 検証を可能にする ID 検証サービスです。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](./tutorial-create-tenant.md)。

## <a name="scenario-description"></a>シナリオの説明

Jumio 統合には、次のコンポーネントが含まれています。

- Azure AD B2C:ユーザーの資格情報の検証を担当する承認サーバー。 ID プロバイダーとも呼ばれます。

- Jumio:ユーザーから提供された ID の詳細を取得して検証するサービス。

- 中間 Rest API:Azure AD B2C と Jumio サービスとの間の統合を実装する API。

- Azure Blob ストレージ:Azure AD B2C ポリシーにカスタム UI ファイルを提供するサービス。

次のアーキテクチャの図に、この実装を示します。

![Jumio と統合された Azure AD B2C のアーキテクチャの図。](./media/partner-jumio/jumio-architecture-diagram.png)

|手順 | 説明 |
|:-----| :-----------|
| 1. | ユーザーは、サインインするかサインアップしてアカウントを作成するためのページに到達します。 Azure AD B2C でユーザー属性が収集されます。
| 2. | Azure AD B2C で中間層 API が呼び出され、ユーザー属性が渡されます。
| 3. | 中間層 API により、ユーザー属性が収集され、Jumio API が使用できる形式に変換されます。 次に、その属性が Jumio に送信されます。
| 4. | Jumio でその情報が使用され、処理された後に、中間層 API に結果が返されます。
| 5. | 中間層 API によりその情報が処理され、関連情報が Azure AD B2C に送り返されます。
| 6. | Azure AD B2C が、中間層 API から情報を受信します。 それが失敗応答である場合、ユーザーにエラー メッセージが表示されます。 成功応答が表示された場合、ユーザーは認証され、ディレクトリに書き込まれます。

## <a name="sign-up-with-jumio"></a>Jumio を使用してサインアップする

Jumio アカウントを作成する方法については、[Jumio](https://www.jumio.com/contact/) にお問い合わせください。

## <a name="configure-azure-ad-b2c-with-jumio"></a>Azure AD B2C と Jumio を構成する

Jumio アカウントを作成したら、そのアカウントを使用して Azure AD B2C を構成します。 以下のセクションでは、そのプロセスについて順番に説明します。

### <a name="deploy-the-api"></a>API をデプロイする

提供された [API コード](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api)を Azure サービスにデプロイします。 このコードは、[これらの手順](/visualstudio/deployment/quickstart-deploy-to-azure)に従って、Visual Studio から発行することができます。

>[!NOTE]
>必要な設定で Azure AD を構成するには、デプロイされたサービスの URL が必要です。

### <a name="deploy-the-client-certificate"></a>クライアント証明書をデプロイする

1. クライアント証明書は Jumio API 呼び出しの保護に役立ちます。 次の PowerShell サンプル コードを使用して、自己署名証明書を作成します。

   ``` PowerShell
   $cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
   $cert.Thumbprint
   $pwdText = "Your password"
   $pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
   Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

   ```

   これで、証明書は ``{your-local-path}`` に指定された場所にエクスポートされます。

3. [この記事](../app-service/configure-ssl-certificate.md#upload-a-private-certificate)の手順に従って、証明書を Azure App Service にインポートします。

### <a name="create-a-signingencryption-key"></a>署名キーと暗号化キーを作成する

文字と数字のみを含む 64 文字を超える長さのランダムな文字列を作成します。

例: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

次の PowerShell スクリプトを使用して、文字列を作成します。

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="configure-the-api"></a>API を構成する

[Azure App Service でアプリケーション設定を構成する](../app-service/configure-common.md#configure-app-settings)ことができます。 この方法では、設定をリポジトリにチェックインすることなく、安全に構成できます。 REST API に次の設定を指定する必要があります。

| アプリケーションの設定 | source | Notes |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Jumio アカウントの構成 |     |
|JumioSettings:AuthPassword | Jumio アカウントの構成 |     |
|AppSettings:SigningCertThumbprint|作成された自己署名証明書の拇印|  |
|AppSettings:IdTokenSigningKey| PowerShell を使用して作成された署名キー | |
| AppSettings:IdTokenEncryptionKey |PowerShell を使用して作成された暗号化キー
| AppSettings:IdTokenIssuer | JWT トークンに使用される発行者 (GUID 値が優先されます) |
| AppSettings:IdTokenAudience  | JWT トークンに使用される対象ユーザー (GUID 値が優先されます) |
|AppSettings:BaseRedirectUrl | Azure AD B2C ポリシーのベース URL | https://{your-tenant-name}.b2clogin.com/{your-application-id}|
| WEBSITE_LOAD_CERTIFICATES| 作成された自己署名証明書の拇印 |

### <a name="deploy-the-ui"></a>UI をデプロイする

1. [ストレージ アカウントで BLOB ストレージ コンテナー](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)を設定します。

2. UI ファイルを [UI フォルダー](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI)からお使いの BLOB コンテナーに格納します。

#### <a name="update-ui-files"></a>UI ファイルを更新する

1. UI ファイルで、[ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue) フォルダーに移動します。

2. 各 HTML ファイルを開きます。

3. `{your-ui-blob-container-url}` を見つけて、お使いの BLOB コンテナーの URL に置き換えます。

4. `{your-intermediate-api-url}` を見つけて、中間 API App Service の URL に置き換えます。

>[!NOTE]
> ベスト プラクティスとして、属性コレクション ページで同意通知を追加することをお勧めします。 ID 検証のために情報がサードパーティのサービスに送信されることをユーザーに通知します。

### <a name="configure-the-azure-ad-b2c-policy"></a>Azure AD B2C ポリシーを構成する

1. ポリシー フォルダーの [Azure AD B2C ポリシー](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies)に移動します。

2. [この記事](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack)に従って、[LocalAccounts スターター パック](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)をダウンロードします。

3. Azure AD B2C テナントのポリシーを構成します。

>[!NOTE]
>お使いの特定のテナントに関連付けるため、提供されているポリシーを更新してください。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. Azure AD B2C テナントを開きます。 **[ポリシー]** で **[Identity Experience Framework]** を選択します。

2. 以前に作成した **SignUpSignIn** を選択します。

3. **[ユーザー フローを実行します]** を選択して、次を行います。

   a. **[アプリケーション]** の場合は、登録済みのアプリを選択します (サンプルは JWT)。

   b. **[応答 URL]** の場合は、**リダイレクト URL** を選択します。

   c. **[ユーザー フローを実行します]** を選択します。

4. サインアップ フローを実行し、アカウントを作成します。

5. Jumio サービスは、ユーザー属性が作成された後、フロー中に呼び出されます。 フローが不完全な場合は、ユーザーがディレクトリに保存されていないことを確認してください。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./custom-policy-get-started.md?tabs=applications)

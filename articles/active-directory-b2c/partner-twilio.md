---
title: Twilio Verify App と Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Azure AD B2C のサンプル オンライン決済アプリを Twilio Verify API と統合する方法について説明します。 動的リンクと強力な顧客認証によって、PSD2 (Payment Services Directive 2) のトランザクション要件に準拠することができます。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 953653a758577ed3d48ca2d81403b4cb363ea294
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994045"
---
# <a name="integrating-twilio-verify-app-with-azure-active-directory-b2c"></a>Twilio Verify App と Azure Active Directory B2C の統合

このチュートリアルでは、Azure Active Directory B2C (Azure AD B2C) のサンプル オンライン決済アプリを Twilio Verify API と統合する方法について説明します。 Twilio Verify App を使用することで、Azure AD B2C のお客様は、動的リンクと強力な顧客認証によって、PSD2 (Payment Services Directive 2) のトランザクション要件に準拠することができます。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](tutorial-create-tenant.md)。
* Twilio の[試用版アカウント](https://www.twilio.com/try-twilio)。

## <a name="scenario-description"></a>シナリオの説明

この Twilio ソリューションは、次のコンポーネントで構成されています。

- サインインまたはサインアップし、ダミーの高リスクのトランザクションを実行できる、.NET [PSD2 デモ Web アプリ](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App)。
- Azure AD B2C を組み合わせた[サインインとサインアップ ポリシー](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy)。
- `id_token_hint` を使用して Twilio Verify API と統合された Azure AD B2C ポリシー。
- `.well-known` OpenId Connect エンドポイントをホストして、`id_token_hint` の検証を可能にする .NET Web アプリ。


    ![Twilio フロー](media/partner-twilio/twilio-flow.png)

| 手順 | 説明 |
|------|------|
| 1     | ユーザーは、PSD2 デモ アプリへのサインインまたはサインアップを開始します。 ユーザーは、サインインとサインアップを組み合わせた Azure AD B2C ポリシーを使用して認証されます。 アプリケーションにトークンが返されます。 サインアップ時に、SMS および携帯電話を使用してユーザーの電話番号が確認され、Azure AD B2C アカウントに記録されます。     |
| 2     | ユーザーは、50.00 ドルの送金など、高リスクのトランザクションを開始します。 PolicyId について、ユーザーの現在のアクセス トークンが評価され、ユーザーがステップアップ カスタム ポリシーを使用して既に認証されているかどうかが確認されます。     |
| 3     | アプリケーションは、取引額 (50.00 ドル) と受取人 (John Doe) を記録し、署名済みトークンを生成します。 このトークンは `id_token_hint` と呼ばれ、要求 `amount:$500, payee:john doe` が含まれています。 `id_token_hint` は、Twilio と統合された Azure AD B2C カスタム ポリシーに要求と共に送信されます。     |
| 4     | Azure AD B2C は、アプリケーションの `/.well-known` OpenId Connect エンドポイントを確認して、id_token_hint の署名を検証します。 検証後、このトークンから要求 (特に、`amount` と `payee`) が抽出されます。 ユーザーには、SMS メッセージによって携帯電話番号を確認するためのページが表示されます。     |
| 5     | ユーザーが SMS メッセージによる電話番号の確認を要求すると、Azure AD B2C は Twilio の Verify API エンドポイントに対して REST API 要求を行います。 また、PSD2 プロセスの一環として、トランザクションの `amount` と `payee` を送信して、ワンタイム パスコード (OTP) を生成します。 Twilio は、ユーザーの登録済み電話番号に SMS メッセージを送信します。     |
| 6     |  ユーザーは SMS メッセージで受け取った OTP を入力し、Azure AD B2C に送信します。 Azure AD B2C は、Twilio の Verify API に対して、この OTP を使用した API 要求を行い、OTP が正しいことを確認します。 最後に、ユーザーが認証をステップアップしたことを示す新しい PolicyId を含むトークンがアプリケーションに発行されます。    |
|      |      |

## <a name="onboard-with-twilio"></a>Twilio を使用してオンボードする

1. Twilio で[試用版アカウント](https://www.twilio.com/try-twilio)を取得します。

2. [こちらの記事](https://support.twilio.com/hc/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console)の説明に従って、Twilio で電話番号を購入します。

3. Twilio コンソールで [Verify API](https://www.twilio.com/console/verify/services) に移動します。[指示](https://www.twilio.com/docs/verify/verifying-transactions-psd2)に従ってサービスを作成し、PSD2 オプションを有効にします。  

## <a name="configure-the-psd2-demo-app"></a>PSD2 デモ アプリを構成する

1. B2C-WebAPI-DotNet ソリューションを開き、web.config で次の値を独自のテナント固有の値に置き換えます。

    ```xml
   <add key="ida:Tenant" value="yourtenant.onmicrosoft.com" />
   <add key="ida:TenantId" value="d6f33888-0000-4c1f-9b50-1590f171fc70" />
   <add key="ida:ClientId" value="6bd98cc8-0000-446a-a05e-b5716ef2651b" />
   <add key="ida:ClientSecret" value="secret" />
   <add key="ida:AadInstance" value="https://yourtenant.b2clogin.com/tfp/{0}/{1}" />
   <add key="ida:RedirectUri" value="https://your hosted psd2 demo app url/" />
   ```

2. [Web アプリ](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App)では、ID トークン ヒント ジェネレーターおよびメタデータ エンドポイントもホストされます。
   - こちらの[サンプルの説明](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#creating-a-signing-certificate)に従って署名証明書を作成します。
   - 証明書に基づいて、web.config の次の行を更新します。
   
   ```xml
   <add key="ida:SigningCertThumbprint" value="4F39D6014818082CBB763E5BA5F230E545212E89" />
   <add key="ida:SigningCertAlgorithm" value="RS256" />
   ```

3. 選択したホスティング プロバイダーにデモ アプリケーションをアップロードします。 証明書をアップロードする手順など、Azure App Service のガイダンスについては、 [こちらのサンプルの説明](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#hosting-the-application-in-azure-app-service)をご覧ください。

4. アプリケーションがホストされている URL に対応する応答 URL を追加して、Azure AD B2C アプリケーションの登録を更新します。

5. [ポリシー ファイル](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy)を開き、 `contoso` のすべてのインスタンスを実際のテナント名に置き換えます。

6. Twilio REST API 技術プロファイル  **Custom-SMS-Enroll** を見つけます。  `ServiceURL`  を Twilio AccountSID  で更新し、From 番号を購入した電話番号に更新します。

7. Twilio REST API 技術プロファイル  **TwilioRestAPI-Verify-Step1**  および  **TwilioRestAPI-Verify-Step2** を見つけ、 `ServiceURL`  を Twilio AccountSID で更新 します。

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C との統合

ポリシー ファイルを Azure AD B2C に追加します。

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。

2. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。

3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。

4. **[Azure AD B2C]**  >  **[Identity Experience Framework]**  >  **[ポリシー キー]** の順に移動します。

5. **B2cRestTwilioClientId** という名前の新しいキーを追加します。  **[手動]** を選択し Twilio AccountSID の値を入力します。

6. **B2cRestTwilioClientSecret** という名前の新しいキーを追加します。  **[手動]** を選択し Twilio 認証トークンの値を入力します。

7. すべてのポリシー ファイルをテナントにアップロードします。

8. サインアップ SMS テキストの GenerateOTPMessageEnrol  要求変換の文字列をカスタマイズします。

## <a name="test-the-solution"></a>ソリューションをテストする

* アプリケーションを参照し、サインイン、サインアップ、送金の各アクションをテストします。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Twilio 統合のコード サンプル](https://github.com/azure-ad-b2c/samples/tree/master/policies/twilio-mfa-psd2)については、GitHub を参照してください  

- [AAD B2C のカスタム ポリシー](custom-policy-overview.md)

- [AAD B2C のカスタム ポリシーの概要](custom-policy-get-started.md?tabs=applications)

---
title: Microsoft Dynamics 365 Fraud Protection で Azure Active Directory B2C を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: リスクのある不正なアカウントを特定するために、Microsoft Dynamics 365 Fraud Protection で Azure Active Directory B2C を構成するためのチュートリアル
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: cf441108c9fd0ae87f265604f6f0706d92516746
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646556"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C を使用した Microsoft Dynamics 365 Fraud Protection の構成

このサンプル チュートリアルでは、[Microsoft Dynamics 365 Fraud Protection](/dynamics365/fraud-protection/overview) (DFP) を Azure Active Directory (AD) B2C と統合する方法についてのガイダンスを提供します。

Microsoft DFP は、新規アカウントの作成やクライアントのエコシステムへのログインが不正なものであるかどうかを評価する機能をクライアントに提供します。 Microsoft DFP での評価は、新しい偽のアカウントを作成したり、既存のアカウントを侵害したりする疑いのある試みを、ブロックまたはチャレンジするために使用できます。 アカウント保護には、人工知能によるデバイス フィンガープリント、リアルタイム リスク評価用の API、クライアントのビジネス ニーズに応じてリスク戦略を最適化するためのルールとリストのエクスペリエンス、クライアントのエコシステムで不正行為の防止の有効性と傾向を監視するためのスコアカードが含まれます。

このサンプルでは、Microsoft DFP のアカウント保護機能と Azure AD B2C ユーザー フローを統合します。 このサービスは、サインインまたはサインアップが行われるたびに、外部でフィンガープリントを確認して、過去または現在の疑わしい動作を監視します。 Azure AD B2C は、Microsoft DFP からデシジョン エンドポイントを呼び出します。このエンドポイントは、識別されたユーザーによる過去および現在のすべての動作に基づいて結果を返します。また、Microsoft DFP サービス内で指定されたカスタム ルールも返します。 Azure AD B2C は、この結果に基づいて承認決定を行い、Microsoft DFP に同じ決定を渡します。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- [Azure AD B2C テナント](./tutorial-create-tenant.md)。 テナントは Azure サブスクリプションにリンクされています。

- Microsoft DFP [サブスクリプション](https://dynamics.microsoft.com/pricing/#Sales)を取得します。 [評価版クライアント バージョン](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin)を設定することもできます。

## <a name="scenario-description"></a>シナリオの説明

Microsoft DFP 統合には次のコンポーネントが含まれます。

- **Azure AD B2C テナント**: ユーザーを認証し、Microsoft DFP のクライアントとして機能します。 ターゲット ポリシーを実行するすべてのユーザーの識別データと診断データを収集する、フィンガープリント スクリプトをホストします。 Microsoft DFP が疑わしいと判断した場合、後でサインインまたはサインアップの試行をブロックまたはチャレンジします。

- **カスタム アプリケーション サービス**: 2 つの目的を果たす Web アプリケーションです。

  - Identity Experience Framework の UI として使用される HTML ページを提供します。 Microsoft Dynamics 365 フィンガープリント スクリプトを埋め込みます。

  - Microsoft DFP を Azure AD B2C に接続する RESTful エンドポイントがある API コントローラーです。 データ処理、構造化を行い、両方のセキュリティ要件に準拠します。

- **Microsoft DFP フィンガープリント サービス**: 動的に埋め込まれたスクリプトです。デバイスのテレメトリとセルフアサート ユーザーの詳細をログに記録して、ユーザーが意思決定プロセスで後で使用するために一意に識別できるフィンガープリントを作成します。

- **Microsoft DFP API エンドポイント**: 決定結果を提供し、クライアント アプリケーションによって実行される操作を反映した、最終的な状態を受け入れます。 セキュリティと API ペイロードの要件が異なるため、Azure AD B2C はエンドポイントと直接通信しません。代わりに、App Service を中間物として使用します。

次のアーキテクチャの図に、この実装を示します。

![Microsoft Dynamics 365 Fraud Protection のアーキテクチャ図を示す画像](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|手順 | 説明 |
|:-----| :-----------|
| 1. | ユーザーがログイン ページにアクセスします。 ユーザーがサインアップを選択して新しいアカウントを作成し、そのページに情報を入力します。 Azure AD B2C でユーザー属性が収集されます。
| 2. | Azure AD B2C で中間層 API が呼び出され、ユーザー属性が渡されます。
| 3. | 中間層 API により、ユーザー属性が収集され、Microsoft DFP API が使用できる形式に変換されます。 その後、それが Microsoft DFP API に送信されます。
| 4. | Microsoft DFP API でその情報が使用され、処理された後に、中間層 API に結果が返されます。
| 5. | 中間層 API によりその情報が処理され、関連情報が Azure AD B2C に送り返されます。
| 6. | Azure AD B2C が、中間層 API から情報を受信します。 それが失敗応答である場合、ユーザーにエラー メッセージが表示されます。 成功応答が表示された場合、ユーザーは認証され、ディレクトリに書き込まれます。

## <a name="set-up-the-solution"></a>ソリューションのセットアップ

1. Azure AD B2C へのフェデレーションを可能にするように構成された、[Facebook アプリケーションを作成](./identity-provider-facebook.md#create-a-facebook-application)します。
2. 作成した [Facebook シークレット](./custom-policy-get-started.md#create-the-facebook-key)を Identity Experience Framework ポリシー キーとして追加します。

## <a name="configure-your-application-under-microsoft-dfp"></a>Microsoft DFP でのアプリケーションの構成

Microsoft DFP を使用するように [Azure AD テナントを設定](/dynamics365/fraud-protection/integrate-real-time-api)します。

## <a name="deploy-to-the-web-application"></a>Web アプリケーションへのデプロイ

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>Microsoft DFP サービス フィンガープリントの実装

[Microsoft DFP デバイス フィンガープリント](/dynamics365/fraud-protection/device-fingerprinting)は、Microsoft DFP アカウント保護に必要です。

>[!NOTE]
>デバイスのより包括的なプロファイリングのために、Azure AD B2C UI ページに加えて、アプリケーション コード内にフィンガープリント サービスを実装することもできます。 アプリケーション コード内のフィンガープリント サービスは、このサンプルには含まれていません。

### <a name="deploy-the-azure-ad-b2c-api-code"></a>Azure AD B2C API コードのデプロイ

[提供されている API コード](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API)を Azure サービスにデプロイします。 このコードは、[Visual Studio から発行](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)できます。

CORS の設定、**許可された配信元** `https://{your_tenant_name}.b2clogin.com` の追加

>[!NOTE]
>必要な設定で Azure AD を構成するために、デプロイされたサービスの URL が後で必要になります。

詳細については、[App Service のドキュメント](../app-service/app-service-web-tutorial-rest-api.md)を参照してください。

### <a name="add-context-dependent-configuration-settings"></a>コンテキストに依存する構成設定の追加

アプリケーション設定を [Azure の App Service](../app-service/configure-common.md#configure-app-settings) で構成します。 これにより、設定をリポジトリにチェックインすることなく、安全に構成できます。 Rest API には次に示す設定が必要です。

| アプリケーションの設定 | source | Notes |
| :-------- | :------------| :-----------|
|FraudProtectionSettings:InstanceId | Microsoft DFP 構成 |     |
|FraudProtectionSettings:DeviceFingerprintingCustomerId | Microsoft デバイス フィンガープリント カスタマー ID |     |
| FraudProtectionSettings:ApiBaseUrl |  Microsoft DFP Portal からのベース URL   | 実稼働 API を呼び出すには、"-int" を削除します。
|  TokenProviderConfig:リソース | https://api.dfp.dynamics-int.com |   実稼働 API を呼び出すには、"-int" を削除します。  |
|   TokenProviderConfig:ClientId       |Fraud Protection マーチャント Azure AD クライアント アプリケーション ID      |       |
| TokenProviderConfig:Authority | https://login.microsoftonline.com/<directory_ID> | Fraud Protection マーチャント Azure AD テナント オーソリティ |
| TokenProviderConfig:CertificateThumbprint* | マーチャント Azure AD クライアント アプリケーションに対する認証に使用する、証明書のサムプリント |
| TokenProviderConfig:ClientSecret* | マーチャント Azure AD クライアント アプリケーションのシークレット | シークレット マネージャーの使用をお勧めします。 |

\* 証明書で認証するかパスワードなどのシークレットを使用して認証するかに応じて、2 つのマークされたパラメーターのうち 1 つだけを設定します。

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C の構成

### <a name="replace-the-configuration-values"></a>構成値を置き換える

提供された[カスタム ポリシー](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)内にある次のプレースホルダーを見つけて、ご自分のインスタンスの対応する値に置き換えます。

| プレースホルダー | 置換後の文字列 | ノート |
| :-------- | :------------| :-----------|
|{your_tenant_name} | テナントの短い名前 |  yourtenant.onmicrosoft.com の "yourtenant"   |
|{your_tenantId} | Azure AD B2C テナントのテナント ID |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   Azure AD B2C テナントに構成されている IdentityExperienceFramework アプリのアプリ ID    |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  Azure AD B2C テナントに構成されている ProxyIdentityExperienceFramework アプリのアプリ ID      |   01234567-89ab-cdef-0123-456789abcdef     |
|  {your_tenant_extensions_appid}   |  テナントのストレージ アプリケーションのアプリ ID   |  01234567-89ab-cdef-0123-456789abcdef  |
|   {your_tenant_extensions_app_objectid}  | テナントのストレージ アプリケーションのオブジェクト ID    | 01234567-89ab-cdef-0123-456789abcdef   |
|   {your_app_insights_instrumentation_key}  |   App Insights インスタンスのインストルメンテーション キー *  |   01234567-89ab-cdef-0123-456789abcdef |
|  {your_ui_base_url}   | UI ファイルが提供される App Service のエンドポイント    | https://yourapp.azurewebsites.net/B2CUI/GetUIPage   |
|   {your_app_service_url}  | App Service の URL    |  https://yourapp.azurewebsites.net  |
|   {your-facebook-app-id}  |  Azure AD B2C とのフェデレーション用に構成した Facebook アプリのアプリ ID   | 000000000000000   |
|  {your-facebook-app-secret}   |  Facebook のアプリ シークレットを保存した際のポリシー キーの名前   | B2C_1A_FacebookAppSecret   |

\* App Insights は別のテナントに配置できます。 この手順は省略可能です。 必要でない場合は、対応する TechnicalProfiles および OrchestrationSteps を削除します。

### <a name="call-microsoft-dfp-label-api"></a>Microsoft DFP ラベル API の呼び出し

ユーザーは[ラベル API を実装](/dynamics365/fraud-protection/integrate-ap-api)する必要があります。 詳細については、[Microsoft DFP API](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0) を参照してください。

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

userID の値は、対応する Azure AD B2C 構成値 (ObjectID) のものと同じである必要があります。

>[!NOTE]
>属性コレクション ページに同意通知を追加します。 ユーザーのテレメトリ情報とユーザー ID 情報がアカウント保護のために記録されることを通知します。

## <a name="configure-the-azure-ad-b2c-policy"></a>Azure AD B2C ポリシーを構成する

1. ポリシー フォルダーの [Azure AD B2C ポリシー](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)に移動します。

2. この[ドキュメント](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack)に従って、[LocalAccounts スターター パック](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)をダウンロードします。

3. Azure AD B2C テナントのポリシーを構成します。

>[!NOTE]
>お使いの特定のテナントに関連付けるため、提供されているポリシーを更新してください。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. Azure AD B2C テナントを開き、[ポリシー] の下にある **[Identity Experience Framework]** を選択します。

2. 以前に作成した **SignUpSignIn** を選択します。

3. **[ユーザー フローを実行します]** を選択し、設定を選択します。

   a. **[アプリケーション]** : 登録済みのアプリを選択します (サンプルは JWT)

   b. **[応答 URL]** : **リダイレクト URL** を選択します

   c. **[ユーザー フローを実行します]** を選択します。

4. サインアップ フローを実行し、アカウントを作成します

5. Microsoft DFP サービスは、ユーザー属性が作成された後、フロー中に呼び出されます。 フローが不完全な場合は、ユーザーがディレクトリに保存されていないことを確認してください。

>[!NOTE]
>[Microsoft DFP ルール エンジン](/dynamics365/fraud-protection/rules)を使用する場合は、Microsoft DFP Portal でルールを直接更新します。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Microsoft DFP のサンプル](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./custom-policy-get-started.md?tabs=applications)

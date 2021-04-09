---
title: Onfido で Azure Active Directory B2C を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: ドキュメント ID と顔による生体認証の検証のために Azure AD B2C 認証と Onfido を統合する方法について説明します。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 46c6eac80ddbff73d99e05c070e66aa1700da174
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96928632"
---
# <a name="tutorial-for-configuring-onfido-with-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Onfido を構成するためのチュートリアル

このサンプル チュートリアルでは、Azure AD B2C を [Onfido](https://onfido.com/) と統合する方法についてのガイダンスを提供します。 Onfido は、ドキュメント ID と顔による生体認証の検証アプリです。 これにより、企業は *Know Your Customer* (顧客確認) と ID 要件をリアルタイムで満たすことができます。 Onfido は、洗練された AI ベースの本人確認を行います。これはまず写真 ID を検証し、次にこれを顔による生体認証と照合します。 このソリューションは、デジタル ID を実在のユーザーに結び付け、不正行為を減らしながら、安全なオンボード エクスペリエンスを提供します。

このサンプルでは、Onfido のサービスをサインアップまたはログイン フローに結びつけ、本人確認を行います。 Onfido の結果に基づいて、ユーザーがアクセスできる製品とサービスに関する情報に基づいた意思決定を行います。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](./tutorial-create-tenant.md)。

- Onfido の[試用版アカウント](https://onfido.com/signup/)。

## <a name="scenario-description"></a>シナリオの説明

Onfido 統合には、次のコンポーネントが含まれています。

- Azure AD B2C テナント – テナントで定義されているカスタム ポリシーに基づいてユーザーの資格情報を検証する承認サーバー。 ID プロバイダーとも呼ばれます。 これは Onfido クライアント アプリ (ユーザー ドキュメントを収集して Onfido API サービスに送信する) をホストします。

- Onfido クライアント – 他の Web ページ内にデプロイされた、構成可能な JavaScript クライアント ドキュメント収集ユーティリティ。 ドキュメントを収集し、ドキュメントのサイズや品質などの事前チェックを行います。

- 中間 Rest API – Azure AD B2C テナントが Onfido API サービスと通信するためのエンドポイントを提供し、データ処理を行い、両方のセキュリティ要件に準拠します。

- Onfido API サービス – Onfido によって提供されるバックエンド サービスで、ユーザーによって提供されたドキュメントを保存して検証します。

次のアーキテクチャの図に、この実装を示します。

![Onfido アーキテクチャ図のスクリーンショット](media/partner-onfido/onfido-architecture-diagram.png)

|手順 | 説明 |
|:-----| :-----------|
| 1. | ユーザーがログイン ページにアクセスします。 ユーザーはサインアップして新しいアカウントを作成し、そのページに情報を入力します。 Azure AD B2C でユーザー属性が収集されます。 Azure AD B2C でホストされている Onfido クライアント アプリは、ユーザー情報の事前チェックを行います。
| 2. | Azure AD B2C で中間層 API が呼び出され、ユーザー属性が渡されます。
| 3. | 中間層 API により、ユーザー属性が収集され、Onfido API が使用できる形式に変換されます。 次に、それが Onfido に送信されます。
| 4. | この情報は Onfido によって使用され、ユーザー ID を検証するために処理されます。 次に、結果が中間層 API に返されます。
| 5. | 中間層 API により情報が処理され、関連情報が正しい JSON 形式で Azure AD B2C に送り返されます。
| 6. | Azure AD B2C が、中間層 API から情報を受信します。 失敗応答が表示された場合は、ユーザーにエラー メッセージが表示されます。 成功応答が表示された場合、ユーザーは認証され、ディレクトリに書き込まれます。

## <a name="onboard-with-onfido"></a>Onfido を使用してオンボードする

1. Onfido アカウントを作成するには、[Onfido](https://onfido.com/signup/) に連絡してください。

2. アカウントが作成されたら、[API キー](https://documentation.onfido.com/)を作成します。 ライブ キーは課金対象ですが、ソリューションを[テストするためのサンドボックス キー](https://documentation.onfido.com/?javascript#sandbox-and-live-differences)を使用することができます。 サンドボックス キーはライブ キーと同じ結果構造を生成しますが、結果は常に事前に決定されます。 ドキュメントは処理も保存もされません。

>[!NOTE]
> このキーは後で必要になります。

Onfido の詳細については、[Onfido API ドキュメント](https://documentation.onfido.com)および [Onfido Developer Hub](https://developers.onfido.com) を参照してください。  

## <a name="configure-azure-ad-b2c-with-onfido"></a>Onfido で Azure AD B2C を構成する

### <a name="part-1---deploy-the-api"></a>パート 1 - API をデプロイする

- 提供された [API コード](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/API/Onfido.Api)を Azure サービスにデプロイします。 このコードは、次の[手順](/visualstudio/deployment/quickstart-deploy-to-azure)に従って、Visual Studio から発行することができます。
- CORS を設定し、 **[Allowed Origin]\(許可されたオリジン\)** を https://{your_tenant_name}.b2clogin.com として追加します。

>[!NOTE]
>必要な設定で Azure AD を構成するには、デプロイされたサービスの URL が必要です。

#### <a name="adding-sensitive-configuration-settings"></a>重要な構成設定の追加

アプリケーション設定は、[Azure の App Service](../app-service/configure-common.md#configure-app-settings) で構成できます。 App Service では、設定をリポジトリにチェックインすることなく、安全に構成できます。 Rest API には次の設定が必要です。

| アプリケーション設定名 | source | Notes |
|:-------------------------|:-------|:-------|
|OnfidoSettings:AuthToken| Onfido アカウント |

### <a name="part-2---deploy-the-ui"></a>パート 2 - UI をデプロイする

#### <a name="configure-your-storage-location"></a>ストレージの場所を構成する

1. [ストレージ アカウントで BLOB ストレージ コンテナー](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)を設定します。

2. UI ファイルを [UI フォルダー](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI)からお使いの BLOB コンテナーに格納します。

3. これらの手順に従って、作成したストレージ コンテナーへの CORS アクセスを許可します。

   a. **[Settings]\(設定\)**  > **[Allowed Origin]\(許可されたオリジン\)** に移動し、「`https://{your_tenant_name}.b2clogin.com`」を入力します。 your-tenant-name を Azure AD B2C テナントの名前に置き換えます。 たとえば、「 https://fabrikam.b2clogin.com 」などです。 テナント名を入力するときは、すべて小文字を使用します。

   b. **[許可されたメソッド]** で、`GET` および `PUT` を選択します。

   c. **[保存]** を選択します。

#### <a name="update-ui-files"></a>UI ファイルを更新する

1. UI ファイルで、[**ocean_blue**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI/ocean_blue) フォルダーに移動します。

2. 各 html ファイルを開きます。

3. {your-ui-blob-container-url} を見つけて、ご自分の UI の **ocean_blue**、**dist**、**assets** フォルダーがある場所の URL に置き換えます。

4. {your-intermediate-api-url} を見つけて、中間 API App Service の URL に置き換えます。

#### <a name="upload-your-files"></a>ファイルをアップロードする

1. UI ファイルを UI フォルダーからお使いの BLOB コンテナーに格納します。

2. [Azure Storage Explorer](../virtual-machines/disks-use-storage-explorer-managed-disks.md) を使用して、ファイルとアクセス許可を管理します。

### <a name="part-3---configure-azure-ad-b2c"></a>パート 3 - Azure AD B2C を構成する

#### <a name="replace-the-configuration-values"></a>構成値を置き換える

提供された[カスタム ポリシー](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/Policies)内にある次のプレースホルダーを見つけて、ご自分のインスタンスの対応する値に置き換えます。

| プレースホルダー | 値で置換 | 例  |
|:---------------|:----------------|:-------------------|
| {your_tenant_name}  | テナントの短い名前 |  yourtenant.onmicrosoft.com の "yourtenant" |
| {your_tenantID} | Azure AD B2C テナントの TenantID | 01234567-89ab-cdef-0123-456789abcdef           |
| {your_tenant_IdentityExperienceFramework_appid}        | Azure AD B2C テナントに構成されている IdentityExperienceFramework アプリのアプリ ID      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | Azure AD B2C テナントに構成されている ProxyIdentityExperienceFramework アプリのアプリ ID | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | テナントのストレージ アプリケーションのアプリ ID                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | テナントのストレージ アプリケーションのオブジェクト ID                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_app_insights_instrumentation_key} | App Insights インスタンスのインストルメンテーション キー *| 01234567-89ab-cdef-0123-456789abcdef|
|{your_ui_file_base_url}| ご自分の UI の **ocean_blue**、**dist**、**assets** フォルダーが配置されている場所の URL | https://yourstorage.blob.core.windows.net/UI/|
| {your_app_service_URL}                                 | 設定したアプリ サービスの URL                                             | `https://yourapp.azurewebsites.net`          |

\* App Insights は別のテナントに配置できます。 この手順は省略可能です。 必要でない場合は、対応する TechnicalProfiles および OrchestrationSteps を削除します。

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>パート 4 - Azure AD B2C ポリシーを構成する

Azure AD B2C テナントを設定してポリシーを構成する方法については、この[ドキュメント](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack)を参照してください。

>[!NOTE]
> ベスト プラクティスとして、属性コレクション ページで同意通知を追加することをお勧めします。 本人確認のために情報がサード パーティのサービスに送信されることをユーザーに通知します。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. Azure AD B2C テナントを開き、[ポリシー] の下にある **[Identity Experience Framework]** を選択します。

2. 以前に作成した **SignUpSignIn** を選択します。

3. **[ユーザー フローを実行します]** を選択し、設定を選択します。

   a. **[アプリケーション]** : 登録済みのアプリを選択します (サンプルは JWT)

   b. **[応答 URL]** : **リダイレクト URL** を選択します

   c. **[ユーザー フローを実行します]** を選択します。

4. サインアップ フローを実行し、アカウントを作成します

5. Onfido サービスは、ユーザー属性が作成された後、フロー中に呼び出されます。 フローが不完全な場合は、ユーザーがディレクトリに保存されていないことを確認してください。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./custom-policy-get-started.md?tabs=applications)

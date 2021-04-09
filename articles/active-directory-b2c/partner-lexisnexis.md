---
title: LexisNexis を使用して Azure Active Directory B2C を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: Azure AD B2C 認証を LexisNexis というプロファイルと ID 検証のサービスと統合する方法を説明します。これは、ユーザー ID の検証や、そのユーザーのデバイスに基づく包括的なリスク評価の提供に使用されます。
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 5f408699cae9580188a3780fc8f8654eaa97c26b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97108435"
---
# <a name="tutorial-for-configuring-lexisnexis-with-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して LexisNexis を構成するためのチュートリアル

このサンプル チュートリアルでは、Azure AD B2C を [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd) と統合する方法についてのガイダンスを提供します。 LexisNexis にはさまざまなソリューションが用意されています。[こちら](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)を参照してください。 このサンプル チュートリアルでは、LexisNexis の **ThreatMetrix** ソリューションについて説明します。 ThreatMetrix は、プロファイルと ID 検証のサービスです。 これは、ユーザー ID を検証し、そのユーザーのデバイスに基づく包括的なリスク評価を提供するために使用されます。

この統合では、サインアップ フロー中にユーザーによって提供されるいくつかのユーザー情報に基づいてプロファイリングが行われます。 ThreatMetrix は、ユーザーのログインを引き続き許可すべきかどうかを決定します。 ThreatMetrix のリスク分析では、次の属性が考慮されます。

- Email
- 電話番号
- ユーザーのマシンから収集されたプロファイル情報

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](./tutorial-create-tenant.md)。

## <a name="scenario-description"></a>シナリオの説明

ThreatMetrix 統合には、次のコンポーネントが含まれています。

- Azure AD B2C – ユーザーの資格情報の検証を担当する承認サーバー。ID プロバイダーとも呼ばれます。

- ThreatMetrix – ThreatMetrix サービスでは、ユーザーが入力した情報を受け取り、ユーザーのマシンから収集されたプロファイリング情報と組み合わせて、ユーザーの操作のセキュリティを検証します。

- カスタム REST API – この API によって、Azure AD B2C と ThreatMetrix サービスとの間の統合が実装されます。

次のアーキテクチャの図に、この実装を示します。

![LexisNexis アーキテクチャ図のスクリーンショット](media/partner-lexisnexis/lexisnexis-architecture-diagram.png)

|手順 | 説明 |
|:--------------|:-------------|
|1. | ユーザーがログイン ページにアクセスします。 ユーザーがサインアップを選択して新しいアカウントを作成し、そのページに情報を入力します。 Azure AD B2C でユーザー属性が収集されます。
| 2. | Azure AD B2C で中間層 API が呼び出され、ユーザー属性が渡されます。
| 3. | 中間層 API により、ユーザー属性が収集され、LexisNexis API が使用できる形式に変換されます。 次に、これが LexisNexis に送信されます。  
| 4. | この情報は LexisNexis によって使用され、リスク分析に基づいてユーザー ID を検証するために処理されます。 次に、結果が中間層 API に返されます。
| 5. | 中間層 API により情報が処理され、関連情報が Azure AD B2C に送り返されます。
| 6. | Azure AD B2C が、中間層 API から情報を受信します。 それが失敗応答である場合、そのユーザーにエラー メッセージが表示されます。 それが成功応答である場合、そのユーザーは認証され、アクセスが許可されます。

## <a name="onboard-with-lexisnexis"></a>LexisNexis を使用してオンボードする

1. LexisNexis アカウントを作成するには、[LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd) にお問い合わせください。

2. 要件を満たす LexisNexis ポリシーを作成します。 [こちら](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)から入手できるドキュメントを参照してください。

>[!NOTE]
> ポリシーの名前は、後で使用されます。

アカウントが作成されると、API の構成に必要な情報が送信されます。 以下のセクションでは、このプロセスについて説明します。

## <a name="configure-azure-ad-b2c-with-lexisnexis"></a>LexisNexis を使用して Azure AD B2C を構成する

### <a name="part-1---deploy-the-api"></a>パート 1 - API をデプロイする

提供された [API コード](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/Api)を Azure サービスにデプロイします。 このコードは、次の[手順](/visualstudio/deployment/quickstart-deploy-to-azure)に従って、Visual Studio から発行することができます。

>[!NOTE]
>必要な設定で Azure AD を構成するには、デプロイされたサービスの URL が必要です。

### <a name="part-2---configure-the-api"></a>パート 2 - API を構成する

アプリケーション設定は、[Azure の App Service で構成](../app-service/configure-common.md#configure-app-settings)できます。  この方法では、設定をリポジトリにチェックインすることなく、安全に構成できます。 REST API に次の設定を指定する必要があります。

| アプリケーションの設定 | source | Notes |
| :-------- | :------------| :-----------|
|ThreatMetrix:url | ThreatMetrix アカウントの構成 |     |
|ThreatMetrix:OrgId | ThreatMetrix アカウントの構成 |     |
|ThreatMetrix:ApiKey |ThreatMetrix アカウントの構成|  |
|ThreatMetrix:ポリシー | ThreatMetrix で作成されたポリシーの名前 | |
| BasicAuth:ApiUsername |API のユーザー名を定義します| ユーザー名は、Azure AD B2C 構成で使用されます
| BasicAuth:ApiPassword | API のパスワードを定義します | パスワードは、Azure AD B2C 構成で使用されます

### <a name="part-3---deploy-the-ui"></a>パート 3 - UI をデプロイする

このソリューションでは、Azure AD B2C によって読み込まれるカスタム UI テンプレートを使用します。 これらの UI テンプレートによってプロファイルが行われ、それが ThreatMetrix サービスに直接送信されます。

こちらの[手順](./customize-ui-with-html.md#custom-page-content-walkthrough)を参照して、含まれている [UI ファイル](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template)を BLOB ストレージ アカウントにデプロイします。 この手順には、BLOB ストレージ アカウントの設定、CORS の構成、パブリック アクセスの有効化が含まれます。

UI は、[オーシャン ブルー テンプレート](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template/ocean_blue)に基づいています。 UI 内のすべてのリンクは、デプロイされた場所を参照するように更新する必要があります。 UI フォルダーで、 https://yourblobstorage/blobcontainer を見つけてデプロイした場所に置き換えます。

### <a name="part-4---create-api-policy-keys"></a>パート 4 - API ポリシー キーを作成する

この[ドキュメント](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys)を参照して、2 つのポリシー キー (API ユーザー名用に 1 つと、前に定義した API パスワード用に 1 つ) を作成します。

このサンプル ポリシーでは、次のキー名を使用します。

- B2C_1A_RestApiUsername

- B2C_1A_RestApiPassword

### <a name="part-5---update-the-api-url"></a>パート 5 - API URL を更新する

提供されている [TrustFrameworkExtensions ポリシー](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)で、`Rest-LexisNexus-SessionQuery` という名前の技術プロファイルを見つけ、上でデプロイした API の場所で `ServiceUrl` メタデータ項目を更新します。

### <a name="part-6---update-ui-url"></a>パート 6 - UI URL を更新する

提供されている [TrustFrameworkExtensions ポリシー](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)で、[検索と置換] を実行し、UI ファイルがデプロイされた場所で https://yourblobstorage/blobcontainer/ を検索します。

>[!NOTE]
> ベスト プラクティスとして、属性コレクション ページで同意通知を追加することをお勧めします。 本人確認のために情報がサード パーティのサービスに送信されることをユーザーに通知します。

### <a name="part-7---configure-the-azure-ad-b2c-policy"></a>パート 7 - Azure AD B2C ポリシーを構成する

この[ドキュメント](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack)を参照して、[Local Accounts スターター パック](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)をダウンロードし、Azure AD B2C テナント用に[ポリシー](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/policy)を構成します。

>[!NOTE]
>お使いの特定のテナントに関連付けるため、提供されているポリシーを更新してください。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. Azure AD B2C テナントを開き、[ポリシー] の下にある **[ユーザー フロー]** を選択します。

2. 以前に作成した **ユーザー フロー** を選択します。

3. **[ユーザー フローを実行します]** を選択し、設定を選択します。

   a. **[アプリケーション]** : 登録済みのアプリを選択します (サンプルは JWT)

   b. **[応答 URL]** : **リダイレクト URL** を選択します

   c. **[ユーザー フローを実行します]** を選択します。

4. サインアップ フローを実行し、アカウントを作成します

5. ログアウトします

6. サインイン フローを実行します  

7. **[続行]** を選択すると、ThreatMetrix パズルが表示されます。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./custom-policy-get-started.md?tabs=applications)

---
title: IDology と Azure Active Directory B2C の統合
titleSuffix: Azure AD B2C
description: Azure AD B2C のサンプル オンライン決済アプリを IDology と統合する方法について説明します。 IDology は、複数のソリューションを持つ ID 検証およびプルーフィング プロバイダーです。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2b6001932b6f53a60eda76b6136611a10011391a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96928683"
---
# <a name="tutorial-for-configuring-idology-with-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して IDology を構成するためのチュートリアル 

このサンプル チュートリアルでは、Azure AD B2C を [IDology](https://www.idology.com/solutions/) と統合する方法についてのガイダンスを提供します。 IDology は、複数のソリューションを持つ ID 検証およびプルーフィング プロバイダーです。 このサンプルでは、IDology による ExpectID ソリューションについて説明します。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](tutorial-create-tenant.md)。

## <a name="scenario-description"></a>シナリオの説明

IDology 統合には、次のコンポーネントが含まれています。

- Azure AD B2C – ユーザーの資格情報の検証を担当する承認サーバー。 ID プロバイダーとも呼ばれます。
- IDology – IDology サービスは、ユーザーによる入力を受け取り、ユーザーの ID を検証します。
- カスタムの Rest API – この API は、Azure AD と IDology サービス間の統合を実装します。

次のアーキテクチャの図に、この実装を示します。

![IDology のアーキテクチャの図](media/partner-idology/idology-architecture-diagram.png)

| 手順 | 説明 |
|------|------|
|1     | ユーザーがサインイン ページに到着します。 |
|2     | ユーザーがサインアップ オプションを選択して新しいアカウントを作成し、ページに情報を入力します。 Azure AD B2C でユーザー属性が収集されます。 |
|3     | Azure AD B2C で中間層 API が呼び出され、ユーザー属性が渡されます。 |
|4     | 中間層 API により、ユーザー属性が収集され、IDOlogy API が使用できる形式に変換されます。 その後、この情報が IDology に送信されます。 |
|5     | IDology が情報を使用して処理し、その結果を中間層 API に返します。 |
|6     | 中間層 API により情報が処理され、関連情報が Azure AD B2C に送り返されます。 |
|7     | Azure AD B2C が、中間層 API から情報を受信します。 **失敗** 応答が表示される場合は、ユーザーにエラー メッセージが表示されます。 **成功** 応答が表示される場合、ユーザーは認証され、ディレクトリに書き込まれます。 |
|      |      |

> [!NOTE]
> Azure AD B2C ではユーザーにステップアップ認証を実行するように依頼することもできますが、このシナリオはこのチュートリアルの範囲外です。

## <a name="onboard-with-idology"></a>IDology を使用してオンボードする

1. IDology にはさまざまなソリューションが用意されています。[こちら](https://www.idology.com/solutions/)を参照してください。 このサンプルでは、ExpectID を使用します。

2. IDology アカウントを作成するには、[IDology](https://www.idology.com/request-a-demo/microsoft-integration-signup/) に連絡してください。

3. アカウントが作成されると、API の構成に必要な情報が送信されます。 以下のセクションでは、このプロセスについて説明します。

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C との統合

### <a name="part-1---deploy-the-api"></a>パート 1 - API をデプロイする

提供された [API コード](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/Api)を Azure サービスにデプロイします。 このコードは、次の[手順](/visualstudio/deployment/quickstart-deploy-to-azure)に従って、Visual Studio から発行することができます。

必要な設定で Azure AD を構成するには、デプロイされたサービスの URL が必要です。

### <a name="part-2---configure-the-api"></a>パート 2 - API を構成する 

アプリケーションの設定は、[Azure の App Service で構成](../app-service/configure-common.md#configure-app-settings)できます。 この方法では、リポジトリにチェックインせずに、安全に設定を構成できます。 REST API に次の設定を指定する必要があります。

| アプリケーションの設定 | source | Notes |
| :-------- | :------------| :-----------|
|IdologySettings:ApiUsername | IDology アカウントの構成 |     |
|IdologySettings:ApiPassword | IDology アカウントの構成 |     |
|WebApiSettings:ApiUsername |API のユーザー名を定義します| ExtId 構成で使用されます |
|WebApiSettings:ApiPassword | API のパスワードを定義します | ExtId 構成で使用されます

### <a name="part-3---create-api-policy-keys"></a>パート 3 - API ポリシー キーを作成する

この[ドキュメント](secure-rest-api.md#add-rest-api-username-and-password-policy-keys)に従って、2 つのポリシー キー (API ユーザー名に 1 つと、前に定義した API パスワードに 1 つ) を作成します。

このサンプル ポリシーでは、次のキー名を使用します。

* B2C_1A_RestApiUsername
* B2C_1A_RestApiPassword

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>パート 4 - Azure AD B2C ポリシーを構成する

1. この[ドキュメント](custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack)に従って、[LocalAccounts スターター パック](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)をダウンロードし、Azure AD B2C テナント用にポリシーを構成します。 手順に従って、「**カスタム ポリシーをテストする**」セクションを完了します。

2. [こちら](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/policy)で 2 つのサンプル ポリシーをダウンロードします。

3. 2 つのサンプル ポリシーを更新します。

   1. 両方のポリシーを開きます。

      1. セクション `Idology-ExpectId-API` で、`ServiceUrl` メタデータ項目を上記でデプロイした API の場所に更新します。

      1. `yourtenant`を Azure AD B2C テナントの名前に置き換えます。
      たとえば、Azure AD B2C テナントの名前が  `contosotenant` であれば、 `yourtenant.onmicrosoft.com`  のすべてのインスタンスを `contosotenant.onmicrosoft.com` に置き換えます。

   1. TrustFrameworkExtensions.xml ファイルを開きます。

      1.  `<TechnicalProfile Id="login-NonInteractive">` 要素を見つけます。  `IdentityExperienceFrameworkAppId`  の両方のインスタンスを、前に作成した IdentityExperienceFramework アプリケーションのアプリケーション ID に置き換えます。

      1.  `ProxyIdentityExperienceFrameworkAppId`  の両方のインスタンスを、前に作成した ProxyIdentityExperienceFramework アプリケーションのアプリケーション ID に置き換えます。

4. 前の手順 1 で Azure AD B2C にアップロードした SignInorSignUp と TrustFrameworkExtensions を、更新された 2 つのサンプル ポリシーに置き換えます。

> [!NOTE]
> ベスト プラクティスとして、属性コレクション ページで同意通知を追加することをお勧めします。 ID 検証のために情報がサードパーティのサービスに送信されることをユーザーに通知します。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. Azure AD B2C テナントを開いて、 **[ポリシー]** で **[ユーザー フロー]** を選択します。

2. 以前に作成した **ユーザー フロー** を選択します。

3. **[ユーザー フローを実行します]** を選択し、設定を選択します。

   1. **[アプリケーション]** - 登録済みのアプリを選択します (サンプルは JWT)。

   1. **[応答 URL]** : **リダイレクト URL** を選択します。

   1. **[ユーザー フローを実行します]** を選択します。

4. サインアップ フローを実行し、アカウントを作成します。

5. サインアウトします。

6. サインアップ フローを実行します。

7. **[続行]** を選択すると、IDology パズルが表示されます。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](custom-policy-get-started.md?tabs=applications)

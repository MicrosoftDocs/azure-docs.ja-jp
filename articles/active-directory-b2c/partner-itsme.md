---
title: itsme OpenID Connect と Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: client_secret ユーザー フロー ポリシーを使用して Azure AD B2C 認証を itsme OIDC と統合する方法について説明します。 itsme はデジタル ID アプリです。 これを使用すると、カードリーダー、パスワード、2 要素認証、および複数の PIN コードを使用せずに、安全にログインできます。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ba7875caa6a1db7638bfeafcfea1efa7b2462152
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87489517"
---
# <a name="configure-itsme-openid-connect-oidc-with-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して itsme OpenID Connect を構成する

itsme デジタル ID アプリを使用すると、カードリーダー、パスワード、2 要素認証、または複数の PIN コードを使用せずに、安全にサインインできます。 itsme アプリは、検証済みの ID で強力な顧客認証を提供します。 この記事では、クライアント シークレット ユーザー フロー ポリシーを使用して Azure AD B2C 認証を itsme の OpenID Connect (OIDC) と統合する方法について説明します。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](tutorial-create-tenant.md)。
* パートナー コードとも呼ばれる、itsme によって提供されるクライアント ID。
* itsme によって提供されるサービス コード。
* itsme アカウントのクライアント シークレット。

## <a name="scenario-description"></a>シナリオの説明

![itsme アーキテクチャの図](media/partner-itsme/itsme-architecture-diagram.png)

<!--
Please clarify step 1 in the description below - we don't have steps in this tutorial for "adapting in the Azure AD B2C Custom Policy- User Journeys" - should this be added somewhere?
-->

| 手順 | 説明 |
|------|------|
|1     | Web サイトまたはアプリケーションで、Azure AD B2C ユーザー フローに適合させることによって、 **[Log in with itsme] (itsme でログイン)** ボタンを組み込みます。 ユーザーがこのボタンをクリックすると、対話フローが開始されます。  |
|2     | Azure AD B2C は、承認要求を itsme クライアント シークレット API に送信することで OpenID Connect フローを開始します。 既知の OpenID 構成エンドポイントには、エンドポイントに関する情報を含めることができます。  |
|3     | itsme 環境が、ユーザーを自分の itsme ID ページにリダイレクトして、ユーザーが自分の電話番号を入力できるようにします。  |
|4     | itsme 環境が、ユーザーから電話番号を受け取り、正しいことを検証します。  |
|5     | 電話番号がアクティブな itsme ユーザーに属している場合は、itsme アプリのアクションが作成されます。  |
|6     | ユーザーが itsme アプリを開き、要求を確認して、アクションを確認します。  |
|7     |  アプリにより、アクションが確認済みであることが itsme 環境に通知されます。 |
|8     |  itsme 環境が、Azure AD B2C に OAuth 承認コードを返します。 |
|9     |  Azure AD B2C が、承認コードを使用して、トークン要求を行います。 |
| 10 | itsme 環境が、トークン要求を確認し、まだ有効である場合は、OAuth アクセス トークンと、要求されたユーザー情報を含む ID トークンを返します。 |
| 11 | 最後に、ユーザーが認証済みユーザーとしてリダイレクト URL にリダイレクトされます。  |
|   |   |

## <a name="onboard-with-itsme"></a>itsme を使用してオンボードする

1. itsme でアカウントを作成するには、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) で itsme にアクセスします。

2. onboarding@itsme.be に電子メールを送信して、itsme アカウントをアクティブ化します。 B2C のセットアップに必要な **パートナー コード** と **サービス コード** が送信されます。

3. itsme パートナー アカウントをアクティブ化すると、**クライアント シークレット** へのワンタイム リンクを含む電子メールが送信されます。

4. [itsme](https://business.itsme.be/en) の指示に従って、構成を完了します。

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C との統合

### <a name="set-up-a-new-identity-provider-in-azure-ad-b2c"></a>Azure AD B2C で新しい ID プロバイダーを設定する

> [!NOTE]
> まだ持っていない場合は、[お使いの Azure サブスクリプションにリンクされている Azure AD B2C テナント](tutorial-create-tenant.md)を作成します。

1. Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。

2. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します (または **[その他のサービス]** を選択し、 **[すべてのサービス]** 検索ボックスを使用して *Azure AD B2C* を検索します)。

3. **[ID プロバイダー]** を選択してから、 **[新しい OpenID Connect プロバイダー ]** を選択します。

4. フォームに次の情報を入力します。

   |プロパティ | 値 |
   |------------ |------- |
   | 名前 | itsme |
   | メタデータ URL | `https://oidc.<environment>.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration` <br>`<environment>` は `e2e` (テスト環境) または `prd` (運用環境) のいずれか  |
   | ClientID     | **クライアント ID** (**パートナー コード** とも呼ばれます)  |
   | クライアント シークレット | お使いの **client_secret** |
   | Scope  | openid service:YOURSERVICECODE プロファイルの電子メール [電話] [アドレス]  |
   |応答の種類 | code |
   |応答モード | query |
   |ドメインのヒント | *空のままにできます* |
   |UserID | sub |
   |表示名 | name |
   |指定された名前 | given_name |
   |Surname | family_name |
   |Email | email|

5. **[保存]** を選択します。

### <a name="configure-a-user-flow"></a>ユーザー フローを構成する

1. Azure AD B2C テナントの **[ポリシー]** で、 **[ユーザー フロー]** を選択します。

2. **[新しいユーザー フロー]** を選択します。

3. **[サインアップとサインイン]** を選択してバージョンを選択し、 **[作成]** を選択します。

4. **[名前]** を入力します。

5. **[ID プロバイダー]** セクションで、 **[itsme]** を選択します。

6. **［作成］** を選択します

7. ユーザー フロー名を選択して、新しく作成したユーザー フローを開きます。

8. **[プロパティ]** を選択し、次の値を調整します。

   * **[アクセスと ID トークンの有効期間 (分)]** を **[5]** に変更します。
   * **[更新トークンのスライディング ウィンドウの有効期間]** を **[期限なし]** に変更します。

### <a name="register-an-application"></a>アプリケーションを登録する

1. B2C テナントの **[管理]** で、 **[アプリの登録]**  >  **[新しい登録]** の順に選択します。

2. アプリケーションの **[名前]** を指定し、 **[リダイレクト URI]** を入力します。 テスト目的の場合は、「`https://jwt.ms`」と入力します。

3. 多要素認証が **[無効]** になっていることを確認します。

4. **[登録]** を選択します。

   a. テスト目的の場合は、 **[認証]** を選択し、 **[暗黙的な許可]** で、 **[アクセス トークン]** と **[ID トークン]** の両方のチェック ボックスをオンにします。  

   b. **[保存]** を選択します。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. B2C テナントの **[ポリシー]** で、 **[ユーザー フロー]** を選択します。

2. 以前に作成したユーザー フローを選択します。

3. **[ユーザー フローを実行します]** を選択します。

   a. **[アプリケーション]** : *登録済みのアプリを選択します*

   b. **[応答 URL]** : *リダイレクト URL を選択します*

4. itsme の **[Identify yourself] (自分自身を識別)** ページが表示されます。  

5. 携帯電話番号を入力し、 **[送信]** を選択します。

6. itsme アプリでアクションを確認します。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

* [Azure AD B2C のカスタム ポリシー](custom-policy-overview.md)

* [Azure AD B2C のカスタム ポリシーの概要](custom-policy-get-started.md?tabs=applications)
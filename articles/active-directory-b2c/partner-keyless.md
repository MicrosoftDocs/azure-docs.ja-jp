---
title: Keyless と Azure Active Directory B2C を構成するチュートリアル
titleSuffix: Azure AD B2C
description: パスワードレス認証のために Azure Active Directory B2C を使用して Keyless を構成するチュートリアル
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 1/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b817cfc347ee79ff7c9cbb4124e3f2b7e4d2b7ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644257"
---
# <a name="tutorial-configure-keyless-with-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C を使用して Keyless を構成する

このサンプル チュートリアルでは、Azure Active Directory (AD) B2C を [Keyless](https://keyless.io/) で構成する方法についてのガイダンスを提供します。 ID プロバイダーとして Azure AD B2C を使用することで、Keyless を任意の顧客アプリケーションに統合し、真のパスワードレス認証をユーザーに提供できます。

Keyless のソリューション **Keyless Zero-Knowledge Biometric (ZKB™)** は、カスタマー エクスペリエンスを向上させ、プライバシーを保護すると同時に、不正行為、フィッシング、資格情報の再利用を排除する、パスワードレスの多要素認証を提供します。

## <a name="pre-requisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- [Azure AD B2C テナント](./tutorial-create-tenant.md)。 テナントは Azure サブスクリプションにリンクされている必要があります。

- Keyless クラウド テナント。無料[試用版アカウント](https://keyless.io/go)を取得します。

- ユーザーのデバイスにインストールされている Keyless Authenticator アプリ。

## <a name="scenario-description"></a>シナリオの説明

Keyless 統合には、次のコンポーネントが含まれています。

- Azure AD B2C – ユーザーの資格情報の検証を受け持つ承認サーバー。ID プロバイダーとも呼ばれます。

- Web およびモバイル アプリケーション – Keyless と Azure AD B2C を使用して保護する対象のモバイルまたは Web アプリケーション。

- Keyless モバイル アプリ – Keyless モバイル アプリは、Azure AD B2C 対応アプリケーションに対する認証に使用されます。

次のアーキテクチャの図に、この実装を示します。

![Keyless アーキテクチャ図を示す画像](./media/partner-keyless/keyless-architecture-diagram.png)

|手順 | 説明 |
|:-----| :-----------|
| 1. | ユーザーがログイン ページにアクセスします。 ユーザーは、サインイン/サインアップを選択し、ユーザー名を入力します
| 2. | 本人確認のためにユーザー属性がアプリケーションから Azure AD B2C に送信されます。
| 3. | Azure AD B2C によってユーザー属性が収集され、Keyless モバイル アプリを通じてユーザー認証を行うために属性が Keyless に送信されます。
| 4. | 顔の生体認証スキャンの形式でプライバシーを維持した認証を行うために、Keyless から登録されているユーザーのモバイル デバイス宛てにプッシュ通知が送信されます。
| 5. | ユーザーがプッシュ通知に応答した後、本人確認の結果に基づいてユーザーが顧客アプリケーションへのアクセスを許可または拒否されます。

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C との統合

### <a name="add-a-new-identity-provider"></a>新しい ID プロバイダーの追加

新しい ID プロバイダーを追加するには、次の手順を実行します。

1. Azure AD B2C テナントのグローバル管理者として **[Azure portal](https://portal.azure.com/#home)** にサインインします。

2. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。

3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。

4. **[ダッシュボード]**  >  **[Azure Active Directory B2C]**  >   **[ID プロバイダー]** の順に移動します

5. **[Identity Providers]** を選択します。

6. **[追加]** を選択します。

### <a name="configure-an-identity-provider"></a>ID プロバイダーの構成

ID プロバイダーを構成するには、次の手順を実行します。

1. **[ID プロバイダーの種類]**  >  **[OpenID Connect (Preview)]\(OpenID Connect (プレビュー))** の順に選択します
2. フォームに入力して、ID プロバイダーを設定します。

   |プロパティ | 値 |
   |:-----| :-----------|
   | 名前   | Keyless |
   | メタデータ URL | ホストされている Keyless Authentication アプリの URI を挿入し、その後に特定のパスを入力します ('https://keyless.auth/.well-known/openid-configuration ' など)。 |
   | クライアント シークレット | Keyless Authentication インスタンスに関連付けられているシークレット。前に構成したものとは異なります。 任意の複雑な文字列を挿入します。 このシークレットは、後で Keyless Container 構成で使用します。|
   | クライアント ID | クライアントの ID。 この ID は、後で Keyless Container 構成で使用します。|
   | スコープ | openid |
   | 応答の種類 | id_token |
   | 応答モード | form_post|

3. **[OK]** を選択します。

4. **[Map this identity provider's claims]** を選択します。

5. フォームに入力して、ID プロバイダーをマップします。

   |プロパティ | 値 |
   |:-----| :-----------|
   | UserID    | サブスクリプションから |
   | 表示名 | サブスクリプションから |
   | 応答モード | サブスクリプションから |

6. **[保存]** を選択して、新しい Open ID Connect (OIDC) ID プロバイダーの設定を完了します。

### <a name="create-a-user-flow-policy"></a>ユーザー フロー ポリシーの作成

これで、B2C ID プロバイダー内に Keyless が新しい OIDC ID プロバイダーとして表示されるはずです。

1. Azure AD B2C テナントの **[ポリシー]** で、 **[ユーザー フロー]** を選択します。

2. **[新規]** のユーザー フローを選択します。

3. **[サインアップとサインイン]** を選択してから、 **[バージョン]** 、 **[作成]** の順に選択します。

4. ポリシーの **[名前]** を入力します。

5. [ID プロバイダー] セクションで、新しく作成した Keyless ID プロバイダーを選択します。

6. ユーザー フローのパラメーターを設定します。 名前を挿入し、作成した ID プロバイダーを選択します。 また、メール アドレスを追加することもできます。 この場合、Azure によってログイン プロシージャが直接 Keyless にリダイレクトされる代わりに、使用するオプションをユーザーが選択できる画面が表示されます。

7. **[多要素認証]** フィールドはそのままにしておきます。

8. **[Enforce conditional access policies]\(条件付きアクセス ポリシーを強制\)** を選択します

9. **[User attributes and token claims]\(ユーザー属性とトークン要求\)** で、[属性の収集] オプションの **[メール アドレス]** を選択します。 Azure AD B2C からクライアント アプリケーションに返すことのできるクレームと一緒に、Azure Active Directory で収集できるユーザーについてのすべての属性を追加できます。

10. **［作成］** を選択します

11. 正常に作成できたら、新しい **[ユーザー フロー]** を選択します。

12. 左側のパネルで、 **[アプリケーション クレーム]** を選択します。 [オプション] で、 **[メール]** チェックボックスをオンにし、 **[保存]** を選択します。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. Azure AD B2C テナントを開き、[ポリシー] の下にある [Identity Experience Framework] を選択します。

2. 以前に作成した SignUpSignIn を選択します。

3. [ユーザー フローを実行します] を選択し、設定を選択します。

   a. [アプリケーション] : 登録済みのアプリを選択します (サンプルは JWT)

   b. [応答 URL] : リダイレクト URL を選択します

   c. [ユーザー フローを実行します] を選択します。

4. サインアップ フローを実行し、アカウントを作成します

5. Keyless は、ユーザー属性が作成された後、フロー中に呼び出されます。 フローが不完全な場合は、ユーザーがディレクトリに保存されていないことを確認してください。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./custom-policy-get-started.md?tabs=applications)
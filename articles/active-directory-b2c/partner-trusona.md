---
title: Trusona と Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Azure AD B2C で ID プロバイダーとして Trusona を追加して、パスワードレス認証を有効にする方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 99368da8b4018e93ad537e4722ffefd476e61291
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99573689"
---
# <a name="integrating-trusona-with-azure-active-directory-b2c"></a>Trusona と Azure Active Directory B2C の統合

Trusona は、パスワードレス認証、多要素認証、およびデジタル ライセンス スキャンを可能にすることで安全にサインインできるようにする、独立系ソフトウェア ベンダー (ISV) プロバイダーです。 この記事では、Azure AD B2C で ID プロバイダーとして Trusona を追加して、パスワードレス認証を有効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](tutorial-create-tenant.md)。
* Trusona の[試用版アカウント](https://www.trusona.com/)

## <a name="scenario-description"></a>シナリオの説明

このシナリオでは、Trusona は、パスワードレス認証を有効にする Azure AD B2C の ID プロバイダーとして機能します。 このソリューションは、次のコンポーネントで構成されています。

* サインインとサインアップを組み合わせた Azure AD B2C ポリシー
* ID プロバイダーとして Azure AD B2C に追加された Trusona
* ダウンロード可能な Trusona アプリ

![Trusona のアーキテクチャの図](media/partner-trusona/trusona-architecture-diagram.png)

| 手順 | 説明 |
|------|------|
|1     | ユーザーがアプリケーションにサインインまたはサインアップしようとします。 ユーザーは、Azure AD B2C のサインインとサインアップ ポリシーを使用して認証されます。 サインアップ中は、Trusona アプリからの以前に検証済みのユーザーの電子メール アドレスが使用されます。     |
|2     | Azure B2C が、暗黙的なフローを使用して、ユーザーを Trusona OpenID Connect (OIDC) の ID プロバイダーにリダイレクトします。     |
|3     | デスクトップ PC ベースのログインの場合は、Trusona に、Trusona アプリでスキャンするための一意でステートレスの、アニメーション化された動的な QR コードが表示されます。 モバイルベースのログインの場合は、Trusona で "ディープ リンク" を使用して Trusona アプリが開きます。 これらの 2 つの方法は、デバイスと最終的なユーザーの探索に使用されます。     |
|4     | ユーザーが、表示された QR コードを Trusona アプリでスキャンします。     |
|5     | ユーザーのアカウントは Trusona クラウド サービスにあり、認証が準備されています。     |
|6     | Trusona クラウド サービスが、Trusona アプリに送信されたプッシュ通知を使用して、ユーザーに認証チャレンジを発行します。<br>a. ユーザーに認証チャレンジが要求されます。 <br> b. ユーザーが、チャレンジを受け入れるか拒否するかを選択します。 <br> c. ユーザーは、セキュリティで保護されたエンクレーブ/信頼できる実行環境で、秘密キーを使用してチャレンジを確認し、署名するために、OS のセキュリティ (生体認証、パスコード、PIN、パターンなど) を使用するように求められます。 <br> d. Trusona アプリで、認証のパラメーターに基づいてリアルタイムで動的なアンチリプレイ ペイロードが生成されます。 <br> e. 応答全体が、セキュリティで保護されたエンクレーブ/信頼できる実行環境の秘密キーによって署名され (2 回目)、検証のために Trusona クラウド サービスに返されます。      |
|7     |  Trusona クラウド サービスが、id_token を使用して、ユーザーを開始アプリケーションにリダイレクトします。 Azure AD B2C は、ID プロバイダーのセットアップ時に構成された Trusona の公開された OpenID 構成を使用して id_token を確認します。    |
|  |  |

## <a name="onboard-with-trusona"></a>Trusona を使用してオンボードする

1. [フォーム](https://www.trusona.com/)に入力して、Trusona アカウントを作成し、作業を開始します。

2. App Store から Trusona モバイル アプリをダウンロードします。 アプリをインストールし、電子メールを登録します。

3. ソフトウェアによって送信された安全な "魔法のリンク" を使用して電子メールを検証します。  

4. セルフサービス用の [Trusona Developer のダッシュボード](https://dashboard.trusona.com)にアクセスします。

5. **[I’m Ready] (準備完了)** を選択して、Trusona アプリを使って自分で認証します。

6. 左側のナビゲーション ウィンドウから、 **[OIDC Integrations] (OIDC の統合)** を選択します。

7. **[Create OpenID Connect Integration] (OpenID Connect 統合の作成)** を選択します。

8. 任意の **[名前]** を指定し、以前に **[Client Redirect Host] (クライアント リダイレクト ホスト) フィールド** に入力したドメイン情報 (Contoso など) を使用します。  

   > [!NOTE]
   > Azure Active Directory の初期ドメイン名が、クライアント リダイレクト ホストとして使用されます。

9. [Trusona 統合ガイド](https://docs.trusona.com/integrations/aad-b2c-integration/)の手順に従います。 メッセージが表示されたら、前の手順で参照されている初期ドメイン名 (たとえば、Contoso) を使用します。  

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C との統合

### <a name="add-a-new-identity-provider"></a>新しい ID プロバイダーの追加

> [!NOTE]
> まだ持っていない場合は、[お使いの Azure サブスクリプションにリンクされている Azure AD B2C テナント](tutorial-create-tenant.md)を作成します。

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。

2. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。

3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。

4. **[ダッシュボード]**  >  **[Azure Active Directory B2C]**  >  **[ID プロバイダー]** の順に移動します。

3. **[Identity Providers]** を選択します。

4. **[追加]** を選択します。

### <a name="configure-an-identity-provider"></a>ID プロバイダーの構成  

1. **[ID プロバイダーの種類]**  >  **[OpenID Connect (Preview)]** の順に選択します。

2. フォームに入力して、ID プロバイダーを設定します。  

   | プロパティ | 値  |
   | :--- | :--- |
   | メタデータ URL | `https://gateway.trusona.net/oidc/.well-known/openid-configuration`|
   | クライアント ID | Trusona から電子メールで送信されます |
   | Scope | OpenID プロファイルの電子メール |
   | 応答の種類 | Id_token |
   | 応答モード  | Form_post |

3. **[OK]** を選択します。  

4. **[Map this identity provider's claims]** を選択します。  

5. フォームに入力して、ID プロバイダーをマップします。

   | プロパティ | 値  |
   | :--- | :--- |
   | UserID | Sub  |
   | Display name | nickname |
   | 指定された名前 | given_name |
   | Surname | Family_name |
   | 応答モード | email |

6. **[OK]** を選択して、新しい OIDC ID プロバイダーのセットアップを完了します。

### <a name="create-a-user-flow-policy"></a>ユーザー フロー ポリシーの作成

これで、B2C ID プロバイダー内に Trusona が **新しい OpenID Connect ID プロバイダー** として表示されるはずです。

1. Azure AD B2C テナントの **[ポリシー]** で、 **[ユーザー フロー]** を選択します。

1. **[新しいユーザー フロー]** を選択します。

1. **[サインアップとサインイン]** を選択してバージョンを選択し、 **[作成]** を選択します。

1. ポリシーの **[名前]** を入力します。

1. **[ID プロバイダー]** セクションで、新しく作成した **Trusona ID プロバイダー** を選択します。

   > [!NOTE]
   > Trusona は本質的に多要素であるため、多要素認証を無効にしておくことをお勧めします。

1. **［作成］** を選択します

1. **[ユーザー属性と要求]** で **[さらに表示する]** を選択します。 フォームで、前のセクションで ID プロバイダーのセットアップ時に指定した属性を、少なくとも 1 つ選択します。

1. **[OK]** を選択します。  

### <a name="test-the-policy"></a>ポリシーのテスト

1. 新しく作成されたポリシーを選択します。

2. **[ユーザー フローを実行します]** を選択します。

3. フォームに返信 URL を入力します。

4. **[ユーザー フローを実行します]** を選択します。 Trusona OIDC ゲートウェイにリダイレクトされるはずです。 Trusona ゲートウェイで、Trusona アプリで、または Trusona モバイル SDK を使用してカスタム アプリで、表示されているセキュア QR コードをスキャンします。

5. セキュア QR コードをスキャンすると、手順 3. で定義した応答 URL にリダイレクトされるはずです。

## <a name="next-steps"></a>次のステップ  

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](custom-policy-overview.md)

- [AAD B2C のカスタム ポリシーの概要](custom-policy-get-started.md?tabs=applications)

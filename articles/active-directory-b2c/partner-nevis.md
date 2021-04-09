---
title: Azure Active Directory B2C と Nevis を構成するチュートリアル
titleSuffix: Azure AD B2C
description: パスワードレス認証のために Azure AD B2C 認証を Nevis と統合する方法について説明します
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 282ec6a25dc381dc51f28534d272bae57d2e792e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98674995"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>パスワードレス認証のために Azure Active Directory B2C を使用して Nevis を構成するチュートリアル

このサンプル チュートリアルでは、Azure AD B2C を [Nevis](https://www.nevis.net/solution/authentication-cloud) で拡張してパスワードレス認証を有効にする方法について説明します。 Nevis は、Nevis Access アプリでモバイルファーストの完全にブランド化されたエンドユーザー エクスペリエンスを提供して、強力な顧客認証を実現するとともに、Payment Services Directive 2 (PSD2) のトランザクション要件を遵守します。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Nevis [試用版アカウント](https://www.nevis-security.com/aadb2c/)

- Azure AD サブスクリプション。 所有していない場合は、[無料アカウント](https://azure.microsoft.com/free/)を入手してください。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](./tutorial-create-tenant.md)。

- Nevis をサインアップ ポリシー フローに統合する場合は、[カスタム ポリシー](./custom-policy-get-started.md)を使用するように構成された Azure AD B2C 環境。

## <a name="scenario-description"></a>シナリオの説明

このシナリオでは、パスワードレス認証を行うために、完全にブランド化されたアクセス アプリをバックエンド アプリケーションに追加します。 このソリューションは、次のコンポーネントで構成されています。

- バックエンドに対してサインインとサインアップのポリシーを組み合わせた Azure AD B2C テナント
- Azure AD B2C を強化するための、Nevis インスタンスとその REST API
- 独自のブランド化されたアクセス アプリ

この図は実装を示しています。

![Azure AD B2C と Nevis を使用した高レベルのパスワード サインイン フロー](./media/partner-nevis/nevis-architecture-diagram.png)

|手順 | 説明 |
|:-----| :-----------|
| 1. | ユーザーが、Azure AD B2C のサインインおよびサインアップ ポリシーを使用して、アプリケーションへのサインインまたはサインアップを試行します。
| 2. | サインアップ時に、QR コードを使用して、Nevis Access アプリがユーザー デバイスに登録されます。 ユーザー デバイスで秘密キーが生成され、ユーザーの要求の署名に使用されます。
| 3. |  Azure AD B2C は、RESTful 技術プロファイルを使用して、Nevis ソリューションでのログインを開始します。
| 4. | ログイン要求は、プッシュ メッセージ、QR コード、またはディープリンクとしてアクセス アプリに送信されます。
| 5. | ユーザーは、生体認証を使用してサインインの試行を承認します。 次に、メッセージが Nevis に返されます。これにより、格納されている公開キーを使用してログインが検証されます。
| 6. | Azure AD B2C は最後の要求を Nevis に送信して、ログインが正常に完了したことを確認します。
| 7. |Azure AD B2C からの成功/失敗のメッセージに基づいて、アプリケーションへのアクセスがユーザーに対して許可または拒否されます。

## <a name="integrate-your-azure-ad-b2c-tenant"></a>Azure AD B2C テナントの統合

### <a name="onboard-to-nevis"></a>Nevis へのオンボード 

[Nevis アカウントにサインアップします](https://www.nevis-security.com/aadb2c/)。
2 つの電子メールを受信します。

1. 管理アカウントの通知

2. モバイル アプリの招待

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>Nevis アカウントへの Azure AD B2C テナントの追加

1. Nevis 管理アカウント試用のメールから、管理キーをクリップボードにコピーします。

2. ブラウザーで https://console.nevis.cloud/ を開きます。

3. キーを使用して管理コンソールにサインインします。

4. **[インスタンスの追加]** を選択します。

5. 新しく作成したインスタンスを選択して開きます。

6. 横のナビゲーション バーで、 **[Custom Integrations]\(カスタム統合\)** を選択します。

7. **[Add custom integration]\(カスタム統合を追加\)** を選択します。

8. 統合の名前として Azure AD B2C テナント名を入力します。

9. URL/ドメインには `https://yourtenant.onmicrosoft.com` を入力します。

10. **[次へ]** を選択します

>[!NOTE]
>後で Nevis のアクセス トークンが必要になります。

11. **[完了]** を選択します。

### <a name="install-the-nevis-access-app-on-your-phone"></a>電話への Nevis Access アプリのインストール

1. Nevis モバイル アプリ試用版のメールから、**Test Flight アプリ** の招待を開きます。

2. アプリをインストールします。

3. 指示に従って Nevis Access アプリをインストールします。

### <a name="integrate-azure-ad-b2c-with-nevis"></a>Azure AD B2C と Nevis の統合

1. [Azure Portal](https://portal.azure.com/)を開きます。

2. Azure AD B2C テナントに切り替えます。 Azure AD B2C テナントは、通常、別のテナントにあるため、適切なテナントが選択されていることを確認します。

3. メニューで、 **[Identity Experience Framework (IEF)]** を選択します。

4. **[ポリシー キー]** を選択します。

5. **[追加]** を選択し、次の設定で新しいキーを作成します。

      a. [オプション] の **[手動]** を選択します。

      b. 名前を **AuthCloudAccessToken** に設定します。

      c. 以前に保存した **Nevis アクセス トークン** を "シークレット" フィールドに貼り付けます。

      d. キーの使用法として **[暗号化]** を選択します。

      e. **[作成]** を選択します。

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>nevis.html の構成および Azure BLOB ストレージへのアップロード

1. ID 環境 (IDE) で、[**ポリシー**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) フォルダーにアクセスします。

2. [**nevis.html**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html) ファイルを開きます。

3. **authentication_cloud_url** を、Nevis 管理コンソールの URL に置き換えます。`https://<instance_id>.mauth.nevis.cloud`

4. 変更内容をファイルに **保存** します。

5. [指示](./customize-ui-with-html.md#2-create-an-azure-blob-storage-account)に従って、**nevis.html** ファイルを Azure BLOB ストレージにアップロードします。

6. [指示](./customize-ui-with-html.md#3-configure-cors)に従って、このファイルのクロスオリジン リソース共有 (CORS) を有効にします。

7. アップロードが完了し、CORS が有効になったら、一覧で **nevis.html** ファイルを選択します。

8. **[概要]** タブで、**URL** の横にある **[リンクのコピー]** アイコンを選択します。

9. 新しいブラウザー タブでリンクを開いて、灰色のボックスが表示されていることを確認します。

>[!NOTE]
>BLOB リンクは後で必要になります。

### <a name="customize-your-trustframeworkbasexml"></a>TrustFrameworkBase.xml のカスタマイズ

1. IDE で、[**ポリシー**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) フォルダーにアクセスします。

2. [**TrustFrameworkBase.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) ファイルを開きます。

3. **yourtenant** を、**TenantId** の Azure テナント アカウント名に置き換えます。

4. **yourtenant** を、**PublicPolicyURI** の Azure テナント アカウント名に置き換えます。

5. すべての **authentication_cloud_url** インスタンスを、Nevis 管理コンソールの URL に置き換えます。

6. 変更内容をファイルに **保存** します。

### <a name="customize-your-trustframeworkextensionsxml"></a>TrustFrameworkExtensions.xml のカスタマイズ

1. IDE で、[**ポリシー**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) フォルダーにアクセスします。

2. [**TrustFrameworkExtensions.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) ファイルを開きます。

3. **yourtenant** を、**TenantId** の Azure テナント アカウント名に置き換えます。

4. **yourtenant** を、**PublicPolicyURI** の Azure テナント アカウント名に置き換えます。

5. また、**BasePolicy** の **TenantId** で、_yourtenant_ を Azure テナント アカウント名に置き換えます。

6. **BuildingBlocks** で、**LoadUri** を、BLOB ストレージ アカウントの _nevis.html_ の BLOB リンク URL に置き換えます。

7. ファイルを **保存** します。

### <a name="customize-your-signuporsigninxml"></a>SignUpOrSignin.xml のカスタマイズ

1. IDE で、[**ポリシー**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) フォルダーにアクセスします。

2. [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) ファイルを開きます。

3. **yourtenant** を、**TenantId** の Azure テナント アカウント名に置き換えます。

4. **yourtenant** を、**PublicPolicyUri** の Azure テナント アカウント名に置き換えます。

5. また、**BasePolicy** の **TenantId** で、**yourtenant** を Azure テナント アカウント名に置き換えます。

6. ファイルを **保存** します。

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Azure AD B2C へのカスタム ポリシーのアップロード

1. [Azure AD B2C テナント](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview)のホームを開きます。

2. **[Identity Experience Framework]** を選択します。

3. **[カスタム ポリシーのアップロード]** を選択します。

4. 変更した **TrustFrameworkBase.xml** ファイルを開きます。

5. **[Overwrite the custom policy if it already exists]\(カスタム ポリシーが既に存在する場合は上書きする\)** チェックボックスを選択します。
6. **[アップロード]** を選択します。

7. **TrustFrameworkExtensions.xml** に対して、手順 5. と 6. を繰り返します。

8. **SignUpOrSignin.xml** に対して、手順 5. と 6. を繰り返します。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

### <a name="test-account-creation-and-nevis-access-app-setup"></a>テスト アカウントの作成および Nevis Access アプリのセットアップ

1. [Azure AD B2C テナント](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview)のホームを開きます。

2. **[Identity Experience Framework]** を選択します。

3. カスタム ポリシーまで下にスクロールし、**B2C_1A_signup_signin** を選択します。

4. **[今すぐ実行]** を選択します。

5. ポップアップ ウィンドウで **[今すぐサインアップ]** を選択します。

6. 自分のメール アドレスを追加します。

7. **[確認コードを送信する]** を選択します。

8. メールから確認コードをコピーします。

9. **[認証]** を選択します。

10. フォームに新しいパスワードと表示名を入力します。

11. **[作成]** を選択します。

12. QR コードのスキャン ページが表示されます。

13. 電話で、**Nevis Access アプリ** を開きます。

14. **[Face ID]** を選択します。

15. 画面に **認証の登録が成功** したと表示されたら、 **[Continue]** を選択します。

16. 電話でもう一度顔認証を行います。

17. デコードされたトークンの詳細が表示される [jwt.ms](https://jwt.ms) ランディング ページに移動します。

### <a name="test-the-pure-passwordless-sign-in"></a>パスワードレス サインインのテスト

1. **Identity Experience Framework** で、**B2C_1A_signup_signin** を選択します。

2. **[今すぐ実行]** を選択します。

3. ポップアップ ウィンドウで、 **[パスワードレス認証]** を選択します。

4. 電子メール アドレスを入力します。

5. **[続行]** をクリックします。

6. 電話で、通知として **[Nevis Access app notification]\(Nevis Access アプリ通知\)** を選択します。

7. 顔認証を行います。

8. トークンが表示される [jwt.ms](https://jwt.ms) ランディング ページに自動的に移動されます。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./custom-policy-get-started.md?tabs=applications)
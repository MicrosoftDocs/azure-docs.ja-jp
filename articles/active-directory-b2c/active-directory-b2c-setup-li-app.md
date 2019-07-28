---
title: LinkedIn アカウントでのサインアップとサインインを設定する - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C を使用するアプリケーションで LinkedIn アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: 5ffe75a58a8cfb2409af3a65ff822023d7ccf5d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508472"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して LinkedIn アカウントでのサインアップおよびサインインを設定する

## <a name="create-a-linkedin-application"></a>LinkedIn アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で [ID プロバイダー](active-directory-b2c-reference-oauth-code.md)として LinkedIn アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ LinkedIn アカウントを持っていない場合は、[https://www.linkedin.com/](https://www.linkedin.com/) で取得できます。

1. LinkedIn アカウントの資格情報を使用して、[LinkedIn 開発者の Web サイト](https://www.developer.linkedin.com/)にサインインします。
2. **[マイ アプリ]** を選択し、 **[アプリケーションの作成]** をクリックします。
3. **[会社名]** 、 **[アプリケーション名]** 、 **[アプリケーションの説明]** 、 **[アプリケーションのロゴ]** 、 **[Application Use] (アプリケーションの用途)** 、 **[Web サイトの URL]** 、 **[勤務先の電子メール]** 、および **[勤務先電話番号]** を入力します。
4. **LinkedIn API の使用条件**に同意し、 **[Submit (送信)]** をクリックします。
5. **[クライアント ID]** と **[クライアント シークレット]** の値をコピーします。 これらは、 **[Authentication Keys] (認証キー)** の下にあります。 テナントで ID プロバイダーとして LinkedIn を構成するには、両方の値が必要です。 **[Client Secret]** は、重要なセキュリティ資格情報です。
6. **[Authorized Redirect URLs] (認証済みのリダイレクト URL)** に「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-tenant-name` をテナントの名前に置き換えます。 テナントが Azure AD B2C に大文字で定義されている場合でも、テナント名を入力するときに、すべての小文字を使用する必要があります。 **[追加]** を選択し、 **[更新]** をクリックします。

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>ID プロバイダーとして LinkedIn アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
4. **[ID プロバイダー]** 、 **[追加]** の順に選択します。
5. **[名前]** を入力します。 たとえば、「*LinkedIn*」と入力します。
6. **[ID プロバイダーの種類]** 、 **[LinkedIn]** の順に選択し、 **[OK]** をクリックします。
7. **[この ID プロバイダーをセットアップします]** を選択し、 **[クライアント ID]** として前に記録したクライアント ID を入力し、前に作成した LinkedIn アカウント アプリケーションの **[クライアント シークレット]** として記録したクライアント シークレットを入力します。
8. **[OK]** をクリックし、 **[作成]** をクリックして LinkedIn アカウントの構成を保存します。

## <a name="migration-from-v10-to-v20"></a>v1.0 から v2.0 に移行する

LinkedIn では最近、[API が v1.0 から v2.0 に更新](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)されました。 移行の一環として、Azure AD B2C がサインアップ中に取得できるのは LinkedIn ユーザーのフル ネームのみとなります。 メール アドレスが、サインアップ時に収集される属性の 1 つである場合、ユーザーがメール アドレスを手動で入力して検証する必要があります。

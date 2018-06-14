---
title: Azure AD B2C 用の Twitter 構成 | Microsoft Docs
description: Azure Active Directory B2C によってセキュリティ保護されたアプリケーションで、Twitter アカウントを使用する顧客にサインアップとサインインを提供します。
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/17/2018
ms.author: davidmu
ms.openlocfilehash: 40e4c5549414765dabc6f37c5ffb5aea519ae673
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2018
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>Azure AD B2C を使用して、Twitter アカウントを使用するコンシューマーにサインアップとサインインを提供する

## <a name="create-a-twitter-application"></a>Twitter アプリケーションを作成する
Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Twitter を使用するには、Twitter アプリケーションを作成し、適切なパラメーターを指定する必要があります。 そのためには、Twitter アカウントが必要です。 アカウントがない場合は、[https://twitter.com/signup](https://twitter.com/signup) で取得できます。

1. [Twitter Apps](https://apps.twitter.com/) に移動し、資格情報を使用してサインインします。
2. **[Create New App]** をクリックします。 
3. フォームの **[Name]****[Description]****[Website]** に値を入力します。
4. **[Callback URL]** に「`https://login.microsoftonline.com/te/{tenant}/{policyId}/oauth1/authresp`」と入力します。 **{tenant}** は、実際のテナントの名前 (例: contosob2c.onmicrosoft.com) に、**{policyId}** は実際のポリシーID（例：b2c_1_policy）に置き換える必要があります。Callback URL は全て小文字でなければならないことに注意してください。
5. **開発者契約**に同意するボックスをオンにし、**[Create your Twitter application]** をクリックします。
6. アプリが作成されたら、リストからアプリを選択し、 **Settings** タブを開きます。
7. **Enable Callback Locking** のチェックを外して **Update settings** をクリックします。
8. **[Keys and Access Tokens]\(キーとアクセス トークン\)** をクリックします。
9. **[Consumer Key]** と **[Consumer Secret]** の値をコピーします。 テナントで ID プロバイダーとして Twitter を構成するには、この両方の値が必要です。

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>テナントで ID プロバイダーとして Twitter を構成する
1. Azure AD B2C テナントの全体管理者として、[Azure Portal](https://portal.azure.com/) にログインします。 
2. Azure AD B2C テナントに切り替えるには、ポータルの右上隅で Azure AD B2C ディレクトリを選択します。
3. **[すべてのサービス]** をクリックし、**[セキュリティ + ID]** のサービスの一覧で **[Azure AD B2C]** を選択します。
4. **[ID プロバイダー]** をクリックします。
4. ID プロバイダー構成のわかりやすい **[名前]** を指定します。 たとえば、「Twitter」と入力します。
5. **[ID プロバイダーの種類]** をクリックし、**[Twitter (プレビュー)]** を選択して、**[OK]** をクリックします。
6. **[この ID プロバイダーをセットアップします]** をクリックします。**[クライアント ID]** に **Twitter のコンシューマー キー**を入力し、**[クライアント シークレット]** に **Twitter のコンシューマー シークレット**を入力します。
7. **[OK]** をクリックし、**[作成]** をクリックして Twitter の構成を保存します。

## <a name="next-steps"></a>次の手順

[組み込みのポリシー](active-directory-b2c-reference-policies.md)を作成するか編集し、ID プロバイダーとして Twitter を追加します。

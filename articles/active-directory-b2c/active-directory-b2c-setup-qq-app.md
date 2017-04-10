---
title: "Azure Active Directory B2C: QQ の構成 | Microsoft Docs"
description: "Azure Active Directory B2C によってセキュリティ保護されたアプリケーションで、QQ アカウントを使用するコンシューマーにサインアップとサインインを提供します。"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 18c2cf94-8004-4de1-81c2-e45be65ce12d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 4448b945be947847293beb710929adc77f19c945
ms.lasthandoff: 03/29/2017


---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>Azure Active Directory B2C: QQ アカウントを使用するコンシューマーにサインアップとサインインを提供する

> [!NOTE]
> この機能はプレビュー段階にあります。
> 

## <a name="create-a-qq-application"></a>QQ アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で ID プロバイダーとして QQ を使用するには、QQ アプリケーションを作成し、適切なパラメーターを指定する必要があります。 そのためには QQ アカウントが必要です。 QQ アカウントがない場合は、[https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) で取得できます。

### <a name="register-for-the-qq-developer-program"></a>QQ 開発者プログラムに登録する

1. [QQ 開発者ポータル](http://open.qq.com)に移動し、QQ アカウントの資格情報を使用してサインインします。
2. サインインしたら、[http://open.qq.com/reg](http://open.qq.com/reg) に移動して開発者として登録します。
3. メニューの **[个人 (個人開発者)]** を選択します。
4. フォームに必要な情報を入力し、**[下一步 (次の手順)]** をクリックします。
5. 電子メールによる確認プロセスを完了します。

> [!NOTE]
> 開発者として登録したら、承認されるまで数日待つ必要があります。 

### <a name="register-a-qq-application"></a>QQ アプリケーションを登録する

1. [https://connect.qq.com/index.html](https://connect.qq.com/index.html) に移動します。
2. **[应用管理 (アプリの管理)]** をクリックします。
3. **[创建应用 (アプリの作成)]** をクリックします。
4. 必要なアプリ情報を入力します。
5. **[创建应用 (アプリの作成)]** をクリックします。
6. 必要な情報を入力します。
7. **[授权回调域 (コールバック URL)]** フィールドに、「`https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`」と入力します。 たとえば、`tenant_name` が contoso.onmicrosoft.com の場合、URL を `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp` に設定します。
8. **[创建应用 (アプリの作成)]** をクリックします。
9. 確認ページで、**[应用管理 (アプリの管理)]** をクリックしてアプリの管理ページに戻ります。
10. 作成したアプリの横の **[查看 (表示)]** をクリックします。
11. **[修改 (編集)]** をクリックします。
12. ページの上部の**アプリ ID** と**アプリ キー**をコピーします。

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>テナントで ID プロバイダーとして QQ を構成する
1. この手順に従って、Azure Portal で [B2C 機能ブレードに移動](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) します。
2. B2C 機能ブレードで、 **[ID プロバイダー]**をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. ID プロバイダー構成のわかりやすい **[名前]** を指定します。 たとえば、「QQ」と入力します。
5. **[ID プロバイダーの種類]** をクリックし、**[QQ]** を選択して、**[OK]** をクリックします。
6. **[この ID プロバイダーをセットアップします]** をクリックします。
7. **クライアント ID** として、先ほどコピーした**アプリ キー**を入力します。
8. **クライアント シークレット**として、先ほどコピーした**アプリケーション シークレット**を入力します。
9. **[OK]** をクリックし、**[作成]** をクリックして QQ の構成を保存します。



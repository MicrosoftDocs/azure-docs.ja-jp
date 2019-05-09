---
title: Azure Active Directory B2C を使用して QQ アカウントでのサインアップおよびサインインを設定する | Microsoft Docs
description: Azure Active Directory B2C を使用するアプリケーションで QQ アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 38e17308f7aab5b28509d7a3509be0de844ee94f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55160163"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して QQ アカウントでのサインアップおよびサインインを設定する

> [!NOTE]
> この機能はプレビュー段階にあります。
> 

## <a name="create-a-qq-application"></a>QQ アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で ID プロバイダーとして QQ アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ QQ アカウントを持っていない場合は、[https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) で取得できます。

### <a name="register-for-the-qq-developer-program"></a>QQ 開発者プログラムに登録する

1. QQ アカウントの資格情報を使用して、[QQ 開発者ポータル](http://open.qq.com)にサインインします。
2. サインインしたら、[http://open.qq.com/reg](http://open.qq.com/reg) に移動して開発者として登録します。
3. **[个人 (個人開発者)]** を選択します。
4. 必要な情報を入力し、 **[下一步 (次のステップ)]** を選択します。
5. 電子メールによる確認プロセスを完了します。 開発者として登録したら、承認されるまで数日待つ必要があります。 

### <a name="register-a-qq-application"></a>QQ アプリケーションを登録する

1. [https://connect.qq.com/index.html](https://connect.qq.com/index.html) に移動します。
2. **[应用管理 (アプリの管理)]** を選択します。
5. **[创建应用 (アプリの作成)]** を選択し、必要な情報を入力します。
7. **[授权回调域 (コールバック URL)]** に、「`https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp`」と入力します。 たとえば、`tenant_name` が contoso の場合は、URL を `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` に設定します。
8. **[创建应用 (アプリの作成)]** を選択します。
9. 確認ページで、**[应用管理 (アプリの管理)]** を選択してアプリの管理ページに戻ります。
10. 作成したアプリの横の **[查看 (表示)]** を選択します。
11. **[修改 (編集)]** を選択します。
12. **アプリ ID** と**アプリ キー**をコピーします。 ID プロバイダーをテナントに追加するには、これらの両方の値が必要です。

## <a name="configure-qq-as-an-identity-provider"></a>QQ を ID プロバイダーとして構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。
4. **[ID プロバイダー]**、**[追加]** の順に選択します。
5. **[名前]** を入力します。 たとえば、「*QQ*」と入力します。
6. **[ID プロバイダーの種類]** を選択し、**[QQ (Preview)]**(QQ (プレビュー)) を選択して、**[OK]** をクリックします。
7. **[この ID プロバイダーをセットアップします]** を選択し、**[クライアント ID]** として前に記録したアプリ ID を入力し、前に作成した QQ アプリケーションの **[クライアント シークレット]** として記録したアプリ キーを入力します。
8. **[OK]** をクリックし、**[作成]** をクリックして QQ の構成を保存します。


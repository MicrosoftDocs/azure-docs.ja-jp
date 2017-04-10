---
title: "Azure Active Directory B2C: WeChat の構成 | Microsoft Docs"
description: "Azure Active Directory B2C によってセキュリティ保護されたアプリケーションで、WeChat アカウントを使用するコンシューマーにサインアップとサインインを提供します。"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: d2424c66-ba68-4d82-847e-d137683527b0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d1c18a3cf8ca9d55e4bca224cce8d6dba75fcb0d
ms.lasthandoff: 03/29/2017


---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-wechat-accounts"></a>Azure Active Directory B2C: WeChat アカウントを使用するコンシューマーにサインアップとサインインを提供する

> [!NOTE]
> この機能はプレビュー段階にあります。
> 

## <a name="create-a-wechat-application"></a>WeChat アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で ID プロバイダーとして WeChat を使用するには、WeChat アプリケーションを作成し、適切なパラメーターを指定する必要があります。 そのためには WeChat アカウントが必要です。 WeChat アカウントがない場合は、WeChat のいずれかのモバイル アプリを使用してサインアップするか、QQ 番号を使用することでアカウントを取得できます。 その後、アカウントを WeChat 開発者プログラムに登録します。 詳細については、 [こちらで](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html)確認できます。

### <a name="register-a-wechat-application"></a>WeChat アプリケーションを登録する

1. [https://open.weixin.qq.com/](https://open.weixin.qq.com/) に移動し、ログインします。
2. **[管理中心 (管理センター)]** をクリックします。
3. 新しいアプリケーションを登録するために必要な手順に従います。
4. **[授权回调域 (コールバック URL)]** に、「`https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`」と入力します。 たとえば、`tenant_name` が contoso.onmicrosoft.com の場合、URL を `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp` に設定します。
5. **アプリ ID** と**アプリ キー**を見つけてコピーします。 この情報は後で必要になります。

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>テナントで ID プロバイダーとして WeChat を構成する
1. この手順に従って、Azure Portal で [B2C 機能ブレードに移動](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) します。
2. B2C 機能ブレードで、 **[ID プロバイダー]**をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. ID プロバイダー構成のわかりやすい **[名前]** を指定します。 たとえば、「WeChat」と入力します。
5. **[ID プロバイダーの種類]** をクリックし、**[WeChat]** を選択して、**[OK]** をクリックします。
6. **[この ID プロバイダーをセットアップします]** をクリックします。
7. **クライアント ID** として、先ほどコピーした**アプリ キー**を入力します。
8. **クライアント シークレット**として、先ほどコピーした**アプリケーション シークレット**を入力します。
9. **[OK]** をクリックし、**[作成]** をクリックして WeChat の構成を保存します。



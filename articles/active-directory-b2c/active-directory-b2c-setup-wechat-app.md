---
title: Azure Active Directory B2C を使用して WeChat アカウントでのサインアップおよびサインインを設定する | Microsoft Docs
description: Azure Active Directory B2C を使用するアプリケーションで WeChat アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: aa44edcf009d381894a581172ea5edffefe946a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508133"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して WeChat アカウントでのサインアップおよびサインインを設定する

> [!NOTE]
> この機能はプレビュー段階にあります。
> 

## <a name="create-a-wechat-application"></a>WeChat アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で ID プロバイダーとして WeChat アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ WeChat アカウントを持っていない場合は、[https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html) で取得できます。

### <a name="register-a-wechat-application"></a>WeChat アプリケーションを登録する

1. WeChat 資格情報を使用して、[ https://open.weixin.qq.com/ ](https://open.weixin.qq.com/) にサインインします。
2. **[管理中心 (管理センター)]** を選択します。
3. 新しいアプリケーションを登録するための手順に従います。
4. **[授权回调域 (コールバック URL)]** に、「`https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 たとえば、テナント名が contoso の場合、URL を`https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`に設定します。
5. **アプリ ID** と**アプリ キー**をコピーします。 ID プロバイダーをテナントに追加するには、これらが必要です。

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>テナントで ID プロバイダーとして WeChat を構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
4. **[ID プロバイダー]** 、 **[追加]** の順に選択します。
5. **[名前]** を入力します。 たとえば、「*WeChat*」と入力します。
6. **[ID プロバイダーの種類]** を選択し、 **[WeChat (Preview)]** (WeChat (プレビュー)) を選択して、 **[OK]** をクリックします。
7. **[この ID プロバイダーをセットアップします]** を選択し、 **[クライアント ID]** として前に記録したアプリ ID を入力し、前に作成した WeChat アプリケーションの **[クライアント シークレット]** として記録したアプリ キーを入力します。
8. **[OK]** をクリックし、 **[作成]** をクリックして WeChat の構成を保存します。


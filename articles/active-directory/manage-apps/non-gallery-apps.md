---
title: アプリ ギャラリーに記載されていないアプリケーションで Azure AD を使用する
description: Azure AD ギャラリーに記載されていないアプリを統合する方法について説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/27/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 9721679938517e38f669f78ee0f5f9f3a80e05a7
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258271"
---
# <a name="using-azure-ad-for-applications-not-listed-in-the-app-gallery"></a>アプリ ギャラリーに記載されていないアプリケーションで Azure AD を使用する

[アプリの追加](add-application-portal.md)に関するクイックスタートでは、Azure AD テナントにアプリを追加する方法について説明しています。

[Azure AD アプリケーション ギャラリー](../saas-apps/tutorial-list.md)の選択項目に加えて、**ギャラリー以外のアプリケーション** を追加することもできます。 

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Azure AD ギャラリーに記載されていないアプリの機能

組織に既に存在するアプリケーションや、Azure AD ギャラリーに属していないベンダーのサードパーティ アプリケーションを追加できます。 [使用許諾契約書](https://azure.microsoft.com/pricing/details/active-directory/)に応じて、以下の機能を使用することができます。

- [Security Assertion Markup Language (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) ID プロバイダーをサポートする任意のアプリケーションのセルフサービス統合 (SP または IdP によって開始)
- [パスワードベースの SSO](sso-options.md#password-based-sso)
- [ユーザー プロビジョニング用の System for Cross-Domain Identity Management (SCIM) プロトコル](../app-provisioning/use-scim-to-provision-users-and-groups.md)を使用するアプリケーションのセルフサービス接続
- [Office 365 アプリ ランチャー](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/)または[マイ アプリ](sso-options.md#linked-sign-on)での任意のアプリケーションへのリンクの追加機能

カスタム アプリケーションと Azure AD を統合する方法に関する開発者向けガイダンスをお探しの場合は、[Azure AD の認証シナリオ](../develop/authentication-vs-authorization.md)に関するページを参照してください。 [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) などの最新のプロトコルを使用してユーザーを認証するアプリを開発した場合は、それを Azure portal の[アプリの登録](../develop/quickstart-register-app.md)エクスペリエンスを使用して Microsoft ID プラットフォームに登録できます。

## <a name="next-steps"></a>次のステップ

- [アプリ管理のクイックスタート シリーズ](view-applications-portal.md)
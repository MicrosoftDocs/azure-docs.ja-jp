---
title: Azure Active Directory による Kerberos の制約付き委任
description: Azure Active Directory を使用した Kerberos の制約付き委任の取得に関するアーキテクチャ ガイダンス。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1c7aa4d2300a6dee44da067b122fc7af97f7aa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172858"
---
# <a name="windows-authentication---kerberos-constrained-delegation-with-azure-active-directory"></a>Windows 認証 - Azure Active Directory による Kerberos の制約付き委任

Kerberos の制約付き委任 (KCD) を使用すると、リソース間で制約付き委任を設定できます。これはサービス プリンシパル名に基づいています。 ドメイン管理者は委任を作成する必要があります。そして、1 つのドメインに制限されます。 リソースベースの KCD は、Active Directory フォレスト内の複数のドメインにユーザーが存在する Web アプリケーションに対して Kerberos 認証を提供する手段としてよく使用されます。

Azure Active Directory アプリケーション プロキシによって、アクセス用の Kerberos チケットと Kerberos の制約付き委任 (KCD) とを必要とする KCD ベースのアプリケーションへのシングル サインオン (SSO) とリモート アクセスが可能になります。

統合 Windows 認証 (IWA) を使用しているオンプレミスの KCD アプリケーションへの SSO を有効にするには、Active Directory でユーザーを借用するためのアクセス許可をアプリケーション プロキシ コネクタに付与します。 このアクセス許可は、ユーザーの代理でトークンを送受信するために、アプリケーション プロキシコ ネクタによって使用されます。

## <a name="use-when"></a>使用する状況

オンプレミスの IWA アプリケーションを対象にして、リモート アクセスを提供し、事前認証を使用して保護し、SSO を提供する必要があります。

![アーキテクチャの図](./media/authentication-patterns/kcd-auth.png)

## <a name="components-of-system"></a>システムのコンポーネント

* **[ユーザー]** : アプリケーション プロキシによって処理されるレガシ アプリケーションにアクセスします。

* **Web ブラウザー**: アプリケーションの外部 URL にアクセスするためにユーザーが操作するコンポーネントです。

* **Azure AD**:ユーザーを認証します。 

* **アプリケーション プロキシ サービス**: リバース プロキシとして機能して、ユーザーからオンプレミスのアプリケーションに要求を送信します。 Azure AD にあります。 アプリケーション プロキシは、任意の条件付きアクセス ポリシーを適用することもできます。

* **アプリケーション プロキシ コネクタ**: アプリケーションへの接続を提供するために、Windows サーバーにオンプレミスでインストールされます。 Azure AD に応答を返します。 Active Directory との KCD ネゴシエーションを実行します。このときに、ユーザーの代理でアプリケーションに対する Kerberos トークンを取得します。

* **Active Directory**: アプリケーション用の Kerberos トークンをアプリケーション プロキシ コネクタに送信します。

* **レガシ アプリケーション**: アプリケーション プロキシからユーザー要求を受け取るアプリケーションです。 レガシ アプリケーションから、アプリケーション プロキシ コネクタに応答が返されます。

## <a name="implement-windows-authentication-kcd-with-azure-ad"></a>Azure AD による Windows 認証 (KCD) を実装する

* [アプリケーション プロキシを使ったアプリへのシングル サインオンの Kerberos の制約付き委任](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md) 

* [Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する](../manage-apps/application-proxy-add-on-premises-application.md)


---
title: Azure Active Directory を使用した SAML 認証
description: Azure Active Directory を使用する SAML 認証のアーカイブに関するアーキテクチャ ガイダンス
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
ms.openlocfilehash: 7cd2aa5e9ff8cbaeead69f11d2e3de7f760b53ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168646"
---
# <a name="saml-authentication-with-azure-active-directory"></a>Azure Active Directory を使用した SAML 認証

Security Assertion Markup Language (SAML) は、ID プロバイダーとサービス プロバイダー間で認証および認可データを交換するためのオープン標準です。 SAML は、セキュリティ アサーション (サービス プロバイダーでアクセス制御の決定を行うために使用されるステートメント) 用の XML ベースのマークアップ言語です。 

SAML 仕様には、次の 3 つのロールが定義されています。

* プリンシパル (通常はユーザー)
* ID プロバイダー (IdP)
* サービス プロバイダー (SP)


## <a name="use-when"></a>使用する状況

エンタープライズ SAML アプリケーション用にシングル サインオン (SSO) エクスペリエンスを提供する必要があります。

特にセキュリティ ドメイン間で SSO を拡張すると、SAML で対処される最も重要なユース ケースの 1 つは SSO ですが、(プロファイルと呼ばれる) 他のユース ケースもあります。 

![SAML のアーキテクチャ図](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>システムのコンポーネント

* **[ユーザー]** :アプリケーションからサービスを要求します。

* **Web ブラウザー**:ユーザーが操作するコンポーネントです。

* **Web アプリ**:SAML がサポートされ、IdP として Azure AD が使用されるエンタープライズ アプリケーションです。

* **トークン**:プリンシパル (ユーザー) に関する IdP で行われたクレーム セットが保持される SAML アサーションです (SAML トークンとも呼ばれます)。 認証情報、属性、および認可決定ステートメントが含まれています。

* **Azure AD**:SAML アプリ用に SSO と多要素認証を提供するエンタープライズ クラウド IdP です。 証明書利用者アプリケーションへの認証サービスを提供しながら、ユーザーに関する ID 情報が同期、保守、管理されます。 

## <a name="implement-saml-authentication-with-azure-ad"></a>Azure AD を使用して SAML 認証を実装する

* [Azure Active Directory を使用して SaaS アプリケーションを統合するためのチュートリアル](../saas-apps/tutorial-list.md) 

* [ギャラリー以外のアプリケーションに SAML ベースのシングル サインオンを構成する](../manage-apps/add-application-portal.md) 

* [Azure AD で SAML プロトコルを使用する方法](../develop/active-directory-saml-protocol-reference.md)
---
title: Azure Active Directory 認証と同期プロトコルの概要
description: Azure AD とレガシ認証プロトコルの統合と同期パターンに関するアーキテクチャ ガイダンス
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
ms.openlocfilehash: ab63bc5bd2819a239741da525eebb2404a47bbf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92441200"
---
# <a name="azure-active-directory-integrations-with-authentication-and-synchronization-protocols"></a>Azure Active Directory と認証および同期プロトコルの統合

Microsoft Azure Active Directory (Azure AD) では、多くの認証と同期プロトコルと統合することができます。 認証統合を行うと、従来の認証方法を使うアプリケーションをほとんどまたはまったく変更せずに、Azure AD とそのセキュリティと管理の機能を使用できます。 同期統合を行うと、ユーザーとグループのデータを Azure AD に同期し、その後、ユーザー Azure AD 管理機能を同期できるようになります。 一部の同期パターンでは、自動プロビジョニングも有効にされます。

## <a name="legacy-authentication-protocols"></a>レガシ認証プロトコル

次の表に、従来の認証プロトコルとの認証 Azure AD 統合とその機能を示します。 表示する認証プロトコルの名前を選択してください。

* 詳細な説明

* いつ使用するか

* アーキテクチャ図

* システム コンポーネントの説明

* 統合を実装する方法のリンク

 

| 認証プロトコル| 認証| 承認| Multi-factor Authentication| 条件付きアクセス |
| - |- | - | - | - |
| [ヘッダーベースの認証](auth-header-based.md)|![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png) |
| [LDAP 認証](auth-ldap.md)| ![チェック マーク](./media/authentication-patterns/check.png)| | |  |
| [OAuth 2.0 認証](auth-oauth2.md)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png) |
| [OIDC 認証](auth-oidc.md)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png) |
| [パスワード ベースの SSO 認証](auth-password-based-sso.md )| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png) |
| [RADIUS 認証]( auth-radius.md)| ![チェック マーク](./media/authentication-patterns/check.png)| | ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png) |
| [リモート デスクトップ ゲートウェイ サービス](auth-remote-desktop-gateway.md)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png) |
| [Secure Shell (SSH)](auth-ssh.md) |  ![チェック マーク](./media/authentication-patterns/check.png)| | ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png) |
| [SAML 認証](auth-saml.md)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png) |
| [Windows 認証 - Kerberos 制約付き委任](auth-kcd.md)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>同期パターン

次の表に、Azure AD 統合の同期パターンとその機能を示します。 表示するパターンの名前を選択します

* 詳細な説明

* いつ使用するか

* アーキテクチャ図

* システム コンポーネントの説明

* 統合を実装する方法のリンク



| 同期パターン| ディレクトリ同期| ユーザー プロビジョニング |
| - | - | - |
| [ディレクトリ同期](sync-directory.md)| ![チェック マーク](./media/authentication-patterns/check.png)|  |
| [LDAP 同期](sync-ldap.md)| ![チェック マーク](./media/authentication-patterns/check.png)|  |
| [SCIM 同期](sync-scim.md)| ![チェック マーク](./media/authentication-patterns/check.png)| ![チェック マーク](./media/authentication-patterns/check.png) |


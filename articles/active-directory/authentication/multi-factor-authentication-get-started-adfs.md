---
title: 2 段階認証 Azure MFA と ADFS - Azure Active Directory
description: Azure MFA および AD FS を開始する方法について説明する Azure Multi-Factor Authentication のページです。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fbbba49ddf2252e22cb32a0b8adc6fa2070e999
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847135"
---
# <a name="getting-started-with-azure-multi-factor-authentication-and-active-directory-federation-services"></a>Azure Multi-Factor Authentication と Active Directory フェデレーション サービスの概要

<center>

![Azure MFA と ADFS の概要](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

組織がオンプレミスの Active Directory と Azure Active Directory を AD FS を使ってフェデレーションする場合、Azure Multi-Factor Authentication の使用には 2 つの選択肢があります。

* Azure Multi-Factor Authentication または Active Directory フェデレーション サービスを使用したクラウド リソースのセキュリティ保護
* Azure Multi-Factor Authentication Server を使用したクラウドおよびオンプレミスのリソースのセキュリティ保護

Azure Multi-Factor Authentication と AD FS を使用したリソースのセキュリティ保護の認証エクスペリエンスを次の表に要約します

| 認証エクスペリエンス - ブラウザー ベースのアプリ | 認証エクスペリエンス - 非ブラウザー ベースのアプリ |
|:--- |:--- |
| Azure Multi-Factor Authentication を使用した Azure AD リソースのセキュリティ保護 |<li>認証の第 1 段階は、AD FS を使用してオンプレミスで実行されます。</li> <li>第 2 段階は、クラウド認証を使用して実行される電話ベースの方式です。</li> |
| Active Directory フェデレーション サービスを使用した Azure AD リソースのセキュリティ保護 |<li>認証の第 1 段階は、AD FS を使用してオンプレミスで実行されます。</li><li>第 2 段階は、要求を受け入れることによりオンプレミスで実行されます。</li> |

フェデレーション ユーザーに対するアプリ パスワードに関する注意事項があります。

* アプリ パスワードはクラウド認証を使用して検証されるため、フェデレーションはバイパスされます。 フェデレーションは、アプリ パスワードを設定するときにのみアクティブに使用されます。
* オンプレミスのクライアント アクセス制御設定は、アプリ パスワードには適用されません。
* アプリ パスワードでは、オンプレミスの認証ログ機能が失われます。
* ディレクトリ同期のアカウントの無効化/削除には 3 時間かかり、クラウド ID のアプリのパスワードの無効化/削除も遅延します。

AD FS を使用した Azure Multi-Factor Authentication または Azure Multi-Factor Authentication Server の設定の詳細については、次の記事を参照してください。

* [Azure Multi-Factor Authentication および AD FS を使用したクラウド リソースのセキュリティ保護](howto-mfa-adfs.md)
* [Azure Multi-Factor Authentication Server と Windows Server 2012 R2 AD FS を使用したクラウドおよびオンプレミスのリソースのセキュリティ保護](howto-mfaserver-adfs-2012.md)
* [Azure Multi-Factor Authentication Server と AD FS 2.0 を使用したクラウドおよびオンプレミスのリソースのセキュリティ保護](howto-mfaserver-adfs-2.md)

---
title: Azure AD Connect とフェデレーション | Microsoft Docs
description: このページは、Azure AD Connect を使用した AD FS の操作に関するすべてのドキュメントの中心となる場所です。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/09/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a97142e0c512f4f95235ad08c94c852906d3efd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92095858"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect とフェデレーション
Azure Active Directory (Azure AD) Connect を使用すると、オンプレミスの Active Directory フェデレーション サービス (AD FS) と Azure AD でフェデレーションを構成できます。 フェデレーション サインインでは、ユーザーはオンプレミスのパスワードを使用して Azure AD ベースのサービスにサインインできます。また、企業ネットワーク上では、パスワードを再度入力する必要はありません。 AD FS によるフェデレーション オプションを使用すると、AD FS の新しいインストールをデプロイすることも、Windows Server 2012 R2 ファームの既存のインストールを指定することもできます。

このトピックは Azure AD Connect のフェデレーション関連の機能に関する情報のホームであり、 すべての関連トピックへのリンクを示しています。 Azure AD Connect へのリンクについては、「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」を参照してください。

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: フェデレーションに関するトピック
| トピック | 内容 |
|:--- |:--- |
| **Azure AD Connect ユーザーのサインイン オプション** | |
| [ユーザー サインイン オプションについて](plan-connect-user-signin.md) |さまざまなユーザー サインイン オプションと、各オプションが Azure サインイン ユーザー エクスペリエンスに及ぼす影響について説明します。 |
| **Azure AD Connect を使用した AD FS のインストール** | |
| [前提条件](how-to-connect-install-custom.md#ad-fs-configuration-prerequisites) |Azure AD Connect 経由の正常な AD FS インストールの前提条件を確認します。 |
| [AD FS ファームの構成](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Azure AD Connect を使用して、新しい AD FS ファームをインストールします。 |
| [代替ログイン ID を使用して Azure AD とフェデレーションする](how-to-connect-fed-management.md#alternateid) | 代替ログイン ID を使用してフェデレーションを構成する  |
| **AD FS 構成の変更** | |
| [信頼を修復する](how-to-connect-fed-management.md#repairthetrust) |オンプレミスの AD FS と Microsoft 365 または Azure 間の現在の信頼を修復します。 |
| [新しい AD FS サーバーの追加](how-to-connect-fed-management.md#addadfsserver) |初期インストール後に、追加の AD FS サーバーで AD FS ファームを拡張します。 |
| [新しい AD FS WAP サーバーの追加](how-to-connect-fed-management.md#addwapserver) |初期インストール後に、追加の Web アプリケーション プロキシ (WAP) サーバーで AD FS ファームを拡張します。 |
| [新しいフェデレーション ドメインの追加](how-to-connect-fed-management.md#addfeddomain) |Azure AD とフェデレーションする別のドメインを追加します。 |
| [TLS/SSL 証明書の更新](how-to-connect-fed-ssl-update.md)| AD FS ファームの TLS/SSL 証明書を更新します。 |
| [Microsoft 365 および Azure AD 用のフェデレーション証明書の更新](how-to-connect-fed-o365-certs.md)|Azure AD で O365 証明書を更新します。|
| **他のフェデレーション構成** | |
| [Azure AD の複数のインスタンスと AD FS の単一インスタンスのフェデレーションを行う](how-to-connect-fed-single-adfs-multitenant-federation.md) | 複数の Azure AD と単一の AD FS ファームとのフェデレーション| 
| [カスタムの会社のロゴまたはイラストの追加](how-to-connect-fed-management.md#customlogo) |AD FS サインイン ページに表示されるカスタム ロゴを指定して、サインイン エクスペリエンスを変更します。 |
| [サインインの説明を追加する](how-to-connect-fed-management.md#addsignindescription) |AD FS サインイン ページのサインインの説明を変更します。 |
| [AD FS の要求規則を変更する](how-to-connect-fed-management.md#modclaims) |Azure AD Connect 同期構成に対応する AD FS の要求規則を変更または追加します。 |


## <a name="additional-resources"></a>その他のリソース
* [2 つの Azure AD と単一の AD FS とのフェデレーション](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [Azure への AD FS のデプロイ](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)
* [Azure Traffic Manager を使用した Azure への可用性に優れた地域間 AD FS デプロイ](/windows-server/identity/ad-fs/deployment/active-directory-adfs-in-azure-with-azure-traffic-manager)
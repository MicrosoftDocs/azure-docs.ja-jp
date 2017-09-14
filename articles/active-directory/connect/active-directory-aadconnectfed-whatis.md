---
title: "Azure AD Connect とフェデレーション | Microsoft Docs"
description: "このページは、Azure AD Connect を使用した AD FS の操作に関するすべてのドキュメントの中心となる場所です。"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: anandy
ms.translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: 589c37d4bb59ceeb37742163fad21d465b3d3827
ms.contentlocale: ja-jp
ms.lasthandoff: 04/10/2017

---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect とフェデレーション
Azure Active Directory (Azure AD) Connect を使用すると、オンプレミスの Active Directory フェデレーション サービス (AD FS) と Azure AD でフェデレーションを構成できます。 フェデレーション サインインでは、ユーザーはオンプレミスのパスワードを使用して Azure AD ベースのサービスにサインインできます。また、企業ネットワーク上では、パスワードを再度入力する必要はありません。 AD FS によるフェデレーション オプションを使用すると、AD FS の新しいインストールをデプロイすることも、Windows Server 2012 R2 ファームの既存のインストールを指定することもできます。

このトピックは Azure AD Connect のフェデレーション関連の機能に関する情報のホームであり、 すべての関連トピックへのリンクを示しています。 Azure AD Connect へのリンクについては、「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」を参照してください。

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: フェデレーションに関するトピック
| トピック | 内容 |
|:--- |:--- |
| **Azure AD Connect ユーザーのサインイン オプション** | |
| [ユーザー サインイン オプションについて](active-directory-aadconnect-user-signin.md) |さまざまなユーザー サインイン オプションと、各オプションが Azure サインイン ユーザー エクスペリエンスに及ぼす影響について説明します。 |
| **Azure AD Connect を使用した AD FS のインストール** | |
| [前提条件](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Azure AD Connect 経由の正常な AD FS インストールの前提条件を確認します。 |
| [AD FS ファームの構成](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Azure AD Connect を使用して、新しい AD FS ファームをインストールします。 |
| [代替ログイン ID を使用して Azure AD とフェデレーションする](active-directory-aadconnect-federation-management.md#alternateid) | 代替ログイン ID を使用してフェデレーションを構成する  |
| **AD FS 構成の変更** | |
| [信頼を修復する](active-directory-aadconnect-federation-management.md#repairthetrust) |オンプレミスの AD FS と Office 365 または Azure 間の現在の信頼を修復します。 |
| [新しい AD FS サーバーの追加](active-directory-aadconnect-federation-management.md#addadfsserver) |初期インストール後に、追加の AD FS サーバーで AD FS ファームを拡張します。 |
| [新しい AD FS WAP サーバーの追加](active-directory-aadconnect-federation-management.md#addwapserver) |初期インストール後に、追加の Web アプリケーション プロキシ (WAP) サーバーで AD FS ファームを拡張します。 |
| [新しいフェデレーション ドメインの追加](active-directory-aadconnect-federation-management.md#addfeddomain) |Azure AD とフェデレーションする別のドメインを追加します。 |
| [SSL 証明書の更新](active-directory-aadconnectfed-ssl-update.md)| AD FS ファームの SSL 証明書を更新します。 |
| **他のフェデレーション構成** | |
| [Azure AD の複数のインスタンスと AD FS の単一インスタンスのフェデレーションを行う](active-directory-aadconnectfed-single-adfs-multitenant-federation.md) | 複数の Azure AD と単一の AD FS ファームとのフェデレーション| 
| [カスタムの会社のロゴまたはイラストの追加](active-directory-aadconnect-federation-management.md#customlogo) |AD FS サインイン ページに表示されるカスタム ロゴを指定して、サインイン エクスペリエンスを変更します。 |
| [サインインの説明を追加する](active-directory-aadconnect-federation-management.md#addsignindescription) |AD FS サインイン ページのサインインの説明を変更します。 |
| [AD FS の要求規則を変更する](active-directory-aadconnect-federation-management.md#modclaims) |Azure AD Connect 同期構成に対応する AD FS の要求規則を変更または追加します。 |


## <a name="additional-resources"></a>その他のリソース
* [2 つの Azure AD と単一の AD FS とのフェデレーション](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)
* [Azure への AD FS のデプロイ](active-directory-aadconnect-azure-adfs.md)
* [Azure Traffic Manager を使用した Azure への可用性に優れた地域間 AD FS デプロイ](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)


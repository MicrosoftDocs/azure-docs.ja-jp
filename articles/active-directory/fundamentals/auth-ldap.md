---
title: Azure Active Directory による LDAP 認証
description: Azure Active Directory を使用した LDAP 認証の取得に関するアーキテクチャ ガイダンス。
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
ms.openlocfilehash: f209d394e1a0c2c4ddde9cbf8df2704647e2822a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168714"
---
# <a name="ldap-authentication-with-azure-active-directory"></a>Azure Active Directory による LDAP 認証

ライトウェイト ディレクトリ アクセス プロトコル (LDAP) は、さまざまなディレクトリ サービスを操作するためのアプリケーション プロトコルです。 Active Directory などのディレクトリサービスによって、[ユーザーおよびアカウントの情報](https://www.dnsstuff.com/active-directory-service-accounts)、パスワードなどのセキュリティ情報が格納されます。 さらにこのサービスを使用することで、ネットワーク上の他のデバイスと情報を共有できるようになります。 電子メール、顧客関係マネージャー (CRM)、人事 (HR) ソフトウェアなどのエンタープライズ アプリケーションで、LDAP を使用すれば、情報の認証、アクセス、および検索を行うことができます。 

このパターンは Azure AD Domain Services (AD DS) を介して Azure Active Directory (Azure AD) によってサポートされます。 これにより、クラウド優先戦略を採用している組織は、オンプレミスの LDAP リソースをクラウドに移行することで自社の環境を最新化することができます。 すぐに得られるメリットは次のとおりです。 

* Azure AD との統合。 ユーザーとグループの追加、またはそれらのオブジェクトに対する属性の変更は、Azure AD テナントから AD DS に自動的に同期されます。 オンプレミスの Active Directory 内でのオブジェクトに対する変更は、まず Azure AD に、次に AD DS に同期されます。

* 操作の簡略化。 オンプレミスのインフラストラクチャを手動で保持してパッチを適用する必要性が軽減されます。 

* 信頼性が高い。 管理された高可用性サービスを利用できます。 

## <a name="use-when"></a>使用する状況

アプリケーションまたはサービスで LDAP 認証を使用する必要があります。

![アーキテクチャの図](./media/authentication-patterns/ldap-auth.png)

## <a name="components-of-system"></a>システムのコンポーネント

* **[ユーザー]** :LDAP に依存するアプリケーションにブラウザーを介してアクセスします。

* **Web ブラウザー**: アプリケーションの外部 URL にアクセスするためにユーザーが操作するインターフェイスです。

* **[仮想ネットワーク]** : レガシ アプリケーションで LDAP サービスを使用するために経由する Azure 内のプライベート ネットワーク。 

* **レガシ アプリケーション**: LDAP を Azure の仮想ネットワークにデプロイする必要があるアプリケーションまたはサーバー ワークロード、あるいはネットワーク ルートを介して AD DS インスタンス IP を可視化できるアプリケーションまたはサーバー ワークロード。 

* **Azure AD**:Azure AD Connect を経由して、組織のオンプレミス ディレクトリから ID 情報を同期します。

* **Azure AD Domain Services (AD DS)** : Azure AD からの一方向の同期を実行して、集中管理された一連のユーザー、グループ、および資格情報へのアクセスを提供します。 AD DS インスタンスは仮想ネットワークに割り当てられます。 AD DS に割り当てられた仮想ネットワークに接続される Azure 内のアプリケーション、サービス、および VM では、共通の AD DS 機能 (LDAP、ドメイン参加、グループ ポリシー、Kerberos、NTLM 認証など) を使用することができます。
   > [!NOTE]
   >  組織がパスワード ハッシュを同期できない環境、またはユーザーがスマート カードを使用してサインインする環境では、AD DS でリソース フォレストを使用することをお勧めします。 

* **Azure AD Connect**:オンプレミスの ID 情報を Microsoft Azure AD に同期するためのツール。 デプロイ ウィザードおよびガイド付きエクスペリエンスを使用すると、Active Directory から Azure AD への同期やサインオンなど、接続に必要な前提条件とコンポーネントを容易に構成できます。 

* **Active Directory**: [ユーザーやアカウント情報などのオンプレミスの ID 情報](https://www.dnsstuff.com/active-directory-service-accounts)、およびパスワードなどのセキュリティ情報を格納するディレクトリ サービス。

## <a name="implement-ldap-authentication-with-azure-ad"></a>Azure AD による LDAP 認証を実装する

* [Azure Active Directory Domain Services インスタンスを作成して構成する](../../active-directory-domain-services/tutorial-create-instance.md) 

* [Azure AD DS インスタンス用の仮想ネットワークを構成する](../../active-directory-domain-services/tutorial-configure-networking.md) 

* [Azure AD DS のマネージド ドメイン用に Secure LDAP を構成する](../../active-directory-domain-services/tutorial-configure-ldaps.md) 

* [Azure AD DS でオンプレミス ドメインに対して出力方向のフォレストの信頼を作成する](../../active-directory-domain-services/tutorial-create-forest-trust.md)


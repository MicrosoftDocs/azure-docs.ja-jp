---
title: Azure AD の参加と Azure Active Directory Domain Services の比較 | Microsoft Docs
description: Azure AD の参加と Azure AD Domain Services を見極める
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: b6f2ca922153bca4afa22c2b07db03ff2973444d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474270"
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>Azure Active Directory の参加と Azure Active Directory Domain Services 間で選定する
この記事では、Azure Active Directory (AD) の参加と Azure AD Domain Services の違いを説明し、お使いのユースケースに基づく選定を支援します。

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>Azure AD の登録と Azure AD の参加デバイス
Azure AD では、組織で使用されているデバイスの ID を管理して、これらのデバイスから企業リソースへのアクセスを制御できます。 ユーザーは Azure AD によって個人用デバイス (BYOD) を登録でき、デバイスに ID を提供することになります。 結果として、ユーザーが Azure AD にサインインし、デバイスを使って安全なリソースにアクセスする場合に、Azure AD でデバイスを認証できます。 さらに、Microsoft Intune などのモバイル デバイス管理 (MDM) ソフトウェアを使用してデバイスを管理できます。 この機能では、管理対象およびポリシーに準拠しているデバイスから、機密性の高いリソースへのアクセスを制限できます。

組織が所有するデバイスを Azure AD に参加させることもできます。 このメカニズムには、Azure AD での個人用デバイスの登録に相当する利点があります。 さらに、ユーザーは企業の資格情報を使用してデバイスにサインインできます。 Azure AD に参加しているデバイスには、次のような利点があります。
* Azure AD によって保護されたアプリケーションに対するシングル サインオン (SSO)
* デバイス間でのユーザー設定の企業ポリシーに準拠しているローミング
* 企業の資格情報を使用したビジネス向け Windows ストアへのアクセス
* Windows Hello for Business
* 企業のポリシーに準拠しているデバイスからアプリおよびリソースへのアクセス制限

| **デバイスの種類** | **デバイス プラットフォーム** | **メカニズム** |
|:---| --- | --- |
| 個人用デバイス | Windows 10、iOS、Android、Mac OS | Azure AD 登録済み |
| オンプレミス AD に参加していない組織所有のデバイス | Windows 10 | Azure AD 参加済み |
| オンプレミス AD に参加している組織所有のデバイス | Windows 10 | ハイブリッド Azure AD 参加済み |

Azure AD に参加または登録しているデバイスでは、最新の OAuth/OpenID 接続ベースのプロトコルを使って、ユーザー認証が行われます。 これらのプロトコルは、インターネット経由で動作するように設計されており、ユーザーが会社のリソースにどこからでもアクセスするモバイル シナリオに最適です。


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>Azure AD Domain Services のマネージド ドメインへのドメイン参加
Azure AD Domain Services では、Azure の仮想ネットワーク内の管理対象 AD ドメインを提供しています。 従来のドメイン参加のメカニズムを使用して、このマネージド ドメインにマシンを参加させることができます。 Windows クライアント (Windows 7、Windows 10) と Windows Server のコンピューターは、マネージド ドメインに参加させることができます。 さらに、マネージド ドメインに Linux と Mac OS のコンピューターを参加させることもできます。 コンピューターを AD ドメインに参加させる場合、ユーザーは会社の資格情報を使用してコンピューターにサインインできます。 これらのコンピューターはグループ ポリシーを使用して管理されるため、必然的に組織のセキュリティ ポリシーに準拠します。

ドメインに参加しているコンピューターで、NTLM または Kerberos 認証プロトコルを使用してユーザー認証が行われます。 ユーザー認証が機能するように、ドメインに参加しているコンピューターからマネージド ドメインのドメイン コントローラーが見える状態になっている必要があります。 そのため、ドメインに参加しているコンピューターが、マネージド ドメインと同じ仮想ネットワーク上に存在している必要があります。 または、ドメインに参加しているコンピューターが、ピアリングされた仮想ネットワーク経由またはサイト間の VPN/ExpressRoute 接続経由で、マネージド ドメインに接続されている必要があります。 そのため、このメカニズムでは、モバイル デバイスや企業ネットワーク外に接続するデバイスにはあまり適していません。

> [!NOTE]
> 技術的には、サイト間 VPN または ExpressRoute 接続経由で、オンプレミスのクライアント ワークステーションをマネージド ドメインに参加させることは可能です。 ただし、エンドユーザーのデバイスの場合は、Azure AD (個人用デバイス) でデバイスを登録する方法か、Azure AD (会社のデバイス) にデバイスを参加させる方法のいずれかを利用することを強くお勧めします。 このメカニズムはインターネット経由でより円滑に機能し、エンドユーザーがどこからでも作業することを可能にします。 Azure AD Domain Services は、お使いのアプリケーションがデプロイされている Azure 仮想ネットワークにデプロイされた Windows または Linux サーバーの仮想マシンに最適です。


## <a name="summary---key-differences"></a>まとめ - 主な相違点
| **特徴** | **Azure AD Join** | **Azure AD Domain Services** |
|:---| --- | --- |
| デバイスの制御 | Azure AD | Azure AD Domain Services のマネージド ドメイン |
| ディレクトリ内の表現 | Azure AD ディレクトリ内のデバイス オブジェクト。 | AAD-DS マネージド ドメインにあるコンピューター オブジェクト。 |
| Authentication | プロトコルに基づく OAuth/OpenID 接続 | Kerberos、NTLM プロトコル |
| 管理 | Intune などのモバイル デバイス管理 (MDM) ソフトウェア | グループ ポリシー |
| ネットワーク | インターネット経由で動作 | コンピューターがマネージド ドメインと同じ仮想ネットワーク上に存在する必要がある。|
| 最適な対象 | エンドユーザーのモバイルまたはデスクトップ デバイス | Azure にデプロイされたサーバーの仮想マシン |


## <a name="next-steps"></a>次の手順
### <a name="learn-more-about-azure-ad-domain-services"></a>Azure AD Domain Services の詳細
* [Azure AD Domain Services の概要](overview.md)
* [機能](active-directory-ds-features.md)
* [デプロイメント シナリオ](scenarios.md)
* [Azure AD Domain Services がユースケースに適しているかを調べる方法](comparison.md)
* [Azure AD Domain Services を Azure AD ディレクトリと同期させる方法について](synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>Azure AD の参加に関する詳細情報
* [Azure Active Directory のデバイス管理の概要](../active-directory/device-management-introduction.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Azure AD ドメイン サービスの使用開始
* [Azure Portal を使用して Azure AD Domain Services を有効にする](create-instance.md)

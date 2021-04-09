---
title: B2B ユーザーとしてローカル パートナー アカウントをクラウドに同期する - Azure AD
description: Azure AD B2B コラボレーションと同じ資格情報を使用して、ローカルで管理されている外部パートナーにローカル リソースとクラウド リソースの両方へのアクセス権を付与します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 11/03/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29aeca30e1fbdd28d3f69597d902a9b714056cd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99575925"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Azure AD B2B コラボレーションを使用してローカルで管理されたパートナーのアカウントにクラウド リソースへのアクセスを許可する

Azure Active Directory (Azure AD) 以前は、オンプレミスの ID システムを持つ組織は、パートナーのアカウントを従来、オンプレミスのディレクトリで管理してきました。 このような組織では、アプリケーションを Azure AD に移行するときに、パートナーが必要なリソースにアクセスできるようにする必要があります。 リソースがオンプレミスにあるかクラウド内にあるかは問題ではありません。 また、パートナー ユーザーがオンプレミスと Azure AD の両方のリソースに同じサインイン資格情報を使用できるようにする必要があります。 

オンプレミス ディレクトリに外部パートナーのアカウントを作成する場合 (たとえば、partners.contoso.com ドメイン内に Wendy Moran という外部ユーザー用にサインイン名 "wmoran" のアカウントを作成する場合) これらのアカウントをクラウドと同期することができます。 具体的には、Azure AD Connect を使用して、パートナー アカウントをクラウドと同期させることができます。これにより、UserType = Guest でユーザー アカウントが作成されます。 その結果、パートナー ユーザーは、ローカル アカウントと同じ資格情報を使用してクラウド リソースにアクセスすることができます。

> [!NOTE]
> [B2B コラボレーションに内部ユーザーを招待する](invite-internal-users.md)方法に関するページを参照してください。 この機能を使用すると、B2B コラボレーションを使用するように内部ゲスト ユーザーを招待することができます。そのアカウントをオンプレミス ディレクトリからクラウドに同期したかどうかは関係ありません。 ユーザーは、B2B コラボレーションを使用するための招待を受け入れると、自分の ID と資格情報を使用して、アクセスするよう指定されたリソースにサインインできるようになります。 パスワードを維持したり、アカウントのライフサイクルを管理したりする必要はありません。

## <a name="identify-unique-attributes-for-usertype"></a>UserType の一意の属性を識別する

UserType 属性の同期を有効にする前に、まず、UserType 属性をオンプレミス Active Directory から派生させる方法を決める必要があります。 つまり、オンプレミス環境内で、外部コラボレーターに対して一意のパラメーターはどれでしょうか。 こうした外部コラボレーターと、組織のメンバーを区別するパラメーターを特定してください。

これには一般的なアプローチが 2 つあります。

- ソース属性として使用する未使用のオンプレミス Active Directory 属性 (extensionAttribute1 など) を指定する。 
- または、UserType 属性の値を他のプロパティから派生させる。 たとえば、オンプレミス Active Directory UserPrincipalName 属性の末尾がドメイン *\@partners.contoso.com* である場合、すべてのユーザーをゲストとして同期する必要があるとします。
 
詳細な属性要件については、[UserType の同期の有効化](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype)に関するページをご覧ください。 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>クラウドに対してユーザーが同期するように Azure AD Connect を構成する

一意の属性を特定したら、これらのユーザーをクラウドに同期させるように Azure AD Connect を構成できます。これにより、UserType = Guest でユーザー アカウントが作成されます。 承認の観点から、これらのユーザーと、Azure AD B2B コラボレーション招待プロセスによって作成された B2B ユーザーを区別することはできません。

実装手順については、[UserType の同期の有効化](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

- [ハイブリッド組織向けの Azure Active Directory B2B コラボレーション](hybrid-organizations.md)
- [Azure AD の B2B ユーザーにオンプレミスのアプリケーションへのアクセスを許可する](hybrid-cloud-to-on-premises.md)
- Azure AD Connect の概要については、「[オンプレミスのディレクトリと Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)」を参照してください。


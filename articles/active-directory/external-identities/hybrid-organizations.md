---
title: ハイブリッド組織向けの B2B コラボレーション - Azure AD
description: Azure AD B2B コラボレーションによって、パートナーがオンプレミスのリソースとクラウド リソースの両方にアクセスできるようにします。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 427f7ad4d6a1b9839b1197ef9f7ca15400ea0f59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87907476"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>ハイブリッド組織向けの Azure Active Directory B2B コラボレーション

Azure Active Directory (Azure AD) の B2B コラボレーションにより、外部パートナーが組織内のアプリやリソースに簡単にアクセスできるようにすることができます。 これは、オンプレミスとクラウドベースの両方のリソースがあるハイブリッド構成であっても同様です。 現在、外部パートナーのアカウントをオンプレミスの ID システムでローカルに管理していても、または Azure AD B2B ユーザーとしてクラウド内で管理していても構いません。 いずれの場所のリソースに対しても、両方の環境に同じサインイン資格情報を使用して、ユーザーにアクセス権を付与できるようになりました。

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Azure AD の B2B ユーザーにオンプレミスのアプリへのアクセス許可する

組織が Azure AD B2B コラボレーション機能を使用して、パートナー組織のゲスト ユーザーを Azure AD に招待する場合、これらの B2B ユーザーにオンプレミスのアプリへのアクセスを提供できるようになりました。

SAML ベースの認証を使用するアプリの場合、Azure Portal を通じて、認証用に Azure AD アプリケーション プロキシを使用して B2B ユーザーがこれらのアプリを入手できるようにすることができます。

統合 Windows 認証 (IWA) を Kerberos の制約付き委任 (KCD) と共に使用するアプリの場合、認証に Azure AD プロキシを使用することもできます。 ただし、承認を機能させるには、オンプレミスの Windows Server Active Directory にユーザー オブジェクトが必要です。 B2B ゲスト ユーザーを表すローカル ユーザー オブジェクトの作成に使用できる方法は 2 つあります。

- Microsoft Identity Manager (MIM) 2016 SP1 と、Microsoft Graph 用 MIM 管理エージェントです。
- PowerShell スクリプトを使用できます。 (このソリューションでは、MIM は不要です。)

これらのソリューションを実装する方法について詳しくは、「[Grant B2B users in Azure AD access to your on-premises applications](hybrid-cloud-to-on-premises.md)」(Azure AD の B2B ユーザーにオンプレミスのアプリケーションへのアクセスを許可する) をご覧ください。

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>ローカルで管理されたパートナーのアカウントにクラウド リソースへのアクセスを許可する

Azure AD 以前は、オンプレミスの ID システムを持つ組織は、パートナーのアカウントを従来、オンプレミスのディレクトリで管理してきました。 このような組織では、アプリとその他のリソースをクラウドに移動した場合に、パートナーが引き続きアクセスできることを確認する必要があります。 これらのユーザーが同じ資格情報セットを使用して、クラウドとオンプレミスの両方のリソースにアクセスできるようにするのが理想的です。 

Azure AD Connect を使用して "ゲスト ユーザー" としてこれらのローカル アカウントをクラウドと同期したり、アカウントを Azure AD B2B ユーザーと同じように動作させたりすることができるようになりました。

会社のデータを保護するために、適切なリソースのみへのアクセスを制御し、これらのゲスト ユーザーを自社の従業員と区別して処理する承認ポリシーを構成できます。

実装について詳しくは、「[Grant locally-managed partner accounts access to cloud resources using Azure AD B2B collaboration](hybrid-on-premises-to-cloud.md)」(Azure AD B2B コラボレーションを使用してローカルで管理されたパートナーのアカウントにクラウド リソースへのアクセスを許可する) をご覧ください。
 
## <a name="next-steps"></a>次のステップ

- [Azure AD の B2B ユーザーにオンプレミスのアプリケーションへのアクセスを許可する](hybrid-cloud-to-on-premises.md)
- [Azure AD B2B コラボレーションを使用してローカルで管理されたパートナーのアカウントにクラウド リソースへのアクセスを許可する](hybrid-on-premises-to-cloud.md)



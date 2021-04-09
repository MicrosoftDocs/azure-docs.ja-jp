---
title: Azure Active Directory を使用した人事主導のプロビジョニングとは | Microsoft Docs
description: 人事主導のプロビジョニングについて概説します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ffe8c1397525348e472e965a407909fee36152d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168449"
---
# <a name="what-is-hr-driven-provisioning"></a>人事主導のプロビジョニングとは

![人事のプロビジョニング](./media/what-is-hr-driven-provisioning/cloud2a.png)

人事主導のプロビジョニングは、人事システムに基づいてデジタル ID を作成するプロセスです。  人事システムが、新しく作成されるデジタル ID の SOA (Start of Authority) として、多くの場合、さまざまなプロビジョニング プロセスの開始点となります。  たとえば、新しい従業員が入社した場合、それらの従業員が人事システムに作成されます。  この作成がトリガーとなって、Active Directory にユーザー アカウントがプロビジョニングされ、その後、そのアカウントが Azure AD Connect によって Azure AD などにプロビジョニングされます。

人事主導のプロビジョニングには、オンプレミス ベースとクラウド ベースとがあります。

## <a name="on-premises-based-hr-provisioning"></a>オンプレミス ベースの人事プロビジョニング
オンプレミス ベースの人事プロビジョニングは、ローカル人事システムと、新しいデジタル ID をプロビジョニングする手段とを使用して行われます。

人事システムは、さまざまなパッケージやソフトウェア バンドルの形で提供され、SQL サーバーや LDAP ディレクトリなどが使用されることもあります。

現在、Microsoft のオンプレミス人事プロビジョニング ソリューションには Microsoft Identity Manager が使用されており、それらの人事システムに新しい ID が作成されたときにプロビジョニングがトリガーされます。

MIM を使用すると、オンプレミスの人事システムから Active Directory または Azure AD にユーザーをプロビジョニングすることができます。

Microsoft Identity Manager とそのサポート対象のシステムについては、[Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016) のドキュメントを参照してください。

[!INCLUDE [active-directory-hr-provisioning.md](../../../includes/active-directory-hr-provisioning.md)]



## <a name="next-steps"></a>次のステップ 
- [ID ライフサイクル管理とは](what-is-identity-lifecycle-management.md)
- [プロビジョニングとは](what-is-provisioning.md)
- [アプリのプロビジョニングとは](what-is-app-provisioning.md)
- [ディレクトリ間のプロビジョニングとは](what-is-inter-directory-provisioning.md)
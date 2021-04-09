---
title: Azure Active Directory を使用したディレクトリ間のプロビジョニングとは | Microsoft Docs
description: ID のディレクトリ間のプロビジョニングについて概説します。
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
ms.openlocfilehash: 4246bc4c62fd8e5e73ff18e383b8bf115d25ede8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643839"
---
# <a name="what-is-inter-directory-provisioning"></a>ディレクトリ間のプロビジョニングとは

ディレクトリは、ネットワーク リソースや項目を検索、管理、整理する目的で使用される共有情報のインフラストラクチャです。  ディレクトリ サービスを使用するアプリケーションの例として、Microsoft Active Directory と Azure AD があります。  だれが何にアクセスできるかや、特定のリソースをだれが使用できるかといったディレクトリ システムの決定は、ID があることで可能となります。

ディレクトリ間のプロビジョニングは、2 つの異なるディレクトリ サービス システム間で ID をプロビジョニングすることです。   ディレクトリ間のプロビジョニングが使用される最も一般的なシナリオは、既に Active Directory に存在するユーザーを Azure AD にプロビジョニングすることです。 このプロビジョニングは、Azure AD Connect 同期または Azure AD Connect クラウド プロビジョニングなどのエージェントを使用して実行できます。

ディレクトリ間のプロビジョニングを使用すると、[ハイブリッド ID](../hybrid/whatis-hybrid-identity.md) 環境を作成することができます。


## <a name="what-types-of-inter-directory-provisioning-does-azure-ad-support"></a>Azure AD でサポートされるディレクトリ間のプロビジョニングの種類

ディレクトリ間のプロビジョニングを行う手段として、Azure AD では現在 3 つの方法がサポートされています。 それらの方法を次に示します。

- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) - ハイブリッド ID に対応し、それを達成するように設計された Microsoft のツールです。たとえば、Active Directory から Azure AD へのディレクトリ間のプロビジョニングに対応します。

- [Azure AD Connect クラウド プロビジョニング](../cloud-sync/what-is-cloud-sync.md) - ハイブリッド ID の目標に適合し、それを達成するように設計された Microsoft の新しいエージェントです。  Active Directory と Azure AD との間でディレクトリ間のプロビジョニングを実現する軽量の環境となっています。

- [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016) - ID とアクセス管理を意図した Microsoft のオンプレミス ソリューションです。組織内のユーザー、資格情報、ポリシー、アクセスを効率的に管理できます。 また、Active Directory や Azure AD などのディレクトリのためのハイブリッド ID 環境を実現する高度なディレクトリ間のプロビジョニングを利用できます。

### <a name="key-benefits"></a>主な利点

ここで説明するディレクトリ間のプロビジョニング機能は、次に挙げるような大きなメリットをビジネスにもたらします。

- [パスワード ハッシュ同期](../hybrid/whatis-phs.md) - ユーザーのオンプレミス AD パスワードのハッシュを Azure AD と同期させるサインイン方法。
- [パススルー認証](../hybrid/how-to-connect-pta.md) - ユーザーがオンプレミスとクラウド内で同じパスワードを使用できるようにするサインイン方法ですが、フェデレーション環境の追加のインフラストラクチャは必要ありません。
- [フェデレーション統合](../hybrid/how-to-connect-fed-whatis.md) - オンプレミスの AD FS インフラストラクチャを使ってハイブリッド環境を構成するために使用できます。 証明書の更新や追加の AD FS サーバー デプロイなどの AD FS 管理機能も提供されます。
- [同期](../hybrid/how-to-connect-sync-whatis.md) - ユーザー、グループ、およびその他のオブジェクトを作成する役割を果たします。  また、オンプレミスのユーザーやグループの ID 情報をクラウド側と一致させる役割もあります。  この同期にはパスワード ハッシュも含まれます。
- [正常性の監視](../hybrid/whatis-azure-ad-connect.md) - 信頼性の高い監視機能が利用でき、そのアクティビティは、Azure portal で一元的に表示されます。 


## <a name="next-steps"></a>次のステップ 
- [ID ライフサイクル管理とは](what-is-identity-lifecycle-management.md)
- [プロビジョニングとは](what-is-provisioning.md)
- [人事主導のプロビジョニングとは](what-is-hr-driven-provisioning.md)
- [アプリのプロビジョニングとは](what-is-app-provisioning.md)
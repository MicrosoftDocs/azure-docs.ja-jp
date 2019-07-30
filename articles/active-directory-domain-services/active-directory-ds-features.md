---
title: Azure Active Directory Domain Services:機能 | Microsoft Docs
description: Azure Active Directory Domain Services の機能
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: e49a37ec95a8cf26a2c63bd90759da35fc537e41
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474243"
---
# <a name="azure-ad-domain-services"></a>Azure AD Domain Services
## <a name="features"></a>機能
Azure AD Domain Services のマネージド ドメインでは、次の機能を使用できます。

* **簡単なデプロイ操作:** わずか数回のクリックで、Azure AD ディレクトリのAzure AD Domain Services を有効にできます。 管理対象ドメインには、クラウド専用のユーザー アカウントとオンプレミスのディレクトリから同期されているユーザー アカウントが含まれています。
* **ドメイン参加のサポート:** マネージド ドメインを使用できる Azure 仮想ネットワークでは、コンピューターを容易にドメインに参加させることができます。 Windows クライアントおよび Windows Server オペレーティング システムで行うドメイン参加操作は、Azure AD Domain Services が提供するドメインに対してシームレスに機能します。 また、このようなドメインに対して自動化されたドメイン参加ツールを使用することもできます。
* **Azure AD ディレクトリごとに 1 つのドメイン インスタンス:** Azure AD ディレクトリごとに 1 つの Active Directory ドメインを作成できます。
* **カスタム名を使用したドメインの作成:** Azure AD Domain Services を使用して、カスタム名を指定してドメインを作成できます (例: "contoso100.com")。 確認済みまたは未確認のいずれかのドメイン名を使用できます。 必要に応じて、Azure AD ディレクトリが提供する組み込みのドメイン サフィックス (つまり、"*.onmicrosoft.com" など) を指定してドメインを作成することができます。
* **Azure AD との統合:** Azure AD Domain Services へのレプリケーションを構成したり管理したりする必要はありません。 Azure AD ディレクトリのユーザー アカウント、グループ メンバーシップ、およびユーザー資格情報 (パスワード) が、Azure AD ドメイン サービスで自動的に利用可能になります。 Azure AD テナントやオンプレミスのディレクトリで新規作成されたユーザーやグループおよび変更が加えられた属性は、Azure AD ドメイン サービスに自動的に同期されます。
* **NTLM と Kerberos の認証:** NTLM と Kerberos の認証が サポートされているので、Windows 統合認証を利用するアプリケーションをデプロイできます。
* **会社の資格情報とパスワードの使用:** Azure AD テナントでのユーザーのパスワードは、Azure AD Domain Services で機能します。 ユーザーは、会社の資格情報を使用してコンピューターをドメインに参加させたり、対話的にまたはリモート デスクトップ経由でサインインしたり、マネージド ドメインに対して認証したりできます。
* **LDAP バインドと LDAP 読み取りのサポート:** LDAP バインドを利用するアプリケーションを使用して、Azure AD Domain Services がサービスを提供するドメインでユーザーを認証できます。 さらに、LDAP 読み取り操作を使用してディレクトリのユーザーまたはコンピューターの属性をクエリするアプリケーションも Azure AD ドメイン サービスに対して機能します。
* **セキュリティで保護された LDAP (LDAPS):** セキュリティで保護された LDAP (LDAPS) を介したディレクトリへのアクセスを有効にできます。 セキュリティで保護された LDAP アクセスは、既定では仮想ネットワーク内で使用できます。 ただし、必要に応じて、インターネット上のセキュリティで保護された LDAP アクセスを有効化することもできます。
* **グループ ポリシー:** ユーザーやコンピューターのコンテナーごとに組み込みの GPO を 1 つ使用すると、必須のセキュリティ ポリシーにより、ユーザー アカウントとドメインに参加したコンピューターに対して、コンプライアンスを強制的に適用できます。 また、独自のカスタム GPO を作成し、カスタムの組織単位に割り当てて[グループ ポリシーを管理する](manage-group-policy.md)こともできます。
* **DNS の管理:** "AAD DC 管理者" グループのメンバーは、DNS 管理 MMC スナップインなどの使い慣れた DNS 管理ツールを使用して、マネージド ドメインの DNS を管理できます。
* **カスタム組織単位 (OU) の作成:** "AAD DC 管理者" グループのメンバーは、マネージド ドメイン内でカスタム OU を作成できます。 これらのユーザーにはカスタム OU に対する完全な管理者特権が付与されるため、カスタム OU のサービス アカウント、コンピューター、グループなどの追加や削除を行うことができます。
* **Azure の多くのグローバル リージョンで使用可能:** Azure AD Domain Services を使用できる Azure リージョンについては、[リージョンごとの Azure サービス](https://azure.microsoft.com/regions/#services/)に関するページを参照してください。
* **高可用性:** Azure AD Domain Services を使用すると、ドメインの高可用性を実現できます。 この機能により、サービスの稼働時間とエラーに対する復元性が確実に向上します。 また、組み込みの正常性監視では、インスタンスに障害が発生したときに新しいインスタンスを作成することで障害を自動的に修正し、ドメインに継続的なサービスを提供します。
* **AD アカウントのロックアウトによる保護:** ユーザーアカウントは、2 分以内に無効なパスワードが 5 回使用された場合、30 分間ロック アウトされます。 アカウントは、自動的に、30 分後にロック解除されます。
* **使い慣れた管理ツールの使用:** Active Directory 管理センターや Active Directory PowerShell などの使い慣れた Windows Server Active Directory 管理ツールを使用して、マネージド ドメインを管理できます。

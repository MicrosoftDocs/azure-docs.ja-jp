---
title: "Azure Active Directory Domain Services: 機能 | Microsoft Docs"
description: "Azure Active Directory ドメイン サービスの機能"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 76987a6e91ae688b3856567073a7d27472e5ba09
ms.openlocfilehash: 34f84de82cbd6c15bd62abedd05e0eff6ce2dc2f
ms.lasthandoff: 01/28/2017


---
# <a name="azure-ad-domain-services"></a>Azure AD Domain Services
## <a name="features"></a>Features (機能)
Azure AD Domain Services 管理対象ドメインでは、次の機能を使用できます。

* **シンプルなデプロイ操作:** 数回クリックするだけで、Azure AD テナントの Azure AD ドメイン サービスを有効にできます。 Azure AD テナントがクラウド テナントかオンプレミスのディレクトリと同期しているかにかかわらず、管理対象ドメインを短時間でプロビジョニングできます。
* **ドメイン参加のサポート:** 管理対象ドメインを使用できる Azure 仮想ネットワークにあるコンピューターを簡単にドメインに参加させることができます。 Windows クライアントおよび Windows Server オペレーティング システムで行うドメイン参加操作は、Azure AD Domain Services が提供するドメインに対してシームレスに機能します。 また、このようなドメインに対して自動化されたドメイン参加ツールを使用することもできます。
* **Azure AD ディレクトリごとに&1; つのドメイン インスタンス:** Azure AD ディレクトリごとに&1; つの Active Directory ドメインを作成できます。
* **カスタム名を使用したドメイン作成:** Azure AD Domain Services を使用して、カスタム名を指定したドメインを作成できます (例: "contoso100.com")。 確認済みまたは未確認のいずれかのドメイン名を使用できます。 必要に応じて、Azure AD ディレクトリが提供する組み込みのドメイン サフィックス (つまり、"*.onmicrosoft.com" など) を指定してドメインを作成することができます。
* **Azure AD と統合:** Azure AD ドメイン サービスへのレプリケーションを構成したり管理したりする必要がありません。 Azure AD ディレクトリのユーザー アカウント、グループ メンバーシップ、およびユーザー資格情報 (パスワード) が、Azure AD ドメイン サービスで自動的に利用可能になります。 Azure AD テナントやオンプレミスのディレクトリで新規作成されたユーザーやグループおよび変更が加えられた属性は、Azure AD ドメイン サービスに自動的に同期されます。
* **NTLM および Kerberos 認証:** NTLM および Kerberos 認証がサポートされているため、Windows 統合認証を利用するアプリケーションをデプロイできます。
* **会社の資格情報とパスワードの使用:** Azure AD テナント内のユーザーのパスワードを Azure AD ドメイン サービスで使用できます。 ユーザーは会社の資格情報をしてコンピューターをドメインに参加させて、対話的に、またはリモート デスクトップ経由でログインし、管理対象ドメインに対して認証できます。
* **LDAP バインドと LDAP 読み取りのサポート**: Azure AD Domain Services が提供するドメイン内のユーザーを認証するために LDAP バインドを利用するアプリケーションを使用できます。 さらに、LDAP 読み取り操作を使用してディレクトリのユーザーまたはコンピューターの属性をクエリするアプリケーションも Azure AD ドメイン サービスに対して機能します。
* **セキュリティで保護された LDAP (LDAPS):** セキュリティで保護された LDAP (LDAPS) 上のディレクトリに対するアクセスを有効化できます。 セキュリティで保護された LDAP アクセスは、既定では仮想ネットワーク内で使用できます。 ただし、必要に応じて、インターネット上のセキュリティで保護された LDAP アクセスを有効化することもできます。
* **グループ ポリシー:** ユーザーおよびコンピューターのコンテナーごとに組み込みの GPO を&1; つずつ使用して、ユーザー アカウントとドメイン参加しているコンピューター向けの必須セキュリティ ポリシーに強制的に準拠させることができます。 また、独自のカスタム GPO を作成し、カスタムの組織単位に割り当ててグループ ポリシーを管理することもできます。
* **DNS の管理:** "AAD DC 管理者" グループのメンバーは、DNS 管理 MMC スナップインなど、使い慣れた DNS 管理ツール () を使用して、管理対象ドメイン の DNS を管理できます。
* **カスタム組織単位 (OU) の作成:** "AAD DC 管理者" グループのメンバーは、管理対象ドメインでカスタム OU を作成できます。 これらのユーザーにはカスタム OU に対する完全な管理者特権が付与されるため、カスタム OU のサービス アカウント、コンピューター、グループなどの追加や削除を行うことができます。
* **複数の Azure リージョンで使用可能:** Azure AD ドメイン サービスを使用できる Azure リージョンについては、 [リージョンごとの Azure サービス](https://azure.microsoft.com/regions/#services/) に関するページを参照してください。
* **高可用性:** Azure AD ドメイン サービスを使用して、ドメインの高可用性を実現できます。 この機能により、サービスの稼働時間とエラーに対する復元性が確実に向上します。 また、組み込みの正常性監視では、インスタンスに障害が発生したときに新しいインスタンスを作成することで障害を自動的に修正し、ドメインに継続的なサービスを提供します。
* **使い慣れた管理ツールの使用:** Active Directory 管理センターや Active Directory PowerShell などの使い慣れた Windows Server Active Directory 管理ツールを使用して、管理対象ドメインを管理できます。


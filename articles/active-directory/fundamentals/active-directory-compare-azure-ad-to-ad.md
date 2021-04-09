---
title: Active Directory と Azure Active Directory の比較
description: このドキュメントでは、Active Directory Domain Services (ADDS) と Azure Active Directory (AD) を比較します。 両方の ID ソリューションでの主な概念を示し、相違点や類似点について説明します。
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 02/26/2020
ms.author: martinco
ms.openlocfilehash: 64a8dabaedc3922ebd8d163b1ea162b7d1584de2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371921"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>Active Directory と Azure Active Directory の比較

Azure Active Directory は、クラウドに対する次世代の ID およびアクセス管理のソリューションです。 Microsoft では、ユーザーごとに 1 つの ID を使用して複数のオンプレミス インフラストラクチャ コンポーネントとシステムを管理する機能を組織に提供するために、Windows 2000 に Active Directory Domain Services を導入しました。

Azure AD では、クラウドとオンプレミス全体のすべてのアプリに対するサービスとしての ID (IDaaS) ソリューションを組織に提供することで、このアプローチを次のレベルに引き上げます。

ほとんどの IT 管理者は、Active Directory Domain Services の概念を理解しています。 次の表に、Active Directory の概念と Azure Active Directory 間の相違点と類似点を示します。

|概念|Active Directory (AD)|Azure Active Directory |
|:-|:-|:-|
|**ユーザー**|||
|プロビジョニング: ユーザー | 組織では、手動で内部ユーザーを作成するか、Microsoft Identity Manager などの社内または自動のプロビジョニング システムを使用して、HR システムと統合します。|既存の AD 組織では [Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md) を使用して、ID をクラウドに同期します。</br> Azure AD では、[クラウド HR システム](../saas-apps/workday-tutorial.md)からユーザーを自動的に作成するためのサポートを追加します。 </br>Azure AD では、[SCIM が有効化された](../app-provisioning/use-scim-to-provision-users-and-groups.md) SaaS アプリ内で ID をプロビジョニングして、ユーザーにアクセスを許可するために必要な詳細情報を自動的にアプリに提供します。 |
|プロビジョニング: 外部 ID| 組織では、専用の外部 AD フォレスト内に外部ユーザーを通常のユーザーとして手動で作成します。これにより、外部 ID (ゲスト ユーザー) のライフサイクルを管理するための管理オーバーヘッドが生じます。| Azure AD では、外部 ID をサポートするための特殊な ID クラスを提供しています。 [Azure AD B2B](/azure/active-directory/b2b/) では、外部ユーザーが確実に有効になるように、彼らの ID へのリンクが管理されます。 |
| エンタイトルメントの管理とグループ| 管理者は、ユーザーをグループのメンバーにすることができます。 アプリとリソースの所有者は、アプリまたはリソースへのアクセス権をグループに付与します。| また、[グループ](./active-directory-groups-create-azure-portal.md)は Azure AD でも使用でき、管理者はグループを使用してリソースへのアクセス許可を付与することも可能です。 Azure AD では、管理者はグループにメンバーシップを手動で割り当てるか、またはクエリを使用してユーザーをグループに動的に含めることができます。 </br> 管理者は Azure AD にある[エンタイトルメント管理](../governance/entitlement-management-overview.md)を使用し、ワークフローと (必要な場合は) 時間ベースの条件を使って、アプリとリソースのコレクションへのアクセス権をユーザーに付与できます。 |
| 管理者の管理|組織では、AD 内のドメイン、組織単位、およびグループの組み合わせを使用して管理者権限を委任し、管理対象のディレクトリとリソースを管理します。| Azure AD には、Azure AD ロールベースのアクセス制御 (Azure AD RBAC) システムを備えた[組み込みのロール](./active-directory-users-assign-role-azure-portal.md)が用意されていますが、その制御する ID システム、アプリ、およびリソースへの特権アクセスを委任するための[カスタム ロールの作成](../roles/custom-overview.md)のサポートは限定されています。</br>Just-In-Time、時間制限付き、またはワークフローベースのアクセスを特権ロールに付与するために、ロールの管理は [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) を使用して拡張できます。 |
| 資格情報の管理| Active Directory での資格情報は、パスワード、証明書の認証、およびスマートカード認証に基づいています。 パスワードは、パスワードの長さ、有効期限、および複雑さに基づくパスワード ポリシーを使用して、管理されます。|Azure AD では、クラウドとオンプレミスに対してインテリジェントな[パスワード保護](../authentication/concept-password-ban-bad.md)を使用します。 保護には、スマート ロックアウトに加えて、共通およびカスタムのパスワード フレーズと代替のブロック機能が含まれます。 </br>Azure AD では、[多要素認証](../authentication/concept-mfa-howitworks.md)と FIDO2 のような[パスワードレス](../authentication/concept-authentication-passwordless.md)の技術を使用して、セキュリティを大幅に向上させています。 </br>Azure AD では、ユーザーに[セルフサービス パスワード リセット](../authentication/concept-sspr-howitworks.md)のシステムを提供することで、サポートのコストを削減しています。 |
| **Apps (アプリ)**|||
| インフラストラクチャ アプリ|Active Directory では、DNS、DHCP、IPSec、WiFi、NPS、VPN アクセスなど、多くのインフラストラクチャのオンプレミス コンポーネントの基礎を形成します|新しいクラウド環境では、Azure AD は、アプリにアクセスするためと、ネットワーク コントロールに依存するための新しいコントロール プレーンです。 ユーザーが認証を行うときに、[条件付きアクセス (CA)](../conditional-access/overview.md)では、必要な条件下でどのユーザーがどのアプリへのアクセス権を持つかを制御します。|
| 従来のアプリとレガシ アプリ| ほとんどのオンプレミス アプリでは、LDAP、Windows 統合認証 (NTLM と Kerberos)、またはヘッダーベースの認証を使用して、ユーザーへのアクセスを制御します。| Azure AD では、オンプレミスで実行されている [Azure AD アプリケーション プロキシ](../manage-apps/application-proxy.md) エージェントを使用して、これらの種類のオンプレミス アプリへのアクセスを提供できます。 この方法を利用して、Azure AD では、移行しているとき、またはレガシ アプリと共存する必要があるときに、Kerberos を使ってオンプレミスで Active Directory ユーザーを認証できます。 |
| SaaS アプリ|Active Directory では、SaaS アプリがネイティブでサポートされず、AD FS などのフェデレーション システムを必要とします。|OAuth2、SAML、および WS-\* 認証をサポートしている SaaS アプリは、認証に Azure AD を使用するように統合できます。 |
| 先進認証を使用した基幹業務 (LOB) アプリ|組織では Active Directory と共に AD FS を使用して、先進認証を必要とする LOB アプリをサポートできます。| 先進認証を必要とする LOB アプリは、認証に Azure AD を使用するように構成できます。 |
| 中間層/デーモン サービス|オンプレミス環境で実行されているサービスは通常、AD サービス アカウントまたはグループ管理サービス アカウント (gMSA) を使用して実行されます。 これらのアプリでは、サービス アカウントのアクセス許可を継承します。| Azure AD には、クラウド内の他のワークロードを実行するための[マネージド ID](../managed-identities-azure-resources/index.yml) が用意されています。 これらの ID のライフサイクルは Azure AD によって管理され、リソース プロバイダーに関連付けられているため、他の目的でバックドア アクセスを取得するために使用することはできません。|
| **デバイス**|||
| モバイル|Active Directory では、サードパーティのソリューションを使用しないモバイル デバイスはネイティブにサポートされていません。| Microsoft のモバイル デバイス管理ソリューションである Microsoft Intune は、Azure AD と統合されています。 Microsoft Intune では、認証中に評価するために、ID システムにデバイスの状態情報を提供しています。 |
| Windows デスクトップ|Active Directory では、グループ ポリシー、System Center Configuration Manager、またはその他のサードパーティのソリューションを使用して Windows デバイスを管理するために、デバイスをドメインに参加させる機能を提供しています。|Windows デバイスは、[Azure AD に参加する](../devices/index.yml)ことができます。 条件付きアクセスでは、認証プロセスの一部としてデバイスが Azure AD に参加しているかどうかを確認できます。 また、Windows デバイスは、[Microsoft Intune](/intune/what-is-intune) を使って管理することもできます。 この場合、条件付きアクセスでは、アプリへのアクセスを許可する前に、デバイスが準拠しているかどうか (最新のセキュリティ更新プログラムやウイルス署名など) を検討します。|
| Windows サーバー| Active Directory では、グループ ポリシーまたはその他の管理ソリューションを使用して、オンプレミスの Windows サーバーに強固な管理機能を提供します。| Azure にある Windows サーバー仮想マシンは、[Azure AD Domain Services](../../active-directory-domain-services/index.yml)を使って管理できます。 [マネージド ID](../managed-identities-azure-resources/index.yml) は、VM が ID システム ディレクトリまたはリソースにアクセスする必要がある場合に、使用できます。|
| Linux/Unix ワークロード|Active Directory では、サードパーティのソリューションを使用しない Windows 以外はネイティブでサポートされません。ただし、Active Directory を使って Kerberos 領域として認証するように Linux コンピューターを構成することはできます。|Linux/Unix VM では、ID システムまたはリソースにアクセスするために、[マネージド ID](../managed-identities-azure-resources/index.yml) を使用できます。 組織によっては、これらのワークロードをマネージド ID も使用できるクラウド コンテナー テクノロジに移行します。|

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory とは](./active-directory-whatis.md)
- [自己管理型の Active Directory Domain Services、Azure Active Directory、およびマネージド Azure Active Directory Domain Services の比較](../../active-directory-domain-services/compare-identity-solutions.md)
- [Azure Active Directory についてよくあるご質問](./active-directory-faq.md)
- [Azure Active Directory の新着情報](./whats-new.md)

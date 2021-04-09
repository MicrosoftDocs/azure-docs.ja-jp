---
title: Azure Active Directory Domain Services の概要 | Microsoft Docs
description: この概要では、Azure Active Directory Domain Services で提供されるサービスの内容と、組織でそれを使用してクラウド内のアプリケーションおよびサービスに ID サービスを提供する方法について説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 01/20/2021
ms.author: justinha
ms.custom: contperf-fy21q1
ms.openlocfilehash: 194a5420f38e99d45f74241f67ae8cfbbaaee8f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98660999"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services とは

Azure Active Directory Domain Services (AD DS) には、マネージド ドメイン サービス (ドメイン参加、グループ ポリシー、ライトウェイト ディレクトリ アクセス プロトコル (LDAP)、Kerberos または NTLM 認証など) が用意されています。 クラウドでドメイン コントローラー (DC) のデプロイ、管理、修正プログラムの適用を行わなくても、これらのドメイン サービスを使用することができます。

Azure AD DS マネージド ドメインを使用すると、最新の認証方法を使用できない、またはディレクトリ参照のたびにオンプレミスの AD DS 環境にアクセスすることが望ましくないレガシ アプリケーションをクラウドで実行できます。 クラウドで AD DS 環境を管理することなく、オンプレミス環境からマネージド ドメインに、これらのレガシ アプリケーションをリフト アンド シフトすることができます。

Azure AD DS は、既存の Azure AD テナントと統合されます。 この統合により、ユーザーは既存の資格情報を使用して、マネージド ドメインに接続されているサービスおよびアプリケーションにサインインできます。 また、既存のグループとユーザー アカウントを使用して、リソースへのアクセスをセキュリティで保護することもできます。 これらの機能により、オンプレミスのリソースを Azure に円滑にリフトアンドシフトすることができます。

> [!div class="nextstepaction"]
> [使用を開始するには、Azure portal を使用して Azure AD DS マネージド ドメインを作成します][tutorial-create]

Azure AD DS の詳細については、こちらのショート ビデオをご覧ください。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LblD]

## <a name="how-does-azure-ad-ds-work"></a>Azure AD DS のしくみ

Azure AD DS のマネージド ドメインを作成するときは、一意の名前空間を定義します。 この名前空間は、ドメイン名 (*aaddscontoso.com* など) です。 2 つの Windows Server ドメイン コントローラー (DC) が、選択した Azure リージョンにデプロイされます。 この DC のデプロイは、レプリカ セットと呼ばれます。

これらの DC の管理、構成、更新を自分で行う必要はありません。 Azure プラットフォームでは、DC は、バックアップや Azure Disk Encryption を使用した保存時の暗号化を含め、マネージド ドメインの一部として扱われます。

マネージド ドメインは、Azure AD からの一方向の同期を実行して、集中管理された一連のユーザー、グループ、および資格情報へのアクセスを提供するように構成されます。 マネージド ドメイン内で直接リソースを作成できますが、それらは Azure AD に同期されません。 マネージド ドメインに接続される Azure 内のアプリケーション、サービス、および VM は、共通の AD DS 機能 (ドメイン参加、グループ ポリシー、LDAP、Kerberos または NTLM 認証など) を使用することができます。

オンプレミスの AD DS 環境とのハイブリッド環境では、[Azure AD Connect][azure-ad-connect] により、ID 情報が Azure AD と同期された後、マネージド ドメインと同期されます。

![AD Connect を使用した、Azure AD Domain Services における Azure AD とオンプレミスの AD DS の同期](./media/active-directory-domain-services-design-guide/sync-topology.png)

Azure AD DS では、ID 情報が Azure AD からレプリケートされるため、クラウド専用の Azure AD テナント、またはオンプレミスの AD DS 環境と同期される Azure AD テナントとも連携します。 両方の環境に同じ Azure AD DS 機能セットが存在します。

* オンプレミスの AD DS 環境を既に使用している場合は、ユーザー アカウント情報を同期させて、ユーザーに一貫性のある ID を提供できます。 詳細については、[マネージド ドメイン内でのオブジェクトと資格情報の同期のしくみ][synchronization]に関するページを参照してください。
* クラウド専用の環境では、従来のオンプレミスの AD DS 環境を必要とすることなく、Azure AD DS で提供される ID の集中管理サービスを利用できます。

マネージド ドメインを拡張して、Azure AD テナントごとに複数のレプリカ セットを使用できます。 レプリカ セットは、Azure AD DS がサポートされている任意の Azure リージョンの、ピアリングされた任意の仮想ネットワークに追加できます。 異なる Azure リージョンにレプリカ セットを追加することで、1 つの Azure リージョンがオフラインになった場合に、レガシ アプリケーションの地理的なディザスター リカバリーが実現されます。 レプリカ セットは現在プレビュー段階です。 詳細については、[マネージド ドメインのレプリカ セットの概念と機能][concepts-replica-sets]に関するページを参照してください。

次のビデオでは、Azure AD DS により、アプリケーションおよびワークロードを統合して、クラウドで ID サービスを提供する方法の概要を説明します。

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

実際の Azure AD DS の展開シナリオを確認するには、次の例を参照してください。

* [ハイブリッド組織向けの Azure AD DS](scenarios.md#azure-ad-ds-for-hybrid-organizations)
* [クラウド専用組織向けの Azure AD DS](scenarios.md#azure-ad-ds-for-cloud-only-organizations)

## <a name="azure-ad-ds-features-and-benefits"></a>Azure AD DS の機能とメリット

クラウド内のアプリケーションおよび VM に ID サービスを提供するために、Azure AD DS は従来の AD DS 環境と完全に互換性があり、ドメイン参加、Secure LDAP (LDAPS)、グループ ポリシー、DNS の管理、LDAP バインドおよび読み取りのサポートなどの運用サービスが提供されます。 LDAP 書き込みのサポートは、マネージド ドメインで作成されたオブジェクトには使用できますが、Azure AD から同期されたリソースには使用できません。

ID オプションの詳細については、[Azure AD DS と、Azure AD、Azure VM 上の AD DS、およびオンプレミスの AD DS の比較][compare]に関するページを参照してください。

Azure AD DS の次の機能により、デプロイ操作と管理操作が簡略化されます。

* **デプロイ操作の簡略化:** Azure portal の単一のウィザードを使用して、Azure AD DS を Azure AD テナントに対して有効にします。
* **Azure AD との統合:** ユーザー アカウント、グループ メンバーシップ、および資格情報は、ご利用の Azure AD テナントから自動的に利用できるようになります。 Azure AD テナントやオンプレミスの AD DS 環境で新規作成されたユーザーおよびグループ、または変更が加えられた属性は、Azure AD DS に自動的に同期されます。
    * Azure AD DS では、Azure AD にリンクされている外部ディレクトリのアカウントは使用できません。 資格情報はこれらの外部ディレクトリでは使用できないため、マネージド ドメインには同期できません。
* **会社の資格情報とパスワードの使用:** Azure AD DS におけるユーザーのパスワードは、Azure AD テナントと同じです。 ユーザーは、会社の資格情報を使用してコンピューターをドメインに参加させたり、対話的にまたはリモート デスクトップ経由でサインインしたり、マネージド ドメインに対して認証したりできます。
* **NTLM と Kerberos の認証:** NTLM と Kerberos の認証がサポートされているので、Windows 統合認証を利用するアプリケーションをデプロイできます。
* **高可用性:** Azure AD DS には複数のドメイン コントローラーが含まれるため、マネージド ドメインの高可用性を実現できます。 この高可用性により、サービスの稼働時間と障害に対する復元性が保証されます。
    * [Azure Availability Zones][availability-zones] をサポートするリージョンでは、回復性を高めるため、これらのドメイン コントローラは複数のゾーンにも分散されます。
    * [レプリカ セット][concepts-replica-sets]を使用することで、1 つの Azure リージョンがオフラインになった場合に、レガシ アプリケーションの地理的なディザスター リカバリーが実現されます。

マネージド ドメインの重要な特徴の一部を以下に示します。

* このマネージド ドメインは、スタンドアロンのドメインです。 オンプレミスのドメインの拡張機能ではありません。
    * 必要であれば、Azure AD DS からオンプレミス AD DS 環境への一方向の出力方向のフォレストの信頼を作成できます。 詳細については、「[Azure AD DS のリソース フォレストの概念と機能][ forest-trusts]」を参照してください。
* IT チームが、このマネージド ドメインのドメイン コントローラーに対して管理、修正プログラムの適用、監視を行う必要はありません。

オンプレミスで AD DS を実行するハイブリッド環境では、マネージド ドメインへの AD レプリケーションを管理する必要はありません。 オンプレミスのディレクトリからのユーザー アカウント、グループ メンバーシップ、および資格情報は、[Azure AD Connect][azure-ad-connect] を介して Azure AD に同期されます。 これらのユーザー アカウント、グループ メンバーシップ、および資格情報は、このマネージド ドメイン内で自動的に利用できるようになります。

## <a name="next-steps"></a>次のステップ

Azure AD DS と他の ID ソリューションとの比較、および同期のしくみの詳細については、次の記事を参照してください。

* [Azure AD DS を Azure AD、Azure VM 上の Active Directory Domain Services、およびオンプレミスの Active Directory Domain Services と比較する][compare]
* [Azure AD Domain Services を Azure AD ディレクトリと同期させる方法][synchronization]
* マネージド ドメインを管理する方法については、[Azure AD DS でのユーザー アカウント、パスワード、および管理の概念][administration-concepts]に関するページを参照してください。

使用を開始するには、[Azure portal を使用してマネージド ドメインを作成][tutorial-create]します。

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
[concepts-replica-sets]: concepts-replica-sets.md

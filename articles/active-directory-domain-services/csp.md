---
title: クラウド ソリューション プロバイダー向けの Azure AD Domain Services | Microsoft Docs
description: Azure クラウド ソリューション プロバイダー向けの Azure Active Directory Domain Services マネージド ドメインを有効にして管理する方法について説明します。
services: active-directory-ds
author: justinha
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: d8edafff9b6534e5f1ce1c4581595ee187dfd432
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619901"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Azure クラウド ソリューション プロバイダー向けの Azure Active Directory Domain Services のデプロイと管理

Azure クラウド ソリューション プロバイダー (CSP) は Microsoft パートナー向けのプログラムであり、さまざまな Microsoft クラウド サービスのライセンス チャネルを提供します。 Azure CSP により、パートナーは販売の管理、課金関係の所有、技術および課金のサポートの提供が可能になり、顧客の単一窓口になることができます。 さらに、Azure CSP では、セルフサービス ポータルや付随する API など、ツールの完全なセットも提供しています。 これらのツールにより、CSP パートナーは Azure リソースを簡単にプロビジョニングおよび管理し、顧客とそのサブスクリプションに対して課金を行うことができます。

[パートナー センター ポータル](/partner-center/azure-plan-lp)は、すべての Azure CSP パートナー向けのエントリ ポイントであり、豊富な顧客管理機能や自動処理などを提供します。 Azure CSP パートナーは、Web ベースの UI を使用するか、PowerShell と各種 API 呼び出しを使用して、パートナー センターの機能を使用できます。

次の図は、CSP モデルのしくみを大まかに示しています。 ここで、Contoso には Azure Active Directory (Azure AD) テナントが存在します。 Contoso は、Azure CSP サブスクリプションにリソースをデプロイして管理する CSP と提携しています。 また、Contoso に直接課金される通常の Azure (Direct) サブスクリプションを持つこともできます。

![CSP モデルの概要](./media/csp/csp_model_overview.png)

CSP パートナーのテナントには、*管理* エージェント、*ヘルプ デスク* エージェント、*販売* エージェントの 3 つの特殊なエージェント グループが含まれています。

*管理* エージェント グループは、Contoso の Azure AD テナント内のテナント管理者ロールに割り当てられています。 その結果、CSP パートナーの管理エージェント グループに属するユーザーには、Contoso の Azure AD テナント内のテナント管理者特権が割り当てられます。

CSP パートナーが Contoso の Azure CSP サブスクリプションをプロビジョニングすると、管理エージェント グループがそのサブスクリプションの所有者ロールに割り当てられます。 これにより、CSP パートナーの管理エージェントは、仮想マシン、仮想ネットワーク、Azure AD Domain Services などの Azure リソースを Contoso に代わってプロビジョニングするために必要な特権を持ちます。

詳細については、「[Azure CSP overview (Azure CSP の概要)](/partner-center/azure-plan-lp)」をご覧ください。

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Azure CSP サブスクリプションで Azure AD DS を使用する利点

Azure Active Directory Domain Services (Azure AD DS) では、Windows Server Active Directory Domain Services と完全に互換性のあるマネージド ドメイン サービス (ドメイン参加、グループ ポリシー、LDAP、Kerberos/NTLM 認証など) が提供されます。 数十年にわたり、これらの機能を使用して AD に対して機能する多数のアプリケーションが構築されてきました。 多くの独立系ソフトウェア ベンダー (ISV) が、顧客の構内でアプリケーションを構築し、展開しています。 これらのアプリケーションは、多くの場合はそれらがデプロイされているさまざまな環境へのアクセスを必要とするため、サポートすることが困難です。 Azure CSP サブスクリプションは、Azure のスケールと柔軟性を備えたよりシンプルな代替手段となります。

Azure AD DS では、Azure CSP サブスクリプションがサポートされています。 顧客の Azure AD テナントに関連付けられている Azure CSP サブスクリプションにアプリケーションをデプロイできます。 その結果、従業員 (サポート スタッフ) は組織の会社の資格情報を使用して、アプリケーションがデプロイされている VM を管理したり、それらの VM に対応したりできます。

顧客の Azure AD テナントに Azure AD DS マネージド ドメインをデプロイすることもできます。 その後、アプリケーションは顧客のマネージド ドメインに接続されます。 Kerberos/NTLM、LDAP、または [System.DirectoryServices API](/dotnet/api/system.directoryservices) に依存するアプリケーション内の機能は、顧客のドメインに対してシームレスに動作します。 エンド カスタマーは、アプリケーションがデプロイされているインフラストラクチャの管理を気にすることなく、そのアプリケーションをサービスとして使用するというメリットが得られます。

Azure AD DS を含め、そのサブスクリプションで使用する Azure リソースに対するすべての課金がチャージ バックされます。 販売、課金、テクニカル サポートなどに関して、顧客との関係の完全な制御を維持できます。Azure CSP プラットフォームの柔軟性により、小規模のサポート エージェント チームが、アプリケーションのインスタンスをデプロイしている多数の顧客にサービスを提供できます。

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Azure AD DS の CSP デプロイ モデル

Azure CSP サブスクリプションで Azure AD DS を使用する方法は 2 つあります。 セキュリティとシンプルさに関する顧客の考慮事項に基づいて適切な方法を選択してください。

### <a name="direct-deployment-model"></a>直接デプロイ モデル

このデプロイ モデルでは、Azure AD DS は、Azure CSP サブスクリプションに属する仮想ネットワーク内で有効になります。 CSP パートナーの管理エージェントには、次の特権があります。

* 顧客の Azure AD テナント内の *グローバル管理者* 特権。
* Azure CSP サブスクリプションでの *サブスクリプション所有者* 特権。

![直接デプロイ モデル](./media/csp/csp_direct_deployment_model.png)

このデプロイ モデルでは、CSP プロバイダーの管理エージェントが顧客の ID を管理できます。 これらの管理エージェントは、新しいユーザーまたはグループのプロビジョニングや、顧客の Azure AD テナント内でのアプリケーションの追加などのタスクを実行できます。

このデプロイ モデルは、専任の ID 管理者がいないか、または代わりに CSP パートナーに ID を管理してほしいと考えている小規模な組織に適している可能性があります。

### <a name="peered-deployment-model"></a>ピアリング デプロイ モデル

このデプロイ モデルでは、Azure AD DS は、顧客に属する仮想ネットワーク内で有効になります。つまり、顧客が支払いを行うダイレクト Azure サブスクリプションです。 CSP パートナーは、顧客の CSP サブスクリプションに属する仮想ネットワーク内にアプリケーションをデプロイできます。 仮想ネットワークは、Azure 仮想ネットワーク ピアリングを使用して接続できます。

このデプロイでは、CSP パートナーによって Azure CSP サブスクリプションにデプロイされたワークロードまたはアプリケーションは、顧客のダイレクト Azure サブスクリプションにプロビジョニングされた顧客のマネージド ドメインに接続できます。

![ピアリング デプロイ モデル](./media/csp/csp_peered_deployment_model.png)

このデプロイ モデルでは特権が分離されるので、CSP パートナーのヘルプデスク エージェントが Azure サブスクリプションを管理し、サブスクリプションにリソースをデプロイして管理できます。 ただし、CSP パートナーのヘルプデスク エージェントに、顧客の Azure AD ディレクトリ上のグローバル管理者特権は必要ありません。 顧客の ID 管理者が組織の ID を引き続き管理できます。

このデプロイ モデルは、ISV がオンプレミスのアプリケーションの (顧客の Azure AD にも接続する必要のある) ホストされたバージョンを提供するシナリオに適している可能性があります。

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>CSP サブスクリプションで Azure AD DS を管理する

Azure CSP サブスクリプションでマネージド ドメインを管理する場合、次の重要な考慮事項が適用されます。

* **CSP 管理エージェントは、その資格情報を使用してマネージド ドメインをプロビジョニングできる。** Azure AD DS では、Azure CSP サブスクリプションがサポートされています。 CSP パートナーの管理エージェント グループに属するユーザーは、新しいマネージド ドメインをプロビジョニングできます。

* **CSP は PowerShell を使用して、その顧客に対する新しいマネージド ドメインの作成をスクリプト化できる。** 詳細については、[PowerShell を使用して Azure AD DS を有効にする方法](powershell-create-instance.md)に関するページを参照してください。

* **CSP 管理エージェントは、その資格情報を使用してマネージド ドメインで継続的な管理タスクを実行することができない。** CSP 管理者ユーザーは、その資格情報を使用してマネージド ドメイン内で日常的な管理タスクを実行することができません。 これらのユーザーは顧客の Azure AD テナントの外部にいるため、顧客の Azure AD テナント内ではその資格情報を使用できません。 Azure AD DS は、これらのユーザーの Kerberos/NTLM パスワード ハッシュにアクセスできないため、マネージド ドメインではユーザーを認証できません。

  > [!WARNING]
  > マネージド ドメインで継続的な管理タスクを実行するには、顧客のディレクトリ内にユーザー アカウントを作成する必要があります。
  >
  > CSP 管理者ユーザーの資格情報を使用してマネージド ドメインにサインインすることはできません。 これを行うには、顧客の Azure AD テナントに属するユーザー アカウントの資格情報を使用します。 これらの資格情報は、VM のマネージド ドメインへの参加、DNS の管理、グループ ポリシーの管理などのタスクに必要です。

* **継続的な管理のために作成されたユーザー アカウントを *AAD DC Administrators* グループに追加する必要がある。** *AAD DC Administrators* グループには、マネージド ドメインで特定の委任された管理タスクを実行する特権があります。 これらのタスクには、DNS の構成、組織単位の作成、グループ ポリシーの管理などが含まれます。
    
    CSP パートナーがマネージド ドメインでこれらのタスクを実行するには、顧客の Azure AD テナント内にユーザー アカウントを作成する必要があります。 このアカウントの資格情報は、CSP パートナーの管理エージェントと共有する必要があります。 また、このユーザー アカウントを使用してマネージド ドメインでの構成タスクを実行できるようにするには、このユーザー アカウントを *AAD DC Administrators* グループに追加する必要があります。

## <a name="next-steps"></a>次のステップ

作業を開始するには、[Azure CSP プログラムに登録](/partner-center/enrolling-in-the-csp-program)してください。 その後、[Azure portal](tutorial-create-instance.md) または [Azure PowerShell](powershell-create-instance.md) を使用して Azure AD Domain Services を有効にすることができます。
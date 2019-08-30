---
title: Azure Cloud Solution Provider 用 Azure Active Directory Domain Services | Microsoft Docs
description: Azure Cloud Solution Provider 用 Azure Active Directory Domain Services。
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: iainfou
ms.openlocfilehash: dc4ad7d8cf9f3267713fd066fa79a4d9d8ab733f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612970"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Azure Cloud Solution Provider (CSP) 用 Azure Active Directory (AD) Domain Services
この記事では、Azure CSP サブスクリプションで Azure AD Domain Services を使用する方法について説明します。

## <a name="overview-of-azure-csp"></a>Azure CSP の概要
Azure CSP は Microsoft パートナー向けのプログラムであり、さまざまな Microsoft クラウド サービスのライセンス チャネルを提供します。 Azure CSP により、パートナーは販売の管理、課金関係の所有、技術および課金のサポートの提供が可能になり、顧客の単一窓口になることができます。 さらに、Azure CSP では、セルフサービス ポータルや付随する API など、ツールの完全なセットも提供しています。 これらのツールにより、CSP パートナーは Azure リソースを簡単にプロビジョニングおよび管理し、顧客とそのサブスクリプションに対して課金を行うことができます。

[パートナー センター ポータル](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)は、すべての Azure CSP パートナーのエントリー ポイントとして機能します。 ポータルでは、豊富な顧客管理機能や自動処理などが提供されます。 Azure CSP パートナーは、Web ベースの UI を使用するか、PowerShell と各種 API 呼び出しを使用して、パートナー センターの機能を使用できます。

次の図は、CSP モデルのしくみを大まかに示しています。 Contoso には、Azure AD Active Directory があります。 Contoso は、Azure CSP サブスクリプションにリソースをデプロイして管理する CSP と提携しています。 また、Contoso に直接課金される通常の Azure (Direct) サブスクリプションを持つこともできます。

![CSP モデルの概要](./media/csp/csp_model_overview.png)

CSP パートナーのテナントには、管理エージェント、ヘルプデスク エージェント、販売エージェントの 3 つの特殊なエージェント グループがあります。 管理エージェント グループは、Contoso の Azure AD ディレクトリでテナント管理者ロールに割り当てられています。 これにより、CSP パートナーの管理エージェント グループに属するユーザーは、Contoso の Azure AD ディレクトリでテナント管理者特権を持ちます。 CSP パートナーが Contoso の Azure CSP サブスクリプションをプロビジョニングすると、管理エージェント グループがそのサブスクリプションの所有者ロールに割り当てられます。 これにより、CSP パートナーの管理エージェントは、仮想マシン、仮想ネットワーク、Azure AD Domain Services などの Azure リソースを Contoso に代わってプロビジョニングするために必要な特権を持ちます。

詳細については、「[Azure CSP overview (Azure CSP の概要)](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)」をご覧ください。

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Azure CSP サブスクリプションで Azure AD Domain Services を使用する利点
Azure AD Domain Services は、Windows Server AD と互換性のあるサービス (LDAP、Kerberos/NTLM 認証、ドメイン参加、グループ ポリシー、DNS など) を Azure で提供します。 数十年にわたり、これらの機能を使用して AD に対して機能する多数のアプリケーションが構築されてきました。 多くの独立系ソフトウェア ベンダー (ISV) が、顧客の構内でアプリケーションを構築し、展開しています。 多くの場合、これらのアプリケーションが展開されているさまざまな環境にアクセスする必要があるため、サポートが煩雑になります。 Azure CSP サブスクリプションは、Azure のスケールと柔軟性を備えたよりシンプルな代替手段となります。

Azure AD Domain Services で Azure CSP サブスクリプションがサポートされるようになりました。 顧客の Azure AD ディレクトリに関連付けられた Azure CSP サブスクリプションにアプリケーションをデプロイできます。 これにより、従業員 (サポート スタッフ) は、会社の資格情報を使用して、アプリケーションがデプロイされている仮想マシンを管理し、対応することができます。 さらに、顧客の Azure AD ディレクトリの Azure AD Domain Services のマネージド ドメインをプロビジョニングできます。 アプリケーションは顧客のマネージド ドメインに接続されます。 そのため、Kerberos/NTLM、LDAP、または [System.DirectoryServices API](/dotnet/api/system.directoryservices) に依存するアプリケーションの機能は、顧客のディレクトリに対してシームレスに動作します。 エンド カスタマーは、アプリケーションがデプロイされているインフラストラクチャの管理を気にする必要なく、アプリケーションをサービスとして使用することで大きなメリットが得られます。

Azure AD Domain Services も含め、そのサブスクリプションで使用した Azure リソースに対するすべての課金がチャージ バックされます。 販売、課金、テクニカル サポートなどに関して、顧客との関係の完全な制御を維持できます。Azure CSP プラットフォームの柔軟性により、小規模のサポート エージェント チームが、アプリケーションのインスタンスをデプロイしている多数の顧客にサービスを提供できます。


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Azure AD Domain Services の CSP デプロイ モデル
Azure CSP サブスクリプションで Azure AD Domain Services を使用する場合、2 つの方法があります。 セキュリティとシンプルさに関する顧客の考慮事項に基づいて適切な方法を選択してください。

### <a name="direct-deployment-model"></a>直接デプロイ モデル
このデプロイ モデルでは、Azure CSP サブスクリプションに属する仮想ネットワーク内で Azure AD Domain Services が有効になります。 CSP パートナーの管理エージェントには、次の特権があります。
* 顧客の Azure AD ディレクトリでのグローバル管理者特権。
* Azure CSP サブスクリプションのサブスクリプション所有者特権。

![直接デプロイ モデル](./media/csp/csp_direct_deployment_model.png)

このデプロイ モデルでは、CSP プロバイダーの管理エージェントが顧客の ID を管理できます。 これらの管理エージェントは、新しいユーザーやグループのプロビジョニングや、顧客の Azure AD ディレクトリ内でのアプリケーションの追加などを実行できます。このデプロイ モデルは、選任の ID 管理者がいない組織や、CSP パートナーに ID を管理してもらうことを望む組織など、小規模の組織に適しています。


### <a name="peered-deployment-model"></a>ピアリング デプロイ モデル
このデプロイ モデルでは、顧客 (つまり、顧客が料金を支払う Azure Direct サブスクリプション) に属する仮想ネットワーク内で Azure AD Domain Services が有効になります。 CSP パートナーは、顧客の CSP サブスクリプションに属する仮想ネットワーク内でアプリケーションをデプロイできます。 仮想ネットワークは、Azure 仮想ネットワーク ピアリングを使用して接続できます。 これにより、CSP パートナーが Azure CSP サブスクリプションにデプロイしたワークロード/アプリケーションは、顧客の Azure Direct サブスクリプションにプロビジョニングされた顧客のマネージド ドメインに接続できます。

![ピアリング デプロイ モデル](./media/csp/csp_peered_deployment_model.png)

このデプロイ モデルでは特権が分離されるので、CSP パートナーのヘルプデスク エージェントが Azure サブスクリプションを管理し、サブスクリプションにリソースをデプロイして管理できます。 ただし、CSP パートナーのヘルプデスク エージェントには、顧客の Azure AD ディレクトリに対するグローバル管理者特権は不要です。 顧客の ID 管理者が組織の ID を引き続き管理できます。

このデプロイ モデルは、ISV (独立系ソフトウェア ベンダー) が、顧客の AD にも接続する必要があるオンプレミス アプリケーションのホストテッド バージョンを提供するシナリオに適しています。


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>CSP サブスクリプションでの Azure AD Domain Services のマネージド ドメインの管理
Azure CSP サブスクリプションでマネージド ドメインを管理する場合、次の重要な考慮事項が適用されます。

* **CSP 管理エージェントは、その資格情報を使用してマネージド ドメインをプロビジョニングできる。** Azure AD Domain Services は、Azure CSP サブスクリプションをサポートしています。 そのため、CSP パートナーの管理エージェント グループに属するユーザーは、新しい Azure AD Domain Services のマネージド ドメインをプロビジョニングできます。

* **CSP は PowerShell を使用して、その顧客に対する新しいマネージド ドメインの作成をスクリプト化できる。** 詳細については、[PowerShell を使用して Azure AD Domain Services を有効にする方法](powershell-create-instance.md)に関する記事を参照してください。

* **CSP 管理エージェントは、その資格情報を使用してマネージド ドメインで継続的な管理タスクを実行することはできない。** CSP 管理者ユーザーは、その資格情報を使用してマネージド ドメイン内で日常的な管理タスクを実行することができません。 これらのユーザーは顧客の Azure AD ディレクトリの外部に存在するので、その資格情報は顧客の Azure AD ディレクトリ内では使用できません。 そのため、Azure AD Domain Services は、これらのユーザーの Kerberos/NTLM パスワード ハッシュにアクセスできません。 その結果、Azure AD Domain Services のマネージド ドメインでは、このようなユーザーを認証することはできません。

  > [!WARNING]
  > **マネージド ドメインで継続的な管理タスクを実行するには、顧客のディレクトリ内にユーザー アカウントを作成する必要があります。**
  > CSP 管理者ユーザーの資格情報を使用して、マネージド ドメインにサインインすることはできません。 サインインするには、顧客の Azure AD ディレクトリに属するユーザー アカウントの資格情報を使用します。 この資格情報は、マネージド ドメインへの仮想マシンの参加、DNS の管理、グループ ポリシーの管理などのタスクに必要です。
  >

* **継続的な管理用に作成されたユーザー アカウントを "AAD DC Administrators" グループに追加する必要がある。** "AAD DC Administrators" グループには、特定の委任された管理タスクをマネージド ドメインで実行する権限があります。 これらのタスクには、DNS の構成、組織単位の作成、グループ ポリシーの管理などがあります。CSP パートナーがマネージド ドメインでこのようなタスクを実行するには、顧客の Azure AD ディレクトリ内にユーザー アカウントを作成する必要があります。 このアカウントの資格情報は、CSP パートナーの管理エージェントと共有する必要があります。 また、このユーザー アカウントを使用して、マネージド ドメインで構成タスクを実行できるようにするには、このユーザーアカウントを "AAD DC Administrators" グループに追加する必要があります。


## <a name="next-steps"></a>次の手順
* [Azure CSP プログラムに登録](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program)し、Azure CSP を通じてビジネスを創出する
* [Azure CSP で利用できる Azure サービス](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)の一覧を確認する
* [PowerShell を使用して Azure AD Domain Services を有効にする](powershell-create-instance.md)
* [Azure AD ドメイン サービスの使用開始](tutorial-create-instance.md)

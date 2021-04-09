---
title: 大規模な Azure Migrate プロジェクトの管理
description: 委任された顧客リソースで Azure Migrate を効果的に使用する方法を学習します。
ms.date: 01/29/2021
ms.topic: how-to
ms.openlocfilehash: ed2fc8ae1a3b87a3b2501c3a390fe7a1822003e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100393583"
---
# <a name="manage-azure-migrate-projects-at-scale-with-azure-lighthouse"></a>Azure Lighthouse による大規模な Azure Migrate プロジェクトの管理

このトピックでは、[Azure Lighthouse](../overview.md) を利用して、複数の Azure Active Directory (Azure AD) テナントにわたってスケーラブルな方法で [Azure Migrate](../../migrate/migrate-services-overview.md) を使用する方法の概要について説明します。

Azure Lighthouse を使用すると、サービス プロバイダーは一度に複数のテナントにわたって大規模に操作を実行できるため、管理タスクがより効率的になります。

Azure Migrate では、オンプレミスのサーバー、インフラストラクチャ、アプリケーション、データを評価し、Azure への移行を行うための一元化されたハブが提供されます。 通常、複数の顧客に対して大規模な評価と移行を実行するパートナーは、[CSP (クラウド ソリューション プロバイダー) サブスクリプション モデル](/partner-center/customers-revoke-admin-privileges)を使用するか、[顧客テナントでゲスト ユーザーを作成する](../../active-directory/external-identities/what-is-b2b.md)ことによって、各顧客サブスクリプションに個別にアクセスする必要があります。

Azure Lighthouse と Azure Migrate の統合により、サービス プロバイダーは、さまざまな顧客のワークロードを大規模に検出、評価、移行でき、顧客は自分の環境を完全に可視化し、制御することができます。 Azure の委任されたリソース管理により、サービス プロバイダーは複数の顧客テナントにわたって管理するすべての Azure Migrate プロジェクトを 1 つのビューで表示できます。

> [!NOTE]
> パートナーは Azure Lighthouse を使用して、オンプレミスの VMware VM、Hyper-V VM、物理サーバー、AWS/GCP インスタンスの検出、評価、移行を実行できます。 [VMware VM の移行](../../migrate/server-migrate-overview.md)には 2 つのオプションがあります。 現在、委任された顧客サブスクリプションの移行プロジェクトで作業する場合は、エージェントベースの移行方法のみを使用できます。顧客のスコープへの委任されたアクセスによる、エージェントレス レプリケーションを使用した移行は現在サポートされていません。

> [!TIP]
> このトピックではサービスのプロバイダーと顧客について触れますが、このガイドラインは、[Azure Lighthouse を使用して複数のテナントを管理する企業](../concepts/enterprise.md)にも当てはまります。

シナリオによっては、顧客テナントまたは管理テナントに Azure Migrate を作成することもできます。 下の考慮事項を確認し、お客様の移行のニーズに最適なモデルを決定してください。

## <a name="create-an-azure-migrate-project-in-the-customer-tenant"></a>Azure Migrate プロジェクトを顧客テナントで作成する

Azure Lighthouse を使用する場合の 1 つのオプションは、Azure Migrate プロジェクトを顧客テナントで作成することです。 その後、管理テナントのユーザーは、移行プロジェクトを作成するときに顧客サブスクリプションを選択できます。 サービス プロバイダーは、管理テナントから必要な移行操作を実行できます。 これには、ワークロードを検出するための Azure Migrate アプライアンスのデプロイ、VM のグループ化とクラウド関連のコストの計算によるワークロードの評価、VM の準備状態の確認、移行の実行などが含まれます。

このシナリオでは、検出と評価の手順を管理テナントから開始して実行できる場合でも、その管理テナントでリソースは作成されず、格納もされません。 移行プロジェクト、オンプレミスのワークロードの評価レポート、移動先での移行済みリソースなどのすべてのリソースが、顧客サブスクリプションにデプロイされます。 ただし、サービス プロバイダーは、独自のテナントとポータルでの操作により、すべての顧客プロジェクトにアクセスできます。

この方法では、複数の顧客にわたって操作するサービス プロバイダーのコンテキスト切り替えを最小限に抑えながら、顧客は自分のすべてのリソースを独自のテナントに保持できます。

このモデルのワークフローは、次のようになります。

1. 顧客は [Azure Lighthouse にオンボードされます](onboard-customer.md)。 Azure Migrate で使用される ID には、共同作成者の組み込みロールが必要です。 このロールを使用する例については、[delegated-resource-management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) サンプル テンプレートを参照してください。
1. 指定されたユーザーは、Azure portal で管理テナントにサインインし、Azure Migrate に移動します。 このユーザーは[ Azure Migrate プロジェクト](../../migrate/create-manage-projects.md)を作成し、適切な委任された顧客サブスクリプションを選択します。
1. その後、ユーザーは[検出と評価のための手順を実行します](../../migrate/tutorial-discover-vmware.md)。

   VMware VMの場合、アプライアンスを構成する前に、検出を vCenter Server データセンター、クラスター、クラスターのフォルダー、ホスト、ホストのフォルダー、または個々の VＭ に制限することができます。 範囲を設定するには、vCenter Server にアクセスするためにアプライアンスで使用されているアカウントにアクセス許可を割り当てます。 これは、複数の顧客の VM がハイパーバイザーでホストされている場合に便利です。 Hyper-V の検出範囲を制限することはできません。

    > [!NOTE]
    > Azure Lighthouse によって提供される委任されたアクセス権で Azure Migrate を使用して、移行用の VMware 仮想マシンを検出して評価することができます。 VMware 仮想マシンの移行では、委任された顧客サブスクリプションの移行プロジェクトで作業している場合、エージェントベースの方法のみが現在サポートされています。

1. ターゲットの顧客サブスクリプションの準備ができたら、Azure Lighthouse によって付与されたアクセス権を使用して移行を続行します。 評価結果と移行されたリソースを含む移行プロジェクトは、ターゲット サブスクリプションの顧客テナントで作成されます。

> [!TIP]
> 移行の前に、基盤インフラストラクチャ リソースをプロビジョニングし、仮想マシンの移行先となるサブスクリプションを準備するために、ランディング ゾーンをデプロイする必要があります。 このランディング ゾーンの一部のリソースへのアクセスまたはその作成には、Azure Lighthouse で現在サポートされていない、所有者の組み込みロールが必要になることがあります。 このようなシナリオでは、顧客は CSP サブスクリプション モデルを使用して、ゲスト アクセスの役割を提供するか、管理者のアクセス権を委任する必要がある場合があります。 マルチテナント ランディング ゾーンを作成する方法については、GitHub の[マルチテナントのランディング ゾーンのデモ ソリューション](https://github.com/Azure/Multi-tenant-Landing-Zones)に関する記事を参照してください。

## <a name="create-an-azure-migrate-project-in-the-managing-tenant"></a>管理テナントで Azure Migrate プロジェクトを作成する

このシナリオでは、検出や評価などの移行関連の操作は、管理テナントのユーザーによって引き続き実行されます。 ただし、移行プロジェクトと関連するすべてのリソースはパートナー テナントに存在するため、顧客はプロジェクトを直接可視化することはできません (ただし、必要に応じて評価を顧客と共有できます)。 各顧客の移行先は顧客のサブスクリプションになります。

このアプローチにより、サービス プロバイダーは移行の検出と評価のプロジェクトを迅速に開始し、それらの初期手順を顧客のサブスクリプションやテナントから抽象化することができます。

このモデルのワークフローは、次のようになります。

1. 顧客は [Azure Lighthouse にオンボードされます](onboard-customer.md)。 Azure Migrate で使用される ID には、共同作成者の組み込みロールが必要です。 このロールを使用する例については、[delegated-resource-management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) サンプル テンプレートを参照してください。
1. 指定されたユーザーは、Azure portal で管理テナントにサインインし、Azure Migrate に移動します。 このユーザーは、管理テナントに属しているサブスクリプションに [Azure Migrate プロジェクトを作成します](../../migrate/create-manage-projects.md)。
1. その後、ユーザーは[検出と評価のための手順を実行します](../../migrate/tutorial-discover-vmware.md)。 オンプレミスの VM は、管理テナントで作成された移行プロジェクト内で検出および評価され、そこから移行されます。

   同じ Hyper-V ホストで複数の顧客を管理している場合は、すべてのワークロードを一度に検出できます。 顧客に固有の VM を同じグループで選択してから評価を作成し、移行先として適切な顧客のサブスクリプションを選択することによって移行を実行できます。 検出範囲を制限する必要はなく、1 つの移行プロジェクトですべての顧客のワークロードを徹底して管理できます。

1. 準備ができたら、委任された顧客サブスクリプションをワークロードのレプリケートと移行の宛先として選択することによって、移行を進めます。 新しく作成されたリソースは顧客サブスクリプションに存在しますが、移行プロジェクトに関連する評価データとリソースは管理テナントに残ります。

注: デプロイする前に、環境を反映するようにパラメーター ファイルを変更する必要があります。 https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate

## <a name="partner-recognition-for-customer-migrations"></a>顧客移行についてのパートナー認識

[Microsoft Partner Network](https://partner.microsoft.com) のメンバーとして、ユーザーは自分のパートナー ID と、委任された顧客リソースの管理に使用されている資格情報をリンクすることができます。 こうすると、Microsoft は、Azure 使用による収益や効果に貢献している組織を、顧客に対して実行している移行プロジェクトなどのタスクに基づいて把握できます。

詳細については、「[パートナー ID をリンクして、委任されたリソースで影響を追跡する](partner-earned-credit.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Migrate](../../migrate/migrate-services-overview.md) に関する詳細情報をご覧ください。
- Azure Lighthouse でサポートされているその他の[テナント間管理エクスペリエンス](../concepts/cross-tenant-management-experience.md)に関する詳細情報をご覧ください。

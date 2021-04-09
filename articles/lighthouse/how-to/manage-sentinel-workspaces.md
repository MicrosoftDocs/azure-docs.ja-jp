---
title: Azure Sentinel ワークスペースの大規模な管理を行う
description: 委任された顧客リソースで Azure Sentinel を効果的に管理する方法を学習します。
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 009edaefe021dedb5d9a40a8cc3bac2c2974ae10
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101702523"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>Azure Sentinel ワークスペースの大規模な管理を行う

サービス プロバイダーは、[Azure Lighthouse](../overview.md) に複数の顧客テナントをオンボードしている場合があります。 Azure Lighthouse を使用すると、サービス プロバイダーは一度に複数の Azure Active Directory (Azure AD) テナントにわたって大規模に操作を実行できるため、管理タスクがより効率的になります。

Azure Sentinel によって、セキュリティ分析と脅威インテリジェンスが実現され、アラートの検出、脅威の可視性、予防的な捜索、および脅威への対応のための 1 つのソリューションが提供されます。 Azure Lighthouse を使用すると、テナントをまたがる複数の Azure Sentinel ワークスペースの大規模な管理を行うことができます。 これにより、複数のワークスペースでクエリを実行、あるいはブックを作成し、接続されたデータ ソースからのデータを視覚化および監視して洞察を得るなどのシナリオが実現します。 クエリやプレイブックなどの IP は管理テナントに残りますが、顧客テナントでセキュリティ管理を実行するために使用できます。

このトピックでは、テナント間の可視性およびマネージド セキュリティ サービスのためのスケーラブルな方法で、[Azure Sentinel](../../sentinel/overview.md) を使用する方法の概要について説明します。

> [!TIP]
> このトピックではサービスのプロバイダーと顧客について触れますが、このガイドラインは、[Azure Lighthouse を使用して複数のテナントを管理する企業](../concepts/enterprise.md)にも当てはまります。

## <a name="architectural-considerations"></a>アーキテクチャに関する考慮事項

Azure Sentinel を使用してサービスとしてのセキュリティ オファリングを構築する必要があるマネージド セキュリティ サービス プロバイダー (MSSP) の場合、個々の顧客テナント内にデプロイされた複数の Azure Sentinel ワークスペースを一元的に監視、管理、構成するために単一のセキュリティ オペレーション センター (SOC) が必要になることがあります。 同様に、複数の Azure AD テナントを持つ企業では、テナントにまたがってデプロイされた複数の Azure Sentinel ワークスペースを一元的に管理することが必要になる場合があります。

このデプロイの一元化されたモデルには、次の利点があります。

- データの所有権は管理対象の各テナントに残ります。
- 地理的境界内にデータを格納するための要件がサポートされます。
- 複数の顧客のデータが同じワークスペースに格納されないため、データの分離が保証されます。
- 管理対象テナントからのデータ窃盗を防ぐことができ、データのコンプライアンスを確保するのに役立ちます。
- 関連コストは、管理テナントではなく、各管理対象テナントに請求されます。
- Azure Sentinel と統合されているすべてのデータ ソースとデータ コネクタからのデータ (Azure AD のアクティビティ ログ、Office 365 のログ、Microsoft Threat Protection のアラートなど) は、各顧客のテナント内に残ります。
- ネットワーク待機時間が短縮されます。
- 新しい子会社または顧客を簡単に追加または削除できます。

> [!NOTE]
> 別の[リージョン](../../availability-zones/az-overview.md#regions)にある委任されたリソースを管理することができます。 ただし、[各国のクラウド](../../active-directory/develop/authentication-national-cloud.md)と Azure パブリック クラウドにわたって行われる、または 2 つの独立した国内クラウドにわたって行われるサブスクリプションの委任はサポートされていません。

## <a name="granular-azure-role-based-access-control-azure-rbac"></a>詳細な Azure ロールベースのアクセス制御 (Azure RBAC)

MSSP によって管理される各顧客サブスクリプションは、[Azure Lighthouse にオンボードする](onboard-customer.md)必要があります。 これにより、管理テナントの指定されたユーザーは、顧客テナントにデプロイされた Azure Sentinel ワークスペースにアクセスして管理操作を実行できます。

承認の作成時に、管理テナントのユーザー、グループ、またはサービス プリンシパルに Azure Sentinel の組み込みロールを割り当てることができます。

- [Azure Sentinel 閲覧者](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel レスポンダー](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel 共同作成者](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

追加機能を実行するために、組み込みロールをさらに割り当てることもできます。 Azure Sentinel で使用できる特定のロールについては、「[Azure Sentinel のアクセス許可](../../sentinel/roles.md)」を参照してください。

顧客がオンボードされたら、指定されたユーザーは管理テナントにログインし、割り当てられたロールを使用して[顧客の Azure Sentinel ワークスペースに直接アクセス](../../sentinel/multiple-tenants-service-providers.md)できます。

## <a name="view-and-manage-incidents-across-workspaces"></a>ワークスペース全体のインシデントを表示および管理する

複数の顧客用の Azure Sentinel リソースを管理する場合、一度に複数のテナントにわたる複数のワークスペースのインシデントを表示して管理することができます。 詳細については、「[多くのワークスペースのインシデントを一度に操作する](../../sentinel/multiple-workspace-view.md)」と「[ワークスペースおよびテナント全体での Azure Sentinel の拡張](../../sentinel/extend-sentinel-across-workspaces-tenants.md)」を参照してください。

> [!NOTE]
> 管理テナント内のユーザーに、管理されているすべてのワークスペースに対する読み取りおよび書き込みのアクセス許可が割り当てられていることを確認してください。 ユーザーが一部のワークスペースに対する読み取りアクセス許可のみを持っている場合、それらのワークスペースでインシデントを選択するときに警告メッセージが表示される可能性があり、そのユーザーはそれらのインシデントや、お客様が (そのアクセス許可を持っている場合でも) それらを使用して選択した他のインシデントを変更することはできません。

## <a name="configure-playbooks-for-mitigation"></a>軽減のためにプレイブックを構成する

[プレイブック](../../sentinel/tutorial-respond-threats-playbook.md)は、アラートがトリガーされたときに自動的に軽減するために使用できます。 これらのプレイブックは、手動で実行することも、特定のアラートがトリガーされたときに自動的に実行することもできます。 プレイブックは、管理テナントまたは顧客テナントにデプロイできます。その対応手順は、セキュリティの脅威に対応するためにどのテナントのユーザーがアクションを実行する必要があるかに基づいて構成されます。

## <a name="create-cross-tenant-workbooks"></a>テナント間のブックを作成する

[Azure Sentinel の Azure Monitor ブック](../../sentinel/overview.md#workbooks)は、接続されたデータ ソースからのデータを視覚化および監視して洞察を得るのに役立ちます。 Azure Sentinel で組み込みのブック テンプレートを使用することも、シナリオに合わせてカスタム ブックを作成することもできます。

管理テナントでブックをデプロイし、顧客テナント全体のデータを監視およびクエリするために大規模なダッシュボードを作成することができます。 詳細については、[ワークスペース間の監視](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks)に関するセクションを参照してください。 

また、ブックは、その顧客に固有のシナリオ用に管理する個々のテナントに直接配置することもできます。

## <a name="run-log-analytics-and-hunting-queries-across-azure-sentinel-workspaces"></a>Azure Sentinel ワークスペースにまたがって Log Analytics とハンティング クエリを実行する

脅威検出のために Log Analytics クエリを作成し、管理テナントで一元的に保存します ([ハンティング クエリ](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-hunting) を含む)。 その後、これらのクエリは、Union 演算子とワークスペースの () 式を使用して、顧客のすべての Azure Sentinel ワークスペースで実行できます。 詳細については、「[ワークスペース間のクエリ](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying)」を参照してください。

## <a name="use-automation-for-cross-workspace-management"></a>ワークスペース間の管理にオートメーションを使用する

オートメーションを使用して複数の Azure Sentinel ワークスペースを管理し、[ハンティング クエリ](../../sentinel/hunting.md)、プレイブック、ブックを構成することができます。 詳細については、「[オートメーションを使用するワークスペース間の管理](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation)」を参照してください。

## <a name="monitor-security-of-office-365-environments"></a>Office 365 環境のセキュリティを監視する

複数のテナントにまたがる Office 365 環境のセキュリティを監視するには、Azure Lighthouse と Azure Sentinel を組み合わせて使用します。 まず、すぐに使える [Office 365 データ コネクタを管理対象テナントで有効にする必要があります](../../sentinel/connect-office-365.md)。これによって、Exchange と SharePoint (OneDrive を含む) でのユーザーと管理者のアクティビティに関する情報を、管理対象テナント内の Azure Sentinel ワークスペースに取り込めるようにします。 これには、ファイルのダウンロードなどのアクション、送信されたアクセス要求、グループ イベントへの変更、メールボックスの操作に関する詳細と、アクションを実行したユーザーの情報が含まれます。 [Office 365 DLP アラート](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-office-365-dlp-events-into-azure-sentinel/ba-p/1031820)も、組み込みの Office 365 コネクタの一部としてサポートされています。

[Microsoft Cloud App Security (MCAS) コネクタ](../../sentinel/connect-cloud-app-security.md)を有効にすると、アラートと Cloud Discovery のログを Azure Sentinel にストリーミングできます。 これにより、クラウド アプリを可視化し、サイバー脅威を特定して対処するための高度な分析を入手し、データの移動を制御できるようになります。 MCAS のアクティビティ ログは [Common Event Format (CEF) を使用して処理する](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-box-com-activity-events-via-microsoft-cloud-app-security/ba-p/1072849)ことができます。

Office 365 データ コネクタのセットアップが完了したら、ワークブック内のデータの表示と分析、クエリを使用してカスタム アラートを作成する、脅威に対応するためのプレイブックを構成するなど、クロステナントの Azure Sentinel 機能を使用できるようになります。

## <a name="next-steps"></a>次のステップ

- [Azure Sentinel](../../sentinel/overview.md) について学習します。
- [Azure Sentinel の価格ページ](https://azure.microsoft.com/pricing/details/azure-sentinel/)を確認します。
- [テナント間の管理エクスペリエンス](../concepts/cross-tenant-management-experience.md)について学習します。


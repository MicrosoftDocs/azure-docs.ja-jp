---
title: サブスクリプション、リソース グループ、またはリージョン間でロジック アプリを移動する
description: ロジック アプリまたは統合アカウントを別の Azure サブスクリプション、リソース グループ、または場所 (リージョン) に移行する
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: aca2c51ff14b99ba41b159cf32e59dc861de7a53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87826213"
---
# <a name="move-logic-app-resources-to-other-azure-resource-groups-regions-or-subscriptions"></a>ロジック アプリのリソースを別の Azure リソース グループ、リージョン、またはサブスクリプションに移動する

ロジック アプリや関連リソースを別の Azure リソース グループ、リージョン、またはサブスクリプションに移行するタスクを完了する方法として、Azure portal、Azure PowerShell、Azure CLI、REST API など、さまざまな方法が用意されています。 リソースを移動する前に、次の考慮事項を確認してください。 

* Azure のリソース グループまたはサブスクリプション間では、[特定の種類のロジック アプリのリソース](../azure-resource-manager/management/move-support-resources.md#microsoftlogic)のみを移動できます。

* Azure サブスクリプションと各 Azure リージョンで使用できるロジック アプリのリソース数の[制限](../logic-apps/logic-apps-limits-and-config.md)を確認します。 これらの制限は、サブスクリプションまたはリソース グループ間でリージョンが同じままである場合に、特定のリソースの種類を移動できるかどうかに影響します。 たとえば、Free レベル統合アカウントは、各 Azure サブスクリプションの各 Azure リージョンに対して、1 つだけ設定できます。

* リソースを移動すると、Azure によって新しいリソース ID が作成されます。 このため、代わりに新しい ID を使用して、移動したリソースに関連付けられているスクリプトまたはツールを更新してください。

* サブスクリプション、リソース グループ、またはリージョン間でロジック アプリを移行した後は、Open Authorization (OAuth) を必要とするすべての接続を再作成または再認可する必要があります。

* [統合サービス環境 (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) は、同じ Azure リージョンまたは Azure サブスクリプションに存在する別のリソース グループにのみ移動できます。 別の Azure リージョンまたは Azure サブスクリプションに存在するリソース グループに ISE を移動することはできません。 また、移動した後に、ロジック アプリのワークフロー、統合アカウント、接続などで、ISE へのすべての参照を更新する必要があります。

## <a name="prerequisites"></a>前提条件

* 移動するロジック アプリまたは統合アカウントの作成に使用したものと同じ Azure サブスクリプション

* 必要なリソースを移動して設定するためのリソース所有者のアクセス許可。 [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/built-in-roles.md#owner) について詳しく確認します。

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>サブスクリプション間でリソースを移動する

ロジック アプリや統合アカウントなどのリソースを別の Azure サブスクリプションに移動するには、Azure portal、Azure PowerShell、Azure CLI、または REST API を使用します。 これらの手順では、リソースのリージョンが同じままの場合に使用できる Azure portal について説明します。 その他の手順や一般的な準備については、「[リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。

1. [Azure portal](https://portal.azure.com)で、移動するロジック アプリのリソースを探して選択します。

1. リソースの **[概要]** ページで、 **[サブスクリプション]** の横にある **[変更]** リンクを選択します。

1. **[リソースの移動]** ページで、ロジック アプリのリソースと、移動する関連リソースを選択します。

1. **[サブスクリプション]** の一覧から、移動先のサブスクリプションを選択します。

1. **[リソース グループ]** の一覧から、移動先のリソース グループを選択します。 または、 **[新しいリソース グループの作成]** を選択して別のリソース グループを作成します。

1. 移動したリソースに関連付けられているスクリプトやツールは、新しいリソース ID で更新されるまで機能しないことを理解していることを確認するために、[確認] ボックスを選択してから、 **[OK]** を選択します。

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>リソース グループ間でリソースを移動する

ロジック アプリや統合アカウント、または[統合サービス環境 (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) などのリソースを別の Azure リソース グループに移動するには、Azure portal、Azure PowerShell、Azure CLI、または REST API を使用します。 これらの手順では、リソースのリージョンが同じままの場合に使用できる Azure portal について説明します。 その他の手順や一般的な準備については、「[リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。

グループ間でリソースを実際に移動する前に、リソースを別のグループに正常に移動できるかどうかをテストできます。 詳細については、「[移動の検証](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move)」を参照してください。

1. [Azure portal](https://portal.azure.com)で、移動するロジック アプリのリソースを探して選択します。

1. リソースの **[概要]** ページで、 **[リソース グループ]** の横にある **[変更]** リンクを選択します。

1. **[リソースの移動]** ページで、ロジック アプリのリソースと、移動する関連リソースを選択します。

1. **[リソース グループ]** の一覧から、移動先のリソース グループを選択します。 または、 **[新しいリソース グループの作成]** を選択して別のリソース グループを作成します。

1. 移動したリソースに関連付けられているスクリプトやツールは、新しいリソース ID で更新されるまで機能しないことを理解していることを確認するために、[確認] ボックスを選択してから、 **[OK]** を選択します。

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>リージョン間でリソースを移動する

ロジック アプリを別のリージョンに移動する場合、オプションはロジック アプリの作成方法によって異なります。 選択したオプションに基づいて、ロジック アプリで接続を再作成または再認証する必要があります。

* Azure portal で、新しいリージョンにロジック アプリを再作成し、ワークフロー設定を再構成します。 時間を節約するには、基になるワークフローの定義と接続をソース アプリからコピー先のアプリにコピーします。 ロジック アプリの背後にある "コード" を表示するには、ロジック アプリ デザイナーのツールバーで **[コード ビュー]** を選択します。

* Visual Studio と Azure Logic Apps Tools for Visual Studio を使用すると、Azure portal から [Azure Resource Manager テンプレート](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)として [ロジック アプリを開いてダウンロードする](../logic-apps/manage-logic-apps-with-visual-studio.md)ことができます。 ほとんどの場合、このテンプレートはデプロイの準備ができており、ワークフロー自体や接続など、ロジック アプリのリソース定義が含まれています。 また、このテンプレートはデプロイ時に使用する値のパラメーターも宣言します。 そうすることで、ロジック アプリをデプロイする場所と方法を、ニーズに応じてより簡単に変更できます。 デプロイに必要な場所とその他の情報を指定するには、個別のパラメーター ファイルを使用します。

* Azure DevOps の Azure Pipelines など、継続的インテグレーション (CI) ツールと継続的デリバリー (CD) ツールを使用してロジック アプリを作成およびデプロイした場合は、これらのツールを使用してアプリを別のリージョンにデプロイできます。

ロジック アプリのデプロイ テンプレートの詳細については、次のトピックを参照してください。

* [概要:Azure Resource Manager テンプレートを使用して Azure Logic Apps のデプロイを自動化する](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Azure portal からロジック アプリを検索して開き、Visual Studio にダウンロードする](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Azure Logic Apps 用の Azure Resource Manager テンプレートを作成する](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Azure Logic Apps 用の Azure Resource Manager テンプレートをデプロイする](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>関連リソース

Azure のオンプレミス データ ゲートウェイ リソースなど、一部の Azure リソースは、これらのリソースを使用するロジック アプリとは異なるリージョンに存在することがあります。 ただし、他の Azure リソース (リンクされた統合アカウントなど) は、ロジック アプリと同じリージョンに存在する必要があります。 シナリオに基づいて、アプリが同じリージョンに存在すると想定しているリソースに、ロジック アプリがアクセスできることを確認します。

たとえば、ロジック アプリを統合アカウントにリンクするには、両方のリソースが同じリージョンに存在している必要があります。 ディザスター リカバリーなどのシナリオでは通常、同じ構成とアーティファクトを持つ統合アカウントが必要です。 その他のシナリオでは、異なる構成とアーティファクトを持つ統合アカウントが必要になる場合があります。

Azure Logic Apps のカスタム コネクタは、そのコネクタの作成者と、同じ Azure サブスクリプションと Azure Active Directory テナントを持つユーザーにのみ表示されます。 これらのコネクタは、ロジック アプリがデプロイされている同じリージョンで利用できます。 詳細については、[組織内でのカスタム コネクタの共有](/connectors/custom-connectors/share)に関するページをご覧ください。

Visual Studio から取得するテンプレートには、ロジック アプリとその接続のリソース定義だけが含まれています。 このため、ロジック アプリで他のリソース (統合アカウントやパートナー、契約、スキーマなどの B2B アーティファクト) を使用している場合は、Azure portal を使用してその統合アカウントのテンプレートをエクスポートする必要があります。 このテンプレートには、統合アカウントとアーティファクトの両方のリソース定義が含まれています。 ただし、テンプレートは完全にはパラメーター化されていません。 そのため、デプロイに使用する値は手動でパラメーター化する必要があります。

### <a name="export-templates-for-integration-accounts"></a>統合アカウント用のテンプレートをエクスポートする

1. [Azure portal](https://portal.azure.com) で、統合アカウントを検索して開きます。

1. 統合アカウント メニューの **[設定]** で、 **[テンプレートのエクスポート]** を選択します。

1. ツールバーで **[ダウンロード]** を選択し、テンプレートを保存します。

1. テンプレートを開いて編集し、デプロイに必要な値をパラメーター化します。

## <a name="next-steps"></a>次のステップ

[Azure リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)

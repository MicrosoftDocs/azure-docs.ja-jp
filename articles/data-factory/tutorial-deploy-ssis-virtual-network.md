---
title: 仮想ネットワークに参加するように Azure-SSIS 統合ランタイムを構成するためのチュートリアル
description: Azure 仮想ネットワークに参加する目的で Azure-SSIS 統合ランタイムを参加させる方法について説明します。
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 3dbbce4adc44696fdd12f6ce948b48b34caaed75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100391237"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>仮想ネットワークに参加するように Azure-SQL Server Integration Services (SSIS) 統合ランタイム (IR) を構成する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

このチュートリアルでは、Azure portal を使用して、仮想ネットワークに参加するように Azure-SQL Server Integration Services (SSIS) 統合ランタイム (IR) を構成する基本的な手順について説明します。

これには次の手順が含まれます。

- 仮想ネットワークを構成する。
- Azure Data Factory ポータルから Azure-SSIS IR を仮想ネットワークに参加させる。

## <a name="prerequisites"></a>前提条件

- **Azure-SSIS 統合ランタイム**: Azure-SSIS 統合ランタイムがない場合は、開始する前に [Azure Data Factory で Azure-SSIS 統合ランタイムをプロビジョニング](tutorial-deploy-ssis-packages-azure.md)してください。

- **ユーザーのアクセス許可**: Azure-SSIS IR を作成するユーザーには、少なくとも Azure Data Factory リソースに対する[ロールを割り当てる](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)必要があります。次のオプションがあります。

    - 組み込みのネットワーク共同作成者ロールを使用します。 このロールには、必要なスコープよりずっと大きなスコープを持つ _Microsoft.Network/\*_ アクセス許可が備わっています。
    - 必要な _Microsoft.Network/virtualNetworks/\*/join/action_ アクセス許可のみを含むカスタム ロールを作成してください。 また、Azure-SSIS IR を Azure Resource Manager 仮想ネットワークに参加させるときに独自のパブリック IP アドレスを使用する場合は、_Microsoft.Network/publicIPAddresses/*/join/action_ アクセス許可もロールに含めてください。

- **Virtual network**。

    - 仮想ネットワークがない場合は、[Azure portal を使用して仮想ネットワークを作成します](../virtual-network/quick-create-portal.md)。

    - 仮想ネットワークのリソース グループが特定の Azure ネットワーク リソースを作成および削除できることを確認します。
    
        Azure SSIS IR では、仮想ネットワークと同じリソース グループ下に特定のネットワーク リソースを作成する必要があります。 これらのリソースには次が含まれます。
        - *\<Guid>-azurebatch-cloudserviceloadbalancer* という名前の Azure ロード バランサー
        - *\<Guid>-azurebatch-cloudservicenetworksecuritygroup という名前のネットワーク セキュリティ グループ
        - -azurebatch-cloudservicepublicip という名前の Azure パブリック IP アドレス
    
        これらのリソースは、Azure-SSIS IR の開始時に作成されます。 これらは、Azure-SSIS IR の停止時に削除されます。 Azure-SSIS IR の停止がブロックされないように、他のリソースでこれらのネットワーク リソースを再利用しないでください。

    - 仮想ネットワークが属するリソース グループまたはサブスクリプション上で[リソースのロック](../azure-resource-manager/management/lock-resources.md)がないことを確認します。 読み取り専用または削除ロックを構成した場合、Azure-SSIS IR の開始と停止が失敗するか、応答しなくなります。

    - 仮想ネットワークが属するリソース グループまたはサブスクリプション下に次のリソースが作成されるのを妨げる Azure Policy の割り当てがないことを確認します。
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- 次の **ネットワーク構成** シナリオについては、このチュートリアルでは扱いません。
    - Azure-SSIS IR に独自のパブリック IP アドレスを使用する。
    - 独自のドメイン ネーム システム (DNS) サーバーを使用する。
    - サブネット上でネットワーク セキュリティ グループ (NSG) を使用する。
    - Azure ExpressRoute またはユーザー定義ルート (UDR) を使用する。
    - カスタマイズされた Azure-SSIS IR を使用する。
    
    詳細については、[仮想ネットワークの構成](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)に関するページをご覧ください。

## <a name="configure-a-virtual-network"></a>仮想ネットワークを構成する

Azure-SSIS IR を参加させる前に、Azure portal を使用して仮想ネットワークを構成します。

1. Microsoft Edge または Google Chrome を起動します。 現時点では、Data Factory UI をサポートしているのはこれらの Web ブラウザーのみです。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[そのほかのサービス]** を選択します。 フィルターを適用して **[仮想ネットワーク]** を選択します。

1. 一覧で目的の仮想ネットワークを選びます。

1. **[仮想ネットワーク]** ページで、 **[プロパティ]** を選びます。

1. **[リソース ID]** のコピー ボタンを選び、仮想ネットワークのリソース ID をクリップボードにコピーします。 クリップボードから OneNote またはファイルに ID を保存します。

1. 左側のメニューの **[サブネット]** を選択します。

    - Azure-SSIS IR が使用するための利用可能なアドレス領域が選択したサブネットに十分にあることを確認してください。 IR ノード番号の 2 倍以上の使用可能な IP アドレスを残しておきます。 Azure は、各サブネット内で一部の IP アドレスを予約します。 これらのアドレスは使用できません。 サブネットの最初と最後の IP アドレスはプロトコル準拠用に予約され、3 つ以上のアドレスが Azure サービスに使用されます。 詳細については、「 [これらのサブネット内の IP アドレスの使用に関する制限はありますか](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Azure-SSIS IR をデプロイするには、GatewaySubnet を選択しないでください。 これは仮想ネットワーク ゲートウェイ専用です。
    - (SQL Database Managed Instance、App Service など) 他の Azure サービスによって排他的に専有されているサブネットを使用しないでください。

1. 仮想ネットワークが含まれる Azure サブスクリプションに Azure Batch プロバイダーが登録されていることを確認します。 または、Azure Batch プロバイダーを登録します。 Azure Batch アカウントがサブスクリプションに既にある場合は、サブスクリプションは Azure Batch に登録されています。 (Data Factory ポータルで Azure-SSIS IR を作成した場合、Azure Batch プロバイダーが自動的に登録されます。)

   1. Azure portal の左側のメニューで、 **[サブスクリプション]** を選択します。

   1. サブスクリプションを選択します。

   1. 左側の **[リソース プロバイダー]** を選択し、**Microsoft.Batch** が登録済みのプロバイダーであることを確認します。

   !["登録済み" 状態の確認](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   **Microsoft.Batch** が一覧に表示されない場合は、サブスクリプションに [空の Azure Batch アカウントを作成](../batch/batch-account-create-portal.md)します。 これは後で削除することができます。

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Azure-SSIS IR を仮想ネットワークに参加させる

Azure Resource Manager 仮想ネットワークまたは従来の仮想ネットワークを構成した後は、Azure-SSIS IR を仮想ネットワークに参加させることができます。

1. Microsoft Edge または Google Chrome を起動します。 現時点では、Data Factory UI をサポートしているのはこれらの Web ブラウザーのみです。

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[データ ファクトリ]** を選択します。 メニューに **[データ ファクトリ]** が表示されない場合は、 **[その他のサービス]** を選択し、 **[インテリジェンス + 分析]** セクションで **[データ ファクトリ]** を選択します。

   ![データ ファクトリの一覧](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 一覧で Azure-SSIS IR を使用するデータ ファクトリを選択します。 データ ファクトリのホーム ページが表示されます。 **[作成と監視]** タイルを選択します。 Data Factory の UI が別のタブに表示されます。

   ![データ ファクトリのホーム ページ](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. データ ファクトリの UI で、 **[編集]** タブに切り替え、 **[接続]** を選択し、 **[統合ランタイム]** タブに切り替えます。

   ![[統合ランタイム] タブ](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Azure-SSIS IR が実行されている場合、 **[統合ランタイム]** 一覧の **[アクション]** 列で Azure-SSIS IR の **[停止]** ボタンを選択します。 Azure-SSIS IR は、停止するまで編集できません。

   ![IR を停止する](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. **[統合ランタイム]** 一覧の **[アクション]** 列で、Azure-SSIS IR の **[編集]** ボタンを選択します。

   ![統合ランタイムを編集する](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. 統合ランタイムの設定パネルで、 **[全般設定]** と **[SQL 設定]** セクションの **[次へ]** ボタンを選択して、先に進みます。

1. **[詳細設定]** セクションで、次の手順を実行します。
   1. **[Select a VNet for your Azure-SSIS Integration Runtime to join, allow ADF to create certain network resources, and optionally bring your own static public IP addresses]\(参加させる Azure-SSIS 統合ランタイムの VNet を選択し、ADF で特定のネットワーク リソースを作成できるようにし、必要に応じて独自の静的パブリック IP アドレスを使用する\)** チェック ボックスをオンにします。

   1. **[サブスクリプション]** で、仮想ネットワークを含む Azure サブスクリプションを選択します。

   1. **[場所]** では、統合ランタイムと同じ場所が選択されています。

   1. **[種類]** で、仮想ネットワークの種類の (クラシックまたは Azure Resource Manager) を選択します。 クラシック仮想ネットワークは間もなく非推奨になるので、Azure Resource Manager 仮想ネットワークを選択することをお勧めします。

   1. **[VNet 名]** で、仮想ネットワークの名前を選択します。 これは、SSISDB をホストするために仮想ネットワーク サービス エンドポイントを備えた SQL Database またはプライベート エンドポイントを備えた SQL Managed Instance に使用されているものと同じである必要があります。 または、オンプレミス ネットワークに接続されているものと同じである必要があります。 それ以外の場合は、Azure-SSIS IR に独自の静的パブリック IP アドレスを使用するための任意の仮想ネットワークを指定できます。

   1. **[サブネット名]** で、お使いの仮想ネットワークのサブネットの名前を選択します。 これは、SSISDB をホストするために仮想ネットワーク サービス エンドポイントを備えた SQL Database に使用されているものと同じである必要があります。 または、SSISDB をホストするためにプライベート エンドポイントを備えた SQL Managed Instance に使用されているものとは異なるサブネットである必要があります。 それ以外の場合は、Azure-SSIS IR に独自の静的パブリック IP アドレスを使用するための任意のサブネットを指定できます。

   1. **[VNet Validation]\(VNet の検証\)** を選択します。 検証が成功した場合は、 **[続行]** を選択します。

   ![仮想ネットワークの詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. **[概要]** セクションで、Azure-SSIS IR のすべての設定を確認します。 次に、 **[更新]** を選択します。

1. Azure-SSIS IR の **[アクション]** 列の **[開始]** ボタンを選択して、Azure-SSIS IR を開始します。 仮想ネットワークに参加する Azure-SSIS IR が開始されるまでに約 20 から 30 分かかります。

## <a name="next-steps"></a>次の手順

[Azure-SSIS IR を仮想ネットワークに参加させる](join-azure-ssis-integration-runtime-virtual-network.md)方法について学習します。
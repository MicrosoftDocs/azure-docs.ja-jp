---
title: Azure Migrate の依存関係の視覚化 | Microsoft Docs
description: Azure Migrate の Server Assessment Service における評価の計算の概要を説明します
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: hamusa
ms.openlocfilehash: 4b6a140ec428ce3b053c41074f02f65f19b8dc72
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102888"
---
# <a name="dependency-visualization"></a>依存関係の視覚化

Azure Migrate: Server Assessment では、Azure への移行についてオンプレミス マシンのグループが評価されます。 Server Assessment の依存関係可視化機能を使用して、グループを作成することができます。 この記事では、次の機能に関する情報を提供します。

> [!NOTE]
> 依存関係可視化機能は、Azure Government では使用できません。

## <a name="overview"></a>概要

Server Assessment の依存関係可視化機能を使用すると、移行評価用の信頼性の高いグループを作成できます。 依存関係の可視化を使用すると、マシンのネットワーク依存関係を表示したり、Azure に一緒に移行するために必要な関連するマシンを特定したりすることができます。 この機能は、アプリケーションが導入されているマシンのノーハウをまったく持っておらず、そのアプリケーションを一緒にAzureに移行する必要がある状況で役立ちます。

## <a name="before-you-start"></a>開始する前に

- Azure Migrate プロジェクトを[作成](how-to-add-tool-first-time.md)していることを確認します。
- プロジェクトを既に作成してある場合は、次のツールを[追加済み](how-to-assess.md)であることを確認します。Azure Migrate: Server Assessment ツールを追加済みであることを確認してください。
- マシンが Azure Migrate で検出されていることを確認します。これは、[VMware](how-to-set-up-appliance-vmware.md) または [Hyper-V](how-to-set-up-appliance-hyper-v.md) 用に Azure Migrate アプライアンスを設定することで実行できます。 アプライアンスでオンプレミスのマシンが検出されて、メタデータとパフォーマンス データが Azure Migrate: Server Assessment にメタデータとパフォーマンス データを送信します。 [詳細情報](migrate-appliance.md)。

## <a name="how-does-it-work"></a>それはどのように機能しますか?

Azure Migrate は、依存関係の視覚化のために [Azure Monitor ログ](../log-analytics/log-analytics-overview.md)の [Service Map](../operations-management-suite/operations-management-suite-service-map.md) ソリューションを使用します。
- 依存関係の視覚化を利用するために、新規または既存の Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付ける必要があります。
- Azure Migrate プロジェクトが作成されている同じサブスクリプションのみに、ワークスペースを作成するか、アタッチすることができます。
- Log Analytics ワークスペースをプロジェクトにアタッチするには:
    1. **[サーバー]** タブの **[Azure Migrate:Server Assessment]** タイルで、 **[概要]** をクリックします。
    2. **[概要]** で下矢印をクリックして、 **[Essentials]** を展開します。
    3. **[OMS ワークスペース]** で、 **[構成が必要]** をクリックします。
    4. **[ワークスペースの構成]** で、新しいワークスペースを作成するか、または既存のものを使用するかを指定します。
    
    ![ワークスペースの追加](./media/how-to-create-group-machine-dependencies/workspace.png)

- ワークスペースを関連付けるときに、新しいワークスペースを作成するか、既存のワークスペースをアタッチするかを選択できます。
  - 新しいワークスペースを作成する場合は、ワークスペースの名前を指定する必要があります。 移行プロジェクトと同様の [Azure 地理的環境](https://azure.microsoft.com/global-infrastructure/geographies/)のリージョンでワークスペースが作成されます。
  - 既存のワークスペースをアタッチするときは、移行プロジェクトと同じサブスクリプション内の使用可能なすべてのワークスペースから選択できます。 表示されるのは、[Service Map がサポートされている](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions)リージョンで作成されたワークスペースのみであることに注意してください。 ワークスペースをアタッチできるようにするには、ワークスペースへの "読み取り" アクセスがあることを確認してください。

  > [!NOTE]
  > いったんワークスペースをプロジェクトにアタッチすると、後で変更できません。

- Azure portal で検索に使用できるキー**移行プロジェクト**と値**プロジェクト名**に関連付けられているワークスペースがタグ付けされます。
- プロジェクトに関連付けられているワークスペースに移動するためには、プロジェクト**概要**ページの **Essentials** セクションに遷移し、ワークスペースをアクセスできます。

    ![Log Analytics ワークスペースを操作する](./media/concepts-dependency-visualization/oms-workspace.png)

依存関係の視覚化を使用するには、分析するオンプレミスの各マシンにエージェントをダウンロードしてインストールする必要があります。  

- 各マシンに [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) をインストールする必要があります。 MMA エージェントのインストール方法の詳細については、[こちら](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma)をご覧ください。
- 各マシンに [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent) をインストールする必要があります。 依存関係エージェントのインストール方法の詳細については、[こちら](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent)をご覧ください。
- また、インターネットに接続されていないマシンの場合、それらに Log Analytics ゲートウェイをダウンロードしてインストールする必要があります。

依存関係の視覚化を使用している場合を除き、評価するマシンにこれらのエージェントは不要です。

## <a name="do-i-need-to-pay-for-it"></a>使用料金が必要になる場合

依存関係可視化機能は、追加料金なしで利用できます。 Server Assessment で依存関係可視化機能を使用するには、Service Map が必要であり、新規または既存の Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付ける必要があります。 Server Assessment の依存関係可視化機能は、最初の 180 日間は無料です。

1. この Log Analytics ワークスペース内で Service Map 以外のソリューションを使用すると、[標準の Log Analytics ](https://azure.microsoft.com/pricing/details/log-analytics/)料金が適用されます。
2. 追加コストなしの移行シナリオをサポートするため、Service Map ソリューションでは、Azure Migrate プロジェクトを Log Analyticsワークスペースに関連付けした後の最初の 180 日間は料金が発生しません。 180 日が経過すると、Log Analytics の標準の料金が適用されます。

ワークスペースにエージェントを登録する際には、エージェントのインストール手順のページで、プロジェクトによって指定された ID とキーを使用します。

Azure Migrate プロジェクトが削除される際、それに伴ってワークスペースが削除されることはありません。 プロジェクトが削除された後は、Service Map を無料で使用することはできず、Log Analytics ワークスペースの有料階層に応じて各ノードが課金されます。

> [!NOTE]
> 依存関係の視覚化機能では、サービス マップは Log Analytics ワークスペース経由で使用されます。 Azure Migrate の一般提供が発表された 2018 年 2 月 28 日以降は、この機能を追加料金なしで使用できるようになりました。 無料のワークスペースを使用するには、新しいプロジェクトを作成する必要があります。 一般提供が開始される前の既存のワークスペースは課金対象のままとなるため、新しいプロジェクトに移動することをお勧めします。

Azure Migrate の価格については、[こちら](https://azure.microsoft.com/pricing/details/azure-migrate/)を参照してください。

## <a name="how-do-i-manage-the-workspace"></a>ワークスペースの管理方法

Azure Migrate 以外で Log Analytics ワークスペースを使用できます。 作成した Azure Migrate プロジェクトを削除しても、ワークスペースは削除されません。 ワークスペースが不要になった場合は手動で[削除](../azure-monitor/platform/manage-access.md)します。

Azure Migrate プロジェクトを削除しない場合は、Azure Migrate で作成されたワークスペースは削除しないでください。 削除した場合は、依存関係可視化機能は、期待どおりに機能しません。

## <a name="next-steps"></a>次の手順
- [マシンの依存関係マッピングを使用したマシンのグループ化](how-to-create-group-machine-dependencies.md)
- [依存関係の可視化の詳細については](https://docs.microsoft.com/azure/migrate/resources-faq#what-is-dependency-visualization)、よく寄せられる質問を確認します。

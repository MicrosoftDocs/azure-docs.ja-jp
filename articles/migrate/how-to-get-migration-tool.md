---
title: Azure Migrate による評価後、マシンを移行する | Microsoft Docs
description: Azure Migrate サービスを使用した評価の実行後、マシンを移行するための推奨事項を取得する方法について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 09/17/2018
ms.author: raynew
ms.openlocfilehash: 068b55e40afc96dbcfae26c8bf7da8a1b9ea349a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53249779"
---
# <a name="migrate-machines-after-assessment"></a>評価後のマシンの移行


[Azure Migrate](migrate-overview.md) は、オンプレミスのマシンを評価して Azure への移行に適しているかどうかを確認し、Azure でマシンを実行する際のサイズとコストの見積りを示します。 現時点では、Azure Migrate では、マシンの移行目的でのみ評価します。 移行自体は、現在、他の Azure サービスを使用して実行されています。

この記事では、移行の評価を実行した後、移行ツールに関する推奨事項を取得する方法について説明します。

> [!NOTE]
> Azure Government の場合、移行ツールに関する推奨事項がありません。

## <a name="migration-tool-suggestion"></a>移行ツールに関する推奨事項

移行ツールに関する推奨事項を取得するには、オンプレミス環境の詳細検出を行う必要があります。 詳細検出を行うには、オンプレミスのマシンにエージェントをインストールします。  

1. Azure Migrate プロジェクトを作成し、オンプレミスのコンピューターを検出し、移行の評価を作成します。 [詳細情報](tutorial-assessment-vmware.md)。
2. 推奨される移行方法を取得する各オンプレミスのマシンに、Azure Migrate エージェントをダウンロードし、インストールします。 [次の手順に従って](how-to-create-group-machine-dependencies.md#prepare-for-dependency-visualization)エージェントをインストールします。
2. リフトアンドシフト移行に適している、オンプレミスのマシンを識別します。 これらは、実行中のアプリへの変更を必要とせず、そのまま移行できる VM です。
3. リフトアンドシフト移行する場合は、Azure Site Recovery を使用することをお勧めします。 [詳細情報](../site-recovery/tutorial-migrate-on-premises-to-azure.md)。 または、Azure への移行をサポートするサード パーティ製ツールを使用することもできます。
4. リフトアンドシフト移行に適していないオンプレミスのマシンがある場合、つまり VM 全体ではなく、特定のアプリを移行する場合は、他の移行ツールを使用できます。 たとえば、SQL Server、MySQL、Oracle データベースなどのオンプレミスのデータベースを Azure に移行する場合は、[Azure Database Migration サービス](https://azure.microsoft.com/campaigns/database-migration/)をお勧めします。


## <a name="review-suggested-migration-methods"></a>推奨される移行方法のレビュー

1. 推奨される移行方法を取得する前に、Azure Migrate プロジェクトを作成し、オンプレミスのマシンを検出し、移行評価を実行する必要があります。 [詳細情報](tutorial-assessment-vmware.md)。
2. 評価が作成されたら、[プロジェクト] > **[概要]**  > **[ダッシュボード]** を選択して表示します。 **[Assessment Readiness]\(アセスメントの準備状態\)** をクリックします。

    ![アセスメントの準備状態](./media/tutorial-assessment-vmware/assessment-report.png)  

3. **[Suggested Tool]\(推奨ツール\)** で、移行に使用できるツールの推奨事項を確認します。

    ![推奨ツール](./media/tutorial-assessment-vmware/assessment-suitability.png)




## <a name="next-steps"></a>次の手順

評価の計算方法の[詳細を確認](concepts-assessment-calculation.md)します。

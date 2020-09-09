---
title: Azure Migrate Server Assessment を使用して AVS 評価を作成する |Microsoft Docs
description: Azure Migrate Server Assessment ツールを使用して AVS 評価を作成する方法について説明します
author: rashi-ms
ms.service: azure-migrate
ms.topic: article
ms.date: 06/26/2020
ms.author: rajosh
ms.openlocfilehash: b2917c10e13f110d7ac9784da16a10fc61eb9298
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288857"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Azure VMware Solution (AVS) の評価を作成する

この記事では、Azure Migrate: Server Assessment を使用して、オンプレミスの VMware VM の Azure VMware Solution (AVS) 評価を作成する方法について説明します。Server Assessment を使用して作成する方法について説明します。

[Azure Migrate](migrate-services-overview.md) は、Azure への移行に役立ちます。 Azure Migrate には、オンプレミス インフラストラクチャ、アプリケーション、およびデータの検出、評価、および Azure への移行を追跡するための一元的なハブが用意されています。 このハブには、評価および移行のための Azure ツールのほか、サードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが用意されています。

## <a name="before-you-start"></a>開始する前に

- Azure Migrate プロジェクトを[作成](how-to-add-tool-first-time.md)していることを確認します。
- プロジェクトを既に作成してある場合は、次のツールを[追加済み](how-to-assess.md)であることを確認します。Azure Migrate: Server Assessment ツールを追加済みであることを確認してください。
- 評価を作成するには、[VMware](how-to-set-up-appliance-vmware.md) 用の Azure Migrate アプライアンスを設定する必要があります。これは、オンプレミスのマシンを検出して Azure Migrate: Server Assessment にメタデータとパフォーマンスデータを送信します。Server Assessment を使用して作成する方法について説明します。 [詳細については、こちらを参照してください](migrate-appliance.md)。
- コンマ区切り値 (CSV) 形式で[サーバーのメタデータをインポート](tutorial-assess-import.md)することもできます。


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Azure VMware Solution (AVS) の評価の概要

Azure Migrate:Server Assessment を使用して作成する方法について説明します。

**評価の種類** | **詳細**
--- | --- 
**Azure VM** | オンプレミスのサーバーを Azure 仮想マシンに移行するための評価。 <br/><br/> このタイプの評価を使用すると、Azure への移行について、オンプレミスの [VMware VM](how-to-set-up-appliance-vmware.md)、[Hyper-V VM](how-to-set-up-appliance-hyper-v.md)、[物理サーバー](how-to-set-up-appliance-physical.md)を評価できます。[詳細情報](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | オンプレミスのサーバーを [Azure VMware Solution (AVS)](../azure-vmware/introduction.md) に移行するための評価。 <br/><br/> この種類の評価を使用すると、Azure VMware Solution (AVS) への移行について、オンプレミスの [VMware VM](how-to-set-up-appliance-vmware.md) を評価できます。[詳細情報](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Azure VMware Solution (AVS) の評価は現在プレビュー段階であり、VMware VM についてのみ作成できます。


Azure VMware Solution (AVS) の評価作成に使用できるサイズ変更の設定基準には、次の 2 種類があります。

**評価** | **詳細** | **データ**
--- | --- | ---
**パフォーマンスベース** | 収集された、オンプレミスの VM のパフォーマンス データに基づく評価。 | **推奨ノード サイズ**: CPU およびメモリ使用率のデータと、ノードの種類、ストレージの種類、および評価用に選択した FTT の設定に基づいています。
**オンプレミス** | オンプレミスのサイズ設定に基づく評価。 | **推奨ノード サイズ**: オンプレミスの VM サイズ、ノードの種類、ストレージの種類、および評価用に選択した FTT の設定に基づいています。


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Azure VMware Solution (AVS) の評価を実行する

次の手順に従って、Azure VMware Solution (AVS) の評価を実行します。

1. 評価を作成するための[ベスト プラクティス](best-practices-assessment.md)を確認します。

2. **[サーバー]** タブの **[Azure Migrate:Server Assessment]** タイルで、 **[評価]** をクリックします。

    ![アクセス](./media/how-to-create-assessment/assess.png)

3. **[サーバーの評価]** で、評価の種類に "Azure VMware Solution (AVS)" を選択し、検出ソースを選択して評価名を指定します。

    ![評価の基本](./media/how-to-create-avs-assessment/assess-servers-avs.png)

4. **[すべて表示]** をクリックして、評価のプロパティを確認します。

    ![AVS 評価のプロパティ](./media/how-to-create-avs-assessment/avs-view-all.png)

5. **[次へ]** をクリックして**評価するマシンを選択**します。 **[グループを選択または作成します]** で **[新規作成]** を選択し、グループ名を指定します。 グループで、評価のために 1 つ以上の VM をまとめます。

6. **[グループにマシンを追加します]** で、グループに追加する VM を選択します。

7. **[次へ]** をクリックして **[評価の確認と作成]** を選択し、評価の詳細を確認します。

8. **[評価を作成します]** をクリックして、グループを作成し、評価を実行します。

    ![AVS の評価を作成する](./media/how-to-create-avs-assessment/avs-assessment-create.png)

9. 評価が作成されたら、それを表示します ( **[サーバー]**  >  **[Azure Migrate: Server Assessment]**  >  **[評価]** )。

10. **[評価のエクスポート]** をクリックし、Excel ファイルとしてダウンロードします。


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Azure VMware Solution (AVS) の評価を確認する

Azure VMware Solution (AVS) の評価の内容は次のとおりです。

- **Azure VMware Solution (AVS) の対応性**: オンプレミスの VM が Azure VMware Solution (AVS) への移行に適しているかどうか。
- **AVS ノード数**: VM の実行に必要な AVS ノードの予測数。
- **すべての AVS ノードの使用率**: すべてのノードにおける CPU、メモリ、および記憶域の使用率の予測。
- **月間コスト見積もり**: オンプレミスの VM を実行しているすべての Azure VMware Solution (AVS) ノードの月間推定コスト。


### <a name="view-an-assessment"></a>評価を表示する

1. **[移行の目標]**  >   **[サーバー]** で、 **[評価]** を **[Azure Migrate: Server Assessment]** でクリックします。

2. **[評価]** で、評価をクリックして開きます。

    ![AVS 評価の概要](./media/how-to-create-avs-assessment/avs-assessment-summary.png)

### <a name="review-azure-vmware-solution-avs-readiness"></a>Azure VMware Solution (AVS) の対応性の確認

1. **[Azure 対応性]** で、VM が AVS への移行の準備ができているかどうかを確認します。

2. VM の状態を確認します。
    - **[Ready for AVS]\(AVS に対応\)** : マシンをそのまま変更せずに Azure (AVS) に移行できます。 AVS で起動し、AVS のフル サポートを受けます。
    - **[条件付きで対応]** : VM には、現在の vSphere バージョンとの互換性に関する問題がある可能性があります。また、VM の全機能を AVS で使用するには、VMware ツールやその他の設定が必要になることもあります。
    - **[Not ready for AVS]\(AVS に未対応\)** : VM は、AVS では起動しません。 たとえば、オンプレミスの VMware VM に、CD-ROM などの外部デバイスが接続されている場合、VMotion 操作は失敗します (VMware VMotion を使用している場合)。
    - **[Readiness 不明]** : オンプレミス環境から収集したメタデータが不十分なために、Azure Migrate がマシンの対応性を判断できませんでした。

3. 推奨されるツールを確認します。
    - **VMware HCX または Enterprise**: VMware マシンの場合、オンプレミスのワークロードを Azure VMware Solution (AVS) プライベート クラウドに移行するために推奨される移行ツールは、VMware Hybrid Cloud Extension (HCX) ソリューションです。 [詳細については、こちらを参照してください](../azure-vmware/hybrid-cloud-extension-installation.md)。
    - **不明**:CSV ファイルを介してインポートされたマシンの場合、既定の移行ツールは不明です。 VMware マシンの場合は、VMware Hybrid Cloud Extension (HCX) ソリューションを使用することをお勧めします。 

4. **[AVS 対応性]** の状態をクリックします。 VM 対応性の詳細を表示し、ドリルダウンして、コンピューティング、ストレージ、ネットワークの設定など、VM の詳細を表示できます。



### <a name="review-cost-details"></a>コスト詳細の確認

このビューには、Azure VMware Solution (AVS) で VM を実行する際の推定コストが表示されます。

1. 月間合計コストを確認します。 コストは、評価されるグループ内のすべての VM について集計されます。 

    - コスト見積もりは、すべての VM のリソース要件を考慮した場合に必要な AVS ノードの数に基づいて算出されます。
    - Azure VMware Solution (AVS) の料金はノードごとであるため、総コストではコンピューティング コストとストレージ コストは配分されません。
    - コスト見積もりは、AVS におけるオンプレミス VM の実行に対するものです。 Azure Migrate Server Assessment では、PaaS または SaaS のコストは考慮されません。
    
2. 月間ストレージ コストの見積もりを確認できます。 このビューには、評価されたグループの集計されたストレージ コストが、ストレージ ディスクの種類ごとに分けて表示されます。

3. ドリルダウンして、特定の VM の詳細を見ることができます。


### <a name="review-confidence-rating"></a>信頼度レーティングを確認する

パフォーマンスベースの評価を実行すると、評価に信頼度レーティングが割り当てられます。

![信頼度レーティング](./media/how-to-create-assessment/confidence-rating.png)

- 1 つ星 (最低) から 5 つ星 (最高) までの評価が与えられます。
- 信頼度レーティングは、評価による推奨サイズの信頼性を判断する目安となります。
- 信頼度レーティングは、評価の計算に必要なデータ ポイントの可用性に基づいています。
- パフォーマンスに基づいてサイズ変更する場合は、サーバー評価の AVS 評価に CPU と VM メモリの使用率データが必要です。 次のパフォーマンス データは収集されますが、AVS 評価のサイズ設定のレコメンデーションでは使用されません。
  - VM に接続されているディスクごとの、ディスク IOPS とスループット データ。
  - VM に接続したネットワーク アダプターごとにパフォーマンス ベースのサイズ設定を処理するためのネットワーク I/O。

評価の信頼度レーティングは、次のとおりです。

**データ ポイントの可用性** | **信頼度レーティング**
--- | ---
0% - 20% | 1 つ星
21% - 40% | 2 つ星
41% - 60% | 3 つ星
61% - 80% | 4 つ星
81% - 100% | 5 つ星

[パフォーマンス データの詳細](concepts-azure-vmware-solution-assessment-calculation.md) 


## <a name="next-steps"></a>次のステップ

- [依存関係マッピング](how-to-create-group-machine-dependencies.md)を使用して、信頼度の高いグループを作成する方法を確認します。
- AVS 評価の計算方法の[詳細を確認](concepts-azure-vmware-solution-assessment-calculation.md)します。

---
title: Azure Migrate Server Assessment を使用して AVS 評価を作成する |Microsoft Docs
description: Azure Migrate Server Assessment ツールを使用して AVS 評価を作成する方法について説明します
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: e386db1ee2042d75a31d4a9de2a5174e904c6b5c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732974"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Azure VMware Solution (AVS) の評価を作成する

この記事では、Azure Migrate: Server Assessment を使用して、オンプレミスの VMware VM の Azure VMware Solution (AVS) 評価を作成する方法について説明します。Server Assessment を使用して作成する方法について説明します。

[Azure Migrate](migrate-services-overview.md) は、Azure への移行に役立ちます。 Azure Migrate には、オンプレミス インフラストラクチャ、アプリケーション、およびデータの検出、評価、および Azure への移行を追跡するための一元的なハブが用意されています。 このハブには、評価および移行のための Azure ツールのほか、サードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが用意されています。

## <a name="before-you-start"></a>開始する前に

- Azure Migrate プロジェクトを[作成](./create-manage-projects.md)していることを確認します。
- プロジェクトを既に作成してある場合は、次のツールを[追加済み](how-to-assess.md)であることを確認します。Azure Migrate: Server Assessment ツールを追加済みであることを確認してください。
- 評価を作成するには、[VMware](how-to-set-up-appliance-vmware.md) 用の Azure Migrate アプライアンスを設定する必要があります。これは、オンプレミスのマシンを検出して Azure Migrate: Server Assessment にメタデータとパフォーマンスデータを送信します。Server Assessment を使用して作成する方法について説明します。 [詳細については、こちらを参照してください](migrate-appliance.md)。
- コンマ区切り値 (CSV) 形式で[サーバーのメタデータをインポート](./tutorial-discover-import.md)することもできます。


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Azure VMware Solution (AVS) の評価の概要

Azure Migrate:Server Assessment を使用して作成する方法について説明します。

**評価の種類** | **詳細**
--- | --- 
**Azure VM** | オンプレミスのサーバーを Azure 仮想マシンに移行するための評価。 <br/><br/> この評価の種類を使用すると、Azure への移行について、オンプレミスの [VMware VM](how-to-set-up-appliance-vmware.md)、[Hyper-V VM](how-to-set-up-appliance-hyper-v.md)、[物理サーバー](how-to-set-up-appliance-physical.md)を評価できます。[詳細情報](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | オンプレミスのサーバーを [Azure VMware Solution (AVS)](../azure-vmware/introduction.md) に移行するための評価。 <br/><br/> この評価の種類を使用すると、Azure VMware Solution (AVS) への移行について、オンプレミスの [VMware VM](how-to-set-up-appliance-vmware.md) を評価できます。[詳細情報](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Azure VMware Solution (AVS) 評価は、VMware VM に対してのみ作成できます。


Azure VMware Solution (AVS) の評価作成に使用できるサイズ変更の設定基準には、次の 2 種類があります。

**評価** | **詳細** | **データ**
--- | --- | ---
**パフォーマンスベース** | 収集された、オンプレミスの VM のパフォーマンス データに基づく評価。 | **推奨ノード サイズ**:CPU およびメモリ使用率のデータと、ノードの種類、ストレージの種類、および評価用に選択した FTT の設定に基づいています。
**オンプレミス** | オンプレミスのサイズ設定に基づく評価。 | **推奨ノード サイズ**: オンプレミスの VM サイズ、ノードの種類、ストレージの種類、および評価用に選択した FTT の設定に基づいています。


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Azure VMware Solution (AVS) の評価を実行する

1. **[サーバー]** ページの **[Windows と Linux のサーバー]** で、 **[サーバーの評価と移行]** をクリックします。

   ![[サーバーの評価と移行] ボタンの場所](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. **Azure Migrate:Server Assessment** で、**評価** をクリックします。

1. **[サーバーの評価]**  >  **[評価の種類]** で、 **[Azure VMware Solution (AVS)]** を選択します。

1. **[検出ソース]** で次の操作を行います。

    - アプライアンスを使用してマシンを検出した場合、 **[Azure Migrate アプライアンスから検出されたマシン]** を選択します。
    - インポートした CSV ファイルを使用してマシンを検出した場合、 **[インポートされたマシン]** を選択します。 
    
1. **[編集]** をクリックして、評価のプロパティを確認します。

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="評価の設定を選択するためのページ":::
 

1. **[評価のプロパティ]**  >  **[ターゲット プロパティ]** で:

    - 移行先となる Azure リージョンを **[ターゲットの場所]** で指定します。
       - サイズとコストは、指定した場所に基づいて推奨されます。
   - **[ストレージの種類]** は、既定で **[vSAN]** に設定されています。 AVS プライベート クラウドでは、これが既定のストレージの種類となります。
   - AVS ノードでは現在、**予約インスタンス** はサポートされません。
1. **[VM サイズ]** では:
    - **[ノードの種類]** は、既定で **[AV36]** に設定されています。 VM を AVS に移行する必要のあるノードが Azure Migrate によって推奨されます。
    - **[FTT 設定、RAID レベル]** で、許容するエラーと RAID の組み合わせを選択します。  選択した FTT オプションとオンプレミスの VM ディスク要件を組み合わせて、AVS で必要とされる vSAN ストレージの合計が決定されます。
    - **[CPU Oversubscription]\(CPU オーバーサブスクリプション\)** で、AVS ノードの 1 つの物理コアに関連付けられる仮想コアの比率を指定します。 オーバーサブスクリプションが 4 対 1 を上回る場合、パフォーマンスが低下する可能性がありますが、Web サーバー タイプのワークロードには使用できます。
    - **[Memory overcommit factor]\(メモリのオーバーコミット率\)** に、クラスター上のメモリのオーバーコミットの比率を指定します。 値 1 は 100% のメモリ使用量、0.5 は 50%、2 は使用可能なメモリの 200% を使用していることを表します。 0\.5 から 10 までの、最大で小数点以下 1 桁の値のみを追加できます。
    - **[Dedupe and compression factor]\(重複排除と圧縮率\)** で、ワークロードで予想される重複排除と圧縮率を指定します。 実際の値は、オンプレミスの vSAN またはストレージ構成から取得できます。これは、ワークロードによって異なる場合があります。 値 3 は 3 倍を意味し、300 GB のディスクでは 100 GB のストレージのみが使用されます。 値 1 は、重複排除も圧縮もないことを意味します。 1 から 10 までの、最大で小数点以下 1 桁の値のみを追加できます。
1. **[ノード サイズ]** で、次の操作を行います。 
    - **[サイズ変更の設定基準]** で、静的メタデータを評価の基準にするか、パフォーマンスベースのデータを評価の基準にするかを選択します。 パフォーマンス データを使用する場合:
        - 評価の基準とするデータ期間を **[パフォーマンス履歴]** で指定します。
        - パフォーマンス サンプルに使用するパーセンタイル値を **[百分位の使用率]** で指定します。 
    - 評価中に使用したいバッファーを **[快適性係数]** で指定します。 ここでは、季節ごとの使用量、短期間のパフォーマンス履歴、将来に使用量が増える可能性などの問題が考慮されます。 たとえば、快適性係数を 2 とした場合、
    
        **コンポーネント** | **有効使用率** | **快適性係数の追加 (2.0)**
        --- | --- | ---
        コア | 2  | 4
        メモリ | 8 GB | 16 GB  

1. **[価格]** では:
    - **[プラン]** には、登録した [Azure プラン](https://azure.microsoft.com/support/legal/offer-details/)が表示されます。そのプランのコストが Server Assessment によって見積もられます。
    - 自分のアカウントの請求通貨を **[通貨]** で選択します。
    - Azure プランとは別に適用されるサブスクリプション固有の割引を **[割引 (%)]** に追加します。 既定の設定は 0% です。

1. 変更内容を確定する場合は **[保存]** をクリックします。

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="評価のプロパティ":::

1. **[サーバーの評価]** で **[次へ]** をクリックします。

1. **[評価するマシンの選択]**  >  **[評価名]** で、評価の名前を指定します。 
 
1. **[グループの選択または作成]** で **[新規作成]** を選択し、グループ名を指定します。 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="VM をグループに追加する":::
 
1. アプライアンスを選択し、グループに追加したい VM を選択します。 続けて、 **[次へ]** をクリックします。

1. **[評価の確認と作成]** で評価の詳細を確認したら、 **[評価の作成]** をクリックしてグループを作成し、評価を実行します。

    > [!NOTE]
    > パフォーマンスベースの評価の場合は、検出の開始後、少なくとも 1 日経ってから評価を作成することをお勧めします。 これにより、パフォーマンス データを収集する時間が確保され、信頼度が上がります。 高い信頼度レーティングを得るためには、検出の開始後、指定したパフォーマンス期間 (日、週、月) を置くのが理想です。


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Azure VMware Solution (AVS) の評価を確認する

Azure VMware Solution (AVS) の評価の内容は次のとおりです。

- **Azure VMware Solution (AVS) の対応性**: オンプレミスの VM が Azure VMware Solution (AVS) への移行に適しているかどうか。
- **AVS ノード数**: VM の実行に必要な AVS ノードの予測数。
- **すべての AVS ノードの使用率**: すべてのノードにおける CPU、メモリ、および記憶域の使用率の予測。
    - 使用率では、vCenter Server、NSX Manager (大規模)、NSX Edge など、クラスター管理オーバーヘッドが事前に考慮されます。HCX がデプロイされている場合は、HCX Manager と IX アプライアンスによる消費 (圧縮と重複除去の前の最大 44vCPU (11 CPU)、75 GB の RAM、722 GB のストレージ) も考慮されます。
- **月間コスト見積もり**: オンプレミスの VM を実行しているすべての Azure VMware Solution (AVS) ノードの月間推定コスト。


### <a name="view-an-assessment"></a>評価を表示する

1. **[移行の目標]**  >   **[サーバー]** で、 **[評価]** を **[Azure Migrate: Server Assessment]** でクリックします。

2. **[評価]** で、評価をクリックして開きます。

    :::image type="content" source="./media/how-to-create-avs-assessment/avs-assessment-summary.png" alt-text="AVS 評価の概要":::

### <a name="review-azure-vmware-solution-avs-readiness"></a>Azure VMware Solution (AVS) の対応性の確認

1. **[Azure 対応性]** で、VM が AVS への移行の準備ができているかどうかを確認します。

2. VM の状態を確認します。
    - **[Ready for AVS]\(AVS に対応\)** : マシンをそのまま変更せずに Azure (AVS) に移行できます。 AVS で起動し、AVS のフル サポートを受けます。
    - **[条件付きで対応]** : VM には、現在の vSphere バージョンとの互換性に関する問題がある可能性があります。また、VM の全機能を AVS で使用するには、VMware ツールやその他の設定が必要になることもあります。
    - **[Not ready for AVS]\(AVS に未対応\)** : VM は、AVS では起動しません。 たとえば、オンプレミスの VMware VM に、CD-ROM などの外部デバイスが接続されている場合、VMotion 操作は失敗します (VMware VMotion を使用している場合)。
    - **[Readiness 不明]** : オンプレミス環境から収集したメタデータが不十分なために、Azure Migrate がマシンの対応性を判断できませんでした。

3. 推奨されるツールを確認します。
    - **VMware HCX または Enterprise**: VMware マシンの場合、オンプレミスのワークロードを Azure VMware Solution (AVS) プライベート クラウドに移行するために推奨される移行ツールは、VMware Hybrid Cloud Extension (HCX) ソリューションです。 [詳細については、こちらを参照してください](../azure-vmware/tutorial-deploy-vmware-hcx.md)。
    - **不明**:CSV ファイルを介してインポートされたマシンの場合、既定の移行ツールは不明です。 VMware マシンの場合は、VMware Hybrid Cloud Extension (HCX) ソリューションを使用することをお勧めします。 

4. **[AVS 対応性]** の状態をクリックします。 VM 対応性の詳細を表示し、ドリルダウンして、コンピューティング、ストレージ、ネットワークの設定など、VM の詳細を表示できます。

### <a name="review-cost-details"></a>コスト詳細の確認

このビューには、Azure VMware Solution (AVS) で VM を実行する際の推定コストが表示されます。

1. 月間合計コストを確認します。 コストは、評価されるグループ内のすべての VM について集計されます。 

    - コスト見積もりは、すべての VM のリソース要件を考慮した場合に必要な AVS ノードの数に基づいて算出されます。
    - Azure VMware Solution (AVS) の料金はノードごとであるため、総コストではコンピューティング コストとストレージ コストは配分されません。
    - コスト見積もりは、AVS におけるオンプレミス VM の実行に対するものです。 AVS 評価では、PaaS や SaaS のコストを考慮しません。
    
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
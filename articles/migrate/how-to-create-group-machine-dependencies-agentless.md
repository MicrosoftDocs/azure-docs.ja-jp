---
title: Azure Migrate Server Assessment でエージェントレスの依存関係の分析を設定する
description: Azure Migrate Server Assessment でエージェントレスの依存関係の分析を設定します。
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: c3aa2aea764af8469152b007e60427724fea398a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045855"
---
# <a name="analyze-server-dependencies-agentless"></a>サーバーの依存関係を分析する (エージェントレス)

この記事では、Azure Migrate:Server Assessment を使用してエージェントレスの依存関係分析を設定する方法について説明します。 [依存関係の分析](concepts-dependency-visualization.md)は、評価や Azure への移行に関してサーバー間の依存関係を特定し、把握するのに役立ちます。

> [!IMPORTANT]
> エージェントレスの依存関係分析は現在、Azure Migrate:Server Assessment ツールで検出された VMware 環境で実行されているサーバーではプレビュー段階です。
> このプレビューはカスタマー サポートの対象であり、運用ワークロードで使用できます。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="current-limitations"></a>現在の制限

- 依存関係の分析ビューでは、現在、グループのサーバーを追加したり削除したりすることはできません。
- 現在、サーバーのグループに対する依存関係マップは使用できません。
- Azure Migrate プロジェクトでは、依存関係のデータ収集を、1,000 台のサーバーに対して同時に有効にすることができます。 1,000 台のサーバーを一括でシーケンス処理することで、さらに多くのサーバーを分析することができます。

## <a name="before-you-start"></a>開始する前に

- Azure Migrate:Server Assessment ツールが追加された [Azure Migrate プロジェクトを作成した](./create-manage-projects.md)ことを確実にします。
- 「[VMware の要件](migrate-support-matrix-vmware.md#vmware-requirements)」を確認し、依存関係の分析を実行します。
- アプライアンスを設定する前に、[アプライアンスの要件](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements)を確認します。
- サーバーで依存関係分析を有効にする前に、[依存関係分析の要件を確認](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)します。

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスをデプロイおよび構成する

1. Azure Migrate アプライアンスを展開するための要件を[確認](migrate-appliance.md#appliance---vmware)します。
2. アプライアンスが[パブリック](migrate-appliance.md#public-cloud-urls)および [Government クラウド](migrate-appliance.md#government-cloud-urls)でアクセスする必要がある Azure の URL について確認します。
3. 検出および評価中にアプライアンスによって収集される[データを確認](migrate-appliance.md#collected-data---vmware)します。
4. アプライアンスのポート アクセス要件に[注意](migrate-support-matrix-vmware.md#port-access-requirements)します。
5. [Azure Migrate アプライアンスを展開](how-to-set-up-appliance-vmware.md)して検出を開始します。 アプライアンスをデプロイするには、OVA テンプレートをダウンロードして VMware にインポートし、vCenter Server で実行されるサーバーを作成します。 アプライアンスをデプロイした後、それを Azure Migrate プロジェクトに登録し、検出を開始するように構成する必要があります。
6. アプライアンスを構成するときに、アプライアンス構成マネージャーで以下を指定する必要があります。
    - 接続先の vCenter Server の詳細。
    - VMware 環境内のサーバーを検出する場合に対象となる vCenter Server 資格情報。
    - ドメイン、Windows (ドメイン以外) または Linux (ドメイン以外) の資格情報にすることができるサーバーの資格情報。 資格情報の指定方法とそれらの処理方法の詳細については、[こちら](add-server-credentials.md)を参照してください。

## <a name="verify-permissions"></a>アクセス許可の確認

- 検出と評価のために [vCenter Server の読み取り専用アカウントを作成する](./tutorial-discover-vmware.md#prepare-vmware)必要があります。 読み取り専用アカウントには、依存関係データを収集するときにサーバーとやりとりするために、 **[Virtual Machines]**  >  **[ゲスト操作]** に対して有効になっている特権が必要です。
- サーバー評価で依存関係データを収集するためにサーバーにアクセスできるようにするには、ユーザー アカウントが必要です。 Windows および Linux サーバーに対するアカウント要件については、[こちら](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)を参照してください。

### <a name="add-credentials-and-initiate-discovery"></a>資格情報を追加して検出を開始する

1. アプライアンス構成マネージャーを開き、アプライアンスの前提条件の確認と登録を完了します。
2. **[資格情報と検出ソースの管理]** パネルに移動します。
1.  **[Step 1: Provide vCenter Server credentials]\(ステップ 1: vCenter Server の資格情報を指定する\)** で、 **[資格情報の追加]** をクリックし、vCenter Server で実行されているサーバーの検出にアプライアンスによって使用される vCenter Server アカウントの資格情報を指定します。
1. **[Step 2: Provide vCenter Server details]\(ステップ 2: VCenter Server の詳細を指定する\)** で、 **[Add discovery source]\(検出ソースの追加\)** をクリックして、ドロップダウンから資格情報のフレンドリ名を選択し、vCenter Server インスタンスの **IP アドレスまたは FQDN** を指定します :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="vCenter Server の詳細に関するアプライアンス構成マネージャーのパネル 3":::
1. **[Step 3: Provide server credentials to perform software inventory, agentless dependency analysis and discovery of SQL Server instances and databases]\(ステップ 3: サーバーの資格情報を指定して、ソフトウェア インベントリ、エージェントレスの依存関係分析、SQL Server インスタンスとデータベースの検出を実行する\)** で、 **[資格情報の追加]** をクリックして複数のサーバー資格情報を指定してソフトウェア インベントリを開始します。
1. vCenter Server の検出を開始するには、 **[検出の開始]** をクリックします。

 vCenter Server 検出が完了した後に、アプライアンスによってインストール済みのアプリケーション、ロールおよび機能 (ソフトウェア インベントリ) の検出が開始されます。ソフトウェア インベントリ時には、追加されたサーバーの資格情報がサーバーに対して反復処理され、エージェントレスの依存関係分析が検証されます。サーバーのエージェントレス依存関係分析はポータルから有効にすることができます。 検証に成功したサーバーのみを選択し、エージェントレスの依存関係分析を有効にすることができます。

## <a name="start-dependency-discovery"></a>依存関係の検出を開始する

依存関係の検出を有効にするサーバーを選択します。

1. **Azure Migrate:Server Assessment** で、 **[Discovered servers]\(検出済みサーバー\)** をクリックします。
2. 検出を確認する **[アプライアンス名]** を選択します。
1. **[依存関係 (エージェントレス)]** 列でサーバーの検証状態を確認できます。
1. **[依存関係の分析]** ドロップダウンをクリックします。
1. **[サーバーの追加]** をクリックします。
1. **[サーバーの追加]** ページで、依存関係の分析を有効にするサーバーを選択します。 依存関係マッピングは、検証が成功したサーバーでのみ有効にすることができます。 次の検証サイクルは、最後の検証タイムスタンプから 24 時間後に実行されます。
1. サーバーを選択した後、 **[サーバーの追加]** をクリックします。

:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png" alt-text="依存関係の分析を開始する":::

サーバーで依存関係の分析を有効にしてから 6 時間前後に依存関係を視覚化することができます。 依存関係の分析のために複数のサーバーを同時に有効にする場合は、[PowerShell](#start-or-stop-dependency-analysis-using-powershell) を使用してこれを行うことができます。

## <a name="visualize-dependencies"></a>依存関係を視覚化する

1. **Azure Migrate:Server Assessment** で、 **[Discovered servers]\(検出済みサーバー\)** をクリックします。
1. 検出を確認する **[アプライアンス名]** を選択します。
1. 依存関係を確認するサーバーを検索します。
1. **[依存関係 (エージェントレス)]** 列の **[依存関係の表示]** をクリックします。
1. **[Time duration]\(期間\)** ドロップダウンを使用して、マップを表示する期間を変更します。
1. **[クライアント]** グループを展開し、選択したサーバーと依存関係があるサーバーの一覧を表示します。
1. **[ポート]** グループを展開し、選択したサーバーからの依存関係があるサーバーの一覧を表示します。
1. いずれかの依存サーバーのマップ ビューに移動するには、サーバー名 > **[サーバー マップを読み込む]** の順にクリックします
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png" alt-text="サーバーのポート グループを展開してサーバー マップを読み込む":::
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png" alt-text="クライアント グループを展開する":::

8. 選択したサーバーを展開し、各依存関係のプロセスレベルの詳細を表示します。
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png" alt-text="サーバーを展開してプロセスを表示する":::

> [!NOTE]
> 依存関係のプロセス情報は、常に使用できるとは限りません。 使用できない場合、依存関係は "不明なプロセス" というマークが付いたプロセスとして示されます。

## <a name="export-dependency-data"></a>依存関係データをエクスポートする

1. **Azure Migrate:Server Assessment** で、 **[Discovered servers]\(検出済みサーバー\)** をクリックします。
2. **[依存関係の分析]** ドロップダウンをクリックします。
3. **[アプリケーションの依存関係のエクスポート]** をクリックします。
4. **[アプリケーションの依存関係のエクスポート]** ページで、目的のサーバーが検出されているアプライアンスを選びます。
5. 開始時刻と終了時刻を選択します。 ダウンロードできるのは過去 30 日間のデータだけであることに注意してください。
6. **[Export dependency]\(依存関係のエクスポート\)** をクリックします。

依存関係データは、CSV 形式でエクスポートおよびダウンロードされます。 ダウンロードされたファイルには、依存関係の分析が有効になっているすべてのサーバー間の依存関係データが含まれています。 
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/export.png" alt-text="依存関係をエクスポートする":::

### <a name="dependency-information"></a>依存関係の情報

エクスポートされた CSV 内の各行は、指定した時間スロットで観察された依存関係に対応します。

次の表は、エクスポートされた CSV のフィールドをまとめたものです。 サーバー名、アプリケーション、プロセスの各フィールドは、エージェントレスの依存関係分析が有効になっているサーバーに対してのみ設定されることに注意してください。

**フィールド名** | **詳細**
--- | --- 
Timeslot (タイムスロット) | 依存関係が観察された時間帯。 <br/> 依存関係データは、現在、6 時間のスロットに対してキャプチャされます。
Source server name (依存元サーバー名) | ソース サーバーの名前 
Source application (依存元アプリケーション) | ソース サーバー上のアプリケーションの名前  
Source process (依存元プロセス) | ソース サーバー上のプロセスの名前  
Destination server name (依存先サーバー名) | 宛先サーバーの名前
宛先 IP | 宛先サーバーの IP アドレス
Destination application (依存先アプリケーション) | 宛先サーバー上のアプリケーションの名前
Destination process (依存先プロセス) | 宛先サーバー上のプロセスの名前  
宛先ポート | 宛先サーバーのポート番号

## <a name="stop-dependency-discovery"></a>依存関係の検出を停止する

依存関係の検出を停止するサーバーを選択します。

1. **Azure Migrate:Server Assessment** で、 **[Discovered servers]\(検出済みサーバー\)** をクリックします。
1. 検出を確認する **[アプライアンス名]** を選択します。
1. **[依存関係の分析]** ドロップダウンをクリックします。
1. **[サーバーの削除]** をクリックします。
1. **[サーバーの削除]** ページで、依存関係の分析を停止するサーバーを選択します。
1. サーバーを選択した後、 **[サーバーの削除]** をクリックします。

複数のサーバーで同時に依存関係を停止する場合は、[PowerShell](#start-or-stop-dependency-analysis-using-powershell) を使用してこれを行うことができます。

## <a name="start-or-stop-dependency-analysis-using-powershell"></a>PowerShell を使用して依存関係の分析を開始または停止する

GitHub の [Azure PowerShell サンプル](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) リポジトリから PowerShell モジュールをダウンロードします。

### <a name="log-in-to-azure"></a>Azure にログインする

1. Connect-AzAccount コマンドレットを使用して、Azure サブスクリプションにログインします。

    ```PowerShell
    Connect-AzAccount
    ```
    Azure Government を使用している場合は、次のコマンドを使用します。
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Azure Migrate プロジェクトを作成したサブスクリプションを選択します 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. ダウンロードした AzMig_Dependencies PowerShell モジュールをインポートします

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>依存関係データの収集を有効または無効にする

1. 次のコマンドを使用して、Azure Migrate プロジェクト内の検出されたサーバーの一覧を取得します。 下の例では、プロジェクト名は FabrikamDemoProject で、それが属しているリソース グループは FabrikamDemoRG です。 サーバーの一覧は FabrikamDemo_VMs.csv に保存されます

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    そのファイルで、サーバーの表示名、依存関係収集の現在の状態および検出されたすべてのサーバーの ARM ID を確認できます。 

2. 依存関係を有効または無効にするには、入力 CSV ファイルを作成します。 このファイルには、"ARM ID" というヘッダーを持つ列が必要です。 CSV ファイル内の追加のヘッダーは無視されます。 前の手順で生成されたファイルを使用して CSV を作成できます。 依存関係を有効または無効にするサーバーが保持されているファイルのコピーを作成します。 

    次の例では、入力ファイル FabrikamDemo_VMs_Enable.csv のサーバーの一覧で依存関係分析が有効になっています。

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    次の例では、入力ファイル FabrikamDemo_VMs_Disable.csv のサーバーの一覧で依存関係分析が無効になっています。

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Power BI でネットワーク接続を視覚化する

Azure Migrate には、一度に多数のサーバーのネットワーク接続を視覚化し、プロセスとサーバーによってフィルター処理するために使用できる Power BI テンプレートが用意されています。 視覚化するには、下の手順に従って、依存関係データを Power BI に読み込みます。

1. GitHub の [Azure PowerShell サンプル](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) リポジトリから PowerShell モジュールと Power BI テンプレートをダウンロードします。

2. 下の手順に従って、Azure にログインします。 
    - Connect-AzAccount コマンドレットを使用して、Azure サブスクリプションにログインします。

        ```PowerShell
        Connect-AzAccount
        ```

    - Azure Government を使用している場合は、次のコマンドを使用します。

        ```PowerShell
        Connect-AzAccount -EnvironmentName AzureUSGovernment
        ```

    - Azure Migrate プロジェクトを作成したサブスクリプションを選択します

        ```PowerShell
        select-azsubscription -subscription "Fabrikam Demo Subscription"
        ```

3. ダウンロードした AzMig_Dependencies PowerShell モジュールをインポートします

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. 次のコマンドを実行します。 このコマンドは、依存関係データを CSV にダウンロードして処理し、Power BI での視覚化に使用できる一意の依存関係の一覧を生成します。 下の例では、プロジェクト名は FabrikamDemoProject で、それが属しているリソース グループは FabrikamDemoRG です。 FabrikamAppliance によって検出されたサーバーの依存関係がダウンロードされます。 一意の依存関係は FabrikamDemo_Dependencies.csv に保存されます。

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. ダウンロードした Power BI テンプレートを開きます

6. ダウンロードした依存関係データを Power BI に読み込みます。
    - Power BI でテンプレートを開きます。
    - ツール バーの **[データの取得]** をクリックします。 
    - 一般的なデータ ソースから **[テキスト/CSV]** を選択します。
    - ダウンロードした依存関係ファイルを選択します。
    - **[読み込み]** をクリックします。
    - テーブルが CSV ファイルの名前でインポートされていることがわかります。 右側のフィールド バーにテーブルが表示されます。 その名前を AzMig_Dependencies に変更します
    - ツールバーの [最新の情報に更新] をクリックします。

    ネットワーク接続のグラフと、依存元サーバー名、依存先サーバー名、依存元プロセス名、依存先プロセス名の各スライサーが、インポートされたデータと共に点灯します。

7. サーバーとプロセスによってフィルター処理されたネットワーク接続のマップを視覚化します。 ファイルを保存します。

## <a name="next-steps"></a>次のステップ

評価のために[サーバーをグループ化](how-to-create-a-group.md)します。
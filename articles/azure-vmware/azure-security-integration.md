---
title: Azure Security Center 統合を使用して Azure VMware Solution VM を保護する
description: Azure Security Center のダッシュボードから Azure のネイティブ セキュリティ ツールを使用して Azure VMware Solution VM を保護します。
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 7292ea4486a61f5b0cfd8f656d2763a3ce655e79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578256"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Azure Security Center 統合を使用して Azure VMware Solution VM を保護する

Azure のネイティブ セキュリティ ツールにより、Azure、Azure VMware Solution、オンプレミスの仮想マシン (VM) から成るハイブリッド環境が保護されます。 この記事では、ハイブリッド環境のセキュリティのために Azure のツールを設定する方法を示します。 これらのツールを使用して、さまざまな脅威を特定して対処します。

## <a name="azure-native-services"></a>Azure のネイティブ サービス

Azure ネイティブ サービスの簡単な概要を次に示します。

- **Log Analytics ワークスペース:** Log Analytics ワークスペースは、ログ データを格納するための特有の環境です。 各ワークスペースには、独自のデータ リポジトリと構成があります。 データ ソースとソリューションは、特定のワークスペースにデータを格納するように構成されます。
- **Azure Security Center:** Azure Security Center は、インフラストラクチャ セキュリティの統合管理システムです。 これにより、データ センターのセキュリティが強化され、クラウドやオンプレミスのハイブリッド ワークロード全体にわたって高度な脅威保護が提供されます。
- **Azure Sentinel:** Azure Sentinel は、クラウドネイティブのセキュリティ情報イベント管理 (SIEM) ソリューションです。 環境全体にわたって、セキュリティ分析、アラート検出、自動化された脅威対応を提供します。

## <a name="topology"></a>トポロジ

![Azure 統合セキュリティのアーキテクチャを示す図。](media/azure-security-integration/azure-integrated-security-architecture.png)

Log Analytics エージェントを使用すると、Azure、Azure VMware Solution、オンプレミスの VM からのログ データの収集が可能になります。 ログ データは Azure Monitor ログに送信され、Log Analytics ワークスペースに格納されます。 Arc 対応サーバーの [VM 拡張機能のサポート](../azure-arc/servers/manage-vm-extensions.md)を使用して、新しい VM や既存の VM のために Log Analytics エージェントをデプロイできます。 

Log Analytics ワークスペースによってログが収集されたら、Azure Security Center を使用して Log Analytics ワークスペースを構成できます。 Azure Security Center では、Azure VMware Solution VM の脆弱性の状態を評価し、重大な脆弱性があればアラートを生成します。 たとえば、オペレーティング システムのパッチの不足、セキュリティの構成の誤り、[エンドポイントの保護](../security-center/security-center-services.md)について評価します。

Log Analytics ワークスペースで、Azure Sentinel によるアラートの検出、脅威の可視性、ハンティング、脅威対応を構成できます。 前の図では、Azure Security Center コネクタを使用して、Azure Security Center が Azure Sentinel に接続されています。 インシデントを作成し、他の脅威とマップするために、Azure Security Center から Azure Sentinel に環境の脆弱性が転送されます。 スケジュールされたルールのクエリを作成して、望ましくないアクティビティを検出し、それをインシデントに変換することもできます。

## <a name="benefits"></a>メリット

- Azure ネイティブ サービスは、Azure、Azure VMware Solution、オンプレミス サービス内にあるハイブリッド環境のセキュリティために使用できます。
- Log Analytics ワークスペースを使用して、データまたはログを単一ポイントに収集し、同じデータを異なる Azure ネイティブ サービスに提示することができます。
- Azure Security Center には、次のような多くの機能が用意されています。
    - ファイルの整合性の監視
    - ファイルレス攻撃の検出
    - オペレーティング システムのパッチ評価 
    - セキュリティの誤った構成の評価
    - エンドポイント保護の評価
- Azure Sentinel では、以下のことができます。
    - クラウドの規模でデータを収集します。オンプレミスと複数のクラウド内の両方で、すべてのユーザー、デバイス、アプリケーション、インフラストラクチャが対象です。
    - 以前は検出されなかった脅威を検出します。
    - 人工知能を使用して脅威を調査し、疑わしいアクティビティを大規模にハンティングします。
    - インシデントに迅速に対応します。一般的なタスクの組み込みのオーケストレーションとオートメーションを使用します。

## <a name="create-a-log-analytics-workspace"></a>Log Analytics ワークスペースの作成

さまざまなソースからデータを収集するには、Log Analytics ワークスペースが必要になります。 詳細については、[Azure portal での Log Analytics ワークスペースの作成](../azure-monitor/logs/quick-create-workspace.md)に関するページを参照してください。 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Security Center をデプロイして Azure VMware Solution VM を構成する

Azure Security Center は事前に構成済みのツールで、デプロイを必要としません。 Azure portal で、**Security Center** を検索し、それを選択します。

### <a name="enable-azure-defender"></a>Azure Defender を有効にする

Azure Defender は、オンプレミスとクラウド内の両方で、ハイブリッド ワークロード全体にわたって Azure Security Center の高度な脅威保護を拡張するものです。 そのため Azure VMware Solution VM を保護するには、Azure Defender を有効にする必要があります。 

1. Security Center で、 **[作業の開始]** を選択します。

2. **[アップグレード]** タブを選択してから、サブスクリプションまたはワークスペースを選択します。 

3. **[アップグレード]** を選択すると、Azure Defender が有効になります。

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Security Center に Azure VMware Solution VM を追加する

1. Azure portal で、**Azure Arc** を検索し、それを選択します。

2. [リソース] の下で **[サーバー]** を選択してから、 **[+追加]** を選択します。

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Azure に Azure VMware Solution VM を追加するための Azure Arc サーバーのページを示すスクリーンショット。":::

3. **[スクリプトの生成]** を選択します。
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="対話型スクリプトを使用してサーバーを追加するためのオプションを示す Azure Arc ページのスクリーンショット。"::: 
 
4. **[前提条件]** タブで、 **[次へ]** を選択します。

5. **[リソースの詳細]** タブで、以下を入力します。 
    - サブスクリプション
    - Resource group
    - リージョン 
    - オペレーティング システム
    - プロキシ サーバーの詳細
    
    次に、**次のステップ: タグ** を選択します。

6. **[タグ]** タブで、 **[次へ]** を選択します。

7. **[スクリプトのダウンロードと実行]** タブで、 **[ダウンロード]** を選択します。

8. オペレーティング システムを指定し、Azure VMware Solution VM でスクリプトを実行します。

## <a name="view-recommendations-and-passed-assessments"></a>推奨事項と合格した評価を表示する

1. Azure Security Center で、左ペインから **[インベントリ]** を選択します。

2. [リソースの種類] で、 **[サーバー - Azure Arc]** を選択します。
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="[リソースの種類] で [サーバー - Azure Arc] が選択されていることを示す、Azure Security Center の [インベントリ] ページのスクリーンショット。":::

3. リソースの名前を選択します。 リソースのセキュリティ正常性の詳細を示すページが表示されます。

4. **[推奨事項の一覧]** で、 **[推奨事項]** タブ、 **[合格した評価]** タブ、 **[利用できない評価]** タブを選択して、以下の詳細を表示します。

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="セキュリティの推奨事項と評価を示す Azure Security Center のスクリーンショット。":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Azure Sentinel ワークスペースをデプロイする

Azure Sentinel は、Log Analytics ワークスペースの上に構築されています。 Azure Sentinel をオンボードする最初のステップは、その目的に使用する Log Analytics ワークスペースを選択することです。

1. Azure portal で、**Azure Sentinel** を検索し、それを選択します。

2. Azure Sentinel ワークスペースのページで、 **[+追加]** を選択します。

3. Log Analytics ワークスペースを選択し、 **[追加]** を選択します。

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Azure VMware Solution VM でセキュリティ イベントのデータ コレクターを有効にする

これで、Azure Sentinel をデータ ソース (この場合はセキュリティ イベント) に接続する準備ができました。

1. Azure Sentinel ワークスペースのページで、構成したワークスペースを選択します。

2. [構成] の下の **[データ コネクタ]** を選択します。

3. [コネクタ名] 列の下の一覧から **[セキュリティ イベント]** を選択し、 **[コネクタ ページを開く]** を選択します。

4. コネクタ ページで、ストリームするイベントを選択し、 **[変更の適用]** を選択します。

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Azure Sentinel の [セキュリティ イベント] ページのスクリーンショット。ここで、ストリームするイベントを選択できます。":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Azure Sentinel を Azure Security Center と接続する  

1. Azure Sentinel ワークスペースのページで、構成したワークスペースを選択します。

2. [構成] の下の **[データ コネクタ]** を選択します。

3. 一覧から **[Azure Security Center]** を選択してから、 **[コネクタ ページを開く]** を選択します。

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Azure Security Center を Azure Sentinel と接続するための選択を示す、Azure Sentinel の [データ コネクタ] ページのスクリーンショット。":::

4. **[接続]** を選択して、Azure Security Center を Azure Sentinel と接続します。

5. **[インシデントの作成]** を有効にして、Azure Security Center のためのインシデントを生成します。

## <a name="create-rules-to-identify-security-threats"></a>セキュリティの脅威を特定するためのルールを作成する

データ ソースを Azure Sentinel に接続したら、検出された脅威に対するアラートを生成するルールを作成できます。 次の例では、Windows サーバーへのサインイン試行で正しくないパスワードが使用された場合のルールを作成します。

1. Azure Sentinel の概要ページで、[構成] の下の **[Analytics]** を選択します。

2. [構成] の下の **[Analytics]** を選択します。

3. **[+ 作成]** を選択し、ドロップダウンで **[スケジュール済みクエリ ルール]** を選択します。

4. **[全般]** タブで、必要な情報を入力します。

    - 名前
    - Description
    - 方針
    - 重大度
    - Status

    **Next:ルールのロジックを設定 >** を選択します。

5. **[ルールのロジックを設定]** タブで、必要な情報を入力します。

    - ルールクエリ (次にクエリの例を示します)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - エンティティのマップ
    - クエリのスケジューリング
    - アラートのしきい値
    - イベントのグループ化
    - 抑制

    **[次へ]** を選択します。

6. **[インシデントの設定]** タブで、 **[この分析ルールによってトリガーされるアラートからインシデントを作成する]** を有効にして、 **[次へ:自動応答>]** を選択します。
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Azure Sentinel で新しいルールを作成するための分析ルール ウィザードのスクリーンショット。[この分析ルールによってトリガーされるアラートからインシデントを作成する] が有効として表示されています。":::

7. **Next:確認>** を選択します。

8. **[確認と作成]** タブで情報を確認し、 **[作成]** を選択します。

Windows サーバーへのサインインの試みが 3 回失敗すると、作成されたルールにより、失敗した試みすべてに対してインシデントがトリガーされます。

## <a name="view-alerts"></a>アラートを表示する

生成されたインシデントは、Azure Sentinel で表示できます。 インシデントを割り当てて、解決されたら閉じることもできます。これらはすべて Azure Sentinel 内から行えます。

1. Azure Sentinel の概要ページに移動します。

2. [脅威の管理] の下で、 **[インシデント]** を選択します。

3. インシデントを選択します。 その後で、解決のためにインシデントをチームに割り当てることができます。

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="インシデントが選択され、解決のためにインシデントを割り当てるオプションが表示されている、Azure Sentinel の [インシデント] ページのスクリーンショット。":::

    問題解決後は閉じることができます。

## <a name="hunt-security-threats-with-queries"></a>クエリを使用してセキュリティの脅威をハンティングする

クエリを作成するか、Azure Sentinel で使用可能な定義済みのクエリを使用して、環境内の脅威を特定できます。 次の手順では、定義済みのクエリを実行します。

1. Azure Sentinel の概要ページに移動します。

2. [脅威の管理] の下で、 **[ハンティング]** を選択します。 定義済みのクエリの一覧が表示されます。

3. クエリを選択してから、 **[クエリの実行]** を選択します。

4. **[結果の表示]** を選択して結果を確認します。

### <a name="create-a-new-query"></a>新しいクエリを作成する

1.  [脅威の管理] の下で **[ハンティング]** を選択してから、 **[+ 新しいクエリ]** を選択します。

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="[+ 新しいクエリ] が強調表示されている、Azure Sentinel の [ハンティング] ページのスクリーンショット。":::

2. 次の情報を入力して、カスタム クエリを作成します。

    - 名前
    - Description
    - カスタム クエリ
    - マッピングの入力
    - 方針
    
3. **［作成］** を選択します 次に、作成したクエリを選択すると、 **[クエリの実行]** と **[結果の表示]** を選択できます。

## <a name="next-steps"></a>次のステップ

Azure VMware Solution VM を保護する方法について説明したので、次のことについて学習します。

- [Azure Defender ダッシュボード](../security-center/azure-defender-dashboard.md)の使用。
- [Azure Sentinel での高度なマルチステージ攻撃の検出](../azure-monitor/logs/quick-create-workspace.md)。
- [Azure VMware Solution VM のライフサイクル管理](lifecycle-management-of-azure-vmware-solution-vms.md)。

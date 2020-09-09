---
title: Azure Migrate を使用したオンプレミス サーバーでのアプリ、ロール、機能の検出
description: Azure Migrate Server Assessment を使用して、オンプレミス サーバー上のアプリ、ロール、および機能を検出する方法について説明します。
ms.topic: article
ms.date: 06/10/2020
ms.openlocfilehash: 535c8ae8c2d6e5d9d175e663a58d47dc76aa0529
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118649"
---
# <a name="discover-machine-apps-roles-and-features"></a>マシンのアプリ、ロール、および機能を検出する

この記事では、Azure Migrate :Server Assessment を使用して、オンプレミス サーバー上のアプリケーション、ロール、および機能を検出する方法について説明します。

オンプレミスのマシンで実行されているアプリ、ロール、機能のインベントリを検出することで、Azure への移行パスを特定し、ワークロードに合わせて調整ができます。 アプリ検出では、VM ゲスト資格情報を使用し、Azure Migrate アプライアンスを使って検出が実行されます。 アプリ検出はエージェントレスです。 VM には何もインストールされません。

> [!NOTE]
> 現在、アプリ検出は VMware VM に対してプレビュー段階であり、検出のみに限定されています。 アプリベースの評価はまだ提供されていません。 


## <a name="before-you-start"></a>開始する前に

- 次を完了したことを確認します。
    - Azure Migrate プロジェクトの[作成](how-to-add-tool-first-time.md)。
    - プロジェクトへの Azure Migrate:Server Assessment ツーのル[追加](how-to-assess.md)。
- [アプリ検出のサポートと要件](migrate-support-matrix-vmware.md#vmware-requirements)を確認します。
- アプリ検出を実行する VM に PowerShell バージョン2.0 以降がインストールされていること、および VMware ツール (10.2.0 より後) がインストールされていることを確認します。
- Azure Migrate アプライアンスを展開するための[要件](migrate-appliance.md)を確認します。


## <a name="deploy-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスをデプロイする

1. Azure Migrate アプライアンスを展開するための要件を[確認](migrate-appliance.md#appliance---vmware)します。
2. アプライアンスが[パブリック](migrate-appliance.md#public-cloud-urls)および [Government クラウド](migrate-appliance.md#government-cloud-urls)でアクセスする必要がある Azure の URL について確認します。
3. 検出および評価中にアプライアンスによって収集される[データを確認](migrate-appliance.md#collected-data---vmware)します。
4. アプライアンスのポート アクセス要件に[注意](migrate-support-matrix-vmware.md#port-access-requirements)します。
5. [Azure Migrate アプライアンスを展開](how-to-set-up-appliance-vmware.md)して検出を開始します。 アプライアンスを展開するには、OVA テンプレートをダウンロードして VMware にインポートし、アプライアンスを VMware VM として作成します。 アプライアンスを構成し、Azure Migrate に登録します。
6. アプライアンスを展開するときに、継続的な検出を開始するには、以下を指定します。
    - 接続先の vCenter Server の名前。
    - アプライアンスから vCenter Server に接続するために作成した資格情報。
    - アプライアンスから Windows/Linux VM に接続するために作成したアカウントの資格情報。

アプライアンスを展開し、資格情報を指定すると、アプリ、機能、およびロールの検出と共に、VM メタデータとパフォーマンス データの継続的な検出がアプライアンスで開始されます。  アプリ検出の期間は、所有している VM の数によって変わります。 通常、500 台の VM のアプリ検出には 1 時間かかります。

## <a name="verify-permissions"></a>アクセス許可の確認

検出と評価のために [vCenter Server の読み取り専用アカウントを作成](tutorial-prepare-vmware.md#set-up-permissions-for-assessment)しました。 読み取り専用アカウントには、アプリ検出用の VM と対話するために、 **[Virtual Machines]**  >  **[ゲスト操作]** に対して有効になっている特権が必要です。

### <a name="add-the-user-account-to-the-appliance"></a>アプライアンスにユーザー アカウントを追加する

次のようにユーザー アカウントを追加します。

1. アプライアンス管理アプリを開きます。 
2. **[Provide vCenter details]\(vCenter の詳細\)** パネルに移動します。
3. **[VM でのアプリケーションと依存関係の検出]** で、 **[資格情報の追加]** をクリックします。
3. **[オペレーティング システム]** を選択し、アカウントのフレンドリ名と、 **[ユーザー名]** / **[パスワード]** を入力します
6. **[保存]** をクリックします。
7. **[保存して検出を開始]** をクリックします。

    ![VM ユーザー アカウントを追加する](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)


## <a name="review-and-export-the-inventory"></a>インベントリの確認とエクスポート

検出が終了すると、アプリ検出用の資格情報を指定した場合は、Azure portal でアプリ インベントリを確認してエクスポートできます。

1. **[Azure Migrate - サーバー]**  >  **[Azure Migrate: Server Assessment]** で、表示されたカウントをクリックして、 **[検出済みサーバー]** ページを開きます。

    > [!NOTE]
    > この段階では、必要に応じて、検出されたマシンの依存関係の分析を設定して、評価対象のマシン間の依存関係を視覚化することもできます。 依存関係の分析の[詳細を見る](concepts-dependency-visualization.md)。

2. **[Applications discovered]\(検出されたアプリケーション\)** で、表示されたカウントをクリックします。
3. **[Application inventory]\(アプリケーション インベントリ\)** では、検出されたアプリ、ロール、および機能を確認できます。
4. インベントリをエクスポートするには、 **[検出済みサーバー]** の **[Export app inventory]\(アプリ インベントリのエクスポート\)** をクリックします。

アプリ インベントリは、Excel 形式でエクスポートおよびダウンロードされます。 **[Application Inventory]\(アプリケーション インベントリ\)** シートには、すべてのマシンで検出されたすべてのアプリが表示されます。

## <a name="next-steps"></a>次のステップ

- 検出されたサーバーの[評価を作成](how-to-create-assessment.md)します。
- [Azure Migrate:Database Assessment](/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017) を使用して SQL Server データベースを評価します。

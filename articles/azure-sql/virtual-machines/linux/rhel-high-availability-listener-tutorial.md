---
title: Azure の RHEL 仮想マシンで SQL Server の可用性グループ リスナーを構成する - Linux 仮想マシン | Microsoft Docs
description: Azure の RHEL 仮想マシンで SQL Server の可用性グループ リスナーを設定する方法について学習します
ms.service: virtual-machines-linux
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 83fde9e957cb2011ce585603e51d331be171bc08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586229"
---
# <a name="tutorial-configure-an-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>チュートリアル:Azure の RHEL 仮想マシンで SQL Server の可用性グループ リスナーを構成する
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> 提供されているチュートリアルは **パブリック プレビュー** 版です。 
>
> このチュートリアルでは SQL Server 2017 と RHEL 7.6 を使用しますが、RHEL 7 または RHEL 8 で SQL Server 2019 を使用して高可用性を構成することもできます。 可用性グループ リソースを構成するためのコマンドは RHEL 8 で変更されています。適切なコマンドの詳細については、記事「[可用性グループのリソースを作成する](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource)」および RHEL 8 のリソースを参照してください。

このチュートリアルでは、Azure の RHEL 仮想マシン (VM) で SQL Server の可用性グループ リスナーを作成する手順について説明します。 学習内容:

> [!div class="checklist"]
> - Azure portal でロード バランサーを作成する
> - ロード バランサーのバックエンド プールを構成する
> - ロード バランサーのプローブを作成する
> - 負荷分散規則を設定する
> - クラスターにロード バランサー リソースを作成する
> - 可用性グループ リスナーを作成する
> - リスナーへの接続をテストする
> - フェールオーバーをテストする

## <a name="prerequisite"></a>前提条件

「[チュートリアル: Azure の RHEL 仮想マシンで SQL Server の可用性グループを構成する](rhel-high-availability-stonith-tutorial.md)」を完了していること

## <a name="create-the-load-balancer-in-the-azure-portal"></a>Azure Portal でロード バランサーを作成する

次の手順では、[Azure portal でのロード バランサー](../windows/availability-group-load-balancer-portal-configure.md)に関する記事の「[Azure portal でのロード バランサーの作成と構成](../windows/availability-group-load-balancer-portal-configure.md#create--configure-load-balancer)」セクションの手順 1 から 4 を実行します。

### <a name="create-the-load-balancer"></a>ロード バランサーを作成する

1. Azure ポータルで、SQL Server の仮想マシンを含んだリソース グループを開きます。 

2. リソース グループで **[追加]** をクリックします。

3. **ロード バランサー** を探し、検索結果から **[ロード バランサー]** (**Microsoft** によって発行されたもの) を選択します。

4. **[ロード バランサー]** ブレードで **[作成]** をクリックします。

5. **[ロード バランサーの作成]** ダイアログボックスで、次のようにロード バランサーを構成します。

   | 設定 | 値 |
   | --- | --- |
   | **名前** |ロード バランサーを表すテキスト名 (例: **sqlLB**)。 |
   | **Type** |**内部** |
   | **Virtual Network** |作成された既定の仮想ネットワークには、**VM1VNET** という名前が設定されているはずです。 |
   | **サブネット** |SQL Server インスタンスが存在するサブネットを選択します。 既定値は **VM1Subnet** である必要があります。|
   | **IP アドレスの割り当て** |**静的** |
   | **プライベート IP アドレス** |クラスター内に作成された `virtualip` IP アドレスを使用します。 |
   | **サブスクリプション** |対象のリソース グループに使用したサブスクリプションを使用します。 |
   | **リソース グループ** |SQL Server インスタンスが存在するリソース グループを選択します。 |
   | **場所** |Azure において SQL Server インスタンスが存在する場所を選択します。 |

### <a name="configure-the-back-end-pool"></a>バックエンド プールを構成する
Azure では、バックエンド アドレス プールを "*バックエンド プール*" と呼んでいます。 この例では、可用性グループに含まれる 3 つの SQL Server インスタンスのアドレスがバックエンド プールとなります。 

1. リソース グループで、作成したロード バランサーをクリックします。 

2. **[設定]** の **[バックエンド プール]** をクリックします。

3. **[バックエンド プール]** の **[追加]** をクリックしてバックエンド アドレス プールを作成します。 

4. **[バックエンド プールの追加]** の **[名前]** に、バックエンド プールの名前を入力します。

5. **[関連付け先]** で、 **[仮想マシン]** を選択します。 

6. 環境内の各仮想マシンを選択し、適切な IP アドレスを各選択項目に関連付けます。

    :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-backend-pool.png" alt-text="バックエンド プールを追加する":::

7. **[追加]** をクリックします。 

### <a name="create-a-probe"></a>プローブを作成する

このプローブで定義された方法で、現在どの SQL Server インスタンスが可用性グループ リスナーを所有しているかが Azure によって確認されます。 Azure は、プローブの作成時に定義されたポートで、IP アドレスに基づいてサービスをプローブします。

1. ロード バランサーの **[設定]** ブレードで **[Health probes (正常性のプローブ)]** をクリックします。 

2. **[Health probes (正常性のプローブ)]** ブレードで **[追加]** をクリックします。

3. **[プローブの追加]** ブレードでプローブを構成します。 次の値を使用してプローブを構成します。

   | 設定 | 値 |
   | --- | --- |
   | **名前** |プローブを表すテキスト名 (例: **SQLAlwaysOnEndPointProbe**)。 |
   | **プロトコル** |**TCP** |
   | **[ポート]** |空いている任意のポートを使用できます (例: *59999*)。 |
   | **間隔** |*5* |
   | **異常のしきい値** |*2* |

4.  **[OK]** をクリックします。 

5. すべての仮想マシンにログインし、次のコマンドを使用してプローブ ポートを開きます。

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Azure はこのプローブを作成、使用して、どの SQL Server インスタンスが可用性グループのリスナーを所有しているかを判定します。

### <a name="set-the-load-balancing-rules"></a>負荷分散規則を設定する

負荷分散規則を使って、ロード バランサーでトラフィックを SQL Server インスタンスにルーティングする方法を構成します。 3 つの SQL Server インスタンスのうち可用性グループ リスナー リソースを所有できるのは一度に 1 つだけであるため、このロード バランサーでは Direct Server Return を有効にします。

1. ロード バランサーの **[設定]** ブレードで **[負荷分散規則]** をクリックします。 

2. **[負荷分散規則]** ブレードで、 **[追加]** をクリックします。

3. **[負荷分散規則の追加]** ブレードを使用して負荷分散規則を構成します。 次の設定を使用します。 

   | 設定 | 値 |
   | --- | --- |
   | **名前** |負荷分散規則を表すテキスト名。 (例: **SQLAlwaysOnEndPointListener**)。 |
   | **プロトコル** |**TCP** |
   | **[ポート]** |*1433* |
   | **バックエンド ポート** |*1433* この規則には **[フローティング IP (Direct Server Return)]** が使用されるため、この値は無視されます。 |
   | **プローブ** |このロード バランサーに対して作成したプローブの名前を使用します。 |
   | **セッション永続化** |**なし** |
   | **アイドル タイムアウト (分)** |*4* |
   | **フローティング IP (ダイレクト サーバー リターン)** |**有効** |

   :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-load-balancing-rule.png" alt-text="負荷分散規則の追加":::

4. **[OK]** をクリックします。 
5. 負荷分散規則が Azure によって構成されます。 以上、可用性グループのリスナーのホストとなっている SQL Server インスタンスにトラフィックをルーティングするようにロード バランサーを構成しました。 

この時点で、リソース グループのロード バランサーがすべての SQL Server マシンに接続されています。 またロード バランサーには、可用性グループへの要求にすべてのマシンが応答できるよう、SQL Server Always On 可用性グループ リスナーの IP アドレスが格納されます。

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>クラスターにロード バランサー リソースを作成する

1. プライマリ仮想マシンにログインします。 Azure ロード バランサーのプローブ ポートを有効にするには、リソースを作成する必要があります (この例では 59999 が使用されています)。 次のコマンドを実行します。

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. `virtualip` および `azure_load_balancer` リソースを含むグループを作成します。

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>制約を追加する

1. Azure ロード バランサーの IP アドレスと AG リソースが同じノードで実行されるように、コロケーション制約を構成する必要があります。 次のコマンドを実行します。

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. 順序制約を作成して、Azure ロード バランサーの IP アドレスよりも前に AG リソースが稼働するようにします。 コロケーション制約により順序制約が暗黙に示されますが、これによって適用されます。

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. 制約を確認するには、次のコマンドを実行します。

    ```bash
    sudo pcs constraint list --full
    ```

    次の出力が表示されます。

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>可用性グループ リスナーを作成する

1. プライマリ ノードで、SQLCMD または SSMS で次のコマンドを実行します。

    - 以下で使用されている IP アドレスを `virtualip` の IP アドレスに置き換えてください。

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. 各 VM ノードにログインします。 次のコマンドを使用して hosts ファイルを開き、各マシンで `ag1-listener` のホスト名解決を設定します。

    ```
    sudo vi /etc/hosts
    ```

    **vi** エディターで、テキストを挿入するために「`i`」と入力します。空白行に、`ag1-listener` の IP アドレスを追加します。 次に、IP アドレスの後にスペースを入れ、`ag1-listener` を追加します。

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    **vi** エディターを終了するには、まず **Esc** キーを押し、コマンド `:wq` を入力してファイルを書き込み、終了します。 これを各ノードで行います。

## <a name="test-the-listener-and-a-failover"></a>リスナーとフェールオーバーをテストする

### <a name="test-logging-in-to-sql-server-using-the-availability-group-listener"></a>可用性グループ リスナーを使用して SQL Server へのログインをテストする

1. SQLCMD を使用し、可用性グループ リスナー名を使用して SQL Server のプライマリ ノードにログインします。

    - 以前に作成したログインを使用し、`<YourPassword>` を適切なパスワードに置き換えます。 次の例では、SQL Server で作成された `sa` ログインを使用します。

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. 接続先のサーバーの名前を確認します。 SQLCMD で次のコマンドを実行します。

    ```sql
    SELECT @@SERVERNAME
    ```

    出力には、現在のプライマリ ノードが表示されます。 フェールオーバーを一度もテストしたことがない場合、これは `VM1` になります。

    `exit` コマンドを入力して、SQL Server セッションを終了します。

### <a name="test-a-failover"></a>フェールオーバーをテストする

1. 次のコマンドを実行して、プライマリ レプリカを `<VM2>` または別のレプリカに手動でフェールオーバーします。 `<VM2>` は、実際のサーバー名の値に置き換えてください。

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. 制約を確認すると、手動フェールオーバーが理由で別の制約が追加されたことがわかります。

    ```bash
    sudo pcs constraint list --full
    ```

    ID `cli-prefer-ag_cluster-master` の制約が追加されたことがわかります。

1. 次のコマンドを使用して、ID が `cli-prefer-ag_cluster-master` の制約を削除します。

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. コマンド `sudo pcs resource` を使用してクラスター リソースを確認すると、プライマリ インスタンスが `<VM2>` になっていることがわかります。

    > [!NOTE]
    > この記事には、Microsoft が使用しなくなった "スレーブ" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。


    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. SQLCMD を使用し、リスナー名を使用してプライマリ レプリカにログインします。

    - 以前に作成したログインを使用し、`<YourPassword>` を適切なパスワードに置き換えます。 次の例では、SQL Server で作成された `sa` ログインを使用します。

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
     ```

1. 接続先のサーバーを確認します。 SQLCMD で次のコマンドを実行します。

    ```sql
    SELECT @@SERVERNAME
    ```

    これで、フェールオーバー先の VM に接続していることがわかります。

## <a name="next-steps"></a>次のステップ

Azure のロード バランサーの詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [Azure VM の SQL Server VM で可用性グループ用のロード バランサーを構成する](../windows/availability-group-load-balancer-portal-configure.md)

---
title: "MPI アプリケーションを実行するように Windows RDMA クラスターを設定する | Microsoft Docs"
description: "H16r、H16mr、A8、または A9 サイズの VM を使用する Windows HPC Pack クラスターを作成し、Azure の RDMA ネットワークを使用して MPI アプリを実行する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 7d9f5bc8-012f-48dd-b290-db81c7592215
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 680b2bec3af18273d0ac43d7102a99392fd8ebe0
ms.lasthandoff: 03/27/2017


---
# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Set up a Windows RDMA cluster with HPC Pack to run MPI applications (HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する)
Azure で [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) と [H シリーズまたはコンピューティング集中型 A シリーズ インスタンス](../../virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を使用して Windows RDMA クラスターを設定し、並列 Message Passing Interface (MPI) アプリケーションを実行します。 HPC Pack クラスターで RDMA 対応の Windows Server ベースのノードを設定すると、MPI アプリケーションは、リモート ダイレクト メモリ アクセス (RDMA) テクノロジに基づく Azure の低待機時間で高スループットのネットワークを介して効率的に通信します。

Linux VM 上で Azure RDMA ネットワークにアクセスする MPI ワークロードを実行する場合は、「 [MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](../../linux/classic/rdma-cluster.md)」を参照してください。

## <a name="hpc-pack-cluster-deployment-options"></a>HPC Pack クラスターのデプロイ オプション
Microsoft HPC Pack は、追加コストなしで提供され、Windows または Linux HPC アプリケーションを実行するための HPC クラスターをオンプレミスまたは Azure で作成するために使用できます。 HPC Pack には、Microsoft が実装する Windows 用 Message Passing Interface (MS-MPI) のランタイム環境が含まれています。 サポートされている Windows Server オペレーティング システムを実行している RDMA 対応のインスタンスで使用する場合、HPC Pack は、Azure RDMA ネットワークにアクセスする Windows MPI アプリケーションを実行するための効率的なオプションとなります。 

この記事では、Microsoft HPC Pack と共に Windows RDMA クラスターを設定する 2 つのシナリオと詳しいガイダンスへのリンクを紹介します。 

* シナリオ 1. コンピューティング集中型 worker ロール インスタンスをデプロイする (PaaS)
* シナリオ 2. 計算ノードをコンピューティング集中型 VM にデプロイする (IaaS)

Windows でコンピューティング集中型インスタンスを使用するための一般的な前提条件については、「[H シリーズとコンピューティング集中型 A シリーズの VM について](../../virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。

## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>シナリオ 1: コンピューティング集中型 worker ロール インスタンスをデプロイする (PaaS)
既存の HPC Pack クラスターから、クラウド サービスで実行される Azure worker ロール インスタンス (Azure ノード) にコンピューティング リソースを追加します (PaaS)。 HPC Pack からの "Azure へのバースト" とも呼ばれるこの機能は、worker ロール インスタンスのサイズの範囲をサポートしています。 Azure ノードを追加するときは、RDMA 対応のサイズのいずれかを指定します。

既存の (通常はオンプレミスの) クラスターから RDMA 対応のインスタンスにバーストする際の考慮事項とバーストの手順を以下に示します。 Azure VM にデプロイされた HPC Pack ヘッド ノードに worker ロール インスタンスを追加する場合も、同様の手順を実行します。

> [!NOTE]
> HPC Pack を使用した Azure へのバーストのチュートリアルについては、 [HPC Pack を使用したハイブリッド クラスターのセットアップ](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)に関するページを参照してください。 次の手順の考慮事項は、特に RDMA 対応の Azure ノードに適用されます。
> 
> 

![Burst to Azure][burst]

### <a name="steps"></a>手順
1. **HPC Pack 2012 R2 ヘッド ノードをデプロイして構成する**
   
    最新の HPC Pack インストール パッケージを [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=49922)からダウンロードします。 Azure のバースト デプロイの要件と手順については、[Microsoft HPC Pack を使用した Azure worker インスタンスへのバースト](https://technet.microsoft.com/library/gg481749.aspx)に関する記事をご覧ください。
2. **Azure のサブスクリプションで管理証明書を構成する**
   
    ヘッド ノードと Azure の間の接続をセキュリティで保護するための証明書を構成します。 オプションと手順については、 [HPC Pack 用に Azure の管理証明書を構成するシナリオ](http://technet.microsoft.com/library/gg481759.aspx)に関するページを参照してください。 テスト デプロイでは、HPC Pack により、Azure サブスクリプションに迅速にアップロードできる既定の Microsoft HPC Azure 管理証明書がインストールされます。
3. **新しいクラウド サービスとストレージ アカウントを作成する**
   
    Azure クラシック ポータルを使用して、RDMA 対応のインスタンスが使用可能なリージョンでのデプロイのためにクラウド サービスとストレージ アカウントを作成します。
4. **Azure ノード テンプレートを作成する**
   
    HPC クラスター マネージャーのノード テンプレートの作成ウィザードを使用します。 手順については、Microsoft HPC Pack を使用して Azure ノードをデプロイする手順に関するページの [Azure ノード テンプレートの作成](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) に関するセクションを参照してください。
   
    初期テスト用に、手動可用性ポリシーをテンプレートに構成することをお勧めします。
5. **クラスターにノードを追加する**
   
    HPC クラスター マネージャーのノードの追加ウィザードを使用します。 詳細については、 [Azure ノードを Windows HPC クラスターに追加する手順](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add)に関するセクションを参照してください。
   
    ノードのサイズを指定するときは、RDMA 対応のインスタンス サイズのいずれかを選択します。
   
   > [!NOTE]
   > HPC Pack は、コンピューティング集中型インスタンスを使用する Azure デプロイへの各バーストで、指定された Azure worker ロール インスタンスに加え、2 つ以上の RDMA 対応のインスタンス (A8 など) をプロキシ ノードとして自動的にデプロイします。 プロキシ ノードは、サブスクリプションに割り当てられたコアを使用します。これにより、Azure worker ロールのインスタンスに加えて料金が発生します。
   > 
   > 
6. **ノードを開始 (プロビジョニング) し、オンラインにしてジョブを実行できるようにする**
   
    ノードを選択し、HPC クラスター マネージャーの **[開始]** 操作を使用します。 プロビジョニングが完了したら、ノードを選択し、HPC クラスター マネージャーの **[オンラインにする]** 操作を使用します。 ノードでジョブを実行する準備が整います。
7. **クラスターにジョブを送信する**
   
   HPC Pack のジョブ送信ツールを使用して、クラスター ジョブを実行します。 [Microsoft HPC Pack のジョブ管理](http://technet.microsoft.com/library/jj899585.aspx)に関するページを参照してください。
8. **ノードを停止 (プロビジョニング解除) する**
   
   ジョブの実行が完了したら、HPC クラスター マネージャーでノードをオフラインにし、 **[停止]** 操作を使用します。

## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>シナリオ 2: 計算ノードをコンピューティング集中型 VM にデプロイする (IaaS)
このシナリオでは、Azure 仮想ネットワークの VM に HPC Pack ヘッド ノードとクラスター計算ノードをデプロイします。 HPC Pack には、自動デプロイ スクリプトや Azure クイックスタート テンプレートなど、[Azure VM でのデプロイ オプション](../../virtual-machines-linux-hpcpack-cluster-options.md)がいくつか用意されています。 例として、下記の考慮事項と手順は、[HPC Pack IaaS デプロイ スクリプト](hpcpack-cluster-powershell-script.md) を使用して Azure での HPC Pack 2012 R2 クラスターのデプロイを自動化する方法を示しています。

![Cluster in Azure VMs][iaas]

### <a name="steps"></a>手順
1. **クライアント コンピューターで HPC Pack の IaaS デプロイ スクリプトを実行して、クラスター ヘッド ノードと計算ノード VM を作成する**
   
    HPC Pack の IaaS デプロイ スクリプト パッケージを [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=49922)からダウンロードします。
   
    クライアント コンピューターを準備し、スクリプトの構成ファイルを作成して、スクリプトを実行するには、 [HPC Pack の IaaS デプロイ スクリプトを使用した HPC クラスターの作成](hpcpack-cluster-powershell-script.md)に関するページを参照してください。 
   
    RDMA 対応のコンピューティング ノードをデプロイする場合は、以下の追加の考慮事項に注意してください。
   
   * **仮想ネットワーク**: 使用する RDMA 対応のインスタンス サイズが使用可能なリージョンで新しい仮想ネットワークを指定します。
   * **Windows Server オペレーティング システム**: RDMA 接続をサポートするには、計算ノード VM 用に Windows Server 2012 R2 または Windows Server 2012 オペレーティング システムを指定します。
   * **クラウド サービス**: ヘッド ノードと計算ノードは、それぞれ異なるクラウド サービスにデプロイすることをお勧めします。
   * **ヘッド ノードのサイズ**: このシナリオでは、ヘッド ノードに少なくとも A4 (XL) サイズを用意することを検討してください。
   * **HpcVmDrivers 拡張機能**: デプロイ スクリプトは、Windows Server オペレーティング システムを持つ A8 または A9 サイズの計算ノードをデプロイするときに、Azure VM エージェントと HpcVmDrivers 拡張機能を自動的にインストールします。 HpcVmDrivers は、RDMA ネットワークに接続するためのドライバーを計算ノード VM にインストールします。 RDMA 対応の H シリーズの VM では、HpcVmDrivers 拡張機能を手動でインストールする必要があります。 「[H シリーズとコンピューティング集中型 A シリーズの VM について](../../virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#access-to-the-rdma-network)」を参照してください。
   * **クラスター ネットワークの構成**: デプロイ スクリプトにより、HPC Pack クラスターはトポロジ 5 (Enterprise ネットワークのすべてのノード) で自動的にセットアップされます。 このトポロジは、VM でのすべての HPC Pack クラスターのデプロイに必要です。 クラスター ネットワークのトポロジを後で変更しないでください。
2. **計算ノードをオンラインにしてジョブを実行できるようにする**
   
    ノードを選択し、HPC クラスター マネージャーの **[オンラインにする]** 操作を使用します。 ノードでジョブを実行する準備が整います。
3. **クラスターにジョブを送信する**
   
    ヘッド ノードに接続してジョブを送信するか、またはオンプレミスのコンピューターを設定してジョブを送信します。 詳細については、 [Azure の HPC クラスターへのジョブの送信](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。
4. **ノードをオフラインにし、停止 (割り当て解除) する**
   
    ジョブの実行が完了したら、HPC クラスター マネージャーでノードをオフラインにします。 次に、Azure の管理ツールを使用して、ノードをシャットダウンします。

## <a name="run-mpi-applications-on-the-cluster"></a>クラスターで MPI アプリケーションを実行する
### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>例: HPC Pack クラスターで mpipingpong を実行する
RDMA 対応のインスタンスの HPC Pack デプロイを確認するには、クラスターで HPC Pack の **mpipingpong** コマンドを実行します。 **mpipingpong** により、ペアになっているノード間でデータのパケットが繰り返し送信され、待機時間とスループットの測定値、RDMA が有効なアプリケーション ネットワークの統計情報が計算されます。 この例は、クラスターの **mpiexec** コマンドを使用して MPI ジョブ (この場合は **mpipingpong**) を実行するための一般的なパターンを示しています。

この例では、“Azure にバースト” 構成 ([シナリオ 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-\(PaaS\) in this article)) で Azure ノードを追加すると仮定します。 HPC Pack を Azure VM のクラスターにデプロイする場合は、コマンド構文を修正して、別のノード グループを指定し、ネットワーク トラフィックを RDMA ネットワークに転送するように追加の環境変数を設定する必要があります。

クラスターで mpipingpong を実行するには:

1. ヘッド ノードまたは適切に構成されたクライアント コンピューターで、コマンド プロンプトを開きます。
2. 4 ノードの Azure バースト デプロイ内のノード ペア間の待機時間を推定するために、次のコマンドを入力して、パケット サイズが小さく繰り返し数が多い設定で mpipingpong を実行するジョブを送信します。
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```
   
    このコマンドを実行すると、送信されたジョブの ID が返されます。
   
    Azure VM にデプロイされた HPC Pack クラスターをデプロイした場合は、1 つのクラウド サービスにデプロイされた計算ノード VM を含むノード グループを指定し、 **mpiexec** コマンドを次のように変更します。
   
    ```Command
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```
3. ジョブが完了したら、出力 (この場合は、ジョブの 1 番目のタスクの出力) を表示するために次のように入力します。
   
    ```Command
    task view <JobID>.1
    ```
   
    ここで、&lt;*JobID*&gt; には、送信されたジョブの ID を指定します。
   
    出力には、次のように待機時間の結果が含まれます。
   
    ![Ping pong latency][pingpong1]
4. Azure バースト ノードのペア間のスループットを推定するために、次のコマンドを入力して、パケット サイズが大きく繰り返し数が少ない設定で **mpipingpong** を実行するジョブを送信します。
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```
   
    このコマンドを実行すると、送信されたジョブの ID が返されます。
   
    Azure VM にデプロイされた HPC Pack クラスターで、手順 2. に示したようにコマンドを変更します。
5. ジョブが完了したら、出力 (この場合は、ジョブの 1 番目のタスクの出力) を表示するために次のように入力します。
   
    ```Command
    task view <JobID>.1
    ```
   
   出力には、次のようにスループットの結果が含まれます。
   
   ![Ping pong throughput][pingpong2]

### <a name="mpi-application-considerations"></a>MPI アプリケーションに関する考慮事項
Azure で HPC Pack を使用して MPI アプリケーションを実行する場合の考慮事項を次に示します。 いくつかの項目は、Azure ノード ("Azure へのバースト" 構成で追加された worker ロールのインスタンス) のデプロイにのみ適用されます。

* クラウド サービスの worker ロール インスタンスは、(たとえば、システムのメンテナンスやインスタンスの障害が原因で) Azure によって通知なく定期的に再プロビジョニングされます。 インスタンスが MPI ジョブの実行中に再プロビジョニングされた場合、インスタンスのデータが失われ、インスタンスが最初にデプロイされた状態に戻り、MPI ジョブが失敗する原因となります。 1 つの MPI ジョブに使用するノードの数が多いほど、およびジョブの実行に時間がかかるほど、ジョブの実行中にいずれかのインスタンスが再プロビジョニングされる可能性が高くなります。 デプロイの 1 つのノードをファイル サーバーとして指定する場合も、この点を考慮してください。
* Azure で MPI ジョブを実行するために RDMA 対応のインスタンスを使用する必要はありません。 HPC Pack でサポートされている任意のインスタンス サイズを使用することができます。 ただし、待機時間やノードを接続するネットワークの帯域幅に依存する比較的大規模な MPI ジョブの実行には RDMA 対応のインスタンスをお勧めします。 その他のサイズを使用して待機時間と帯域幅に依存する MPI ジョブを実行する場合は、サイズが小さなジョブを実行して、1 つのタスクが少数のノードで実行されるようにすることをお勧めします。
* Azure インスタンスにデプロイされたアプリケーションには、アプリケーションに関連付けられているライセンス条件が適用されます。 クラウドで実行するためのライセンスまたはその他の制限事項については、商用アプリケーションのベンダーに確認してください。 すべてのベンダーが従量課金制ライセンスを提供しているとは限りません。
* オンプレミスのノード、共有、ライセンス サーバーにアクセスするには、Azure インスタンスでさらに設定が必要になります。 たとえば、オンプレミスのライセンス サーバーにアクセスできるように Azure ノードを有効にする場合、サイト間 Azure 仮想ネットワークを構成できます。
* Azure インスタンスで MPI アプリケーションを実行するには、 **hpcfwutil** コマンドを実行して、各 MPI アプリケーションをインスタンスの Windows ファイアウォールに登録します。 これにより、ファイアウォールによって動的に割り当てられたポートで MPI 通信が実行されます。
  
  > [!NOTE]
  > Azure デプロイへのバースト用には、ファイアウォールの例外コマンドを、クラスターに追加されたすべての新しい Azure ノードで自動的に実行されるように構成することもできます。 **hpcfwutil** コマンドを実行してアプリケーションが動作することを確認した後、Azure ノードのスタートアップ スクリプトに次のコマンドを追加します。 詳細については、「 [Use a Startup Script for Azure Nodes](https://technet.microsoft.com/library/jj899632.aspx)」をご覧ください。
  > 
  > 
* HPC Pack では、CCP_MPI_NETMASK クラスター環境変数を使用して、MPI 通信で許容されるアドレスの範囲を指定します。 HPC Pack 2012 R2 以降では、CCP_MPI_NETMASK クラスター環境変数は、(オンプレミスまたは Azure VM の) ドメインに参加しているクラスター計算ノード間の MPI 通信にのみ影響を与えます。 この変数は、Azure へのバースト構成で追加されたノードで無視されます。
* MPI ジョブは、異なるクラウド サービス (たとえば、異なるノード テンプレートを使用した Azure へのバースト デプロイ、または複数のクラウド サービスにデプロイされた Azure VM 計算ノード) にデプロイされた Azure インスタンス間で実行できません。 異なるノード テンプレートを使用して開始された複数の Azure ノード デプロイの場合は、MPI ジョブを 1 つの Azure ノードのセットで実行する必要があります。
* Azure ノードをクラスターに追加してオンラインにすると、HPC ジョブ スケジューラ サービスは、すぐにノードでジョブを開始しようとします。 ワークロードの一部のみを Azure で実行できる場合は、ジョブ テンプレートを更新または作成して、Azure で実行できるジョブの種類を定義してください。 たとえば、ジョブ テンプレートを使用して送信されたジョブが Azure ノードでのみ実行されるようにするには、ジョブ テンプレートにノード グループ プロパティを追加し、必須の値として AzureNodes を選択します。 Azure ノードに対応するカスタム グループを作成するには、Add-HpcGroup HPC PowerShell コマンドレットを使用します。

## <a name="next-steps"></a>次のステップ
* HPC Pack を使用する代わりに、Azure Batch サービスを開発し、Azure の計算ノードの管理されたプールで MPI アプリケーションを実行します。 「 [Azure Batch でのマルチインスタンス タスクを使用した Message Passing Interface (MPI) アプリケーションの実行](../../../batch/batch-mpi.md)」をご覧ください。
* Azure RDMA ネットワークにアクセスする Linux MPI ワークロードを実行する場合は、「 [MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](../../linux/classic/rdma-cluster.md)」を参照してください。

<!--Image references-->
[burst]:media/hpcpack-rdma-cluster/burst.png
[iaas]:media/hpcpack-rdma-cluster/iaas.png
[pingpong1]:media/hpcpack-rdma-cluster/pingpong1.png
[pingpong2]:media/hpcpack-rdma-cluster/pingpong2.png


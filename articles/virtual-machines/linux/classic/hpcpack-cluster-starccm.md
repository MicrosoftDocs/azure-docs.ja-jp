---
title: "Linux VM で HPC Pack を使用して STAR-CCM+ を実行する | Microsoft Docs"
description: "Microsoft HPC Pack クラスターを Azure にデプロイし、RDMA ネットワークに接続された複数の Linux コンピューティング ノードで STAR-CCM+ ジョブを実行します。"
services: virtual-machines-linux
documentationcenter: 
author: xpillons
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 75523406-d268-4623-ac3e-811c7b74de4b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 09/13/2016
ms.author: xpillons
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: c83d39c87f789397116601e3e775a4f0ed3eac72
ms.lasthandoff: 03/27/2017


---
# <a name="run-star-ccm-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Azure の Linux RDMA クラスター上で Microsoft HPC Pack を使用して STAR-CCM+ を実行する
この記事では、Microsoft HPC Pack クラスターを Azure にデプロイし、InfiniBand で相互接続された複数の Linux コンピューティング ノードで [CD-adapco STAR-CCM+](http://www.cd-adapco.com/products/star-ccm%C2%AE) ジョブを実行する方法について説明します。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC Pack は、Microsoft Azure 仮想マシンのクラスター上で各種の大規模な HPC および並列アプリケーション (MPI アプリケーションなど) を実行する機能を備えています。 HPC Pack では、HPC Pack クラスターにデプロイされた Linux コンピューティング ノード VM で Linux HPC アプリケーションを実行する機能もサポートしています。 HPC Pack での Linux コンピューティング ノードの使用の概要については、「[Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](hpcpack-cluster.md)」をご覧ください。

## <a name="set-up-an-hpc-pack-cluster"></a>HPC Pack クラスターをセットアップする
HPC Pack IaaS デプロイ スクリプトを [ダウンロード センター](https://www.microsoft.com/en-us/download/details.aspx?id=44949) からダウンロードして、ローカルに抽出します。

Azure PowerShell は必須です。 Azure PowerShell をローカル コンピューターでまだ構成していない場合は、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」の記事を参照してください。

Azure Marketplace から入手できる Linux イメージ (Azure 用の InfiniBand ドライバーを含んでいる) は、本記事の執筆時点では SLES 12、CentOS 6.5、CentOS 7.1 用となります。 この記事は SLES 12 の使用を前提としています。 HPC をサポートしているすべての Linux イメージの名前を Marketplace から取得するには、次の PowerShell コマンドを実行します。

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

イメージが置かれている場所とイメージ名 (**ImageName**) のリストが出力されます。イメージ名は、後からデプロイ テンプレートで使用します。

クラスターをデプロイする前に、HPC Pack デプロイ テンプレート ファイルを作成する必要があります。 対象となるクラスターが小さいので、ドメイン コントローラーをヘッド ノードとし、そこでローカル SQL データベースをホストすることにします。

以下に示したのは、そのようなヘッド ノードをデプロイするテンプレートです。**MyCluster.xml** という名前の XML ファイルを作成し、**SubscriptionId**、**StorageAccount**、**Location**、**VMName**、**ServiceName** の値をそれぞれ実際の環境に合わせて変更してください。

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

管理者特権でのコマンド プロンプトで次の PowerShell コマンドを実行して、ヘッド ノードの作成を開始しましょう。

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

20 ～ 30 分でヘッド ノードの準備が完了します。 ヘッド ノードには、Azure ポータルから仮想マシンの **接続** アイコンをクリックして接続できます。

最終的に DNS フォワーダーの修正が必要となる場合があります。 その場合は DNS マネージャーを起動してください。

1. DNS マネージャーでサーバー名を右クリックし、**[プロパティ]** を選択して、**[フォワーダー]** タブをクリックします。
2. **[編集]** ボタンをクリックしてフォワーダーをすべて削除し、**[OK]** をクリックします。
3. **[フォワーダーが利用できない場合にルート ヒントを使用する]** チェック ボックスがオンになっていることを確認し、**[OK]** をクリックします。

## <a name="set-up-linux-compute-nodes"></a>Linux 計算ノードのセットアップ
Linux コンピューティング ノードのデプロイには、ヘッド ノードを作成したときと同じデプロイ テンプレートを使用します。

**MyCluster.xml** ファイルをローカル コンピューターからヘッド ノードにコピーし、デプロイするノード数に応じて **NodeCount** タグの値を更新してください (20 以下)。 Azure のクォータで利用できるコア数に注意してください。A9 インスタンスごとに 16 コアがご利用のサブスクリプションから消費されます。 同じ予算でもっと多くの VM を使用する必要がある場合は、A9 インスタンスの代わりに A8 インスタンス (8 コア) を使用してください。

ヘッド ノードから HPC Pack IaaS デプロイ スクリプトをコピーします。

管理者特権のコマンド プロンプトで、次の Azure PowerShell コマンドを実行します。

1. **Add-AzureAccount** を実行して、ご利用の Azure サブスクリプションに接続します。
2. 複数のサブスクリプションを利用している場合は、 **Get-AzureSubscription** を実行してそれらをリストします。
3. **Select-AzureSubscription -SubscriptionName xxxx -Default** コマンドを実行して、既定のサブスクリプションを設定します。
4. **.\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml** を実行して Linux コンピューティング ノードのデプロイを開始します。
   
   ![ヘッド ノードのデプロイの操作][hndeploy]

HPC Pack クラスター マネージャー ツールを起動します。 数分後、クラスターのコンピューティング ノードの一覧に Linux コンピューティング ノードが一定間隔で表示されます。 クラシック デプロイメント モデルでは、IaaS VM が 1 つずつ作成されます。 そのためノード数が多くなると、すべてデプロイし終えるまでに、かなりの時間がかかる場合があります。

![HPC Pack クラスター マネージャーの Linux ノード][clustermanager]

クラスター内のすべてのノードが稼働状態になったら、続けてインフラストラクチャ関連の設定を行います。

## <a name="set-up-an-azure-file-share-for-windows-and-linux-nodes"></a>Windows と Linux のノードに使用する Azure Files 共有のセットアップ
Azure Files サービスを使用して、スクリプト、アプリケーション パッケージ、およびデータ ファイルを保存できます。 Azure Files は、永続ストアとして Azure BLOB ストレージ上に CIFS 機能を提供します。 スケーラビリティの点では決して秀でたソリューションではありませんが、きわめてシンプルであり、専用の VM を必要としません。

Azure Files 共有は、「[Windows で Azure File Storage を使用する](../../../storage/storage-dotnet-how-to-use-files.md)」の手順に従って作成します。

ストレージ アカウントの名前 **saname**、ファイル共有名 **sharename**、ストレージ アカウント キー **sakey** はメモしておいてください。

### <a name="mount-the-azure-file-share-on-the-head-node"></a>ヘッド ノードへの Azure Files 共有のマウント
管理者特権でコマンド プロンプトを開き、次のコマンドを実行して、資格情報をローカル コンピューターの資格情報コンテナーに格納します。

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

次に、Azure Files 共有をマウントするには、次のコマンドを実行します。

```
    net use Z: \\<saname>.file.core.windows.net\<sharename> /persistent:yes
```

### <a name="mount-the-azure-file-share-on-linux-compute-nodes"></a>Linux コンピューティング ノードへの Azure Files 共有のマウント
HPC Pack に付属する便利なツールとして clusrun ツールがあります。 このコマンド ラインを使用して、一連のコンピューティング ノードに対して同時に同じコマンドを実行することができます。 ここでは、このツールを使用して Azure Files 共有をマウントし、再起動後も共有状態が維持されるようにします。
ヘッド ノードから管理者特権でコマンド プロンプトを開き、以下のコマンドを実行してください。

マウント ディレクトリを作成するには、次のコマンドを実行します。

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Azure Files 共有をマウントするには、次のコマンドを実行します。

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

マウントの共有状態を維持するには、次のコマンドを実行します。

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## <a name="install-star-ccm"></a>STAR-CCM+ のインストール
Azure VM のインスタンス A8 とインスタンス A9 は InfiniBand に対応し、RDMA の機能を備えています。 Azure Marketplace において、これらの機能に必要なカーネル ドライバーが利用できるのは、Windows Server 2012 R2、SUSE 12、CentOS 6.5、および CentOS 7.1 の各イメージです。 Azure には、これらのドライバーをサポートする MPI ライブラリとして、Microsoft MPI と Intel MPI (リリース 5.x) の 2 つがあります。

CD-adapco STAR-CCM+ リリース 11.x 以降には、Intel MPI Version 5.x が同梱されているため、Azure の InfiniBand に対応します。

Linux64 STAR-CCM+ パッケージは、 [CD-adapco のポータル](https://steve.cd-adapco.com)から入手できます。 ここでは、混合精度のバージョン 11.02.010 を使用しました。

ヘッド ノードの **/hpcdata** (Azure Files 共有) で、以下の内容を含んだ **setupstarccm.sh** という名前のシェル スクリプトを作成します。 このスクリプトが個々のコンピューティング ノードで実行され、STAR-CCM+ がローカルにセットアップされます。

#### <a name="sample-setupstarcmsh-script"></a>サンプル setupstarcm.sh スクリプト
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
次に、すべての Linux コンピューティング ノードに STAR-CCM+ をセットアップします。管理者特権でコマンド プロンプトを開き、次のコマンドを実行してください。

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

コマンドの実行中、クラスター マネージャーのヒートマップを使用して CPU 使用率を監視できます。 数分ですべてのノードが正しくセットアップされます。

## <a name="run-star-ccm-jobs"></a>STAR-CCM+ ジョブの実行
HPC Pack は、STAR-CCM+ ジョブを実行するためのジョブ スケジューラ機能として使用します。 そのためには、ジョブを開始して STAR-CCM+ を実行するためのスクリプトがいくつか必要となります。 最初は単純化のため、入力データを Azure Files 共有に保存します。

STAR-CCM+ ジョブは、以下に示した PowerShell スクリプトを使用してキューに追加します。 このスクリプトは次の 3 つの引数を受け取ります。

* モデル名
* 使用するノードの数
* 各ノードで使用するコアの数

STAR-CCM+ はメモリの帯域幅を使い切ってしまう可能性があるので、通常は、コンピューティング ノードあたりの使用コア数を少なくし、新しいノードを追加することをお勧めします。 ノードごとの厳密なコア数は、プロセッサ ファミリと相互接続速度によって異なります。

ノードは目的のジョブ専用に割り当てられます。 他のジョブと共有することはできません。 ジョブは MPI ジョブとして直接開始されるわけではなく、 **runstarccm.sh** シェル スクリプトから MPI ランチャーが開始されます。

入力モデルと **runstarccm.sh** スクリプトは、**/hpcdata** という先ほどマウントした共有場所に格納されます。

ログ ファイルは、STAR-CCM+ の出力ファイルと共に、ジョブ ID に基づく名前で **/hpcdata 共有**に格納されます。

#### <a name="sample-submitstarccmjobps1-script"></a>サンプル SubmitStarccmJob.ps1 スクリプト
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job     
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
**runner.java** は、必要な STAR-CCM+ Java モデル ランチャーとログ コードに置き換えてください。

#### <a name="sample-runstarccmsh-script"></a>サンプル runstarccm.sh スクリプト
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
        let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
        -power -podkey "<yourkey>" -rsh ssh \
        -mpi intel -fabric UDAPL -cpubind bandwidth,v \
        -mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
        -batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

私たちが行ったテストでは、Power-On-Demand ライセンス トークンを使用しました。 このトークンでは、**$CDLMD_LICENSE_FILE** 環境変数を **1999@flex.cd-adapco.com** に設定し、コマンド ラインの **-podkey** オプションでキーを設定する必要があります。

このスクリプトは初期化処理を行った後、HPC Pack によって設定された **$CCP_NODES_CORES** 環境変数から、MPI ランチャーで使用されるホスト ファイルを作成するための一連のノードを抽出します。 ジョブに使用される一連のコンピューティング ノード名が、このホスト ファイルに 1 行に 1 件ずつ記述されます。

**$CCP_NODES_CORES** の形式は、次のパターンに従います。

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

各値の説明:

* `<Number of nodes>` は、このジョブに割り当てるノードの数です。
* `<Name of node_n_...>` は、このジョブに割り当てる各ノードの名前です。
* `<Cores of node_n_...>` は、このジョブに割り当てるノードのコア数です。

さらに、(パラメーター **$NBCORESPERNODE** で指定された) ノードごとのコア数と、ノード数 (**$NBNODES**) とに基づいて、コア数 (**$NBCORES**) が計算されます。

Azure 上の Intel MPI では、MPI 関連のオプションを次のように指定します。

* `-mpi intel` は、Intel MPI を指定します。
* `-fabric UDAPL` は、Azure InfiniBand 動詞を使用します。
* `-cpubind bandwidth,v` は、MPI と STAR-CCM+ の組み合わせに帯域幅を最適化します。
* `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"` は、Azure InfiniBand と連携するように Intel MPI を設定します。また、ノードあたりの必要なコア数を設定します。
* `-batch` は、UI を表示せずに STAR-CCM+ をバッチ モードで起動します。

ジョブを開始するにあたっては、クラスター マネージャーでノードが稼働状態であること、またオンライン状態であることを確認してください。 確認後、PowerShell のコマンド プロンプトから次のコマンドを実行します。

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## <a name="stop-nodes"></a>ノードの停止
テストの完了後、ノードを停止してから起動するには、次の HPC Pack PowerShell コマンドを実行します。

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## <a name="next-steps"></a>次のステップ
その他の Linux ワークロードも試してみましょう。 たとえば、次を参照してください。

* [Azure の Linux コンピューティング ノード上で Microsoft HPC Pack を使用して NAMD を実行する](hpcpack-cluster-namd.md)
* [Azure の Linux RDMA クラスター上で Microsoft HPC Pack を使用して OpenFoam を実行する](hpcpack-cluster-openfoam.md)

<!--Image references-->
[hndeploy]:media/hpcpack-cluster-starccm/hndeploy.png
[clustermanager]:media/hpcpack-cluster-starccm/ClusterManager.png


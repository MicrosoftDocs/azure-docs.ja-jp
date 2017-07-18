---
title: "MPI アプリケーションを実行するように Linux RDMA クラスターを設定する | Microsoft Docs"
description: "H16r、H16mr、A8、または A9 サイズの VM の Linux クラスターを作成し、Azure RDMA ネットワークを使用して MPI アプリを実行します。"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 01834bad-c8e6-48a3-b066-7f1719047dd2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: b002d21b198fa4887e30d30c20ec67d21ae30579
ms.lasthandoff: 04/03/2017


---
# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>MPI アプリケーションを実行するように Linux RDMA クラスターを設定する
Azure で [H シリーズまたはコンピューティング集中型 A シリーズの VM](../a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して Linux RDMA クラスターを設定し、並列 Message Passing Interface (MPI) アプリケーションを実行する方法について説明します。 この記事では、クラスターで Intel MPI を実行するために Linux HPC イメージを準備する手順を説明します。 準備ができたら、このイメージと、RDMA 対応の Azure VM サイズのいずれか (現時点では H16r、H16mr、A8、または A9) を使用して、VM のクラスターをデプロイします。 リモート ダイレクト メモリ アクセス (RDMA) テクノロジに基づく低待機時間で高スループットのネットワークを介して効率的に通信する MPI アプリケーションを実行するには、このクラスターを使用します。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、[Azure Resource Manager](../../../resource-manager-deployment-model.md) とクラシックの 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

## <a name="cluster-deployment-options"></a>クラスターのデプロイ オプション
ジョブ スケジューラを使用する場合、または使用しない場合に、Linux の RDMA クラスターの作成に使用できる方法を次に示します。

* **Azure CLI スクリプト**: 後で示すように、RDMA 対応の VM のクラスターをデプロイするスクリプトを作成するには、[Azure コマンド ライン インターフェイス](../../../cli-install-nodejs.md) (CLI) を使用します。 クラシック デプロイメント モデルのサービス管理モードの CLI では、クラスター ノードを順番に作成します。そのため、多くのコンピューティング ノードのデプロイには数分かかる場合があります。 クラシック デプロイメント モデルの使用時に RDMA ネットワーク接続を有効にするには、同じクラウド サービス内に VM をデプロイしてください。
* **Azure Resource Manager テンプレート**: RDMA ネットワークに接続する RDMA 対応の VM のクラスターをデプロイするには、Resource Manager デプロイメント モデルを使用することもできます。 [独自のテンプレートを作成する](../../../resource-group-authoring-templates.md)か、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)」で Microsoft またはコミュニティから提供されたテンプレートを確認して、目的のソリューションをデプロイすることができます。 リソース マネージャー テンプレートは、Linux クラスターをデプロイするための高速で信頼性の高い方法を提供します。 Resource Manager デプロイメント モデルの使用時に RDMA ネットワーク接続を有効にするには、同じ可用性セット内に VM をデプロイしてください。
* **HPC Pack**: Azure で Microsoft HPC Pack クラスターを作成し、サポートされる Linux ディストリビューションを実行する RDMA 対応の計算ノードを追加して RDMA ネットワークにアクセスします。 詳しくは、「[チュートリアル: Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](hpcpack-cluster.md)」をご覧ください。

## <a name="sample-deployment-steps-in-the-classic-model"></a>クラシック モデルでのサンプル デプロイの手順
次の手順では、Azure CLI を使用して、Azure Marketplace から SUSE Linux Enterprise Server (SLES) 12 SP1 HPC VM をデプロイし、それをカスタマイズして、カスタム VM イメージを作成する方法を説明します。 その後、そのイメージを使用して、RDMA 対応の VM のクラスターをデプロイするスクリプトを作成できます。

> [!TIP]
> Azure Marketplace の CentOS ベースの HPC イメージに基づいて、RDMA 対応の VM のクラスターをデプロイする場合は、同様の手順を実行します。 説明したとおり、一部の手順は若干異なります。 
>
>

### <a name="prerequisites"></a>前提条件
* **クライアント コンピューター**: Azure と通信するための Mac、Linux、または Windows クライアント コンピューターが必要です。 これらの手順は、Linux クライアントを使用していることを前提とします。
* **Azure サブスクリプション**: サブスクリプションがない場合は、 [無料アカウント](https://azure.microsoft.com/free/) を数分で作成することができます。 大規模なクラスターでは、従量課金制のサブスクリプションまたはその他の購入オプションを検討してください。
* **VM サイズの可用性**: RDMA に対応しているインスタンス サイズは H16r、H16mr、A8、A9 です。 各 Azure リージョンで利用できるかどうかについては、「 [リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/) 」を参照してください。
* **コア クォータ**: コンピューティング集中型の VM のクラスターをデプロイするには、コア クォータを増やすことが必要な場合があります。 たとえば、8 個の A9 VM をデプロイする場合は、少なくとも 128 コアが必要です。 サブスクリプションによっては、H シリーズを含む特定の VM サイズ ファミリにデプロイできるコア数が制限されることがあります。 クォータを増やすためのリクエストは、[オンライン カスタマー サポートに申請](../../../azure-supportability/how-to-create-azure-support-request.md) (無料) してください。
* **Azure CLI**: Azure CLI を[インストール](../../../cli-install-nodejs.md)し、クライアント コンピューターから [Azure サブスクリプションに接続します](../../../xplat-cli-connect.md)。

### <a name="provision-an-sles-12-sp1-hpc-vm"></a>SLES 12 SP1 HPC VM のプロビジョニング
Azure CLI で Azure にサインインした後に、`azure config list` を実行して出力にサービス管理モードが表示されることを確認します。 表示されない場合は、次のコマンドを実行して asm モードに設定します。

    azure config mode asm


使用する権限があるすべてのサブスクリプションを一覧表示するには、次を入力します。

    azure account list

現在のアクティブなサブスクリプションは、`Current` が `true` に設定されています。 クラスターの作成に使用するサブスクリプションがこれではない場合、適切なサブスクリプション ID をアクティブなサブスクリプションとして設定します。

    azure account set <subscription-Id>

Azure で一般公開された SLES 12 SP1 HPC イメージを表示するには、次のようなコマンドを実行します (シェル環境で **grep** がサポートされていると仮定)。

    azure vm image list | grep "suse.*hpc"

次のようなコマンドを実行して、SLES 12 SP1 HPC イメージで RDMA 対応の VM をプロビジョニングします。

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

各値の説明:

* サイズ (この例では A9) は、RDMA 対応の VM サイズの 1 つです。
* 外部 SSH ポート番号 (この例では、SSH の規定である 22) は任意の有効なポート番号です。 内部 SSH ポート番号は 22 に設定されます。
* 新しいクラウド サービスが、場所によって指定される Azure リージョンに作成されます。 選択した VM サイズが利用可能な場所を指定します。
* SUSE 優先サポート (追加料金がかかります) の場合、SLES 12 SP1 イメージ名には、現在のところ次のいずれかを使用できます。 

 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824`

  `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824`


### <a name="customize-the-vm"></a>VM のカスタマイズ
VM のプロビジョニングが完了したら、VM の外部 IP アドレス (または DNS 名) と構成済みポート番号を使用して VM に SSH 接続し、その後 VM をカスタマイズします。 接続について詳しくは、「[Linux を実行する仮想マシンにログオンする方法](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 手順の完了にルート アクセスが必要でない限り、VM で構成したユーザーとしてコマンドを実行します。

> [!IMPORTANT]
> Microsoft Azure では Linux VM にルート アクセスが提供されません。 ユーザーとして VM に接続されているときに管理アクセス権を取得するには、`sudo` を使用してコマンドを実行します。
>
>

* **更新プログラム**: zypper を使用して更新プログラムをインストールします。 NFS ユーティリティをインストールすることもできます。

  > [!IMPORTANT]
  > SLES 12 SP1 HPC VM では、カーネルの更新プログラムを適用しないことをお勧めします。適用した場合、Linux RDMA ドライバーに関連する問題が発生する可能性があります。
  >
  >
* **Intel MPI**: 次のコマンドを実行して、SLES 12 SP1 HPC VM への Intel MPI のインストールを完了します。

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
* **メモリのロック**: MPI コードによって、RDMA で使用可能なメモリをロックするには、/etc/security/limits.conf ファイルで、次の設定を追加または変更します。 このファイルを編集するにはルート アクセスが必要です。

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

  > [!NOTE]
  > テスト目的で、memlock を無制限に設定することもできます。 たとえば、「 `<User or group name>    hard    memlock unlimited`」のように入力します。 詳しくは、「[Best Known Methods for Setting Locked Memory Size (ロックされたメモリ サイズを設定するためのよく知られた方法)](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size)」をご覧ください。
  >
  >
* **SLES VM 用 SSH キー**: MPI ジョブの実行時に SLES クラスター内の計算ノード間でユーザー アカウントの信頼を確立するために、SSH キーを生成します。 CentOS ベースの HPC VM をデプロイした場合、この手順は実行しないでください。 この記事で後述する、イメージをキャプチャし、クラスターをデプロイした後に、クラスター ノード内にパスワードなしの SSH 信頼関係をセットアップする手順を参照してください。

    SSH キーを作成するには、次のコマンドを実行します。 入力を求められたら、パスワードを設定せずに、単に **Enter** を選択して既定の場所でキーを生成します。

        ssh-keygen

    公開キーを、既知の公開キー用の authorized_keys ファイルに追加します。

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    ~/.ssh ディレクトリにある config ファイルを編集 (存在しない場合は作成) します。 Azure で使用するプライベート ネットワークの IP アドレスの範囲を指定します (この例では 10.32.0.0/16)。

        host 10.32.0.*
        StrictHostKeyChecking no

    または、クラスター内の各 VM のプライベート ネットワーク IP アドレスを、次のように一覧表示します。

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

  > [!NOTE]
  > 特定の IP アドレスまたは範囲を指定しない場合、`StrictHostKeyChecking no` と構成すると、潜在的なセキュリティ リスクが生じる可能性があります。
  >
  >
* **アプリケーション**: イメージをキャプチャする前に、必要なアプリケーションをインストールしたり、その他のカスタマイズを実行したりします。

### <a name="capture-the-image"></a>イメージのキャプチャ
イメージをキャプチャするには、最初に、Linux VM で次のコマンドを実行します。 このコマンドにより、VM がプロビジョニング解除されますが、設定したユーザー アカウントと SSH キーは維持されます。

```
sudo waagent -deprovision
```

クライアント コンピューターで次の Azure CLI コマンドを実行し、イメージをキャプチャします。 詳しくは、「[従来の Linux 仮想マシンをイメージとしてキャプチャする方法](capture-image.md)」をご覧ください。  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

これらのコマンドを実行した後、使用する VM イメージがキャプチャされ、VM が削除されます。 カスタム イメージをクラスターにデプロイする準備ができました。

### <a name="deploy-a-cluster-with-the-image"></a>イメージを使用したクラスターのデプロイ
次の Bash スクリプトの該当部分を環境に合った適切な値に変更し、クライアント コンピューターで実行します。 クラシック デプロイメント モデルの Azure では VM を順番にデプロイするため、このスクリプトで示されている 8 個の A9 VM をデプロイするまでに数分かかります。

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>CentOS HPC クラスターに関する考慮事項
SLES 12 for HPC ではなく、Azure Marketplace にある CentOS ベースの HPC イメージのいずれかに基づいてクラスターを設定する場合は、前のセクションの一般的な手順に従います。 VM をプロビジョニングおよび構成する際は、次の違いに注意してください。

- Intel MPI は、CentOS ベースの HPC イメージからプロビジョニングされた VM に既にインストールされています。
- メモリのロックの設定は、VM の /etc/security/limits.conf ファイルに既に追加されています。
- キャプチャ用にプロビジョニングする VM には SSH キーを生成しないでください。 代わりに、クラスターのデプロイ後にユーザーベースの認証を設定することをお勧めします。 詳しくは、次のセクションをご覧ください。  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>クラスター上でパスワードなしの SSH 信頼関係を設定する
CentOS ベースの HPC クラスターで、計算ノード間に信頼関係を構築するには、ホストベースの認証とユーザーベースの認証という 2 つの方法があります。 ホストベースの認証については、この記事で扱っていませんが、一般的に、デプロイ時の拡張スクリプトで実行されます。 ユーザーベースの認証は、デプロイ後に信頼関係を構築する場合に便利です。また、SSH キーを生成し、クラスター内のコンピューティング ノード間で共有する場合に必要です。 この方法は、一般的にパスワードなしの SSH ログインと呼ばれ、MPI ジョブを実行するときに必要です。

コミュニティから提供されたサンプル スクリプトについては、[GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) を参照してください。CentOS ベースの HPC クラスターで簡単なユーザー認証を有効にすることができます。 次の手順で、このスクリプトをダウンロードして使用します。 このスクリプトを変更するか、他の方法を使用して、クラスター コンピューティング ノード間にパスワードなしの SSH 認証を確立することもできます。

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh

スクリプトを実行するには、サブネットの IP アドレスのプレフィックスを知っている必要があります。 クラスター ノードのいずれかで次のコマンドを実行して、プレフィックスを取得します。 出力は 10.1.3.5 のようになります。プレフィックスは 10.1.3 の部分です。

    ifconfig eth0 | grep -w inet | awk '{print $2}'

コンピューティング ノードの共通ユーザー名、コンピューティング ノードのそのユーザーの共通パスワード、前のコマンドから返されたサブネットのプレフィックスという 3 つのパラメーターを使用してスクリプトを実行します。

    ./user_authentication.sh <myusername> <mypassword> 10.1.3

このスクリプトでは、次の処理が実行されます。

* .ssh というホスト ノードにディレクトリを作成します。これはパスワードなしのログインの場合に必要です。
* パスワードなしのログインで、クラスター内の任意のノードからのログインを許可するように .ssh ディレクトリに構成ファイルを作成します。
* クラスター内のすべてのノードについてノード名とノードの IP アドレスを含むファイルを作成します。 これらのファイルはスクリプトの実行後も残るので、後で参照できます。
* 各クラスター ノード (ホスト ノードを含む) のプライベート キーとパブリック キーのペアを作成し、authorized_keys ファイルにエントリを作成します。

> [!WARNING]
> このスクリプトを実行すると、セキュリティ上のリスクが生じる可能性があります。 ~/.ssh のパブリック キー情報を配布しないようにしてください。
>
>

## <a name="configure-intel-mpi"></a>Intel MPI の構成
Azure Linux RDMA で MPI アプリケーションを実行するには、Intel MPI に固有な特定の環境変数を構成する必要があります。 次に、アプリケーションの実行に必要な変数を構成するサンプル Bash スクリプトを示します。 Intel MPI のインストールに必要な場合、mpivars.sh のパスを変更します。

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

ホスト ファイルの形式は、次のとおりです。 クラスター内の各ノードに対して 1 つの行を追加します。 DNS 名ではなく、前に仮想ネットワークから定義したプライベート IP アドレスを指定します。 たとえば、IP アドレス 10.32.0.1 と 10.32.0.2 を持つ 2 つのホストでは、ファイルに次の内容が含まれています。

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>基本的な 2 ノード クラスターでの MPI の実行
この操作をまだ行っていない場合は、まず Intel MPI の環境を設定します。

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-an-mpi-command"></a>MPI コマンドを実行する
MPI が正しくインストールされており、少なくとも 2 つのコンピューティング ノード間で通信できることを示すために、いずれかのコンピューティング ノードで MPI コマンドを実行します。 次の **mpirun** コマンドは、2 つのノードで **hostname** コマンドを実行します。

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
出力では、 `-hosts`の入力として渡したすべてのノードの名前が一覧表示されます。 たとえば、2 つのノードを含む **mpirun** コマンドは、次のような出力を返します。

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>MPI ベンチマークの実行
次の Intel MPI コマンドでは、pingpong ベンチマークを実行して、クラスターの構成および RDMA ネットワークへの接続を確認します。

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```

2 つのノードを持つ動作中のクラスターでは、次のような出力が表示されます。 Azure RDMA ネットワークでは、最大 512 バイトのメッセージ サイズに対して、3 マイクロ秒以下の待機時間が予想されます。

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>次のステップ
* Linux クラスター上で、Linux MPI アプリケーションをデプロイし、実行します。
* Intel MPI のガイダンスについては、[Intel MPI Library のドキュメント](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/)を参照してください。
* [クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) を実行し、CentOS ベースの HPC イメージを使用して Intel Lustre クラスターを作成してみてください。 詳しくは、「[Deploying Intel Cloud Edition for Lustre on Microsoft Azure](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/)」(Microsoft Azure 上で Intel Cloud Edition for Lustre を展開する) をご覧ください。


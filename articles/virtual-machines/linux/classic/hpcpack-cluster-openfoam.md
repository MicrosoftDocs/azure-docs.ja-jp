---
title: "Linux VM で HPC Pack を使用して OpenFOAM を実行する | Microsoft Docs"
description: "Microsoft HPC Pack クラスターを Azure にデプロイし、RDMA ネットワークに接続された複数の Linux 計算ノードで OpenFOAM ジョブを実行します。"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: c0bb1637-bb19-48f1-adaa-491808d3441f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 07/22/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: a779dd05623e84250d5595311d7d87d83f1298ad
ms.lasthandoff: 03/27/2017


---
# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Azure の Linux RDMA クラスター上で Microsoft HPC Pack を使用して OpenFoam を実行する
この記事では、Azure 仮想マシンで OpenFoam を実行する一例を紹介します。 ここでは、Linux 計算ノードを含む Microsoft HPC Pack クラスターを Azure にデプロイし、Intel MPI で [OpenFoam](http://openfoam.com/) ジョブを実行します。 計算ノードに RDMA 対応の Azure VM を使用できるため、計算ノードは Azure RDMA ネットワーク経由で通信します。 Azure で OpenFoam を実行するその他のオプションとして、 Marketplace で入手できる、完全に構成済みの商用のイメージ (UberCloud の [OpenFoam 2.3 on CentOS 6](https://azure.microsoft.com/marketplace/partners/ubercloud/openfoam-v2dot3-centos-v6/) など) を [Azure Batch](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) で実行するというものがあります。 

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

OpenFOAM (Open Field Operation And Manipulation) は、オープンソースの計算流体力学 (CFD) ソフトウェア パッケージです。工学分野や科学分野の企業や学術機関で幅広く利用されています。 メッシュ化するためのツール (特に、複雑な CAD ジオメトリ用の並列メッシュ処理機構である snappyHexMesh) や、前処理と後処理のためのツールが備わっています。 ほぼすべての処理が並列に実行され、ユーザーは、コンピューターのハードウェアを最大限に活用できます。  

Microsoft HPC Pack は、Microsoft Azure 仮想マシンのクラスター上で大規模な HPC および並列アプリケーション (MPI アプリケーションなど) を実行する機能を備えています。 HPC Pack では、HPC Pack クラスターにデプロイされた Linux コンピューティング ノード VM で Linux HPC アプリケーションを実行する機能もサポートしています。 HPC Pack での Linux コンピューティング ノードの使用の概要については、「[Azure の HPC Pack クラスターで Linux コンピューティング ノードを使用開始する](hpcpack-cluster.md)」をご覧ください。

> [!NOTE]
> この記事では、HPC Pack を使用して Linux MPI ワークロードを実行する方法について説明します。 ここでは、Linux のシステム管理と Linux クラスターでの MPI ワークロードの実行について、ある程度の知識があることを前提としています。 この記事に示すものとは異なるバージョンの MPI および OpenFOAM を使用する場合は、インストールおよび構成の手順を一部変更する必要が生じることがあります。 
> 
> 

## <a name="prerequisites"></a>前提条件
* **RDMA 対応の Linux 計算ノードを含む HPC Pack クラスター** - [Azure Resource Manager テンプレート](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)または [Azure PowerShell スクリプト](hpcpack-cluster-powershell-script.md)を使用して、A8、A9、H16r、または H16rm サイズの Linux 計算ノードを含む HPC Pack クラスターをデプロイします。 どちらのオプションについても、前提条件および手順について詳しくは、「 [Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](hpcpack-cluster.md) 」を参照してください。 PowerShell スクリプトによるデプロイ オプションを選択した場合は、この記事の最後にあるサンプル ファイルのサンプル構成ファイルを参照してください。 A8 サイズの Windows Server 2012 R2 ヘッド ノード 1 つと A8 サイズの SUSE Linux Enterprise Server 12 計算ノード 2 つから成る Azure ベースの HPC Pack クラスターをデプロイする際に、この構成を使用します。 該当する値は、実際のサブスクリプションとサービス名に置き換えてください。 
  
  **その他の注意事項**
  
  * Azure の Linux RDMA ネットワークの前提条件については、「[H シリーズとコンピューティング集中型 A シリーズの VM について](../../virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。
  * Powershell スクリプトによるデプロイ オプションを使用した場合は、すべての Linux 計算ノードを 1 つのクラウド サービスにデプロイし、RDMA ネットワーク接続を使用します。
  * Linux ノードをデプロイした後は、SSH 接続を使用してその他の管理タスクを実行します。 Azure Portal で、各 Linux VM の SSH 接続の詳細を探します。  
* **Intel MPI** - Azure の SLES 12 HPC 計算ノード上で OpenFOAM を実行するには、[Intel.com サイト](https://software.intel.com/en-us/intel-mpi-library/)から Intel MPI Library 5 ランタイムをインストールする必要があります。 (CentOS ベースの HPC イメージには、Intel MPI 5 がプレインストールされています)。その後、必要に応じて Linux 計算ノードに Intel MPI をインストールします。 この手順の準備をするには、Intel に登録した後、確認の電子メールに含まれる関連 Web ページへのリンクをクリックします。 次に、適切なバージョンの Intel MPI の .tgz ファイルのダウンロード リンクをコピーします。 この記事は、Intel MPI バージョン 5.0.3.048 に基づきます。
* **OpenFOAM Source Pack** - [OpenFOAM Foundation のサイト](http://openfoam.org/download/2-3-1-source/)から Linux 用の OpenFOAM Source Pack ソフトウェアをダウンロードします。 この記事は、OpenFOAM-2.3.1.tgz としてダウンロードできる Source Pack Version 2.3.1 に基づいて説明しています。 Linux 計算ノードに対する OpenFOAM のアンパックとコンパイルについては、この記事で後述する手順に従ってください。
* **EnSight** (省略可) - OpenFOAM シミュレーションの結果を確認するには、可視化分析プログラムである [EnSight](https://www.ceisoftware.com/download/) をダウンロードしてインストールします。 ライセンスとダウンロードの詳細については、EnSight のサイトを参照してください。

## <a name="set-up-mutual-trust-between-compute-nodes"></a>コンピューティング ノードの相互の信頼関係をセットアップする
複数の Linux ノード上でクロス ノード ジョブを実行するには、すべてのノードが互いに信頼関係を持っている必要があります ( **rsh** または **ssh** によって)。 Microsoft HPC Pack IaaS デプロイ スクリプトを使用して HPC Pack クラスターを作成する場合は、指定した管理者アカウントに対して永続的な相互の信頼関係がスクリプトによって自動的にセットアップされます。 管理者以外のユーザーをクラスター ドメインに作成した場合は、それらのユーザーにジョブを割り当てるときに、ノード間に一時的な相互の信頼関係をセットアップする必要があります。ジョブ終了後、この関係は破棄します。 ユーザーごとに信頼を確立するには、HPC Pack で信頼関係に使用するクラスターに RSA キー ペアを指定します。

### <a name="generate-an-rsa-key-pair"></a>RSA キー ペアの生成
公開キーと秘密キーを含む RSA キー ペアは、Linux **ssh-keygen** コマンドを実行することで簡単に作成できます。

1. Linux コンピューターにログオンします。
2. 次のコマンドを実行します。
   
   ```
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > **Enter** キーを押して、コマンドが完了するまで既定の設定を使用します。 ここで、パスフレーズは入力しないでください。パスワードの入力を求めるメッセージが表示されたときには、**Enter** キーを押してください。
   > 
   > 
   
   ![RSA キー ペアの生成][keygen]
3. ディレクトリを ~/.ssh ディレクトリに変更します。 秘密キーは id_rsa に格納され、公開キーは id_rsa.pub に格納されます。
   
   ![公開キーと秘密キー][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>HPC Pack クラスターにキー ペアを追加する
1. HPC Pack の管理者アカウント (デプロイ スクリプトを実行したときにセットアップした管理者アカウント) を使用してヘッド ノードへのリモート デスクトップ接続を行います。
2. 標準的な Windows Server 手順を使用して、クラスターの Active Directory ドメインにドメイン ユーザー アカウントを作成します。 たとえば、ヘッド ノードで Active Directory ユーザーとコンピューター ツールを使用します。 この記事の例では、hpclab\hpcuser という名前のドメイン ユーザーを作成することを前提とします。
3. C:\cred.xml という名前のファイルを作成し、そこに RSA キーのデータをコピーします。 サンプルの cred.xml ファイルは、この記事の最後にあります。
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
4. コマンド プロンプトを開き、次のコマンドを入力して、hpclab\hpcuser アカウントの資格情報データを設定します。 **extendeddata** パラメーターを使用して、キー データ用に作成した C:\cred.xml ファイルの名前を渡します。
   
   ```
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   このコマンドは、出力なしで正常に終了します。 ジョブを実行するために必要なユーザー アカウントの資格情報を設定したら、cred.xml ファイルを安全な場所に保存するか、または削除します。
5. Linux ノードの 1 つに対して RSA キー ペアを生成した場合は、キーの使用が終わった後に、それらを削除することを忘れないでください。 HPC Pack は、既存の id_rsa ファイルまたは id_rsa.pub ファイルを見つけた場合、相互の信頼関係を設定しません。

> [!IMPORTANT]
> 共有クラスター上でクラスター管理者として Linux ジョブを実行することは、お勧めできません。管理者によって送信されたジョブが Linux ノードのルート アカウントで実行されることが、その理由です。 ただし、管理者以外のユーザーによって送信されたジョブは、ジョブ ユーザーと同じ名前を持つローカルの Linux ユーザー アカウントで実行されます。 この場合、HPC Pack は、ジョブに割り当てられたノード間に、この Linux ユーザーの相互の信頼関係を設定します。 ジョブを実行する前に Linux ノードに対して手動で Linux ユーザーをセットアップすることも、ジョブの送信時に HPC Pack がユーザーを自動的に作成するようにすることもできます。 HPC Pack でユーザーを作成した場合、ジョブの完了後にユーザーは HPC Pack によって削除されます。 セキュリティの脅威を軽減するために、HPC Pack は、ジョブの完了後にキーを削除します。
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Linux ノード用にファイル共有をセットアップする
ここでは、ヘッド ノード上のフォルダーで標準の SMB 共有をセットアップします。 Linux ノードが共通のパスを使用してアプリケーション ファイルにアクセスできるようにするには、Linux ノードに共有フォルダーをマウントします。 必要であれば他のファイル共有方法 (Azure Files 共有、NFS 共有など) を使用することもできます。特に Azure Files 共有は多くのシナリオに対応します。 「[Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](hpcpack-cluster.md)」に説明されているファイル共有の説明と手順をご覧ください。

1. ヘッド ノードにフォルダーを作成します。読み書き権限を設定して、フォルダーを全員で共有します。 たとえばヘッド ノードで、C:\OpenFOAM を \\\\SUSE12RDMA-HN\OpenFOAM として共有します。 ここで、*SUSE12RDMA-HN* はヘッド ノードのホスト名を表します。
2. Windows PowerShell ウィンドウを開き、次のコマンドを実行します。
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
   clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
   ```

最初のコマンドで「/openfoam」という名前のフォルダーが LinuxNodes グループのすべてのノードで作成されます。 2 つ目のコマンドでは、dir_mode ビットと file_mode ビットが "777" に設定された共有フォルダー //SUSE12RDMA-HN/OpenFOAM が Linux ノードにマウントされます。 コマンドの *username* と *password* は、ヘッド ノード上のユーザーの資格情報とする必要があります。

> [!NOTE]
> 2 つ目のコマンドの "\`" 記号は PowerShell のエスケープ記号です。 "\`," は "," (コンマ) がコマンドの一部であることを意味します。
> 
> 

## <a name="install-mpi-and-openfoam"></a>MPI と OpenFOAM のインストール
RDMA ネットワークで OpenFOAM を MPI ジョブとして実行するには、Intel MPI ライブラリを使って OpenFOAM をコンパイルする必要があります。 

最初に、いくつかの **clusrun** コマンドを実行して、Linux ノードに Intel MPI ライブラリ (まだインストールしていない場合) と OpenFOAM をインストールします。 先ほど構成したヘッド ノードの共有場所を使用して、Linux ノード間でインストール ファイルを共有します。

> [!IMPORTANT]
> ここで説明するインストールとコンパイルの手順は例です。 必要となるコンパイラやライブラリが正しくインストールされるようにするには、Linux システムの管理に関する知識がある程度必要です。 ご使用のバージョンの Intel MPI および OpenFOAM の特定の環境変数や設定を変更しなければならない場合があります。 詳細については、[Intel MPI Library for Linux のインストール ガイド](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)と [OpenFOAM Source Pack のインストール](http://openfoam.org/download/2-3-1-source/)に関するページを、使用環境に応じてご覧ください。
> 
> 

### <a name="install-intel-mpi"></a>Intel MPI のインストール
ダウンロードした Intel MPI インストール パッケージ (この例では l_mpi_p_5.0.3.048.tgz) をヘッド ノード上の C:\OpenFoam に保存し、Linux ノードが /openfoam からこのファイルにアクセスできるようにします。 そのうえで、 **clusrun** を実行してすべての Linux ノードに Intel MPI ライブラリをインストールします。

1. 次のコマンドで、各ノードの /opt/intel にインストール パッケージをコピーして抽出します。
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
   ```
2. Intel MPI Library をサイレント インストールするには、silent.cfg ファイルを使用します。 例については、この記事の最後にあるサンプル ファイルを参照してください。 このファイルを共有フォルダー /openfoam に格納します。 silent.cfg ファイルの詳細については、 [Intel MPI Library for Linux インストール ガイドのサイレント インストール](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall)に関するページを参照してください。
   
   > [!TIP]
   > silent.cfg ファイルは必ず、Linux の改行コード (CR LF ではなく LF のみ) でテキスト ファイルとして保存してください。 この手順により、スクリプトは Linux ノード上で適切に動作します。
   > 
   > 
3. サイレント モードで Intel MPI Library をインストールします。
   
   ```
   clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
   ```

### <a name="configure-mpi"></a>MPI の構成
テストを行うため、各 Linux ノード上の /etc/security/limits.conf に以下の行を追加してください。

    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


limits.conf ファイルを更新した後で Linux ノードを再起動します。 たとえば、次の **clusrun** コマンドを使用します。

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

再起動後、共有フォルダーが /openfoam としてマウントされていることを確認してください。

### <a name="compile-and-install-openfoam"></a>OpenFOAM のコンパイルとインストール
ダウンロードした OpenFOAM Source Pack のインストール パッケージ (この例では OpenFOAM-2.3.1.tgz) をヘッド ノード上の C:\OpenFoam に保存し、Linux ノードが /openfoam からこのファイルにアクセスできるようにします。 そのうえで **clusrun** コマンドを実行し、すべての Linux ノードで OpenFOAM をコンパイルします。

1. すべての Linux ノードに /opt/OpenFOAM フォルダーを作成し、そのフォルダーにソース パッケージをコピーして抽出します。
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
   ```
2. Intel MPI Library を使って OpenFOAM をコンパイルするにはまず、Intel MPI と OpenFOAM の両方について、いくつかの環境変数を設定します。 変数の設定には settings.sh という bash スクリプトを使用します。 例については、この記事の最後にあるサンプル ファイルを参照してください。 このファイルを共有フォルダー /openfoam に格納します (Linux の改行コードで保存すること)。 このファイルには、後で OpenFOAM ジョブを実行するときに使用する MPI と OpenFOAM のランタイムの設定が格納されています。
3. OpenFOAM をコンパイルするために必要な依存パッケージをインストールします。 そのために、Linux のディストリビューションによっては、最初にリポジトリの追加が必要になる場合があります。 次のような **clusrun** コマンドを実行します。
   
    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
   
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
   
    必要な場合は、個々の Linux ノードに SSH で接続し、コマンドが正しく動作することを確認します。
4. 以下のコマンドを実行して、OpenFOAM をコンパイルします。 コンパイル処理は完了までに少し時間がかかります。また、大量のログ情報が標準出力に生成されるので、**/interleaved** オプションを使用して、少しずつ出力が表示されるようにしてください。
   
   ```
   clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
   ```
   
   > [!NOTE]
   > コマンドの "\`" 記号は PowerShell のエスケープ記号です。 "\`&" は "&" がコマンドの一部であることを意味します。
   > 
   > 

## <a name="prepare-to-run-an-openfoam-job"></a>OpenFOAM ジョブを実行するための準備
ここでは、sloshingTank3D という MPI ジョブ (OpenFoam サンプルの 1 つ) を 2 つの Linux ノード上で実行するための準備を行います。 

### <a name="set-up-the-runtime-environment"></a>ランタイム環境のセットアップ
Linux ノードに MPI と OpenFOAM のランタイム環境をセットアップするには、ヘッド ノードの Windows PowerShell ウィンドウで次のコマンドを実行します (このコマンドは、SUSE Linux に対してのみ有効です。 )

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>サンプル データの準備
先ほど構成した (/openfoam としたマウント) ヘッド ノードの共有場所を使用して、Linux ノード間でファイルを共有します。

1. いずれかの Linux 計算ノードに SSH で接続します。
2. OpenFOAM ランタイム環境のセットアップが済んでいない場合は、次のコマンドで設定します。
   
   ```
   $ source /openfoam/settings.sh
   ```
3. sloshingTank3D サンプルを共有フォルダーにコピーし、そのフォルダーに移動します。
   
   ```
   $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/
   
   $ cd /openfoam/sloshingTank3D
   ```
4. このサンプルの既定のパラメーターを使用すると、実行に数十分かかる場合があります。そのため、実行時間を短縮するために、一部のパラメーターを変更することができます。 簡単な方法としては、system/controlDict ファイルで時間ステップ変数 deltaT と writeInterval を変更します。 このファイルには、時間の制御やソリューションデータの読み取りと書き込みに関連した入力データがすべて格納されています。 たとえば deltaT の値を 0.05 から 0.5 に、writeInterval の値を 0.05 から 0.5 に変更します。
   
   ![Modify step variables][step_variables]
5. system/decomposeParDict ファイル内の変数に適切な値を指定します。 この例では、それぞれ 8 個のコアを持つ Linux ノードを 2 つ使用しているため、numberOfSubdomains を 16 に、hierarchicalCoeffs の n を (1 1 16) に設定します。つまり、16 のプロセスで OpenFOAM を並列実行することになります。 詳細については、「[OpenFOAM User Guide: 3.4 Running applications in parallel (OpenFOAM ユーザー ガイド: 3.4 アプリケーションの並列実行)](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4)」をご覧ください。
   
   ![Decompose processes][decompose]
6. sloshingTank3D ディレクトリから次のコマンドを実行して、サンプル データを準備します。
   
   ```
   $ . $WM_PROJECT_DIR/bin/tools/RunFunctions
   
   $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict
   
   $ runApplication blockMesh
   
   $ cp 0/alpha.water.org 0/alpha.water
   
   $ runApplication setFields  
   ```
7. ヘッド ノードの C:\OpenFoam\sloshingTank3D にサンプル データ ファイルがコピーされていることがわかります  (C:\OpenFoam はヘッド ノード上の共有フォルダー)。
   
   ![Data files on the head node][data_files]

### <a name="host-file-for-mpirun"></a>mpirun のホスト ファイル
この手順では、 **mpirun** コマンドで使用するホスト ファイル (計算ノードのリスト) を作成します。

1. いずれかの Linux ノードの /openfoam に、hostfile という名前のファイルを作成します。このファイルには、すべての Linux ノードの /openfoam/hostfile でアクセスできます。
2. 実際の Linux ノードの名前をこのファイルに入力します。 この例では、ファイルに次の名前が含まれています。
   
   ```       
   SUSE12RDMA-LN1
   SUSE12RDMA-LN2
   ```
   
   > [!TIP]
   > ヘッド ノードの C:\OpenFoam\hostfile にこのファイルを作成することもできます。 この方法を選択した場合は、Linux の改行コード (CR LF ではなく LF のみ) を使ったテキスト ファイルとして保存してください。 これにより、スクリプトは Linux ノード上で適切に動作します。
   > 
   > 
   
   **Bash スクリプト ラッパー**
   
   Linux ノードが多数存在し、なおかつその一部でのみジョブを実行する場合、固定ホスト ファイルの使用はお勧めしません。どのノードがジョブに割り当てられるかを把握できないためです。 この場合は、**mpirun** の bash スクリプト ラッパーを作成し、ホスト ファイルを自動的に作成してください。 この記事の最後にある bash スクリプト ラッパーのサンプル (hpcimpirun.sh) を探し、/openfoam/hpcimpirun.sh として保存できます。 そのサンプル スクリプトでは、次の処理が実行されます。
   
   1. RDMA ネットワークを介して MPI ジョブを実行するために、 **mpirun**の環境変数に加え、いくつかのコマンド パラメーターを設定します。 この場合、次の変数が設定されます。
      
      * I_MPI_FABRICS=shm:dapl
      * I_MPI_DAPL_PROVIDER=ofa-v2-ib0
      * I_MPI_DYNAMIC_CONNECTION=0
   2. 環境変数 $CCP_NODES_CORES に従ってホスト ファイルを作成します。この変数は、ジョブを起動したときに HPC ヘッド ノードによって設定されます。
      
      $CCP_NODES_CORES の形式は、次のパターンに従います。
      
      ```
      <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
      ```
      
      各値の説明:
      
      * `<Number of nodes>` - このジョブに割り当てるノードの数。  
      * `<Name of node_n_...>` - このジョブに割り当てる各ノードの名前。
      * `<Cores of node_n_...>` - このジョブに割り当てるノードのコア数。
      
      たとえば、ジョブを実行するために 2 つのノードが必要である場合、$CCP_NODES_CORES は次のようになります。
      
      ```
      2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
      ```
   3. **mpirun** コマンドを呼び出し、コマンド ラインに 2 つのパラメーターを追加します。
      
      * `--hostfile <hostfilepath>: <hostfilepath>` - スクリプトによって作成されたホスト ファイルのパス。
      * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}` - HPC Pack のヘッド ノードによって設定される環境変数。このジョブに割り当てる合計コア数を格納します。 この場合、**mpirun** のプロセスの数を指定します。

## <a name="submit-an-openfoam-job"></a>OpenFOAM ジョブの送信
いよいよ HPC クラスター マネージャーでジョブを送信します。 いくつかのジョブ タスクでは、コマンド ラインにスクリプト hpcimpirun.sh を指定する必要があります。

1. クラスター ヘッド ノードに接続し、HPC クラスター マネージャーを開始します。
2. **[リソース管理]** で、Linux 計算ノードが**オンライン**状態にあることを確認します。 オンライン状態にない場合は、その計算ノードを選択し、 **[オンラインにする]**をクリックします。
3. **[ジョブ管理]** で、**[新しいジョブ]** をクリックします。
4. ジョブの名前を入力します (たとえば、 *sloshingTank3D*)。
   
   ![Job details][job_details]
5. **[ジョブ リソース]**で、リソースの種類として “ノード” を選択し、[最小] を 2 に設定します。 この例では、この構成により、それぞれ 8 個のコアを持つ 2 つの Linux ノード上でジョブが実行されます。
   
   ![[ジョブ リソース]][job_resources]
6. 左側のナビゲーションで **[Edit Tasks (タスクの編集)]** をクリックしてから、**[追加]** をクリックしてタスクをジョブに追加します。 次のコマンド ラインと設定を使用して、4 つのタスクをジョブに追加します。
   
   > [!NOTE]
   > OpenFOAM と MPI のランタイム環境は、`source /openfoam/settings.sh` を実行することによってセットアップされます。そのため、以下に示したすべてのタスクで、OpenFOAM コマンドの前にこのコマンドが呼び出されています。
   > 
   > 
   
   * **タスク 1**。 **decomposePar** を実行して、**interDyMFoam** を並列実行するためのデータ ファイルを生成します。
     
     * タスクに 1 つのノードを割り当てる
     * **[コマンド ライン]** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
     * **作業ディレクトリ** - /openfoam/sloshingTank3D
     
     次の図を参照してください。 残りのタスクも同様に構成します。
     
     ![Task 1 details][task_details1]
   * **タスク 2**。 **interDyMFoam** を並列実行してサンプルを計算します。
     
     * タスクに 2 つのノードを割り当てる
     * **[コマンド ライン]** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`
     * **作業ディレクトリ** - /openfoam/sloshingTank3D
   * **タスク 3**。 **reconstructPar** を実行して、各 processor_N_ ディレクトリにある time ディレクトリのセットを結合し、1 つのセットにします。
     
     * タスクに 1 つのノードを割り当てる
     * **[コマンド ライン]** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`
     * **作業ディレクトリ** - /openfoam/sloshingTank3D
   * **タスク 4**。 **foamToEnsight** を並列実行して、OpenFOAM の結果ファイルを EnSight 形式に変換し、case ディレクトリにある Ensight という名前のディレクトリにこの EnSight ファイルを格納します。
     
     * タスクに 2 つのノードを割り当てる
     * **[コマンド ライン]** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`
     * **作業ディレクトリ** - /openfoam/sloshingTank3D
7. これらのタスクに依存関係を追加します。タスクの昇順に追加してください。
   
   ![Task dependencies][task_dependencies]
8. **[送信]** をクリックして、このジョブを実行します。
   
   既定では、HPC Pack は、ログオンした現在のユーザー アカウントとしてジョブを送信します。 **[送信]**をクリックした後、ユーザー名とパスワードの入力を求めるダイアログ ボックスが表示される場合があります。
   
   ![ジョブの資格情報][creds]
   
   条件によっては、HPC Pack は前に入力されたユーザー情報を記憶していて、このダイアログ ボックスを表示しません。 このダイアログ ボックスがもう一度表示されるようにするには、コマンド プロンプトで次のコマンドを入力し、ジョブを送信します。
   
   ```
   hpccred delcreds
   ```
9. ジョブの実行には、サンプルに対して設定したパラメーターによって数十分から数時間かかります。 ヒート マップでは、Linux ノードでジョブが実行されていることがわかります。 
   
   ![ヒート マップ][heat_map]
   
   それぞれのノードで 8 個のプロセスが開始されています。
   
   ![Linux processes][linux_processes]
10. ジョブが終了したら、C:\OpenFoam\sloshingTank3D 下のフォルダーにあるジョブの結果と、C:\OpenFoam にあるログ ファイルを探します。

## <a name="view-results-in-ensight"></a>EnSight で結果を表示する
OpenFOAM ジョブの結果は、 [EnSight](https://www.ceisoftware.com/) を使用して必要に応じて可視化したり分析したりすることができます。 EnSight での可視化とアニメーションの詳細については、こちらの [ビデオ ガイド](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html)を参照してください。

1. ヘッド ノードに EnSight をインストールし、起動します。
2. C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case を開きます。
   
   ビューアーにタンクが表示されます。
   
   ![Tank in EnSight][tank]
3. **internalMesh** から **Isosurface** を作成し、**alpha_water** 変数を選択します。
   
   ![Create an isosurface][isosurface]
4. 前の手順で作成した **Isosurface_part** の色を設定します。 たとえば水色に設定します。
   
   ![Edit isosurface color][isosurface_color]
5. **[Parts]** パネルの **[walls]** を選択して **[walls]** から **[Iso-volume]** を作成し、ツール バーの **[Isosurfaces]** ボタンをクリックします。
6. ダイアログ ボックスで **[Type]** に **[Isovolume]** を選び、**[Isovolume range]** の [Min] を「0.5」に設定します。 Isovolume を作成するには、 **[Create with selected parts (選択したパーツで作成)]**をクリックします。
7. 前の手順で作成した **Iso_volume_part** の色を設定します。 たとえば濃い水色に設定します。
8. **walls**の色を設定します。 たとえば透明白色に設定します。
9. これで **[Play]** をクリックすると、シミュレーションの結果が表示されます。
   
    ![Tank result][tank_result]

## <a name="sample-files"></a>サンプル ファイル
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>PowerShell スクリプトによるクラスター デプロイ用の XML 構成ファイルのサンプル
 ```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>suse12rdmavnet</VNetName>
    <SubnetName>SUSE12RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>SUSE12RDMA-HN</VMName>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <NodeCount>2</NodeCount>
      <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

### <a name="sample-credxml-file"></a>サンプル cred.xml ファイル
```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```
### <a name="sample-silentcfg-file-to-install-mpi"></a>MPI をインストールするためのサンプル silent.cfg ファイル
```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no


```

### <a name="sample-settingssh-script"></a>サンプル settings.sh スクリプト
```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


### <a name="sample-hpcimpirunsh-script"></a>サンプル hpcimpirun.sh スクリプト
```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
    ${MPIRUN} $*
else
    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

    # Get every node name and write into the hostfile file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the mpirun with hostfile arg
    ${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]:media/hpcpack-cluster-openfoam/keygen.png
[keys]:media/hpcpack-cluster-openfoam/keys.png
[step_variables]:media/hpcpack-cluster-openfoam/step_variables.png
[data_files]:media/hpcpack-cluster-openfoam/data_files.png
[decompose]:media/hpcpack-cluster-openfoam/decompose.png
[job_details]:media/hpcpack-cluster-openfoam/job_details.png
[job_resources]:media/hpcpack-cluster-openfoam/job_resources.png
[task_details1]:media/hpcpack-cluster-openfoam/task_details1.png
[task_dependencies]:media/hpcpack-cluster-openfoam/task_dependencies.png
[creds]:media/hpcpack-cluster-openfoam/creds.png
[heat_map]:media/hpcpack-cluster-openfoam/heat_map.png
[tank]:media/hpcpack-cluster-openfoam/tank.png
[tank_result]:media/hpcpack-cluster-openfoam/tank_result.png
[isosurface]:media/hpcpack-cluster-openfoam/isosurface.png
[isosurface_color]:media/hpcpack-cluster-openfoam/isosurface_color.png
[linux_processes]:media/hpcpack-cluster-openfoam/linux_processes.png


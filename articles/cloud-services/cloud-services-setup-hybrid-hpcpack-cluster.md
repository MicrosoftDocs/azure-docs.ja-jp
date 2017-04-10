---
title: "Microsoft HPC Pack を使用したハイブリッド HPC クラスターのセットアップ | Microsoft Docs"
description: "Microsoft HPC Pack と Azure を使用して、小規模なハイブリッド ハイパフォーマンス コンピューティング (HPC) クラスターをセットアップする方法について説明します。"
services: cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: b11f3e1b-b9e1-4b0d-8455-6607b88814e9
ms.service: cloud-services
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: a0e80acad4e8db2177e699f90f7296c64b38e718
ms.openlocfilehash: da7102882b9dcf7db1285c0ec004ce1ad3859853


---
# <a name="set-up-a-hybrid-high-performance-computing-hpc-cluster-with-microsoft-hpc-pack-and-on-demand-azure-compute-nodes"></a>Microsoft HPC Pack とオンデマンド Azure のコンピューティング ノードを使用して、ハイブリッド ハイ パフォーマンス コンピューティング (HPC) クラスターをセットアップする
Microsoft HPC Pack 2012 R2 と Azure を使用して、小規模なハイブリッド ハイ パフォーマンス コンピューティング (HPC) クラスターをセットアップします。 クラスターは、オンプレミスのヘッド ノード (Windows Server オペレーティング システムと HPC Pack を実行するコンピューター) と、Azure クラウド サービス上に worker ロール インスタンスとしてオンデマンドでデプロイされるいくつかの計算ノードで構成されます。 コンピューティング ジョブを、ハイブリッド クラスター上で実行することができます。

![ハイブリッド HPC クラスター][Overview] 

このチュートリアルでは、「クラウド バースト」とも呼ばれる方法を紹介します。Azure 上で多くのコンピューティング処理を要するアプリケーションを実行するためにコンピューティング リソースをオンデマンドで使用する、拡張性に優れた方法です。

このチュートリアルは、コンピューティング クラスターまたは HPC Pack 2012 R2 を使用した経験がない読者を対象に作成されています。 これは、ハイブリッド コンピューティング クラスターを迅速にデプロイする方法のデモンストレーションを目的としています。 大規模なハイブリッド HPC Pack クラスターを運用環境にデプロイするため、または HPC Pack 2016 を使用するための考慮事項と手順については、[詳細なガイダンス](http://go.microsoft.com/fwlink/p/?LinkID=200493)を参照してください。 Azure 仮想マシンでの自動クラスター デプロイなど、HPC Pack に関するその他のシナリオについては、[Azure での Microsoft HPC Pack を使用した HPC クラスター オプション](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション** - Azure サブスクリプションがない場合は、 [無料アカウント](https://azure.microsoft.com/free/) を数分で作成することができます。
* **Windows Server 2012 R2 または Windows Server 2012 を実行するオンプレミスのコンピューター** - このコンピューターが HPC クラスターのヘッド ノードになります。 Windows Server をまだ実行していない場合は、 [評価版](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2)をダウンロードしてインストールできます。
  
  * コンピューターは Active Directory ドメインに結合されている必要があります。 Windows Server のフレッシュ インストールを使ったテスト シナリオでは、Active Directory ドメイン サービス サーバー ロールを追加し、ヘッド ノード コンピューターを新しいドメイン フォレストのドメイン コントローラーとして昇格させることができます (Windows Server のドキュメントを参照)。
  * HPC Pack をサポートするには、オペレーティング システムは、英語、日本語、中国語 (簡体字) のいずれかでインストールする必要があります。
  * 重要な更新プログラムがインストールされていることを確認します。
* **HPC Pack 2012 R2** - [ダウンロード](http://go.microsoft.com/fwlink/p/?linkid=328024) して、ヘッド ノード コンピューターに、またはネットワーク上にコピーします。 Windows Server のインストールと同じ言語を選択してください。

    >[!NOTE]
    > HPC Pack 2012 R2 ではなく HPC Pack 2016 を使用する場合は、追加の構成が必要になります。 [詳細なガイダンス](http://go.microsoft.com/fwlink/p/?LinkID=200493)を参照してください。
    > 
* **ドメイン アカウント** - このアカウントは、HPC Pack をインストールするヘッド ノード上で、ローカル管理者権限を使用して構成する必要があります。
* **TCP 接続 (ポート 443)** 。

## <a name="install-hpc-pack-on-the-head-node"></a>HPC Pack をヘッド ノードにインストールする
最初に、HPC クラスターのヘッド ノードにする、Windows Server が実行されているオンプレミスのコンピューターに Microsoft HPC Pack をインストールします。

1. ローカル管理者権限があるドメイン アカウントでヘッド ノードにログオンします。
2. HPC Pack のインストール ファイルから Setup.exe を実行して、HPC Pack のインストール ウィザードを開始します。
3. **[HPC Pack 2012 R2 Setup (HPC Pack 2012 R2 のセットアップ)]** 画面で、**[新たにインストールするか、新機能を既存のインストールに追加する]** をクリックします。
   
    ![HPC Pack 2012 セットアップ][install_hpc1]
4. **[Microsoft ソフトウェア ユーザー契約書]** ページで、**[次へ]** をクリックします。
5. **[インストールの種類の選択]** ページで、**[ヘッド ノードを作成することで新しい HPC クラスターを作成する]** をクリックし、**[次へ]** をクリックします。
   
    ![インストールの種類の選択][install_hpc2]
6. ウィザードによってインストール前のテストがいくつか実行されます。 すべてのテストが成功したら、 **[次へ]** on the **[次へ]** をクリックします。 失敗した場合は、表示された情報を確認して、必要な変更を環境に加えます。 テストをもう一度実行するか、必要に応じてインストール ウィザードをもう一度開始します。
   
    ![[次へ]][install_hpc3]
7. **[HPC DB 構成]** ページで、すべての HPC データベースについて **[ヘッド ノード]** が選択されていることを確認し、**[次へ]** をクリックします。
   
    ![DB 構成][install_hpc4]
8. ウィザードの残りのページは既定の設定のままにします。 **[必要なコンポーネントのインストール]** ページで、**[インストール]** をクリックします。
   
    ![[インストール]][install_hpc6]
9. インストールが完了したら、**[HPC クラスター マネージャーを起動する]** チェック ボックスをオフにし、**[完了]** をクリックします。 (HPC クラスター マネージャーは、後の手順で開始します)。
   
    ![[完了]][install_hpc7]

## <a name="prepare-the-azure-subscription"></a>Azure サブスクリプションを準備する
[Azure クラシック ポータル](https://manage.windowsazure.com) を使用して、Azure サブスクリプションで次の手順を実行します。 これは、後で、オンプレミスのヘッド ノードから Azure ノードをデプロイするために必要です。 詳しい手順については、以降のセクションで説明します。

* 管理証明書のアップロード (ヘッド ノードと Azure サービス間の接続をセキュリティ保護するために必要です)
* Azure ノード (worker ロール インスタンス) を実行する Azure クラウド サービスの作成
* Azure のストレージ アカウントの作成
  
  > [!NOTE]
  > Azure サブスクリプション ID を記録しておきます。これは後で必要になります。 この ID を見つけるには、クラシック ポータルで **[設定]** > **[サブスクリプション]** の順にクリックします。
  > 
  > 

### <a name="upload-the-default-management-certificate"></a>既定の管理証明書のアップロード
HPC Pack ではヘッド ノードに自己署名証明書 (既定の Microsoft HPC Azure 管理証明書) がインストールされます。これを Azure 管理証明書としてアップロードできます。 この証明書は、テスト目的および概念実証のデプロイ用に提供されます。

1. ヘッド ノード コンピューターから、 [Azure クラシック ポータル](https://manage.windowsazure.com)にサインインします。
2. **[設定]** > **[管理証明書]** の順にクリックします。
3. コマンド バーで、 **[アップロード]**をクリックします。
   
    ![証明書の設定][upload_cert1]
4. ヘッド ノードでファイル C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer を参照し、 **チェック マーク**のボタンをクリックします。
   
    ![証明書のアップロード][install_hpc10]

管理証明書のリストに **[既定の Microsoft HPC Azure 管理]** が表示されます。

### <a name="create-an-azure-cloud-service"></a>Azure クラウド サービスの作成
> [!NOTE]
> パフォーマンスを最適化するには、クラウド サービスとストレージ アカウント (後の手順で作成) を同じリージョンに作成します。
> 
> 

1. クラシック ポータルで、コマンド バーの **[新規]**をクリックします。
2. **[コンピューティング]** > **[クラウド サービス]** > **[簡易作成]** の順にクリックします。
3. クラウド サービスの URL を入力し、 **[クラウド サービスを作成する]**をクリックします。
   
    ![サービスの作成][createservice1]

### <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成
1. クラシック ポータルで、コマンド バーの **[新規]**をクリックします。
2. **[Data Services]** > **[ストレージ]** > **[簡易作成]** の順にクリックします。
3. アカウントの URL を入力し、 **[ストレージ アカウントの作成]**をクリックします。
   
    ![ストレージの作成][createstorage1]

## <a name="configure-the-head-node"></a>ヘッド ノードを構成する
HPC クラスター マネージャーを使用して Azure ノードをデプロイし、ジョブを送信するには、最初にクラスターの構成手順をいくつか実行する必要があります。

1. ヘッド ノードで、HPC クラスター マネージャーを開始します。 **[ヘッド ノードの選択]** ダイアログ ボックスが表示されたら、**[ローカル コンピューター]** をクリックします。 **[展開作業一覧]** が表示されます。
2. **[必須の展開タスク]** で、**[ネットワークの構成]** をクリックします。
   
    ![ネットワークの構成][config_hpc2]
3. ネットワーク構成ウィザードで、 **[企業ネットワークのみのすべてのノード]** (トポロジ 5) を選択します。
   
    ![トポロジ 5][config_hpc3]
   
   > [!NOTE]
   > これはデモンストレーションを目的とした最も単純な構成であり、ヘッド ノードには、Active Directory とインターネットに接続するための&1; つのネットワーク アダプターのみが必要です。 このチュートリアルでは、追加のネットワークを必要とするクラスター シナリオは扱いません。
   > 
   > 
4. ウィザードの残りのページは既定の設定のままにして **[次へ]** をクリックします。 **[レビュー]** タブで、**[構成]** をクリックして、ネットワーク構成を完了します。
5. **[展開作業一覧]**で、 **[インストール資格情報の提供]**を参照してください。
6. **[インストール資格情報]** ダイアログ ボックスで、HPC Pack のインストールに使用するドメイン アカウントの資格情報を入力します。 次に、 **[OK]**をクリックします
   
    ![[インストール資格情報]][config_hpc6]
   
   > [!NOTE]
   > HPC Pack サービスでは、インストール資格情報のみを使用して、ドメインに参加している計算ノードをデプロイします。 このチュートリアルで追加する Azure ノードはドメインに参加していません。
   > 
   > 
7. **[展開作業一覧]**で、 **[新しいノードの名前付けの構成]**を参照してください。
8. **[ノードの名前付け系列の指定]** ダイアログ ボックスで、既定の命名系のままにして、**[OK]** をクリックします。
   
    ![ノードの名前付け][config_hpc8]
   
   > [!NOTE]
   > この命名系では、ドメインに参加している計算ノードの名前のみが生成されます。 Azure worker ノードは自動的に命名されます。
   > 
   > 
9. **[展開作業一覧]**で、 **[ノード テンプレートの作成]**を参照してください。 ノード テンプレートを使用して、Azure ノードをクラスターに追加します。
10. ノード テンプレートの作成ウィザードで、次の操作を行います。
    
    a. **[ノード テンプレートの種類の選択]** ページで、**[Azure ノード テンプレート]** をクリックし、**[次へ]** をクリックします。
    
    ![ノード テンプレート][config_hpc10]
    
    b. **[次へ]** をクリックし、既定のテンプレート名をそのまま使用します。
    
    c. 次に、 **[既定の Microsoft HPC Azure 管理]** を選択します。 次に、**[管理証明書]** で **[参照]** をクリックして、**[Default HPC Azure Management (既定の HPC Azure 管理)]** を選択します。 その後、 **[次へ]**をクリックします。
    
    ![ノード テンプレート][config_hpc12]
    
    d. **[サービス情報の入力]** ページで、前の手順で作成したクラウド サービスとストレージ アカウントを選択します。 その後、 **[次へ]**をクリックします。
    
    ![ノード テンプレート][config_hpc13]
    
    e. ウィザードの残りのページは既定の設定のままにして **[次へ]** をクリックします。 **[レビュー]** タブで、**[作成]** をクリックしてノード テンプレートを作成します。
    
    > [!NOTE]
    > 既定では、Azure ノード テンプレートに、HPC クラスター マネージャーを使用して、ノードを手動で開始 (プロビジョニング) および停止するための設定が含まれています。 Azure ノードを自動的に開始および停止するスケジュールを構成することもできます。
    > 
    > 

## <a name="add-azure-nodes-to-the-cluster"></a>クラスターに Azure ノードを追加する
ここでは、ノード テンプレートを使用して、Azure ノードをクラスターに追加します。 ノードをクラスターに追加すると、ノードの構成情報が保存されるため、ノードをいつでもクラウド サービスのロール インスタンスとして開始 (プロビジョニング) することができます。 Azure ノードについてサブスクリプションに課金されるのは、クラウド サービスでロール インスタンスを実行した後だけです。

このチュートリアルでは、2 つの小規模ノードを追加します。

1. HPC クラスター マネージャーの **[ノード管理]** (最近のバージョンの HPC Pack では **[リソース管理]**) で、**[操作]** ウィンドウの **[ノードの追加]** をクリックします。
   
    ![[ノードの追加]][add_node1]
2. ノードの追加ウィザードの **[展開方法の選択]** ページで、**[Azure ノードを追加する]** をクリックして、**[次へ]** をクリックします。
   
    ![Azure ノードの追加][add_node1_1]
3. **[新規ノードの追加]** ページで、前に作成した Azure ノード テンプレート ("**既定の AzureNode テンプレート**" という名前が付いています) を選択します。 ノードの数に **[2]** を、ノードのサイズに **[小規模]** を選択し、**[次へ]** ボタンをクリックします。
   
    ![ノードの指定][add_node2]
   
    使用できるサイズの詳細については、「 [Cloud Services のサイズ](cloud-services-sizes-specs.md)」を参照してください。
4. **[ノードの追加ウィザードの完了]** ページで、**[完了]** をクリックします。
   
     HPC クラスター マネージャーに、**AzureCN-0001** と **AzureCN-0002** という名前の&2; つの Azure ノードが表示されます。 どちらも **[未展開]** 状態です。
   
    ![追加されたノード][add_node3]

## <a name="start-the-azure-nodes"></a>Azure ノードを開始する
Azure のクラスター リソースを使用する場合、HPC クラスター マネージャーを使用して、Azure ノードを開始 (プロビジョニング) してオンラインにします。

1. HPC クラスター マネージャーの **[ノード管理]** (最近のバージョンの HPC Pack では **[リソース管理]**) で、どちらか&1; つまたは両方のノードをクリックして、**[操作]** ウィンドウの **[開始]** をクリックします。
   
   ![ノードの開始][add_node4]
2. **[Windows Azure ノードの起動]** ダイアログ ボックスで、**[開始]** をクリックします。
   
    ![ノードの開始][add_node5]
   
    ノードが **[プロビジョニング]** 状態に移行します。 プロビジョニング ログを表示して、プロビジョニング進捗状況を確認します。
   
    ![ノードのプロビジョニング][add_node6]
3. 数分後、Azure ノードのプロビジョニングが完了し、ノードが **[オフライン]** 状態になります。 この状態では、ロール インスタンスは実行中ですが、クラスター ジョブは受け入れられません。
4. ロール インスタンスが実行中であることを確認するために、[クラシック ポータル](https://manage.windowsazure.com)で、**[クラウド サービス]** > *<クラウド サービスの名前>* > **[インスタンス]** の順にクリックします。
   
    ![実行中のインスタンス][view_instances1]
   
    2 つの worker ロール インスタンスがサービスで実行中であることがわかります。 HPC Pack では、ヘッド ノードと Azure 間の通信を処理するために、2 つの **HpcProxy** インスタンス (サイズは M) も自動的にデプロイされます。
5. Azure ノードをオンラインにしてクラスター ジョブを実行するには、ノードを選択し右クリックして、 **[オンラインにする]**をクリックします。
   
    ![オフラインのノード][add_node7]
   
    ノードが **[オンライン]** 状態であることが表示されます。

## <a name="run-a-command-across-the-cluster"></a>クラスター全体にコマンドを実行する
インストールを確認するには、HPC Pack の **clusrun** コマンドを使用して、1 つ以上のクラスター ノードでコマンドまたはアプリケーションを実行します。 簡単な例として、 **clusrun** を使用して、Azure ノードの IP 構成を取得する手順を紹介します。

1. ヘッド ノードで、コマンド プロンプトを開きます。
2. 次のコマンドを入力します。
   
    `clusrun /nodes:azurecn* ipconfig`
3. 次のような出力が表示されます。
   
    ![clusrun][clusrun1]

## <a name="run-a-test-job"></a>テスト ジョブを実行する
ここで、ハイブリッド クラスター上で実行するテスト ジョブを送信します。 この例は、ごく単純な "パラメーター スイープ" ジョブ (本質的に並列なコンピューティングの一種) です。 この例では、 **set /a** コマンドを使用してそれ自体に整数を追加するサブタスクを実行します。 クラスター内のすべてのノードが、1 ～ 100 の整数に関するサブタスクを完了します。

1. HPC クラスター マネージャーの **[ジョブ管理]** で、**[操作]** ウィンドウの **[新しいパラメーター スイープ ジョブ]** をクリックします。
   
    ![新しいジョブ][test_job1]
2. **[新しいパラメーター スイープ ジョブ]** ダイアログ ボックスの **[コマンド ライン]** で、「`set /a *+*`」と入力します (表示されている既定のコマンド ラインを上書きしてください)。 残りの設定については既定値のままにして、 **[送信]** をクリックしてジョブを送信します。
   
    ![パラメーター スイープ][param_sweep1]
3. ジョブが完了したら、 **[マイ スイープ タスク]** ジョブをダブルクリックします。
4. **[タスクの表示]**をクリックして、サブタスクの計算された出力を表示します。
   
    ![タスクの結果][view_job361]
5. サブタスクの計算を実行したノードを表示するには、**[割り当てたノード]** をクリックします。 (クラスターによっては異なるノード名が表示されます)。
   
    ![タスクの結果][view_job362]

## <a name="stop-the-azure-nodes"></a>Azure ノードを停止する
クラスターを試した後は、Azure ノードを停止して、アカウントへの不必要な課金を避けることができます。 ノードを停止するとクラウド サービスが停止し、Azure ロール インスタンスが削除されます。

1. HPC クラスター マネージャーの **[ノード管理]** (最近のバージョンの HPC Pack では **[リソース管理]**) で、両方の Azure ノードを選択します。 次に、**[操作]** ウィンドウで **[停止]** をクリックします。
   
    ![ノードの停止][stop_node1]
2. **[Windows Azure ノードの起動]** ダイアログ ボックスで、**[停止]** をクリックします。
   
    ![ノードの停止][stop_node2]
3. ノードが **[停止中]** 状態に移行します。 数分後、HPC クラスター マネージャーに、ノードが **[未展開]**状態であることが表示されます。
   
    ![未配置のノード][stop_node4]
4. ロール インスタンスが Azure で実行されていないことを確認するために、[クラシック ポータル](https://manage.windowsazure.com)で、**[クラウド サービス]** > *<クラウド サービスの名前>* > **[インスタンス]** の順にクリックします。 運用環境にはインスタンスはデプロイされません。
   
    ![インスタンスなし][view_instances2]
   
    このチュートリアルはこれで終わりです。

## <a name="next-steps"></a>次のステップ
* [HPC Pack](https://technet.microsoft.com/library/cc514029) のドキュメントを確認してください。
* ハイブリッド HPC Pack クラスターの大規模なデプロイを設定するには、 [Microsoft HPC Pack を使用した Azure worker ロール インスタンスへのバースト](http://go.microsoft.com/fwlink/p/?LinkID=200493)に関するページを参照してください。
* Azure Resource Manager テンプレートの使用など、Azure で HPC Pack クラスターを作成するその他の方法については、[Azure での Microsoft HPC Pack を使用した HPC クラスター オプション](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。
* Azure での大規模なコンピューティングと HPC クラウド ソリューションの範囲の詳細については、「 [Azure における大規模なコンピューティング: バッチとハイ パフォーマンス コンピューティング (HPC) に関するテクニカル リソース](../batch/big-compute-resources.md) 」を参照してください。

[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc2.png
[install_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc3.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
[upload_cert1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/upload_cert1.png
[createstorage1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createstorage1.png
[createservice1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createservice1.png
[config_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc2.png
[config_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc3.png
[config_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc6.png
[config_hpc8]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc8.png
[config_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc10.png
[config_hpc12]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc12.png
[config_hpc13]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc13.png
[add_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1.png
[add_node1_1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1_1.png
[add_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node2.png
[add_node3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node3.png
[add_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node4.png
[add_node5]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node5.png
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node2.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png



<!--HONumber=Feb17_HO1-->



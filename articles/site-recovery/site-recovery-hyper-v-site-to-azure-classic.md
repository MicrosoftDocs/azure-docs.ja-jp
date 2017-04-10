---
title: "クラシック ポータルで Azure に VMware VM をレプリケートする | Microsoft Docs"
description: "この記事では、コンピューターが VMM クラウドで管理されていない場合に Hyper-V 仮想マシンを Azure にレプリケートする方法について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 3f4c4483-e3dd-495a-bd02-c16e9e28c88d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/21/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 2b5e2c2d5cc572ba951a61fd1eb78613c43ec453
ms.lasthandoff: 03/25/2017


---
# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-without-vmm-with-azure-site-recovery"></a>Azure Site Recovery を利用し、オンプレミス Hyper-V 仮想マシンと Azure (VMM なし) の間で複製する
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-hyper-v-site-to-azure.md)
> * [PowerShell - Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
> * [クラシック ポータル](site-recovery-hyper-v-site-to-azure-classic.md)
>
>

この記事では、Azure Portal で [Azure Site Recovery](site-recovery-overview.md) サービスを使用して、オンプレミスの Hyper-V 仮想マシンを Azure にレプリケートする方法について説明します。 このシナリオでは、Hyper-V サーバーは VMM クラウドで管理されていません。

コメントはこの記事の末尾に投稿し、技術的な質問は [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="site-recovery-in-the-azure-portal"></a>Azure ポータルの Site Recovery

Azure には、リソースの作成と操作に関して、[Azure Resource Manager とクラシックの](../resource-manager-deployment-model.md) 2 種類のデプロイメント モデルがあります。 Azure のポータルも、Azure クラシック ポータルと Azure Portal の 2 種類があります。

この記事では、従来のポータルでデプロイする方法について説明します。 既存のコンテナーを保持する場合は、クラシック ポータルを使用できます。 クラシック ポータルを使用して、新しいコンテナーを作成することはできません。

## <a name="site-recovery-in-your-business"></a>ビジネス用途の Site Recovery

組織には、予定されたダウンタイムと予定外のダウンタイムの間にアプリとデータの実行と利用可能な状態を維持し、できるだけ早く通常の動作状態に復旧させる方法を決定する BCDR の戦略が必要です。 Site Recovery でできることを次に示します。

* Hyper-V VM で実行されているビジネス アプリのオフサイト保護。
* レプリケーション、フェールオーバー、復旧の一元的な設定、管理、監視。
* Azure への簡単なフェールオーバーと、Azure からオンプレミス サイトの Hyper-V ホスト サーバーへのフェールバック (復元)。
* 階層化されたアプリケーション ワークロードをまとめてフェールオーバーできるように複数の VM が含まれた復旧計画。

## <a name="azure-prerequisites"></a>Azure の前提条件
* [Microsoft Azure](https://azure.microsoft.com/) のアカウントが必要です。 アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。
* レプリケートしたデータを格納するには Azure ストレージ アカウントが必要です。 アカウントでは geo レプリケーションを有効にする必要があります。 アカウントは Azure Site Recovery コンテナーと同じリージョンにあり、同じサブスクリプションに関連付けられている必要があります。 [Azure ストレージについてはこちらを参照してください。](../storage/storage-introduction.md) [新しい Azure Portal](../storage/storage-create-storage-account.md) を使用して作成したストレージ アカウントのリソース グループ間での移動はサポートされていません。
* プライマリ サイトからフェールオーバーするとき、Azure 仮想マシンがネットワークに接続されるように、Azure 仮想ネットワークが必要になります。

## <a name="hyper-v-prerequisites"></a>Hyper-V の前提条件
* ソースのオンプレミス サイトには、Hyper-V の役割がインストールされているか、**Microsoft Hyper-V Server 2012 R2** が付属した **Windows Server 2012 R2** を実行する 1 つ以上のサーバーが必要になります。 このサーバーの要件:
* 1 つまたは複数の仮想マシンが含まれています。
* 直接、またはプロキシを経由して、インターネットに接続します。
* サポート技術情報 [2961977](https://support.microsoft.com/en-us/kb/2961977 "KB2961977") に説明が記載されている修正プログラムを実行しています。

## <a name="virtual-machine-prerequisites"></a>仮想マシンの前提条件
保護対象の仮想マシンは、 [Azure 仮想マシンの要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠している必要があります。

## <a name="provider-and-agent-prerequisites"></a>プロバイダーとエージェントの前提条件
Azure Site Recovery のデプロイの一部として、各 Hyper-V サーバーで Azure Site Recovery プロバイダーと Azure Recovery Services エージェントをインストールします。 以下の点に注意してください。

* 常に最新バージョンのプロバイダーとエージェントを実行することが推奨されます。 これらは Site Recovery ポータルで入手できます。
* コンテナー内のすべての Hyper-V サーバーで、プロバイダーとエージェントが同じバージョンである必要があります。
* サーバーで実行されているプロバイダーは、インターネットで Site Recovery に接続します。 このことは、プロキシを使用せずに、現在 Hyper-V サーバーで構成されているプロキシ設定を使用して、またはプロバイダーのインストール中に構成するカスタム プロキシ設定を使用して行えます。 使用するプロキシ サーバーがこれらの URL にアクセスし、Azure に接続できることを確認する必要があります。

  * *.accesscontrol.windows.net
  * *.backup.windowsazure.com
  * *.hypervrecoverymanager.windowsazure.com
  * *.store.core.windows.net      
  * *.blob.core.windows.net
  - https://www.msftncsi.com/ncsi.txt
  - time.windows.com
  - time.nist.gov
* また、「 [Azure Datacenter の IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653) 」に記載されている IP アドレスと HTTPS (443) プロトコルを許可します。 使用を計画している Azure リージョンの IP の範囲と米国西部の IP の範囲を許可する必要があります。

次の図は、Site Recovery でオーケストレーションやレプリケーションに使用される、さまざまな通信チャネルと通信ポートを示しています。

![B2A Topology](./media/site-recovery-hyper-v-site-to-azure-classic/b2a-topology.png)

## <a name="step-1-create-a-vault"></a>ステップ 1: コンテナーの作成
1. [管理ポータル](https://portal.azure.com)にサインインします。
2. **[Data Services]**  >  **[Recovery Services]** の順に展開し、**[Site Recovery コンテナー]** をクリックします。
3. **[新規作成]**  >  **[簡易作成]** の順にクリックします。
4. **[名前]**ボックスに、コンテナーを識別する表示名を入力します。
5. **[リージョン]**ボックスで、コンテナーのリージョンを選択します。 サポートされているリージョンを確認するには、「 [Azure Site Recovery Pricing Details (Azure Site Recovery の料金の詳細)](https://azure.microsoft.com/pricing/details/site-recovery/)」で利用可能地域をご覧ください。
6. **[コンテナーの作成]**をクリックします。

    ![新しいコンテナー](./media/site-recovery-hyper-v-site-to-azure-classic/vault.png)

ステータス バーを確認して、コンテナーが正常に作成されたことを確かめます。 メイン [Recovery Services] ページで、コンテナーは **[アクティブ]** と表示されています。

## <a name="step-2-create-a-hyper-v-site"></a>ステップ 2: HYPER-V サイトを作成します。
1. [Recovery Services] ページで、コンテナーをクリックして [クイック スタート] ページを開きます。 [クイック スタート] は、アイコンを使っていつでも開くことができます。

    ![クイック スタート](./media/site-recovery-hyper-v-site-to-azure-classic/quick-start-icon.png)
2. ドロップダウン リストで、 **[オンプレミスの Hyper-V サイトと Microsoft Azure 間]**を選択します。

    ![Hyper-V のサイトのシナリオ](./media/site-recovery-hyper-v-site-to-azure-classic/select-scenario.png)
3. **[Hyper-V サイトの作成]** で、**[Hyper-V サイトの作成]** をクリックします。 サイトの名前を指定して、保存します。

    ![Hyper-V サイト](./media/site-recovery-hyper-v-site-to-azure-classic/create-site.png)

## <a name="step-3-install-the-provider-and-agent"></a>ステップ 3: プロバイダーとエージェントのインストール
保護する VM のある各 Hyper-V サーバーにプロバイダーとエージェントをインストールします。

Hyper-V クラスターをインストールする場合は、フェールオーバー クラスター内の各ノードでステップ 5 ～ 11 を実行します。 すべてのノードが登録されて、保護が有効になると、クラスター内のノード間で移行する場合でも仮想マシンは保護されます。

1. **[Hyper-V サーバーの準備]** で、**[登録キーのダウンロード]** ファイルをクリックします。
2. **[登録キーのダウンロード]** ページで、サイトの横にある **[ダウンロード]** をクリックします。 Hyper-V サーバーが簡単にアクセスできる安全な場所に、キーをダウンロードします。 キーは生成後 5 日間有効です。

    ![登録キー](./media/site-recovery-hyper-v-site-to-azure-classic/download-key.png)
3. **[プロバイダーのダウンロード]** をクリックして最新のバージョンを取得します。
4. コンテナーに登録する各 Hyper-V サーバーで、ファイルを実行します。 このファイルにより、2 つのコンポーネントがインストールされます。
   * **Azure Site Recovery プロバイダー**- Hyper-V サーバーと Azure Site Recovery ポータルとの間で、通信と調整を処理します。
   * **Azure Recovery Services エージェント**- ソースの Hyper-V サーバーで稼働する仮想マシンと Azure ストレージの間のデータ転送を処理します。
5. **[Microsoft Update]** で、アップデートの内容を設定できます。 この設定を有効にすることで、Microsoft Update のポリシーに従って、プロバイダーとエージェントのアップデートがインストールされます。

    ![Microsoft 更新プログラム](./media/site-recovery-hyper-v-site-to-azure-classic/provider1.png)
6. **[インストール]** で、プロバイダーとエージェントを Hyper-V サーバーにインストールする場所を指定します。

    ![インストール場所](./media/site-recovery-hyper-v-site-to-azure-classic/provider2.png)
7. インストール後は、セットアップを続行し、サーバーをコンテナーに登録します。
8. **[コンテナーの設定]** ページで **[参照]** をクリックし、キー ファイルを選択します。 Azure Site Recovery のサブスクリプション、コンテナー名、Hyper-V サーバーが属している Hyper-V サイトを指定します。

    ![サーバー登録](./media/site-recovery-hyper-v-site-to-azure-classic/provider8.PNG)
9. **[インターネット接続]** ページで、プロバイダーが Azure Site Recovery に接続する方法を指定します。 **[既定のシステム プロキシ設定を使用]** を選択して、サーバー上に構成されている既定のインターネット接続設定を使用します。 値を指定しない場合は、既定の設定が使用されます。

   ![インターネット設定](./media/site-recovery-hyper-v-site-to-azure-classic/provider7.PNG)
10. サーバーのコンテナーへの登録が開始されます。

    ![サーバー登録](./media/site-recovery-hyper-v-site-to-azure-classic/provider15.PNG)
11. 登録が完了すると、Azure Site Recovery により Hyper-V サーバーからメタデータが取得され、コンテナーの **[サーバー]** ページにある **[Hyper-V サイト]** タブにサーバーが表示されます。

### <a name="install-the-provider-from-the-command-line"></a>コマンドラインからプロバイダーをインストールする
別の方法としては、コマンドラインから Azure Site Recovery プロバイダーをインストールできます。 Windows 2012 R2 Server Core を実行しているコンピューターにプロバイダーをインストールする場合、この方法を使用する必要があります。 コマンド ラインから次のように実行します。

1. プロバイダーのインストール ファイルと登録キーをフォルダーにダウンロードします。 たとえば、C:\ASR です。
2. 管理者としてコマンド プロンプトを開き、次のように入力します。

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. 次のコマンドを実行し、プロバイダーをインストールします。

        C:\ASR> setupdr.exe /i
4. 次のコマンドを実行し、登録を完了します。

        CD C:\Program Files\Microsoft Azure Site Recovery Provider
        C:\Program Files\Microsoft Azure Site Recovery Provider\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

パラメーターは次のとおりです。

* **/Credentials**: ダウンロードした登録キーの場所を指定します。
* **/FriendlyName**: Hyper-V ホスト サーバーを識別する名前を指定します。 この名前はポータルに表示されます。
* **/EncryptionEnabled**: 省略可能です。 Azure でレプリカ仮想マシンを暗号化するかどうかを指定します (保存時の暗号化)。
* **/proxyAddress**; **/proxyport**; **/proxyUsername**; **/proxyPassword**: 省略可能です。 カスタム プロキシを使用する場合、あるいは既存のプロキシで認証が必要な場合、プロキシ パラメーターを指定します。

## <a name="step-4-create-an-azure-storage-account"></a>ステップ 4: Azure のストレージ アカウントを作成する
* **[リソースの準備]** で、**[ストレージ アカウントの作成]** を選択し、Azure ストレージ アカウントを作成します (保有していない場合)。 アカウントでは geo レプリケーションを有効にする必要があります。 アカウントは Azure Site Recovery コンテナーと同じリージョンにあり、同じサブスクリプションに関連付けられている必要があります。

    ![[ストレージ アカウントの作成]](./media/site-recovery-hyper-v-site-to-azure-classic/create-resources.png)

> [!NOTE]
> 1. [新しい Azure ポータル](../storage/storage-create-storage-account.md) を使用して作成したストレージ アカウントをリソース グループ間で移動する操作はサポートされていません。
> 2. Site Recovery のデプロイ用のストレージ アカウントについては、同じサブスクリプション内のリソース グループ間またはサブスクリプション間での[ストレージ アカウントの移行](../azure-resource-manager/resource-group-move-resources.md)はサポートされていません。
>

## <a name="step-5-create-and-configure-protection-groups"></a>ステップ 5: 保護グループの作成と構成
保護グループは、同じ保護設定を使用して保護する仮想マシンの論理グループです。 保護設定を保護グループに適用すると、これらの設定はグループに追加するすべての仮想マシンに適用されます。

1. **[保護グループの作成と構成]** で、**[保護グループの作成]** をクリックします。 前提条件が満たされていない場合は、メッセージが発行され、 **[詳細の表示]** をクリックすると、詳細情報が表示されます。
2. **[保護グループ]** タブで、保護グループを追加します。 名前、ソースの Hyper-V サイト、ターゲットの **Azure**、Azure Site Recovery のサブスクリプション名、Azure ストレージ アカウントを指定します。

    ![保護グループ](./media/site-recovery-hyper-v-site-to-azure-classic/protection-group.png)
3. **[レプリケーション設定]** で **[コピーの頻度]** を設定し、ソースとターゲット間でデータの差分を同期する頻度を指定します。 30 秒、5 分間、または 15 分に設定できます。
4. **[保持する復旧ポイントの保持]** で、何時間分の復旧履歴を格納するかを指定します。
5. **[アプリケーションの整合性スナップショットの頻度]** で、ボリューム シャドウ コピー サービス (VSS) を使用して、スナップショットの取得時にアプリケーションの整合性を保証するスナップショットを取得するかどうかを指定できます。 既定ではこれらは取得されません。 この値は、構成する追加の復旧ポイント数よりも少ない値にしてください。 これは、仮想マシンが、Windows オペレーティング システムを実行している場合にのみサポートされます。
6. **[初期レプリケーションの開始時刻]** に、保護グループ内の仮想マシンの初期レプリケーションを Azure に送信する時刻を指定します。

    ![保護グループ](./media/site-recovery-hyper-v-site-to-azure-classic/protection-group2.png)

## <a name="step-6-enable-virtual-machine-protection"></a>ステップ 6: 仮想マシンの保護の有効化
仮想マシンを保護グループに追加し、保護を有効にします。

> [!NOTE]
> 静的 IP アドレスを持ち、Linux を実行している VM は保護できません。
>
>

1. 保護グループの **[コンピューター]** タブで、**[Add virtual machines to protection groups to enable protection (仮想マシンを保護グループに追加して保護を有効にする)]** をクリックします。
2. **[仮想マシンの保護の有効化]** ページで、保護する仮想マシンを選択します。

    ![[仮想マシンの保護の有効化]](./media/site-recovery-hyper-v-site-to-azure-classic/add-vm.png)

    保護の有効化ジョブが開始されます。 ジョブの進捗状況は **[ジョブ]** タブで追跡できます。 保護の最終処理のジョブが実行されると、仮想マシンは、フェールオーバーを実行できる状態になります。
3. 保護の設定後、次のことを行えます。

   * **[保護された項目]**  >  **[保護グループ]**  >  *保護グループ名*  >  **[Virtual Machines]** の順に移動し、仮想マシンを表示します。**[プロパティ]** タブでマシンの詳細をドリルダウンできます。
   * **[保護された項目]**  >  **[保護グループ]**  >  *保護グループ名*  >  **[Virtual Machines]** *仮想マシン名*  >  **[構成]** の順に移動し、仮想マシンのフェールオーバーのプロパティを構成します。 利用できる構成は、次のとおりです。

     * **名前**: Azure の仮想マシンの名前。
     * **サイズ**: フェールオーバーする仮想マシンのターゲット サイズ。

       ![仮想マシンのプロパティの構成](./media/site-recovery-hyper-v-site-to-azure-classic/vm-properties.png)
   * *[保護された項目]** > **[保護グループ]** > *[<保護グループ名>]* > **[Virtual Machines]***[<仮想マシン名>]* > **[構成]** の順に移動し、次に示す追加の仮想マシンの設定を構成します:

     * **ネットワーク アダプター**: ネットワーク アダプターの数は、ターゲット仮想マシンに指定したサイズによって異なります。 仮想マシンのサイズでサポートされている NIC の数については、「 [仮想マシン サイズの仕様](../virtual-machines/virtual-machines-linux-sizes.md) 」を参照してください。

       仮想マシンのサイズを変更し、設定を保存すると、次回 **[構成]** ページを開くときに、ネットワーク アダプターの数が変更されます。 ターゲット仮想マシンのネットワーク アダプターの数は、ソース仮想マシン上のネットワーク アダプターの最小数と、選択した仮想マシンのサイズでサポートされているネットワーク アダプターの最大数です。 次に説明します。

       * ソース マシン上のネットワーク アダプターの数が、ターゲット マシンのサイズに許可されているアダプターの数以下の場合、ターゲットのアダプターの数は、ソースと同じになります。
       * ソース仮想マシン用のアダプターの数が、ターゲットのサイズに許可されている数を超える場合は、ターゲットの最大サイズが使用されます。
       * たとえば、ソース マシンに 2 つのネットワーク アダプターがあり、ターゲット マシンのサイズが 4 つをサポートしている場合は、ターゲット マシンのアダプターの数は、2 つになります。 ソース マシンに 2 つのアダプターがあるが、サポートされているターゲット サイズで 1 つしかサポートしていない場合、ターゲット マシンのアダプターの数は 1 つだけになります。

     * **Azure ネットワーク**: 仮想マシンのフェールオーバー先のネットワークを指定します。 仮想マシンに複数のネットワーク アダプターがある場合は、すべてのアダプターが、同じ Azure ネットワークに接続する必要があります。
     * **サブネット** : 仮想マシン上の各ネットワーク アダプターに対して、フェールオーバー後にマシンが接続する Azure ネットワーク内のサブネットを選択します。
     * **ターゲット IP アドレス**: 静的 IP アドレスを使用するようソース仮想マシンのネットワーク アダプターが構成されている場合、フェールオーバー後にターゲット仮想マシンの IP アドレスが同じになるよう、ターゲット仮想マシンの IP アドレスを指定できます。  IP アドレスを指定しない場合、フェールオーバー時に、使用可能なアドレスが割り当てられます。 使用中のアドレスを指定すると、フェールオーバーは失敗します。

     > [!NOTE]
     > Site Recovery のデプロイ用のネットワークでは、同じサブスクリプション内のリソース グループ間またはサブスクリプション間での[ネットワークの移行](../azure-resource-manager/resource-group-move-resources.md)はサポートされていません。
     >

     ![仮想マシンのプロパティの構成](./media/site-recovery-hyper-v-site-to-azure-classic/multiple-nic.png)




## <a name="step-7-create-a-recovery-plan"></a>ステップ 7: 復旧計画の作成
デプロイをテストするために、単一の仮想マシンに対して、または 1 つ以上の仮想マシンを含む復旧計画に対してテスト フェールオーバーを実行できます。 [こちら](site-recovery-create-recovery-plans.md) を参照してください。

## <a name="step-8-test-the-deployment"></a>ステップ 8: デプロイのテスト
Azure へのテスト フェールオーバーを実行する方法は 2 つあります。

* **Azure ネットワークを使用しないテスト フェールオーバー**— この種のテスト フェールオーバーでは、仮想マシンが Azure で正しく動作することを確認します。 フェールオーバー後、仮想マシンはどの Azure ネットワークにも接続しません。
* **Azure ネットワークを使用するテスト フェールオーバー**— この種のフェールオーバーでは、レプリケーション環境全体が正しく動作することと、フェールオーバーされた仮想マシンが指定したターゲットの Azure ネットワークに接続されることを確認します。 テスト フェールオーバーでは、テストの仮想マシンのサブネットはレプリカの仮想マシンのサブネットに基づいて決定されることに注意してください。 これは、レプリカの仮想マシンのサブネットが、ソースの仮想マシンのサブネットに基づいている通常のレプリケーションとは異なります。

Azure ネットワークを指定せずにテスト フェールオーバーを実行する場合、何も用意する必要がありません。

ターゲット Azure ネットワークを指定してテスト フェールオーバーを実行するには、Azure 運用ネットワークから独立した新しい Azure ネットワークを作成する必要があります (Azure で新しいネットワークを作成する場合の既定の動作)。 詳細については、 [テスト フェールオーバーの実行に関するページ](site-recovery-failover.md) を参照してください。

レプリケーションとネットワークのデプロイを完全にテストするには、複製された仮想マシンが予想どおり機能するようにインフラストラクチャを設定する必要があります。 これを行うには、たとえば、DNS を利用し、仮想マシンをドメイン コントローラーとして設定し、それを Site Recovery を利用して Azure に複製し、テスト フェールオーバーを実行してテスト ネットワークでそれを作成します。  [こちら](site-recovery-active-directory.md#test-failover-considerations) を参照してください。

次のようにテスト フェールオーバーを実行します。

> [!NOTE]
> Azure へのフェールオーバーを実行するときに最適なパフォーマンスを得るには、保護されたマシンに Azure エージェントをインストールしておきます。 これは、ブートの速度を上げ、問題が発生した場合の診断にも役立ちます。 Linux エージェントは[こちら](https://github.com/Azure/WALinuxAgent)から、Windows エージェントは[こちら](http://go.microsoft.com/fwlink/?LinkID=394789)から入手できます
>
>

1. **[復旧計画]** タブで、計画を選択し、**[テスト フェールオーバー]** をクリックします。
2. **[テスト フェールオーバーの確認]** ページで、**[なし]** または特定の Azure ネットワークを選択します。  **[なし]** を選択した場合、テスト フェールオーバーでは、仮想マシンが Azure に正しくレプリケートされたかどうかは確認されますが、レプリケーションのネットワーク構成は確認されないことに注意してください。

    ![[テスト フェールオーバー]](./media/site-recovery-hyper-v-site-to-azure-classic/test-nonetwork.png)
3. **[ジョブ]** タブで、フェールオーバーの進行状況を追跡できます。 仮想マシンのテスト レプリカも Azure ポータルで確認できます。 オンプレミスのネットワークから、仮想マシンへのアクセスをセットアップすると、仮想マシンへのリモート デスクトップ接続を開始できます。
4. フェールオーバーが **[テストの完了]** フェーズに達したら、**[テストの完了]** をクリックして、テスト フェールオーバーを終了します。 **[ジョブ]** タブまでドリルダウンし、フェールオーバーの進行状況と状態を追跡して、必要な操作を実行します。
5. フェールオーバー後、Azure Portal で仮想マシンのテスト レプリカを確認できます。 オンプレミスのネットワークから、仮想マシンへのアクセスをセットアップすると、仮想マシンへのリモート デスクトップ接続を開始できます。

   1. 仮想マシンが正常に起動することを確認します。
   2. フェールオーバー後に Azure でリモート デスクトップを使用して仮想マシンに接続する場合は、テストのフェールオーバーを実行する前に、仮想マシンのリモート デスクトップ接続を有効にします。 また、仮想マシンに RDP エンドポイントを追加する必要もあります。 [Azure Automation Runbook](site-recovery-runbook-automation.md) を活用して、この処理を実行できます。
   3. フェールオーバー後、リモート デスクトップでパブリック IP アドレスを使用して Azure の仮想マシンに接続する場合、パブリック アドレスを使用して仮想マシンに接続することを妨げるドメイン ポリシーがないことを確認します。
6. テストが完了したら、以下の手順を実行します。

   * **[テスト フェールオーバーが完了しました]**をクリックします。 テスト環境をクリーンアップして、自動的に電源をオフにし、テスト仮想マシンを削除します。
   * **[メモ]** をクリックして、テスト フェールオーバーに関連する監察結果をすべて記録し、保存します。
7. フェールオーバーが **[テストの完了]** フェーズに達したら、次の手順に従って検証を行います。
   1. Azure ポータルで、レプリカ仮想マシンを表示します。 仮想マシンが正常に起動することを確認します。
   2. オンプレミスのネットワークから、仮想マシンへのアクセスをセットアップすると、仮想マシンへのリモート デスクトップ接続を開始できます。
   3. **[テストの完了]** をクリックして終了します。
   4. **[メモ]** をクリックして、テスト フェールオーバーに関連する監察結果をすべて記録し、保存します。
   5. **[テスト フェールオーバーが完了しました]**をクリックします。 テスト環境をクリーンアップして、自動的に電源をオフにし、テスト仮想マシンを削除します。

## <a name="next-steps"></a>次のステップ
デプロイを実行できる状態に設定した後、フェールオーバーの詳細について、 [こちら](site-recovery-failover.md) を参照してください。


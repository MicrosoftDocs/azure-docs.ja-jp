---
title: "VMVware VM または物理サーバーを別のサイトにレプリケートする (Azure クラシック ポータル) | Microsoft Docs"
description: "VMware VM または Windows/Linux 物理サーバーを Azure Site Recovery を使用してセカンダリ サイトにレプリケートする場合は、この記事を参照してください。"
services: site-recovery
documentationcenter: 
author: nsoneji
manager: jwhit
editor: 
ms.assetid: b2cba944-d3b4-473c-8d97-9945c7eabf63
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: 3b606aa6dc3b84ed80cd3cc5452bbe1da6c79a8b
ms.openlocfilehash: cff6ef0c1c49110edc53ec82f88d9875439aab8a
ms.lasthandoff: 01/30/2017


---
# <a name="replicate-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site-in-the-classic-azure-portal"></a>Azure クラシック ポータルでオンプレミスの VMware 仮想マシンまたは物理サーバーをセカンダリ サイトにレプリケートする

## <a name="overview"></a>概要
Azure Site Recovery の InMage Scout は、オンプレミスの VMWare サイト間のリアルタイムのレプリケーションを実現します。 InMage Scout は、Azure Site Recovery サービスのサブスクリプションに含まれています。

## <a name="prerequisites"></a>前提条件
**Azure アカウント**: [Microsoft Azure](https://azure.microsoft.com/) アカウントが必要です。 アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。 [こちら](https://azure.microsoft.com/pricing/details/site-recovery/) をご覧ください。

## <a name="step-1-create-a-vault"></a>ステップ 1: コンテナーの作成
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. [新規]、[管理]、[Backup and Site Recovery (OMS) (バックアップと Site Recovery (OMS))] の順にクリックします。 または、[参照]、[Recovery Services コンテナー]、[追加] の順にクリックします。
3. **[名前]** に、コンテナーを識別するフレンドリ名を入力します。 複数のサブスクリプションがある場合は、いずれかを選択します。
4. **[リソース グループ]** で、新しいリソース グループを作成するか、既存のリソース グループを選択します。 Azure リージョンを指定して、すべての必須フィールドに必要事項を入力します。
5. **[場所]** で、コンテナーのリージョンを選択します。 サポートされているリージョンについては、「 [Azure Site Recovery の価格](https://azure.microsoft.com/pricing/details/site-recovery/)」をご覧ください。
6. ダッシュボードからコンテナーにすばやくアクセスするには、[ダッシュボードにピン留めする] をクリックし、[作成] をクリックします。
7. 新しいコンテナーは、[ダッシュボード] の [すべてのリソース] と、メインの [Recovery Services コンテナー] ブレードに表示されます。

## <a name="step-2-configure-the-vault-and-download-inmage-scout-components"></a>手順 2: コンテナーの構成および InMage Scout コンポーネントのダウンロード
1. [Recovery Services コンテナー] ブレードでコンテナーを選択し、[設定] をクリックします。
2. **[設定]**  >  **[作業の開始]** で、**[Site Recovery]**、**[手順 1: インフラストラクチャを準備する]**  >  **[保護の目標]** をクリックします。
3. **[保護の目標]** で、[復旧サイトへ] を選択し、[Yes, with VMware vSphere Hypervisor (はい、VMware vSphere ハイパーバイザーを使用する)] を選択します。 次に、[OK] をクリックします。
4. **[Scout のセットアップ]** で、[ダウンロード] をクリックして InMage Scout 8.0.1 GA ソフトウェアと登録キーをダウンロードします。 必要なすべてのコンポーネントのセットアップ ファイルは、ダウンロードした .zip ファイルに含まれています。

## <a name="step-3-install-component-updates"></a>手順 3: コンポーネント更新プログラムのインストール
最新の [更新プログラム](#updates)について確認します。 更新プログラムのファイルを、次の順序でサーバーにインストールします。

1. RX サーバー (存在する場合)
2. 構成サーバー
3. プロセス サーバー
4. マスター ターゲット サーバー
5. vContinuum サーバー
6. ソース サーバー (Windows および Linux サーバーの両方)

次のように、更新プログラムをインストールします。

1. [更新プログラム](https://aka.ms/asr-scout-update4) の .zip ファイルをダウンロードします。 この .zip ファイルには、次のファイルが含まれています。

   * RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
   * CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
   * UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe
   * UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
   * vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe
   * RHEL5、OL5、OL6、SUSE 10、SUSE 11 向け UA 更新プログラム 4: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
2. .zip ファイルを抽出します。<br>
3. **RX サーバー**: **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** を RX サーバーにコピーし、展開します。 抽出先のフォルダーで、 **/Install**を実行します。<br>
4. **サーバー/プロセス サーバー**: **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** を構成サーバーとプロセス サーバーにコピーします。 ファイルをダブルクリックして実行します。<br>
5. **Windows マスター ターゲット サーバー**: 統合エージェントを更新するには、**UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** をマスター ターゲット サーバーにコピーします。 ファイルをダブルクリックして実行します。 統合エージェントはソース サーバーにも適用できます。 後で説明する手順に従って、ソース サーバーにも統合エージェントをインストールしてください。<br>
6. **vContinuum サーバー**: **vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe** を vContinuum サーバーにコピーします。  vContinuum ウィザードを閉じたことを確認してください。 ファイルをダブルクリックして実行します。<br>
7. **Linux マスター ターゲット サーバー**: 統合エージェントを更新するには、**UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** をマスター ターゲット サーバーにコピーし、抽出します。 抽出先のフォルダーで、 **/Install**を実行します。<br>
8. **Windows ソース サーバー**: 統合エージェントを更新するには、**UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** をソース サーバーにコピーします。 ファイルをダブルクリックして実行します。<br>
9. **Linux ソース サーバー**: 統合エージェントを更新するには、UA ファイルの対応するバージョンを Linux サーバーにコピーし、抽出します。 抽出先のフォルダーで、 **/Install**を実行します。  例: RHEL 6.7 64 ビット サーバーの場合は、**UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** をサーバーにコピーし、抽出します。 抽出先のフォルダーで、 **/Install**を実行します。

## <a name="step-4-set-up-replication"></a>手順 4: レプリケーションの設定
1. ソースとターゲットの VMware サイト間にレプリケーションを設定します。
2. 手順については、製品と共にダウンロードされる InMage Scout の資料をご覧ください。 または、次の資料をご覧ください。

   * [リリース ノート](https://aka.ms/asr-scout-release-notes)
   * [互換性対応表](https://aka.ms/asr-scout-cm)
   * [ユーザー ガイド](https://aka.ms/asr-scout-user-guide)
   * [RX ユーザー ガイド](https://aka.ms/asr-scout-rx-user-guide)
   * [クイック インストール ガイド](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>更新プログラム
### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 更新プログラム 4
Scout 更新プログラム 4 は累積的な更新プログラムです。 更新プログラム&1; から&3; までのすべての修正に加え、以下の新しいバグ修正と機能強化が含まれています。

**新しいプラットフォームのサポート**

* VCenter/vSphere 6.0、6.1、6.2 のサポートが追加されました
* 以下の Linux オペレーティング システムのサポートが追加されました
  * Red Hat Enterprise Linux (RHEL)7.0、7.1、7.2
  * CentOS 7.0、7.1、7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64 ビット **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** は、Scout GA 基本パッケージ **InMage_Scout_Standard_8.0.1 GA.zip** にパッケージ化されています。 [手順 1](#step-1-create-a-vault) で説明したように、ポータルから Scout GA パッケージをダウンロードしてください。
>
>

**バグ修正と機能強化**

* 不要な再同期の問題を防ぐため、次の Linux OS およびクローンのシャットダウン処理が強化されました。
  * Red Hat Enterprise Linux (RHEL) 6.x
  * Oracle Linux (OL) 6.x
* Linux で、統合エージェントのインストール ディレクトリのフォルダーへの完全なアクセス許可がローカル ユーザーのみに制限されるようになりました。
* Windows で、SQL や SharePoint クラスターのような負荷の高い分散アプリケーションで共通の分散整合性ブックマークを発行しているときにタイムアウトになる問題があります。
* ログに関連する修正が CX 基本インストーラーに追加されました。
* VMware vCLI 6.0 のダウンロード リンクが Windows マスター ターゲット基本インストーラーに追加されています。
* フェールオーバーおよび障害復旧訓練中のネットワーク構成の変更に関するチェックとログがさらに追加されました。
* リテンション情報が CX に報告されないことがあります。  
* 物理クラスターで、ソース ボリュームの圧縮が発生した場合に vContinuum ウィザードでのボリューム サイズ変更操作が失敗します。
* クラスター ディスクが PRDM ディスクの場合、"Failed to find the disk signature" (ディスク署名が見つかりませんでした) というエラーが発生してクラスターによる保護が失敗していました。
* cxps トランスポート サーバーが範囲外の例外のためクラッシュします。
* vContinuum ウィザードのプッシュ インストール ページで、サーバー名と IP 列のサイズを変更できるようになりました。
* RX API の機能強化
  * 利用可能な最新&5; つの共通の整合性ポイントが提供されます (保証済みタグのみ)。
  * 保護されているすべてのデバイスの容量と空き領域の詳細が提供されます。
  * ソース サーバーの Scout ドライバーの状態が提供されます。

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** 基本パッケージに、更新された CX 基本インストーラー **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe** と Windows マスター ターゲット基本インストーラー **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe** が含まれるようになりました。 すべての新しいインストールで、新しい CX および Windows マスター ターゲット GA が使用されます。
> * 更新プログラム 4 は、8.0.1 GA に直接適用できます。
> * 構成サーバーと RX の更新プログラムは、適用後にロールバックすることはできません。
>
>

### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 更新プログラム 3
更新プログラム 3 には、以下のバグの修正と機能強化が含まれています。

* プロキシを経由する場合、構成サーバーと RX が Site Recovery コンテナーへの登録に失敗します。
* 正常性レポートで、目標復旧時点 (RPO) が満たされていない時間数が更新されません。
* ESX ハードウェアの詳細またはネットワークの詳細に UTF-8 文字が含まれている場合、構成サーバーが RX と同期しません。
* 復旧後、Windows Server 2008 R2 ドメイン コント ローラーの起動が失敗します。
* オフライン同期が正常に動作しません。
* 仮想マシン (VM) のフェールオーバー後に、 レプリケーション ペアの削除処理が CX UI で長時間停止状態となり、ユーザーがフェールバックを完了することも、操作を再開することもできなくなります。
* SQL クライアントなどのアプリケーション接続の切断を減らすことを目的に、整合性ジョブによって行われるスナップショット操作が全体的に最適化されました。
* Windows でのスナップショット作成に必要なメモリ使用量を減らすことで、整合性ツール (VACP.exe) のパフォーマンスが改善されました。
* パスワードが 16 文字を超える場合、プッシュ インストール サービスがクラッシュします。
* 資格情報が変更された場合、vContinuum が新しい vCenter 資格情報を確認せず、入力も求めません。
* Linux で、マスター ターゲットのキャッシュ マネージャー (cachemgr) がプロセス サーバーからファイルをダウンロードしないため、レプリケーション ペアの調整が発生します。
* 物理フェールオーバー クラスター (MSCS) ディスクの順序がノード間で統一されていない場合、一部のクラスター ボリュームにレプリケーションが設定されません。
  <br/>この修正を適用するには、クラスターを再保護する必要があることに注意してください。  
* RX が Scout 7.1 から Scout 8.0.1 にアップグレードされた後、SMTP 機能が正常に動作しません。
* ロールバック操作の完了までの時間を追跡するために、より多くの統計がロールバック操作ログに追加されました。
* ソース サーバーの Linux オペレーティング システムに追加されたサポートは次のとおりです。
  * Red Hat Enterprise Linux (RHEL) 6 update 7
  * CentOS 6 update 7
* CX および RX の UI に、ビットマップ モードに移行するペアについての通知が表示されるようになりました。
* 次のセキュリティ修正プログラムが RX に追加されました。

| **問題の説明** | **実装手順** |
| --- | --- |
| パラメーターの改ざんによる承認のバイパス |未承認ユーザーのアクセスを制限。 |
| クロスサイト リクエスト フォージェリ |ページごとにランダムに生成されるページ トークンの概念を導入。 <br/>その結果: <li> 1 人のユーザーに対して&1; つのサインイン インスタンスのみが存在。</li><li>ページ更新が機能しなくなり、ダッシュボードにリダイレクトされる。</li> |
| 安全でないファイルのアップロード |特定の拡張子にファイルを限定。 許可される拡張子: 7z、aiff、asf、avi、bmp、csv、doc、docx、fla、flv、gif、gz、gzip、jpeg、jpg、log、mid、mov、mp3、mp4、mpc、mpeg、mpg、ods、odt、pdf、png、ppt、pptx、pxd、qt、ram、rar、rm、rmi、rmvb、rtf、sdc、sitd、swf、sxc、sxw、tar、tgz、tif、tiff、txt、vsd、wav、wma、wmv、xls、xlsx、xml、zip |
| 永続的なクロスサイト スクリプティング |入力検証を追加。 |

> [!NOTE]
> * Site Recovery の更新プログラムはすべて累積的です。 更新プログラム 3 には、更新プログラム 1 と更新プログラム 2 のすべての修正点が含まれています。 更新プログラム 3 は、8.0.1 GA に直接適用できます。
> * 構成サーバーと RX の更新プログラムは、適用後にロールバックすることはできません。
>
>

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 更新プログラム 2 (2015 年 12 月 3 日)
更新プログラム 2 の修正内容は次のとおりです。

* **構成サーバー**: 構成サーバーが Site Recovery に登録される際に、31 日間の無料計測機能が予期どおりに動作しない問題が修正されます。
* **統合エージェント**: マスター ターゲット サーバーをバージョン 8.0 から 8.0.1 にアップグレードする際に更新プログラムがインストールされないという、更新プログラム 1 の問題が修正されます。

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 更新プログラム 1
更新プログラム 1 には、以下のバグの修正と新機能が含まれています。

* 各サーバー インスタンスについて&31; 日間の無料保護が提供されます。 これにより、機能をテストしたり、概念実証を設定したりすることができます。
  * Site Recovery Scout によるサーバーの保護が初めて開始された時点から 31 日間、フェールオーバーとフェールバックを含むサーバーへのすべての操作が無料となります。
  * 32 日目以降は、お客様が所有するサイトに対する Azure Site Recovery 保護について、各保護対象サーバーに Standard インスタンスの料金が請求されます。
  * 現在課金されている保護対象サーバーの数は、いつでも Azure Site Recovery コンテナーのダッシュボード ページで確認できます。
* vSphere コマンド ライン インターフェイス (vCLI) 5.5 更新プログラム 2 のサポートが追加されました。
* ソース サーバーの Linux オペレーティング システムに追加されたサポートは次のとおりです。
  * RHEL 6 Update 6
  * RHEL 5 Update 11
  * CentOS 6 Update 6
  * CentOS 5 Update 11
* 次の問題に対処するバグの修正が提供されます。
  * 構成サーバーまたは RX サーバーに対するコンテナー登録に失敗する。
  * クラスター化された仮想マシンを再開時に再保護すると、クラスターのボリュームが適切に表示されない。
  * マスター ターゲット サーバーがオンプレミスの運用仮想マシンとは異なる ESXi サーバーでホストされている場合、フェールバックが失敗する。
  * 8.0.1 にアップグレードすると構成ファイルのアクセス許可が変更され、保護や操作に影響が出る。
  * 再同期のしきい値が適切に適用されないため、レプリケーションの動作に不整合が生じる。
  * 構成サーバーのインターフェイスに、RPO 設定が正しく表示されない。 圧縮されないデータ値に、誤って圧縮された値が表示される。
  * vContinuum ウィザードで削除操作を行っても適切に削除されず、レプリケーションが構成サーバーのインターフェイスから削除されない。
  * vContinuum ウィザードで、MSCS 仮想マシンの保護中にディスク ビューで **[詳細]** をクリックするとディスクの選択が解除される。
  * 物理 - 仮想 (P2V) シナリオで、仮想マシンの復旧中に CIMnotify や CqMgHost などの必須 HP サービスが手動に移行しない。 これにより、起動時間が長くなる。
  * マスター ターゲット サーバーに 27 個以上のディスクがある場合、Linux 仮想マシンの保護が失敗する。

## <a name="next-steps"></a>次のステップ
疑問点がある場合は、 [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿して質問してください。


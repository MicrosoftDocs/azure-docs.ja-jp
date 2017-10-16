---
title: "Azure Site Recovery を使用して VMware VM または物理サーバーのセカンダリ サイトへのディザスター リカバリーをセットアップする | Microsoft Docs"
description: "Azure Site Recovery を使用して、セカンダリ サイトへの VMware VM または Windows/Linux 物理サーバーのディザスター リカバリーをセットアップする方法を説明します。"
services: site-recovery
documentationcenter: 
author: nsoneji
manager: jwhit
editor: 
ms.assetid: 68616d15-398c-4f40-8323-17b6ae1e65c0
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: da1df5546b7f99549a693c4e2df4eefb7a423c7f
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>オンプレミスの VMware 仮想マシンまたは物理サーバーのセカンダリ サイトへのディザスター リカバリーをセットアップする

[Azure Site Recovery](site-recovery-overview.md) の InMage Scout は、オンプレミスの VMware サイト間のリアルタイムのレプリケーションを実現します。 InMage Scout は、Azure Site Recovery サービスのサブスクリプションに含まれています。 


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- すべてのコンポーネントのサポート要件を[レビュー](site-recovery-support-matrix-to-sec-site.md)する。
- レプリケートするマシンが[レプリケート マシンのサポート](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions)に準拠していることを確認する。


## <a name="create-a-vault"></a>コンテナーの作成

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="choose-a-protection-goal"></a>保護の目標を選択する

レプリケート対象、レプリケート場所を選択します。

1. **[Site Recovery]** > **[インフラストラクチャの準備]** > **[保護の目標]** の順にクリックします。
2. **[復旧サイトへ]** > **[Yes, with VMware vSphere Hypervisor]\(はい、VMware vSphere Hypervisor を使う\)** の順に選択します。 次に、 **[OK]**をクリックします
3. **[Scout のセットアップ]** で、InMage Scout 8.0.1 GA ソフトウェアと登録キーをダウンロードします。 すべてのコンポーネントのセットアップ ファイルは、ダウンロードした .zip ファイルに含まれています。

## <a name="install-component-updates"></a>コンポーネント更新プログラムのインストール

 最新の[更新プログラム](#updates)をレビューしてインストールします。 更新プログラムは、次の順序でサーバーにインストールする必要があります。

1. RX サーバー (該当する場合)
2. 構成サーバー
3. プロセス サーバー
4. マスター ターゲット サーバー
5. vContinuum サーバー
6. ソース サーバー (Windows および Linux サーバーの両方)

次のように、更新プログラムをインストールします。

1. [更新プログラム](https://aka.ms/asr-scout-update5) の .zip ファイルをダウンロードします。 このファイルには、以下が含まれています。

   * RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
   * CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
   * UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
   * UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
   * vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe
   * RHEL5、OL5、OL6、SUSE 10、SUSE 11 向け UA 更新プログラム 4: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
2. .zip ファイルを抽出します。
    - **RX サーバー**: **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** を RX サーバーにコピーし、展開します。 抽出先のフォルダーで、 **/Install**を実行します。
    - **構成サーバーとプロセス サーバー**: **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** を構成サーバーとプロセス サーバーにコピーします。 ファイルをダブルクリックして実行します。<br>
    - **Windows マスター ターゲット サーバー**: 統合エージェントを更新するには、**UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** をサーバーにコピーします。 ファイルをダブルクリックして実行します。 統合エージェントはソース サーバーにも適用できます。 ソースが更新プログラム 4 に更新されていない場合、統合エージェントを更新する必要があります。
    - **vContinuum サーバー**: **vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe** をサーバーにコピーします。  vContinuum ウィザードを閉じたことを確認してください。 ファイルをダブルクリックして実行します。
    - **Linux マスター ターゲット サーバー**: 統合エージェントを更新するには、**UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** をマスター ターゲット サーバーにコピーし、抽出します。 抽出先のフォルダーで、 **/Install**を実行します。
    - **Windows ソース サーバー**: 更新プログラム 4 を既に実行している場合、ソース サーバーで更新プログラム 5 エージェントをインストールする必要はありません。 統一されたエージェントを更新するには、ソース サーバーに **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe**をコピーします。 ファイルをダブルクリックして実行します。
    - **Linux ソース サーバー**: 統合エージェントを更新するには、統合エージェント ファイルの対応するバージョンを Linux サーバーにコピーし、抽出します。 抽出先のフォルダーで、 **/Install**を実行します。  例: RHEL 6.7 64 ビット サーバーの場合は、**UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** をサーバーにコピーし、抽出します。 抽出先のフォルダーで、 **/Install**を実行します。

## <a name="enable-replication"></a>Enable replication

1. ソースとターゲットの VMware サイト間にレプリケーションを設定します。
2. 手順については、製品と共にダウンロードされる InMage Scout の資料をご覧ください。 または、次の資料をご覧ください。

   * [リリース ノート](https://aka.ms/asr-scout-release-notes)
   * [互換性対応表](https://aka.ms/asr-scout-cm)
   * [ユーザー ガイド](https://aka.ms/asr-scout-user-guide)
   * [RX ユーザー ガイド](https://aka.ms/asr-scout-rx-user-guide)
   * [クイック インストール ガイド](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>更新プログラム

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 更新プログラム 5
Scout 更新プログラム 5 は累積的な更新プログラムです。 更新プログラム 1 から更新プログラム 4 までのすべての修正と、下記の新しい修正が含まれています。
- Site Recovery Scout 更新プログラム 4 から更新プログラム 5 までの修正は、特にマスター ターゲットおよび vContinuum コンポーネント向けです。
- ソース サーバー、マスター ターゲット、構成、プロセス、および RX サーバーが既に更新プログラム 5 を実行している場合、マスター ターゲット サーバー上にのみ適用します。 

#### <a name="new-platform-support"></a>新しいプラットフォームのサポート
* SUSE Linux Enterprise Server 11 Service Pack 4(SP4)
* SLES 11 SP4 64 ビット **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** は、Scout GA 基本パッケージ (**InMage_Scout_Standard_8.0.1 GA.zip**) でパッケージ化されています。 「[コンテナーの作成](#create-a-vault)」の説明に従って、ポータルから GA パッケージをダウンロードします。


#### <a name="bug-fixes-and-enhancements"></a>バグ修正と機能強化

* Windows クラスターのサポート信頼性向上のための修正
    * P2V MSCS クラスター ディスクのいくつかが回復後に RAW になる問題を修正。
    * P2V MSCS クラスターの回復がディスク順序不一致のために失敗する問題を修正。
    * MSCS クラスターのディスク追加操作がディスク サイズ不一致エラーで失敗する問題を修正。
    * RDM LUN マッピングを持つソース MSCS クラスターの準備完了チェックがサイズ検証で失敗する問題を修正。
    * SCSI が一致しない問題のために単一ノード クラスター保護が失敗する問題を修正。 
    * ターゲット クラスター ディスクが存在する場合、P2V Windows クラスター サーバーの再保護が失敗する問題を修正。 
    
* フェールバック保護中に、選択されたマスター ターゲット サーバーが保護されたソース マシンと同じ ESXi サーバーにない場合 (フォワード保護中)、フェールバック回復中に vContinuum が間違ったマスター ターゲット サーバーを取得し、回復操作が失敗する、という問題を修正。

#### <a name="issues"></a>発行
* P2V クラスター修正は、Site Recovery Scout 更新プログラム 5 で新しく保護された物理 MSCS クラスターのみに適用されます。 以前の更新プログラムを持つ保護された P2V MSCS クラスターに、クラスターの修正をインストールするには、[Site Recovery Scout リリース ノート](https://aka.ms/asr-scout-release-notes)のセクション 12 に記載されているアップグレードの手順に従います。
* 再保護の際に、各クラスター ノードで、同じ一連のディスクが最初に保護されたときのようにアクティブである場合は、物理 MSCS クラスターの再保護では既存のターゲット ディスクのみ再利用できます。 そうでない場合、[Site Recovery Scout リリース ノート](https://aka.ms/asr-scout-release-notes)のセクション 12 にある手動の手順に従って、ターゲット側のディスクを適切なデータストア パスに移動し、再保護中に再利用するようにします。 アップグレードの手順に従わずに P2V モードで MSCS クラスターを再保護すると、ターゲット ESXi サーバーに新しいディスクが作成されます。 データストアから古いディスクを手動で削除する必要が生じます。
* ソース SLES11 またはいずれかの Service Pack の付いた SLES11 サーバーが正常に再起動されたら、再同期する**ルート** ディスクのレプリケーション ペアを手動でマークします。 CX のインターフェイスでは、通知されません。 再同期するルート ディスクをマークしないと、データ整合性の問題が起きる可能性があります。


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 更新プログラム 4
Scout 更新プログラム 4 は累積的な更新プログラムです。 更新プログラム 1 から更新プログラム 3 までのすべての修正と、下記の新しい修正が含まれています。

#### <a name="new-platform-support"></a>新しいプラットフォームのサポート

* vCenter/vSphere 6.0、6.1、6.2 のサポートが追加されました
* これらの Linux オペレーティング システムのサポートが追加されました。
  * Red Hat Enterprise Linux (RHEL)7.0、7.1、7.2
  * CentOS 7.0、7.1、7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64 ビット **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** は、Scout GA 基本パッケージ (**InMage_Scout_Standard_8.0.1 GA.zip**) でパッケージ化されています。 「[コンテナーの作成](#create-a-vault)」の説明に従って、ポータルから Scout GA パッケージをダウンロードします。

#### <a name="bug-fixes-and-enhancements"></a>バグ修正と機能強化

* 不要な再同期の問題を防ぐため、次の Linux オペレーティング システムおよびクローンのシャットダウン処理が強化されました。
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Linux で、統合エージェントのインストール ディレクトリのフォルダーへのすべてのアクセス許可がローカル ユーザーのみに制限されるようになりました。
* Windows で、SQL Server や SharePoint クラスターのような負荷の高い分散アプリケーションで共通の分散整合性ブックマークを発行しているときにタイムアウトになる問題を修正しました。
* 構成サーバーの基本インストーラーの、ログに関連する修正。
* VMware vCLI 6.0 へのダウンロード リンクが Windows マスター ターゲット基本インストーラーに追加されました。
* フェールオーバーとディザスター リカバリーのテスト中のネットワーク構成変更のために、追加のチェックとログが追加されました。
* リテンション期間の情報が構成サーバーにレポートされないという問題の修正。  
* 物理クラスターで、ソース ボリュームを縮小するときにボリュームのサイズ変更が vContinuum ウィザードで失敗するという問題の修正。
* クラスター ディスクが PRDM ディスクの場合、"Failed to find the disk signature" (ディスク署名が見つかりませんでした) というエラーが発生してクラスター保護が失敗するという問題の修正。
* cxps トランスポート サーバーが範囲外の例外のためクラッシュする問題の修正。
* vContinuum ウィザードの **[プッシュ インストール]** ページで、サーバー名と IP アドレスの列をサイズ変更できるようになりました。
* RX API の機能強化:
  * 最新 5 つの共通の整合性ポイントが利用可能になりました (保証済みタグのみ)。
  * 保護されているデバイスのすべてについて、容量と空き領域の詳細が表示されます。
  * ソース サーバー上の Scout ドライバーの状態がわかります。

#### <a name="issues"></a>発行

* **InMage_Scout_Standard_8.0.1_GA.zip** 基本パッケージには、以下が含まれます。
    * 更新された構成サーバーの基本インストーラー (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
    * Windows マスター ターゲット基本インストーラー (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
    * 新規インストールの場合はすべて、新しい構成サーバーと Windows マスター ターゲット GA ビットを使用します。
* 更新プログラム 4 は、8.0.1 GA に直接適用できます。
* 構成サーバーと RX の更新プログラムは、適用後にロールバックすることはできません。


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 更新プログラム 3

Site Recovery の更新プログラムはすべて累積的です。 更新プログラム 3 には、更新プログラム 1 と 更新プログラム 2 のすべての修正が含まれています。 更新プログラム 3 は、8.0.1 GA に直接適用できます。 構成サーバーと RX の更新プログラムは、適用後にロールバックすることはできません。

#### <a name="bug-fixes-and-enhancements"></a>バグ修正と機能強化
更新プログラム 3 では、次の問題が修正されました。

* プロキシを経由する場合、構成サーバーと RX はコンテナーに登録されません。
* 正常性レポートで、目標復旧時点 (RPO) に達していない時間数が更新されません。
* ESX ハードウェアの詳細またはネットワークの詳細に UTF-8 文字が含まれている場合、構成サーバーが RX と同期しません。
* 復旧後、Windows Server 2008 R2 ドメイン コント ローラーが開始しません。
* オフライン同期が、予想どおりに動作していません。
* VM のフェールオーバー後に、構成サーバー コンソールでレプリケーション ペアの削除が長時間にわたり進行しません。 ユーザーは、フェールバックの完了または操作の再開ができません。
* SQL Server クライアントなどのアプリケーション接続の切断を減らすことを目的に、整合性ジョブによるスナップショット操作が全体的に最適化されました。
* 整合性ツール (VACP.exe) のパフォーマンスが改善されました。 Windows 上でのスナップショット作成に必要なメモリ使用量が削減されました。
* パスワードが 16 文字を超える場合、プッシュ インストール サービスがクラッシュします。
* 資格情報が変更される場合、vContinuum は新しい vCenter 資格情報を確認せず、入力を求めません。
* Linux で、マスター ターゲットのキャッシュ マネージャー (cachemgr) はプロセス サーバーからファイルをダウンロードしません。 そのため、レプリケーション ペアの調整が発生します。
* 物理フェールオーバー クラスター (MSCS) ディスクの順序がノード間で統一されていない場合、一部のクラスター ボリュームにレプリケーションが設定されません。 この修正を適用するには、クラスターを再保護しなければいけません。  
* RX が Scout 7.1 から Scout 8.0.1 にアップグレードされた後、SMTP 機能が正常に動作しません。
* ロールバック操作の完了までの時間を追跡するために、より多くの統計がロールバック操作ログに追加されました。
* ソース サーバーの Linux オペレーティング システムに追加されたサポートは次のとおりです。
  * Red Hat Enterprise Linux (RHEL) 6 update 7
  * CentOS 6 update 7
* 構成サーバーと RX コンソールが、ビットマップ モードに移行するペアの通知を表示するようになりました。
* 次のセキュリティ修正プログラムが RX に追加されました。
    * パラメーター改ざんによる承認バイパス: 非該当ユーザーにアクセスを制限します。
    * クロスサイト リクエスト フォージェリ: ページのトークンの概念が実装され、ページごとにランダムに生成されます。 つまり、同じユーザーに対してシングル サインイン インスタンスのみが存在し、ページの更新は機能しません。 代わりに、ダッシュボードにリダイレクトします。
    * 悪意のあるファイルのアップロード: ファイルは特定の拡張子、つまり 7z、aiff、asf、avi、bmp、csv、doc、docx、fla、flv、gif、gz、gzip、jpeg、jpg、log、mid、mov、mp3、mp4、mpc、mpeg、mpg、ods、odt、pdf、png、ppt、pptx、pxd、qt、ram、rar、rm、rmi、rmvb、rtf、sdc、sitd、swf、sxc、sxw、tar、tgz、tif、tiff、txt、vsd、wav、wma、wmv、xls、xlsx、xml、zip に制限されています。
    * 永続的なクロス サイト スクリプティング: 入力の検証が追加されました。

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 更新プログラム 2 (2015 年 12 月 3 日)

更新プログラム 2 の修正内容は次のとおりです。

* **構成サーバー**: 構成サーバーが Site Recovery に登録される際に、31 日間の無料計測機能が予期どおりに動作しない問題。
* **統合エージェント**: マスター ターゲット サーバーをバージョン 8.0 から 8.0.1 にアップグレードする際に更新プログラムがインストールされないという更新プログラム 1 の問題の修正。

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 更新プログラム 1
更新プログラム 1 には、以下のバグの修正と新機能が含まれています。

* 各サーバー インスタンスについて 31 日間の無料保護が提供されます。 これにより、機能をテストしたり、概念実証を設定したりすることができます。
* フェールオーバーとフェールバックを含め、サーバーでのすべての操作は、31 日間無料です。 サーバーが最初に Site Recovery Scout で保護された時点から、時間がカウントされます。 32 日目以降は、お客様が所有するサイトに対する Site Recovery 保護について、各保護対象サーバーに Standard インスタンスの料金が請求されます。
* 現在課金されている保護対象サーバーの数は、いつでもコンテナーの**ダッシュボード**で確認できます。
* vSphere コマンドライン インターフェイス (vCLI) 5.5 更新プログラム 2 のサポートが追加されました。
* ソース サーバー上の次の Linux オペレーティング システムにサポートが追加されました。
    * RHEL 6 Update 6
    * RHEL 5 Update 11
    * CentOS 6 Update 6
    * CentOS 5 Update 11
* 次の問題に対処するバグの修正が提供されます。
  * 構成サーバーまたは RX サーバーに対するコンテナー登録に失敗する。
  * クラスター化された VM を再開時に再保護すると、クラスターのボリュームが適切に表示されない。
  * マスター ターゲット サーバーがオンプレミスの運用 VM とは異なる ESXi サーバーでホストされている場合、フェールバックが失敗する。
  * 8.0.1 にアップグレードすると構成ファイルのアクセス許可が変更される。 この変更によって保護や操作に影響が出る。
  * 再同期のしきい値が適切に適用されないため、レプリケーションの動作に不整合が生じる。
  * 構成サーバー コンソールに、RPO 設定が正しく表示されない。 圧縮されないデータ値に、誤って圧縮された値が表示される。
  * vContinuum ウィザードで削除操作を行っても適切に削除されず、レプリケーションが構成サーバー コンソールから削除されない。
  * vContinuum ウィザードで、MSCS VM の保護中にディスク ビューで **[詳細]** をクリックするとディスクの選択が自動的に解除される。
  * 物理 - 仮想 (P2V) シナリオで、VM の復旧中に必須 HP サービス (CIMnotify や CqMgHost など) が手動に移行しない。 この問題により、起動時間が長くなる。
  * マスター ターゲット サーバーに 27 個以上のディスクがある場合、Linux VM の保護が失敗する。



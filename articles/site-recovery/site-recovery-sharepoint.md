---
title: Azure Site Recovery を使用して多層 SharePoint アプリケーションのディザスター リカバリーを設定する | Microsoft Docs
description: この記事では、Azure Site Recovery を使用して、多層 SharePoint のディザスター リカバリーを設定する方法を説明します。
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: sutalasi
ms.openlocfilehash: bc6d9e7214d2b7cd009e7562357bed420e49f185
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325105"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Azure Site Recovery を使用して多層 SharePoint アプリケーションのディザスター リカバリーを設定する

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して SharePoint アプリケーションを保護する方法の詳細を説明します。


## <a name="overview"></a>概要

Microsoft SharePoint は、グループあるいは部署が情報を整理、コラボレート、および共有するのに役立つ強力なアプリケーションです。 SharePoint は、イントラネット ポータルや、ドキュメントおよびファイル管理、コラボレーション、ソーシャル ネットワーク、エクストラネット、web サイト、エンタープライズ検索、およびビジネス インテリジェンスを提供できます。 また、システム統合やプロセス統合、ワークフロー自動化機能もあります。 通常 組織は、ダウンタイムやデータ損失が重視される第 1 層アプリケーションとみなされます。

現在、Microsoft SharePoint には、既製のディザスター リカバリー機能はありません｡ 災害の種類と規模に関係なく、復旧では待機データ センターが使用され、ファームを復旧することができます。 一次データセンター側の機能停止からローカルの冗長システムやバックアップを回復できない場合は、待機データ センターが必要です。

ディザスター リカバリー ソリューションでは、SharePoint などの複雑なアプリケーション アーキテクチャを中心に復旧計画のモデリングを行えるようにすべきです。 また、さまざまな階層間のアプリケーション マッピングを処理する独自のステップを追加する機能も用意し、災害発生時はわずかな RTO でクリック 1 回でのフェイルオーバーを提供できるようにします。

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して SharePoint アプリケーションを保護する方法の詳細を説明します。 また、3 層の SharePoint アプリケーションを Azure にレプリケートするさいのベスト プラクティスや、ディザスター リカバリー訓練の実施方法、アプリケーションを Azure にフェールオーバーする方法についても説明します。

下記は、Azure への多層アプリケーションの回復を扱ったビデオです。

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>前提条件

開始する前に、以下を理解していることを確認してください。

1. [Azure への仮想マシンのレプリケート](site-recovery-vmware-to-azure.md)
2. [復旧ネットワークの設計](site-recovery-network-design.md)方法
3. [Azure へのテスト フェールオーバーの実行](site-recovery-test-failover-to-azure.md)
4. [Azure へのフェールオーバーの実行](site-recovery-failover.md)
5. [ドメイン コントローラーのレプリケート](site-recovery-active-directory.md)方法
6. [SQL Server のレプリケート](site-recovery-sql.md)方法

## <a name="sharepoint-architecture"></a>SharePoint のアーキテクチャ

SharePoint は階層型トポロジーとサーバー ロールを使用して 1 つまたは複数のサーバーにデプロイすることで、特定の目標や目的に合ったファーム デザインを実現できます。 多数の同時ユーザー、大量のコンテンツ項目をサポートしている通常の大規模で高デマンドの SharePoint サーバー ファームは、そのスケーラビービリティ戦略の一環としてサービスのグループ化を利用します。 このアプローチでは、サービスを専用のサーバー上で実行します。それらサービスはグループにまとめられ、専用のサーバーが 1 つのグループとしてスケールアウトされます。 下記のトポロジは、3 層 SharePoint サーバー ファームに対するサービスとサーバーのグループ化を表しています。 さまざまな SharePoint トポロジについての詳細な指針については、SharePoint のマニュアルおよび製品系列のアーキテクチャを参照してください。 [本書](https://technet.microsoft.com/library/cc303422.aspx)でも、SharePoint 2013 のデプロイに関する詳細を説明しています。



![デプロイ パターン 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Site Recovery のサポート

この記事の作成には、VMware 仮想マシンと Windows Server 2012 R2 Enterprise を使用しました。 SharePoint 2013 Enterprise Edition および SQL Server 2014 Enterprise Edition も使用しています。 Site Recovery レプリケーションはアプリケーションに依存しません。このため、ここで紹介している推奨事項は次のシナリオにも適用できます。

### <a name="source-and-target"></a>ソースとターゲット

**シナリオ** | **セカンダリ サイトへ** | **Azure へ**
--- | --- | ---
**Hyper-V** | はい | はい
**VMware** | はい | はい
**物理サーバー** | はい | はい
**Azure** | NA | はい

### <a name="sharepoint-versions"></a>SharePoint のバージョン
次の SharePoint Server のバージョンがサポートされています。

* SharePoint Server 2013 Standard
* SharePoint Server 2013 Enterprise
* SharePoint Server 2016 Standard
* SharePoint Server 2016 Enterprise

### <a name="things-to-keep-in-mind"></a>留意事項

アプリケーション内の任意の階層で共有ディスクベースのクラスターを使用している場合、Site Recovery レプリケーションを使って仮想マシンをレプリケートすることはできません。 アプリケーションが提供するネイティブのレプリケーションを使用してから、[復旧計画](site-recovery-create-recovery-plans.md)を使用してすべての階層をフェールオーバーできます。

## <a name="replicating-virtual-machines"></a>仮想マシンをレプリケートする

[このガイダンス](site-recovery-vmware-to-azure.md)に従うことで、Azure に全仮想マシンをレプリケートすることができます。

* レプリケーションが完了したら、必ず、各階層の各仮想マシンに移動し、[Replicated item > Settings > Properties > Compute and Network] で同じ可用性セットを選択してください。 たとえば Web 階層に 3 台の仮想マシンがある場合は、必ず、その 3 台の仮想マシンのすべてが Azure の同じ可用性セットのメンバーになるように設定します。

    ![Set-Availability-Set](./media/site-recovery-sharepoint/select-av-set.png)

* Active Directory と DNS の保護に関するガイダンスは、[Protect Active Directory and DNS](site-recovery-active-directory.md)ドキュメントを参照してください。

* SQL Server 上で動作するデータベース層の保護に関するガイダンスは、[Protect SQL Server](site-recovery-active-directory.md) ドキュメントを参照してください。

## <a name="networking-configuration"></a>ネットワーク構成

### <a name="network-properties"></a>Network properties

* アプリ層と Web 層の仮想マシンについては、仮想マシンがフェールオーバー後に適切な DR ネットワークにアタッチできるように、Azure ポータルでネットワーク設定を行います。

    ![ネットワークを選択](./media/site-recovery-sharepoint/select-network.png)


* 静的 IP を使用している場合は、 **[ターゲット IP]** フィールドで仮想マシンに割り当てる IP を指定します。

    ![静的 IP を設定](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS とトラフィックのルーティング

インターネットに面しているサイトの場合は、Azure サブスクリプションで[[Priority] 型の Traffic Manager](../traffic-manager/traffic-manager-create-profile.md) プロファイルを作成します。 そして、次の方法で DNS と Traffic Managerプロファイル の設定をします。


| **Where** | **ソース** | **ターゲット**|
| --- | --- | --- |
| パブリック DNS | SharePoint サイト用のパブリック DNS <br/><br/> 例: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| オンプレミス DNS | sharepointonprem.contoso.com | オンプレミスファーム上のパブリック IP |


Traffic Manager プロファイルで[プライマリ エンドポイントと復旧エンドポイントを作成します](../traffic-manager/traffic-manager-configure-priority-routing-method.md)。 オンプレミス エンドポイントには外部エンドポイント、Azure エンドポイントにはパブリック IP を使用してください。 優先順位は、必ずオンプレミス エンドポイントを高く設定します。

Traffic Manager がフェールオーバー後に可用性ポストを自動的に検出できるようにするには、SharePoint の Web 層上の特定のポート (800 など) でテスト ページをホストします。 これは、SharePoint サイトで匿名認証を有効にできない場合の回避策です。

以下の設定値で [Traffic Manager プロファイル](../traffic-manager/traffic-manager-configure-priority-routing-method.md)を設定します。

* ルーティング方法 - [優先順位]
* DNS の有効時間 (TTL) - [30 秒]
* エンドポイント モニター設定 - 匿名認証を有効にできる場合は、特定の Web サイト エンドポイントを指定できます。 あるいは、特定のポート (たとえば 800) でテスト ページを利用できます。

## <a name="creating-a-recovery-plan"></a>復旧計画の作成

復旧計画では、多層アプリケーションのさまざまな階層のフェールオーバーをシーケンス処理できるため、アプリケーションの整合性が維持されます。 以下の手順に従って、多層 Web アプリケーションの復旧計画を作成します。 復旧計画の作成の詳細については、[こちら](site-recovery-runbook-automation.md#customize-the-recovery-plan)を参照してください。

### <a name="adding-virtual-machines-to-failover-groups"></a>フェールオーバー グループへの仮想マシンの追加

1. アプリおよび Web 層の仮想マシンを追加することによって復旧計画を作成します｡
2. [カスタマイズ] をクリックして仮想マシンをグループ化します｡ 既定では､すべての仮想マシンが｢グループ 1｣のメンバーです｡

    ![RP をカスタマイズする](./media/site-recovery-sharepoint/rp-groups.png)

3. 別のグループ (グループ 2) を作成し､Web 層の全仮想マシンをその新しいグループに移動します｡ これで､アプリ層の仮想マシンは｢グループ 1｣のメンバー､Web 層の仮想マシンは｢グループ 2｣のメンバーになります｡ これで､アプリ層の仮想マシンが先ず起動してから､Web 層の仮想マシンが起動します｡


### <a name="adding-scripts-to-the-recovery-plan"></a>復旧計画へのスクリプトの追加

次の [Deploy to Azure] ボタンをクリックすると、オートメーション アカウントによく使われる Azure Site Recovery のスクリプトをデプロイできます。 公開されているスクリプトを使用する場合は､必ず､そのスクリプトのガイダンスに従ってください｡

[![Azure へのデプロイ](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. SQL 可用性グループをフェールオーバーするための前処理スクリプトを｢グループ 1｣に追加します｡ サンプル スクリプトで公開されている｢ASR-SQL-FailoverAG｣スクリプトを使用してください｡ 必ずスクリプトのガイダンスに従い､適宜､スクリプトの内容を変更します｡

    ![Add-AG-Script-Step-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-Script-Step-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. フェールオーバー後のWeb 層仮想マシン (グループ 2) 上のロード バランサーをアタッチするための後処理スクリプトを追加します｡ サンプル スクリプトで公開されている｢ASR-AddSingleLoadBalancer｣スクリプトを使用してください｡ 必ずスクリプトのガイダンスに従い､適宜､スクリプトの内容を変更します｡

    ![Add-LB-Script-Step-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-Script-Step-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Azure 内の新しいファームを指し示すには､DNS レコードを更新するための手動ステップを追加します｡

    * インターネットに面しているサイトの場合､フェールオーバー後の DNS 更新は必要ありません｡ ｢Networking guidance｣の説で説明している手順に従って Traffic Manager の設定をします｡ 前節の説明通りに Traffic Manager プロファイルを設定したら､Azure 仮想マシン上のダミー ポート (この例では 800) を開くためのスクリプトを追加します｡

    * インターネットに面しているサイトの場合は､新しい Web 層仮想マシンのロード バランサー の IP を指し示すよう DNS レコードを更新するための手動ステップを追加します｡

4. バックアップから検索アプリケーションを復元するか､新しい検索サービスを起動するための手動ステップを追加します｡

5. バックアップから検索サービスアプリケーションを復元する場合は､次の手順に従ってください｡

    * この手順では、重大イベントの前に Search Service Application のバックアップを実施済みで､かつそのバックアップが障害復旧サイトに存在していると仮定しています。
    * バックアップは､そのスケジュールを設定し (たとえば 1 日に 1 回)､コピー手順を使って DR サイトにバックアップを置くようにすることで簡単に行うことができます｡ コピー手順には､AzCopy (Azure Copy) などのスクリプト形式のプログラムや､DFSR (Distributed File Services Replication) の設定などを含めることができます｡
    * SharePoint ファームが稼働していますから､Central Administration に移動し､ [バックアップと復元] から [復元] を選択します｡ 復元では､指定していたバックアップ先の問い合わせがあります (値の更新が必要になることがあります)｡ 復元する Search Service Application のバックアップを選択します｡
    * アプリケーションが復元されます｡ 復元では､同じトポロジ (サーバーが同数)で､それらサーバーに同じハードディスクドライブ文字が割り当てられていると想定していることに留意してください｡ 詳細は､[SharePoint 2013](https://technet.microsoft.com/library/ee748654.aspx) ドキュメントの｢Restore Search service application｣を参照してください｡


6. 新しい Search Service Application を使って開始する場合は､以下の手順に従います｡

    * この手順では､｢Search Administration｣データベースのバックアップが DR サイトに存在するものと書いてしています｡
    * 他の Search Service Application のデータベースをレプリケートしていないため､それらデータベースを再作成する必要があります｡ このためには､Central Administration に移動し､Search Service Application を削除します｡ 検索インデックスをホストしている任意のサーバーから､インデックス ファイルを削除します｡
    * Search Service Application を再作成します｡これにより､すべてのデータベースが再作成されます｡ GUI からすべてのアクションを行うことは不可能であるため､このサービスアプリケーションを再作成するスクリプトを用意しておくことをお勧めします｡ たとえば､インデックスの場所 (ドライブ) や検索トポロジは､SharePoint PowerShell コマンドレットを使用してのみ設定できます｡ Windows PowerShell の cmdlet Restore-SPEnterpriseSearchServiceApplication を使用し､ログ出荷されてレプリケート済みの Search Administration データベース､Search_Service__DB を指定します｡ このコマンドレットは検索設定とスキーマ､管理対象プロパティ､ルール､およびソースを提供し､他のすべてのコンポーネントの既定セットを作成します｡
    * Search Service Application を再作成したら､すべてのコンテンツソースについてそれぞれ全クロールを開始して Search Service を開始します｡ オンプレミス ファームの､推奨検索事項などの一部分析情報が失われます｡

7. すべての手順を完了したら､復旧計画を保存します｡最終的な復旧計画は以下のようになります｡

    ![保存された復旧計画](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>テスト フェールオーバーの実行
[このガイダンス](site-recovery-test-failover-to-azure.md)に従って、テスト フェールオーバーを実行します。

1.  Azure Portal に移動し、Recovery Services コンテナーを選択します。
2.  SharePoint アプリケーション用に作成した復旧計画をクリックします｡
3.  [テスト フェールオーバー] をクリックします。
4.  テスト フェールオーバー プロセスを開始する復旧ポイントと Azure 仮想ネットワークを選択します。
5.  セカンダリ環境が立ち上がったら、検証を行うことができます。
6.  検証が完了したら､復旧計画で [Cleanup test failover] をクリックできるようになります｡テスト フェールオーバー環境がクリーンアップされます｡

AD および DNS に対するテスト フェールオーバーの実行に関するガイダンスは､ [Test failover considerations for AD and DNS](site-recovery-active-directory.md#test-failover-considerations) ドキュメントを参照してください｡

SQL Always ON 可用性グループのテスト フェールオーバーの実行に関するガイダンスについては､[Azure Site Recovery を使用したアプリケーションの DR の実行とテスト フェールオーバーの実行](site-recovery-sql.md#disaster-recovery-of-an-application) に関するドキュメントをご覧ください。

## <a name="doing-a-failover"></a>フェールオーバーの実行
フェールオーバーの実行については､[このガイダンス](site-recovery-failover.md)に従ってください｡

1.  Azure Portal に移動し､Recovery Service コンテナーを選択します｡
2.  SharePoint アプリケーション用に作成した復旧計画をクリックします｡
3.  [フェールオーバー] をクリックします。
4.  フェールオーバー プロセスを開始する復旧ポイントを選択します。

## <a name="next-steps"></a>次の手順
[Site Recovery を利用した他のアプリケーションのレプリケーションについては](site-recovery-workload.md)､他の場所でも説明しています｡

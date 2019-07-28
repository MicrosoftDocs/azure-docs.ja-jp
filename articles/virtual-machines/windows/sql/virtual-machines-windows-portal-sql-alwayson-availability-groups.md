---
title: Azure Resource Manager VM の高可用性を設定する | Microsoft Docs
description: このチュートリアルでは、Azure Resource Manager モードの Azure 仮想マシンで Always On 可用性グループを作成する方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: mikeraymsft
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: bddc83d55c8909412f7f935a4324a6f316a82cd7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "62129555"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Azure Virtual Machines の Always On 可用性グループを自動的に構成するリソース マネージャー

このチュートリアルでは、Azure Resource Manager 仮想マシンを使用する SQL Server 可用性グループを作成する方法について説明します。 このチュートリアルでは、テンプレートの構成に Azure ブレードを使用します。 既定の設定を確認し、必要な設定を入力して、ポータルでブレードを更新します。

この完全なチュートリアルでは、次の要素を含む Azure Virtual Machines 上の SQL Server 可用性グループを作成します。

* 複数のサブネット (フロントエンドのサブネットとバックエンドのサブネットなど) を含む仮想ネットワーク
* Active Directory ドメインを持つ 2 つのドメイン コントローラー
* SQL Server を実行し、バックエンドのサブネットにデプロイされて Active Directory ドメインに参加している 2 つの仮想マシン
* 3 ノードのフェールオーバー クラスター (ノード マジョリティ クォーラム モデル)
* 可用性データベースの 2 つの同期コミット レプリカを含む可用性グループ

次の図は完全なソリューションを表しています。

![Azure の可用性グループのテスト ラボ アーキテクチャ](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

このソリューションのすべてのリソースは、1 つのリソース グループに属しています。

このチュートリアルを開始する前に、次を確認してください。

* Azure アカウントを既に所有している。 お持ちでない場合は、 [試用版アカウントにサインアップ](https://azure.microsoft.com/pricing/free-trial/)してください。
* GUI を使用して、仮想マシン ギャラリーから SQL Server 仮想マシンをプロビジョニングする方法を知っている。 詳細については、「[Azure ポータルでの SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)」を参照してください。
* 可用性グループについて十分に理解している。 詳細については、「 [AlwaysOn 可用性グループ (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)」をご覧ください。

> [!NOTE]
> SharePoint での SQL Server 可用性グループの使用に関心がある場合は、「 [SQL Server 2012 の AlwaysOn 可用性グループを SharePoint 2013 用に構成する](https://technet.microsoft.com/library/jj715261.aspx)」をご覧ください。
>
>

このチュートリアルでは、Azure Portal を使用して次の作業を行います。

* ポータルから AlwaysOn テンプレートを選択する。
* テンプレート設定を確認し、環境用にいくつかの構成設定を更新する。
* Azure が環境全体を作成する過程を監視する。
* ドメイン コントローラーに接続してから、SQL Server を実行するサーバーに接続する。

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>ギャラリーからクラスターをプロビジョニングする
Azure では、ソリューション全体のギャラリー イメージを提供します。 テンプレートを見つけるには、次を実行します。

1. アカウントを使用して Azure Portal にサインインします。
2. Azure Portal で、 **[リソースの作成]** をクリックして **[新規]** ウィンドウを開きます。
3. **[新規]** ウィンドウで **AlwaysOn** を検索します。
   ![AlwaysOn テンプレートを見つける](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. 検索結果から **[SQL Server AlwaysOn Cluster (SQL Server AlwaysOn クラスター)]** を見つけます。
   ![AlwaysOn テンプレート](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. **[デプロイ モデルの選択]** で **[Resource Manager]** を選択します。

### <a name="basics"></a>基本
**[基本]** をクリックし、次の設定を構成します。

* **[管理者ユーザー名]** は、ドメインの管理者権限を持つユーザー アカウントであり、また SQL Server の両方のインスタンスの SQL Server sysadmin 固定サーバー ロールのメンバーです。 このチュートリアルでは **DomainAdmin** を使用します。
* **[パスワード]** は、ドメイン管理者アカウントのパスワードです。 複雑なパスワードを使用します。 パスワードを確認入力します。
* **[サブスクリプション]** は、可用性グループ用にデプロイされたすべてのリソースの実行に対して課金されるサブスクリプションです。 アカウントに複数のサブスクリプションがある場合は、別のサブスクリプションを指定できます。
* **[リソース グループ]** は、このテンプレートで作成されたすべての Azure リソースが属するグループの名前です。 このチュートリアルでは **SQL-HA-RG** を使用します。 詳細については、「[Azure Resource Manager の概要](../../../azure-resource-manager/resource-group-overview.md#resource-groups)」を参照してください。
* **[場所]** は、このチュートリアルでリソースを作成する Azure リージョンです。 Azure リージョンを選択します。

次のスクリーンショットは、設定が完了した **[基本]** ブレードを示しています。

![基本](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Click **OK**.

### <a name="domain-and-network-settings"></a>ドメインおよびネットワークの設定
この Azure ギャラリー テンプレートでは、ドメインとドメイン コントローラーを作成します。 また、ネットワークと 2 つのサブネットも作成します。 このテンプレートでは、既存のドメインまたは仮想ネットワークには、サーバーを作成できません。 次の手順では、ドメインおよびネットワーク設定を構成します。

**[Domain and network settings] \(ドメインおよびネットワークの設定)** ブレードで、ドメインとネットワークの設定の、事前設定されている次の値を確認します。

* **[フォレスト ルート ドメイン名]** は、クラスターをホストする Active Directory ドメインのドメイン名です。 このチュートリアルでは **contoso.com** を使用します。
* **[仮想ネットワーク名]** は、Azure の仮想ネットワークのネットワーク名です。 このチュートリアルでは **autohaVNET** を使用します。
* **[Domain Controller subnet name (ドメイン コントローラーのサブネット名)]** は、ドメイン コントローラーをホストする仮想ネットワークの部分の名前です。 **subnet-1** を使用します このサブネットでは、アドレス プレフィックスとして **10.0.0.0/24** を使用します。
* **[SQL Server のサブネット名]** は、SQL サーバーとファイル共有監視を実行するサーバーをホストする仮想ネットワークの部分の名前です。 **subnet-2** を使用します。 このサブネットでは、アドレス プレフィックスとして **10.0.1.0/26** を使用します。

Azure の仮想ネットワークの詳細については、[仮想ネットワークの概要](../../../virtual-network/virtual-networks-overview.md)に関する記事をご覧ください。  

**[Domain and network settings] \(ドメインおよびネットワークの設定)** は次のスクリーンショットのようになります。

![ドメインおよびネットワークの設定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

これらの値は、必要に応じて変更することができます。 このチュートリアルでは、事前設定されている値を使用します。

設定を確認し、 **[OK]** をクリックします。

### <a name="availability-group-settings"></a>可用性グループの設定
**[Availability group settings (可用性グループの設定)]** で、可用性グループとリスナーの事前設定されている値を確認します。

* **[可用性グループ名]** は、可用性グループのクラスター化されたリソース名です。 このチュートリアルでは **Contoso-ag** を使用します。
* **[可用性グループ リスナーの名前]** は、クラスターおよび内部ロード バランサーによって使用されます。 SQL Server に接続するクライアントは、この名前を使用し、データベースの適切なレプリカに接続できます。 このチュートリアルでは **Contoso-listener** を使用します。
* **[可用性グループ リスナーのポート]** には、SQL Server リスナーが使用する TCP ポートを指定します。 このチュートリアルでは、既定のポート **1433** を使用します。

これらの値は、必要に応じて変更することができます。 このチュートリアルでは、事前設定されている値を使用します。  

![可用性グループの設定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Click **OK**.

### <a name="virtual-machine-size-storage-settings"></a>VM サイズおよび記憶域の設定
**[VM size, storage settings] \(VM サイズおよび記憶域の設定)** では、SQL Server 仮想マシンのサイズを選択し、その他の設定を確認します。

* **[SQL Server virtual machine size] \(SQL Server 仮想マシンのサイズ)** は、SQL Server を実行する両方の仮想マシンのサイズです。 ワークロードに適した仮想マシンのサイズを選択します。 このチュートリアル用に環境を構築する場合は **DS2** を使用します。 実稼働ワークロードでは、ワークロードをサポートできる仮想マシン サイズを選択します。 多くの実稼働ワークロードでは、**DS4** 以上が必要です。 このテンプレートでは、このサイズの仮想マシンを 2 つ構築し、それぞれに SQL Server をインストールします。 詳細については、 [仮想マシンのサイズ](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。

> [!NOTE]
> Azure によって、SQL Server Enterprise Edition がインストールされます。 価格は、エディションと仮想マシンのサイズによって異なります。 現在の価格の詳細については、「[Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)」を参照してください。
>
>

* **[Domain controller virtual machine size (ドメイン コントローラーの仮想マシンのサイズ)]** は、ドメイン コントローラー用の仮想マシンのサイズです。 このチュートリアルでは **D2**を使用します。
* **[File Share Witness virtual machine size (ファイル共有監視の仮想マシンのサイズ)]** は、ファイル共有監視の仮想マシンのサイズです。 このチュートリアルでは **A1** を使用します。
* **[SQL ストレージ アカウント]** は、SQL Server のデータとオペレーティング システム ディスクを保持するストレージ アカウントの名前です。 このチュートリアルでは **alwaysonsql01** を使用します。
* **[DC Storage account (DC ストレージ アカウント)]** は、ドメイン コントローラーのストレージ アカウントの名前です。 このチュートリアルでは **alwaysondc01** を使用します。
* **[SQL Server data disk size in TB (SQL Server のデータ ディスク サイズ (TB))]** は、SQL Server のデータ ディスクの TB 単位でのサイズです。 1 から 4 までの数字を指定します。 このチュートリアルでは **1** を使用します。
* **[Storage optimization (記憶域の最適化)]** では、ワークロードの種類に基づいて SQL Server の仮想マシンの特定の記憶域の構成を設定します。 このシナリオのすべての SQL Server 仮想マシンでは、Azure ディスクのホスト キャッシュが読み取り専用に設定された Premium ストレージを使用します。 また、次の 3 つのいずれかの設定を選択すると、ワークロード用に SQL Server の設定を最適化できます。

  * **[General workload] \(一般的なワークロード)** では、特定の構成は設定しません。
  * **[トランザクション処理]** では、トレース フラグ 1117 と 1118 を設定します。
  * **[データ ウェアハウス]** では、トレース フラグ 1117 と 610 を設定します。

このチュートリアルでは **[General workload] \(一般的なワークロード)** を使用します。

![VM サイズの記憶域の設定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

設定を確認し、 **[OK]** をクリックします。

#### <a name="a-note-about-storage"></a>記憶域に関する注意点
さらなる最適化には、SQL Server のデータ ディスクのサイズが関係します。 Azure では、データ ディスクのテラバイトごとに、Premium ストレージが 1 TB 追加されます。 サーバーに 2 TB 以上が必要な場合、テンプレートは各 SQL Server 仮想マシンに記憶域プールを作成します。 記憶域プールとは、容量を増やし、回復性、およびパフォーマンスを向上させるために複数のディスクが構成された仮想化された記憶域の形式です。  その後、記憶域プールに記憶域スペースが作成され、オペレーティング システムに 1 つのデータ ディスクとして提供されます。 テンプレートは、このディスクを SQL Server のデータ ディスクとして指定します。 テンプレートは、次の設定を使用して SQL Server の記憶域プールを調整します。

* ストライプ サイズは、仮想ディスクのインターリーブ設定です。 トランザクションのワークロードでは、64 KB を使用します。 データ ウェアハウスのワークロードでは、256 KB を使用します。
* 回復性については単純です (回復性はありません)。

> [!NOTE]
> Azure Premium ストレージは、1 つのリージョンに 3 つのデータのコピーを保持するローカル冗長であるため、記憶域プールに追加の回復性は不要です。
>
>

* 列数は、記憶域プール内のディスクの数と同じです。

記憶域スペースと記憶域プールの詳細については、次のページを参照してください。

* [記憶域スペースの概要](https://technet.microsoft.com/library/hh831739.aspx)
* [Windows Server バックアップと記憶域プール](https://technet.microsoft.com/library/dn390929.aspx)

SQL Server の構成のベスト プラクティスについては、「[Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md)」を参照してください。

### <a name="sql-server-settings"></a>SQL Server の設定
**[SQL Server の設定]** では、SQL Server 仮想マシン名のプレフィックス、SQL Server のバージョン、SQL Server のサービス アカウントとパスワード、SQL の自動修正メンテナンス スケジュールを確認および変更できます。

* **[SQL Server Name Prefix] \(SQL Server 名のプレフィックス)** は、各 SQL Server 仮想マシンの名前を作成するために使用します。 このチュートリアルでは **sqlserver** を使用します。 テンプレートにより、SQL Server 仮想マシンに *sqlserver-0* および *sqlserver-1* という名前が付けられます。
* **[SQL Server のバージョン]** は、SQL Server のバージョンです。 このチュートリアルでは **[SQL Server 2014]** を使用します。 **[SQL Server 2012]** または **[SQL Server 2016]** を選択することもできます。
* **[SQL Server service account user name (SQL Server サービス アカウント ユーザー名)]** は、SQL Server サービスのドメイン アカウント名です。 このチュートリアルでは **sqlservice** を使用します。
* **[パスワード]** は、SQL Server サービス アカウントのパスワードです。  複雑なパスワードを使用します。 パスワードを確認入力します。
* **[SQL Auto Patching maintenance schedule] \(SQL 自動修正メンテナンス スケジュール)** では、Azure が SQL Server に修正を自動的に適用する曜日を特定します。 このチュートリアルでは、「**日曜日**」と入力します。
* **[SQL Auto Patching maintenance start hour (SQL 自動修正メンテナンスの開始時間)]** は、Azure リージョンで自動修正が開始される時間です。

> [!NOTE]
> 各仮想マシンの修正時間は 1 時間に調整されています。 サービスの中断を防ぐため、一度に 1 つの仮想マシンにのみ修正が適用されます。
>
>

![SQL Server の設定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

設定を確認し、 **[OK]** をクリックします。

### <a name="summary"></a>まとめ
[概要] ページでは Azure によって設定が検証されます。 テンプレートをダウンロードすることもできます。 概要を確認します。 Click **OK**.

### <a name="buy"></a>購入
この最後のブレードには **[使用条件]** と **[プライバシー ポリシー]** が掲載されています。 この情報を確認します。 Azure で仮想マシンと可用性グループに必要な他のすべてのリソースの作成を開始する準備ができたら、 **[作成]** をクリックします。

Azure Portal によって、リソース グループとすべてのリソースが作成されます。

## <a name="monitor-deployment"></a>デプロイの監視
Azure ポータルでデプロイの進捗状況を監視できます。 デプロイを表すアイコンが、Azure Portal のダッシュボードに自動的に固定されます。

![Azure ダッシュ ボード](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>SQL Server への接続
SQL Server の新しいインスタンスは、インターネットに接続された IP アドレスを持つ仮想マシンで実行されます。 各 SQL Server 仮想マシンに、直接リモート デスクトップ (RDP) 接続することができます。

SQL Server に RDP 接続するには、次の手順に従います。

1. Azure Portal のダッシュ ボードで、デプロイが成功したことを確認します。
2. **[リソース]** をクリックします。
3. **[リソース]** ブレードで、SQL Server を実行する仮想マシンの 1 つのコンピューター名である **[sqlserver-0]** をクリックします。
4. **sqlserver-0** のブレードで **[接続]** をクリックします。 ブラウザーから、リモート接続オブジェクトを開くか保存するかをたずねられます。 **[開く]** をクリックします。
5. **リモート デスクトップ接続** で、このリモート接続の発行元が識別できないことが通知される場合があります。 **[接続]** をクリックします。
6. Windows のセキュリティによって、プライマリ ドメイン コントローラーの IP アドレスに接続するための資格情報の入力が求められます。 **[別のアカウントを使用する]** をクリックします。 **[ユーザー名]** に「**contoso\DomainAdmin**」と入力します。 このアカウントは、テンプレートで管理者ユーザー名を設定したときに構成したものです。 テンプレートを構成したときに選択した複雑なパスワードを使用します。
7. **リモート デスクトップ** により、セキュリティ証明書の問題のためこのリモート コンピューターを認証できなかったという警告が表示される場合があります。 そのセキュリティ証明書の名前が表示されます。 このチュートリアルに従った場合、名前は **sqlserver-0.contoso.com** になります。 **[はい]** をクリックします。

これで SQL Server 仮想マシンに RDP 接続できました。 SQL Server Management Studio を開き、SQL Server の既定のインスタンスに接続して、可用性グループが構成済みであることを確認できます。

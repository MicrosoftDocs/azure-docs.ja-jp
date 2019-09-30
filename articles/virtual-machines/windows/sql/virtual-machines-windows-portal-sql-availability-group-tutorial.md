---
title: SQL Server 可用性グループ - Azure Virtual Machines - チュートリアル | Microsoft Docs
description: このチュートリアルでは、Azure Virtual Machines に SQL Server Always On 可用性グループを作成する方法を説明します。
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.openlocfilehash: 6485b7c102977f4fb6963418084f4da050c68558
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036521"
---
# <a name="tutorial-configure-always-on-availability-group-in-azure-vm-manually"></a>チュートリアル:Azure VM での AlwaysOn 可用性グループの手動構成

このチュートリアルでは、Azure Virtual Machines に SQL Server Always On 可用性グループを作成する方法を説明します。 チュートリアル全体では、2 つの SQL Server にデータベース レプリカで可用性グループを作成します。

**推定所要時間**: 前提条件が満たされてから完了までに約 30 分かかります。

次の図は、チュートリアルで構築するものを示しています。

![可用性グループ](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、SQL Server Always On 可用性グループの基本的な知識があることを前提としています。 詳しくは、「[Always On 可用性グループの概要 (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx)」をご覧ください。

次の表に、このチュートリアルを開始する前に完了している必要がある前提条件を示します。

|  |要件 |説明 |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | 2 つの SQL Server | - Azure 可用性セット内 <br/> - 単一のドメイン内 <br/> - フェールオーバー クラスタリング機能インストール済み |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | クラスター監視用のファイル共有 |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server サービス アカウント | ドメイン アカウント |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server エージェント サービス アカウント | ドメイン アカウント |  
|![正方形](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|ファイアウォール ポートを開く | - SQL Server:**1433** (既定インスタンス用) <br/> - データベース ミラーリング エンドポイント:**5022** または使用可能な任意のポート <br/> - 可用性グループ ロードバランサーの IP アドレスの正常性プローブ:**59999** または使用可能な任意のポート <br/> - クラスター コア ロードバランサーの IP アドレスの正常性プローブ:**58888** または使用可能な任意のポート |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|フェールオーバー クラスタリング機能を追加する | 両方の SQL Server にこの機能が必要です |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|インストール ドメイン アカウント | - 各 SQL Server 上のローカル管理者 <br/> - SQL Server の各インスタンスの SQL Server sysadmin 固定サーバー ロールのメンバー  |


チュートリアルを始める前に、[Azure Virtual Machines で Always On 可用性グループを作成するための前提条件を満たす](virtual-machines-windows-portal-sql-availability-group-prereq.md)必要があります。 これらの前提条件が既に満たされている場合は、「[クラスターを作成する](#CreateCluster)」に進んでかまいません。

  >[!NOTE]
  > このチュートリアルで説明する手順の多くは、[Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) と [Azure クイック スタート テンプレート](virtual-machines-windows-sql-availability-group-quickstart-template.md)で自動化できるようになりました。


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>クラスターを作成する

前提条件が完了した後は、最初に、2 つの SQL Sever と監視サーバーを含む Windows Server フェールオーバー クラスターを作成します。

1. SQL Server と監視サーバー両方の管理者であるドメイン アカウントを使って、1 番目の SQL Server に RDP 接続します。

   >[!TIP]
   >[前提条件のドキュメント](virtual-machines-windows-portal-sql-availability-group-prereq.md)に従っている場合、**CORP\Install** という名前のアカウントを作成しているはずです。 このアカウントを使います。

2. **[サーバー マネージャー]** ダッシュボードで、 **[ツール]** を選択し、 **[フェールオーバー クラスター マネージャー]** をクリックします。
3. 左のウィンドウで、 **[フェールオーバー クラスター マネージャー]** を右クリックし、 **[クラスターの作成]** をクリックします。
   ![クラスターの作成](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. クラスターの作成ウィザードの各ページで、次の表の設定を使って、単一ノード クラスターを作成します。

   | ページ | 設定 |
   | --- | --- |
   | 開始する前に |既定値を使用 |
   | サーバーの選択 |1 番目の SQL Server の名前を **[サーバー名を入力してください]** に入力し、 **[追加]** をクリックします。 |
   | 検証の警告 |**[いいえ、このクラスターに Microsoft のサポートは必要ありませんので、検証テストを実行しません。[次へ] をクリックして、クラスターの作成を続行します。]** を選択します。 |
   | クラスター管理用のアクセス ポイント |**[クラスター名]** にクラスター名を入力します (例: **SQLAGCluster1**)。|
   | 確認 |記憶域スペースを使用している場合を除き、既定値を使用します。 この表の次の注を参照してください。 |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Windows Server フェールオーバー クラスターの IP アドレスを設定する

  > [!NOTE]
  > Windows Server 2019 では、クラスターは**クラスターネットワーク名**ではなく、**分散サーバー名**を作成します。 Windows Server 2019 を使用している場合は、このチュートリアルでクラスターのコア名を参照するすべての手順をスキップしてください。 クラスター ネットワーク名は、[PowerShell](virtual-machines-windows-portal-sql-create-failover-cluster.md#windows-server-2019) を使用して作成できます。 詳細については、ブログ記事内の「[フェールオーバー クラスター: クラスター ネットワーク オブジェクト](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)」を参照してください。 

1. **フェールオーバー クラスター マネージャー**で、 **[クラスター コア リソース]** まで下にスクロールして、クラスターの詳細を展開します。 **[名前]** と **[IP アドレス]** リソースの両方が **[失敗]** 状態で表示されます。 クラスターにコンピューター自体と同じ IP アドレスが割り当てられていて、アドレスが重複するため、IP アドレス リソースをオンラインにすることができません。

2. 失敗した **IP アドレス** リソースを右クリックし、 **[プロパティ]** をクリックします。

   ![クラスターのプロパティ](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. **[静的 IP アドレス]** を選択し、仮想マシンと同じサブネットの使用可能なアドレスを指定します。

4. **[クラスター コア リソース]** セクションで、クラスター名を右クリックして、 **[オンラインにする]** をクリックします。 両方のリソースがオンラインになるまで待ちます。 クラスター名リソースがオンラインになると、新しい AD コンピューター アカウントで DC サーバーが更新されます。 この AD アカウントは、後で可用性グループのクラスター化サービスを実行するときに使います。

### <a name="addNode"></a>他の SQL Server をクラスターに追加する

他の SQL Server をクラスターに追加します。

1. ブラウザー ツリーでクラスターを右クリックし、 **[ノードの追加]** をクリックします。

    ![クラスターにノードを追加](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. **ノードの追加ウィザード**で、 **[次へ]** をクリックします。 **[サーバーの選択]** ページで、2 番目の SQL Server を追加します。 サーバー名を **[サーバー名を入力してください]** に入力し、 **[追加]** をクリックします。 完了したら、 **[次へ]** をクリックします。

1. **[検証の警告]** ページで **[いいえ]** をクリックします (実際の運用時には、検証テストを実施する必要があります)。 次に、 **[次へ]** をクリックします。

8. 記憶域スペースを使っている場合は、 **[確認]** ページの **[使用可能な記憶域をすべてクラスターに追加する]** チェック ボックスをオフにします。

   ![ノードの追加の確認](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >記憶域スペースを使っている場合に、 **[使用可能な記憶域をすべてクラスターに追加する]** をオフにしないと、Windows はクラスター作成処理中に仮想ディスクをデタッチします。 その結果、記憶域スペースがクラスターから削除され、PowerShell を使って再アタッチされるまで、仮想ディスクはディスク マネージャーやエクスプローラーに表示されなくなります。 記憶域スペースは、複数のディスクを記憶域プールにグループ化します。 詳細については、[記憶域スペース](https://technet.microsoft.com/library/hh831739)に関するページを参照してください。

1. **[次へ]** をクリックします。

1. **[完了]** をクリックします。

   フェールオーバー クラスター マネージャーで、クラスターに新しいノードがあることが示され、そのノードが **[ノード]** コンテナーの一覧に表示されます。

10. リモート デスクトップ セッションからログアウトします。

### <a name="add-a-cluster-quorum-file-share"></a>クラスター クォーラム ファイル共有を追加する

この例では、Windows クラスターはファイル共有を使ってクラスター クォーラムを作成します。 このチュートリアルでは、"ノードおよびファイル共有マジョリティ" クォーラムを使います。 詳しくは、「[フェールオーバー クラスターのクォーラム構成について](https://technet.microsoft.com/library/cc731739.aspx)」をご覧ください。

1. リモート デスクトップ セッションでファイル共有監視メンバー サーバーに接続します。

1. **サーバー マネージャー**で、 **[ツール]** をクリックします。 **[コンピューターの管理]** を開きます。

1. **[共有フォルダー]** をクリックします。

1. **[共有]** を右クリックし、 **[新しい共有...]** をクリックします。

   ![新しい共有](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   **共有フォルダーの作成ウィザード**を使って共有を作成します。

1. **[フォルダー パス]** で、 **[参照]** をクリックして、共有フォルダーのパスを検索するか作成します。 **[次へ]** をクリックします。

1. **[名前、説明および設定]** で、共有名とパスを確認します。 **[次へ]** をクリックします。

1. **[共有フォルダーのアクセス許可]** で、 **[アクセス許可のカスタマイズ]** を設定します。 **[カスタム...]** をクリックします。

1. **[アクセス許可のカスタマイズ]** で、 **[追加...]** をクリックします。

1. クラスターの作成に使うアカウントがフル コントロールを持っていることを確認します。

   ![新しい共有](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Click **OK**.

1. **[共有フォルダーのアクセス許可]** で、 **[完了]** をクリックします。 もう一度 **[完了]** をクリックします。  

1. サーバーからログアウトします。

### <a name="configure-cluster-quorum"></a>クラスター クォーラムを構成する

次に、クラスター クォーラムを設定します。

1. リモート デスクトップで 1 番目のクラスター ノードに接続します。

1. **フェールオーバー クラスター マネージャー**で、クラスターを右クリックし、 **[他の操作]** をポイントして、 **[クラスター クォーラム設定の構成...]** をクリックします。

   ![新しい共有](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. **クラスター クォーラム構成ウィザード**で、 **[次へ]** をクリックします。

1. **[クォーラム構成オプションの選択]** で、 **[クォーラム監視を選択する]** を選び、 **[次へ]** をクリックします。

1. **[クォーラム監視の選択]** で、 **[ファイル共有監視を構成する]** をクリックします。

   >[!TIP]
   >Windows Server 2016 では、クラウド監視がサポートされています。 この種類の監視を選んだ場合は、ファイル共有監視は必要ありません。 詳しくは、「[Deploy a cloud witness for a Failover Cluster](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)」(フェールオーバー クラスターにクラウド監視を展開する) をご覧ください。 このチュートリアルでは、以前のオペレーティング システムでサポートされているファイル共有監視を使います。

1. **[ファイル共有監視の構成]** で、作成した共有のパスを入力します。 **[次へ]** をクリックします。

1. **[確認]** で設定を確認します。 **[次へ]** をクリックします。

1. **[完了]** をクリックします。

クラスター コア リソースにファイル共有監視が構成されます。

## <a name="enable-availability-groups"></a>可用性グループを有効にする

次に、**AlwaysOn 可用性グループ**機能を有効にします。 以下の手順は、両方の SQL Server に対して実行してください。

1. **スタート**画面から **SQL Server 構成マネージャー**を起動します。
2. ブラウザー ツリーで、 **[SQL Server のサービス]** をクリックし、 **[SQL Server (MSSQLSERVER)]** サービスを右クリックして、 **[プロパティ]** をクリックします。
3. 次に示すように、 **[AlwaysOn 高可用性]** タブをクリックし、 **[AlwaysOn 可用性グループを有効にする]** をオンにします。

    ![AlwaysOn 可用性グループの有効化](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. **[Apply]** をクリックします。 ポップアップ ダイアログで **[OK]** をクリックします。

5. SQL Server サービスを再起動します。

もう一方の SQL Server についても同じ手順を繰り返します。

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, click **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and click **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and click **Next**.
6. In the **Profile** page, accept the default settings and click **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then click **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>1 番目の SQL Server でデータベースを作成する

1. sysadmin 固定サーバー ロールのメンバーであるドメイン アカウントを使って、1 番目の SQL Server に対する RDP ファイルを起動します。
1. SQL Server Management Studio を開き、1 番目の SQL Server に接続します。
7. **オブジェクト エクスプローラー**で、 **[データベース]** を右クリックし、 **[新しいデータベース]** をクリックします。
8. **[データベース名]** に「**MyDB1**」と入力し、 **[OK]** をクリックします。

### <a name="backupshare"></a>バックアップ共有を作成する

1. **サーバー マネージャー**の 1 番目の SQL Server で、 **[ツール]** をクリックします。 **[コンピューターの管理]** を開きます。

1. **[共有フォルダー]** をクリックします。

1. **[共有]** を右クリックし、 **[新しい共有...]** をクリックします。

   ![新しい共有](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   **共有フォルダーの作成ウィザード**を使って共有を作成します。

1. **[フォルダー パス]** で、 **[参照]** をクリックして、データベース バックアップ共有フォルダーのパスを検索するか作成します。 **[次へ]** をクリックします。

1. **[名前、説明および設定]** で、共有名とパスを確認します。 **[次へ]** をクリックします。

1. **[共有フォルダーのアクセス許可]** で、 **[アクセス許可のカスタマイズ]** を設定します。 **[カスタム...]** をクリックします。

1. **[アクセス許可のカスタマイズ]** で、 **[追加...]** をクリックします。

1. SQL Server アカウントと SQL Server エージェント サービス アカウントの両方にフル コントロールがあることを確認します。

   ![新しい共有](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Click **OK**.

1. **[共有フォルダーのアクセス許可]** で、 **[完了]** をクリックします。 もう一度 **[完了]** をクリックします。  

### <a name="take-a-full-backup-of-the-database"></a>データベースの完全バックアップを作成する

ログ チェーンを初期化するには、新しいデータベースをバックアップする必要があります。 新しいデータベースのバックアップを作成しないと、データベースを可用性グループに含めることはできません。

1. **オブジェクト エクスプローラー**で、データベースを右クリックし、 **[タスク...]** をポイントして、 **[バックアップ]** をクリックします。

1. **[OK]** をクリックして、既定のバックアップ場所に完全バックアップを作成します。

## <a name="create-the-availability-group"></a>可用性グループの作成
次の手順で可用性グループを構成できるようになりました。

* 1 番目の SQL Server でデータベースを作成する
* データベースの完全バックアップとトランザクション ログ バックアップの両方を作成する
* **NORECOVERY** オプションを使って、完全バックアップとログ バックアップを 2 番目の SQL Server に復元する
* 同期コミット、自動フェールオーバー、読み取り可能なセカンダリ レプリカを含む可用性グループ (**AG1**) を作成する

### <a name="create-the-availability-group"></a>可用性グループを作成する

1. 1 番目の SQL Server へのリモート デスクトップ セッションを開きます。 SSMS の**オブジェクト エクスプローラー**で、 **[AlwaysOn 高可用性]** を右クリックして **[新しい可用性グループ ウィザード]** をクリックします。

    ![[新しい可用性グループ ウィザード] の起動](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. **[説明]** ページで **[次へ]** をクリックします。 **[可用性グループ名の指定]** ページで、 **[可用性グループ名]** に可用性グループの名前 (例: **AG1**) を入力します。 **[次へ]** をクリックします。

    ![新しい AG ウィザード、AG 名の指定](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. **[データベースの選択]** ページで、データベースを選び、 **[次へ]** をクリックします。

   >[!NOTE]
   >対象とするプライマリ レプリカで完全バックアップを少なくとも 1 つは作成しているため、このデータベースは可用性グループの前提条件を満たしています。

   ![新しい AG ウィザード、データベースの選択](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. **[レプリカの指定]** ページで **[レプリカの追加]** をクリックします。

   ![新しい AG ウィザード、レプリカの指定](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. **[サーバーに接続]** ダイアログが表示されます。 2 番目のサーバーの名前を **[サーバー名]** に入力します。 **[接続]** をクリックします。

   **[レプリカの指定]** ページに戻ると、 **[可用性レプリカ]** の一覧に 2 番目のサーバーが表示されていることがわかります。 次に示すようにレプリカを構成します。

   ![新しい可用性グループ ウィザード、レプリカの指定 (完了)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. **[エンドポイント]** をクリックして、この可用性グループのデータベース ミラーリング エンドポイントを表示します。 [データベース ミラーリング エンドポイントのファイアウォール ルール](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall)を設定するときに使ったものと同じポートを使います。

    ![新しい可用性グループ ウィザード、最初のデータの同期を選択](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. **[最初のデータ同期を選択]** ページで、 **[完全]** を選び、共有ネットワークの場所を指定します。 この場所としては、[先に作成したバックアップ共有](#backupshare)を使います。 この例では、 **\\\\\<1 番目の SQL Server\>\Backup\\** でした。 **[次へ]** をクリックします。

   >[!NOTE]
   >完全同期では、SQL Server の 1 番目のインスタンスにあるデータベースの完全バックアップが作成されて、2 番目のインスタンスに復元されます。 大規模なデータベースの場合、完全同期は長い時間がかかることがあるのでお勧めできません。 手動でデータベースのバックアップを作成し、`NO RECOVERY` で復元することにより、この時間を短縮できます。 可用性グループを構成する前に 2 番目の SQL Server のデータベースが `NO RECOVERY` で既に復元されている場合は、 **[参加のみ]** を選びます。 可用性グループを構成した後でバックアップを作成する場合は、 **[最初のデータ同期をスキップ]** を選びます。

    ![新しい可用性グループ ウィザード、最初のデータの同期を選択](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. **[検証]** ページで **[次へ]** をクリックします。 このページは次の図のようになります。

    ![新しい AG ウィザード、検証](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >可用性グループ リスナーを構成していないため、リスナー構成に関する警告が表示されます。 Azure Virtual Machines で Azure Load Balancer を作成した後にリスナーを作成するので、この警告は無視してかまいません。

10. **[概要]** ページで、 **[完了]** をクリックし、新しい可用性グループが構成されるまで待ちます。 **[進行状況]** ページで **[詳細]** をクリックすると、進行状況が詳しく表示されます。 ウィザードが完了したら、 **[結果]** ページを調べて、可用性グループが正常に作成されたことを確認します。

     ![新しい AG ウィザード、結果](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. **[閉じる]** をクリックしてウィザードを終了します。

### <a name="check-the-availability-group"></a>可用性グループを確認する

1. **オブジェクト エクスプローラー**で、 **[AlwaysOn 高可用性]** 、 **[可用性グループ]** の順に展開します。 このコンテナー内に新しい可用性グループが表示されます。 可用性グループを右クリックし、 **[ダッシュボードの表示]** をクリックします。

   ![AG ダッシュボードの表示](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   **AlwaysOn ダッシュボード** は次のように表示されます。

   ![AG ダッシュボード](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   レプリカ、各レプリカのフェールオーバー モード、および同期の状態を確認できます。

2. **フェールオーバー クラスター マネージャー**で、クラスターをクリックします。 **[役割]** を選びます。 使った可用性グループの名前は、クラスターでの役割です。 リスナーを構成していないため、その可用性グループにはクライアント接続用の IP アドレスがありません。 Azure Load Balancer を作成した後で、リスナーを構成します。

   ![フェールオーバー クラスター マネージャー内のAG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > フェールオーバー クラスター マネージャーから、可用性グループのフェールオーバーを実行しないでください。 すべてのフェールオーバー操作は、SSMS の **AlwaysOn ダッシュボード** から実行する必要があります。 詳細については、[フェールオーバー クラスター マネージャーと可用性グループの使用に関する制限事項](https://msdn.microsoft.com/library/ff929171.aspx)のページを参照してください。
    >

この時点で、SQL Server の 2 つのインスタンス上にレプリカを持つ可用性グループができています。 可用性グループは、インスタンス間で移動できます。 リスナーがないため、可用性グループにまはだ接続できません。 Azure Virtual Machines では、リスナーにはロード バランサーが必要です。 次に、Azure でロード バランサーを作成します。

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Azure Load Balancer を作成する

Azure Virtual Machines では、SQL Server 可用性グループにはロード バランサーが必要です。 ロード バランサーは、可用性グループ リスナーと Windows Server フェールオーバー クラスターの IP アドレスを保持しています。 このセクションでは、Azure Portal でロード バランサーを作成する方法の概要を説明します。

Azure Load Balancer には、Standard Load Balancer または Basic Load Balancer のいずれかを使用できます。 Standard Load Balancer には、Basic Load Balancer よりも多くの機能があります。 可用性グループで、(可用性セットではなく) 可用性ゾーンを使用する場合は、Standard Load Balancer が必要です。 ロード バランサーの種類の違いについては、「[Load Balancer の SKU の比較](../../../load-balancer/load-balancer-overview.md#skus)」を参照してください。

1. Azure Portal で、SQL Server が存在するリソース グループに移動し、 **[+ 追加]** をクリックします。
1. 「**ロード バランサー**」を検索します。 Microsoft が公開しているロード バランサーを選びます。

   ![フェールオーバー クラスター マネージャー内のAG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. **Create** をクリックしてください。
1. ロード バランサーに関して次のパラメーターを構成します。

   | Setting | フィールド |
   | --- | --- |
   | **Name** |ロード バランサーのテキスト名を使います (例: **sqlLB**)。 |
   | **Type** |内部 |
   | **Virtual Network** |Azure 仮想ネットワークの名前を使います。 |
   | **サブネット** |仮想マシンが存在するサブネットの名前を使います。  |
   | **IP アドレスの割り当て** |静的 |
   | **IP アドレス** |サブネットで利用できるアドレスを使います。 可用性グループ リスナーにはこのアドレスを使用します。 これはクラスター IP アドレスと異なることに注意してください。  |
   | **サブスクリプション** |仮想マシンと同じサブスクリプションを使います。 |
   | **Location** |仮想マシンと同じ場所を使います。 |

   Azure Portal のブレードは次のようになります。

   ![Load Balancer の作成](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. **[作成]** をクリックして、ロード バランサーを作成します。

ロード バランサーを構成するには、バックエンド プールとプローブを作成し、負荷分散規則を設定する必要があります。 これらは Azure Portal で行います。

### <a name="add-backend-pool-for-the-availability-group-listener"></a>可用性グループ リスナーのバックエンド プールを追加する

1. Azure Portal で、可用性グループに移動します。 新しく作成したロード バランサーを表示するため、ビューの更新が必要な場合があります。

   ![リソース グループでのロード バランサーの検索](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. ロード バランサーをクリックし、 **[バックエンド プール]** をクリックして、 **[+ 追加]** をクリックします。

1. バックエンド プールの名前を入力します。

1. VM を含む可用性セットにバックエンド プールを関連付けます。

1. **[ターゲット ネットワーク IP 構成]** の下で、 **[仮想マシン]** を確認し、可用性グループ レプリカをホストする仮想マシンを両方とも選択します。 ファイル共有監視サーバーは含めないでください。

   >[!NOTE]
   >仮想マシンを両方とも指定しないと、プライマリ レプリカにしか接続できません。

1. **[OK]** をクリックして、バックエンド プールを作成します。

### <a name="set-the-probe"></a>プローブを設定する

1. ロード バランサーをクリックし、 **[正常性プローブ]** をクリックして、 **[+ 追加]** をクリックします。

1. リスナーの正常性プローブを次のように設定します。

   | Setting | 説明 | 例
   | --- | --- |---
   | **Name** | Text | SQLAlwaysOnEndPointProbe |
   | **プロトコル** | TCP を選びます | TCP |
   | **ポート** | 未使用の任意のポート | 59999 |
   | **間隔**  | プローブの試行の間隔 (秒単位) |5 |
   | **異常のしきい値** | 仮想マシンが異常と判断されるために必要な、連続したプローブ失敗の数  | 2 |

1. **[OK]** をクリックして、正常性プローブを設定します。

### <a name="set-the-load-balancing-rules"></a>負荷分散規則を設定する

1. ロード バランサーをクリックし、 **[負荷分散規則]** をクリックして、 **[+ 追加]** をクリックします。

1. 次のようにリスナーの負荷分散規則を設定します。

   | Setting | 説明 | 例
   | --- | --- |---
   | **Name** | Text | SQLAlwaysOnEndPointListener |
   | **フロントエンド IP アドレス** | アドレスを選びます |ロード バランサーの作成時に作成したアドレスを使います。 |
   | **プロトコル** | TCP を選びます |TCP |
   | **ポート** | 可用性グループ リスナーのポートを使用する | 1433 |
   | **バックエンド ポート** | Direct Server Return に Floating IP を設定するときは、このフィールドは使われません | 1433 |
   | **プローブ** |プローブに指定した名前 | SQLAlwaysOnEndPointProbe |
   | **セッション永続化** | ドロップダウン リスト | **なし** |
   | **アイドル タイムアウト** | TCP 接続を開いたままにしておく時間 (分) | 4 |
   | **フローティング IP (ダイレクト サーバー リターン)** | |有効 |

   > [!WARNING]
   > Direct Server Return は作成の間に設定されます。 この値は変更しないでください。

1. **[OK]** をクリックして、リスナーの負荷分散規則を設定します。

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Windows Server フェールオーバー クラスター (WSFC) のクラスターコア IP アドレスを追加する

WSFC の IP アドレスもロード バランサー上に存在する必要があります。

1. ポータルで同じ Azure ロード バランサーに対して **[フロントエンド IP 構成]** をクリックし、 **[+ 追加]** をクリックします。 クラスター コア リソース内の WSFC 用に構成した IP アドレスを使用します。 この IP アドレスを静的として設定します。

1. このロード バランサーに対して **[正常性プローブ]** をクリックし、 **[+ 追加]** をクリックします。

1. WSFC クラスターのコア IP アドレスの正常性プローブを次のように設定します。

   | Setting | 説明 | 例
   | --- | --- |---
   | **Name** | Text | WSFCEndPointProbe |
   | **プロトコル** | TCP を選びます | TCP |
   | **ポート** | 未使用の任意のポート | 58888 |
   | **間隔**  | プローブの試行の間隔 (秒単位) |5 |
   | **異常のしきい値** | 仮想マシンが異常と判断されるために必要な、連続したプローブ失敗の数  | 2 |

1. **[OK]** をクリックして、正常性プローブを設定します。

1. 負荷分散規則を設定します。 **[負荷分散規則]** をクリックし、 **[+ 追加]** をクリックします。

1. クラスターのコア IP アドレスの負荷分散規則を次のように設定します。

   | Setting | 説明 | 例
   | --- | --- |---
   | **Name** | Text | WSFCEndPoint |
   | **フロントエンド IP アドレス** | アドレスを選びます |WSFC の IP アドレスの構成時に作成したアドレスを使用します。 これはリスナーの IP アドレスとは異なります |
   | **プロトコル** | TCP を選びます |TCP |
   | **ポート** | クラスター IP アドレスのポートを使用します。 これは、リスナー プローブ ポートには使用されない使用可能なポートです。 | 58888 |
   | **バックエンド ポート** | Direct Server Return に Floating IP を設定するときは、このフィールドは使われません | 58888 |
   | **プローブ** |プローブに指定した名前 | WSFCEndPointProbe |
   | **セッション永続化** | ドロップダウン リスト | **なし** |
   | **アイドル タイムアウト** | TCP 接続を開いたままにしておく時間 (分) | 4 |
   | **フローティング IP (ダイレクト サーバー リターン)** | |有効 |

   > [!WARNING]
   > Direct Server Return は作成の間に設定されます。 この値は変更しないでください。

1. **[OK]** をクリックして、負荷分散規則を設定します。

## <a name="configure-listener"></a> リスナーを構成する

次の手順は、フェールオーバー クラスター上の可用性グループ リスナーの構成です。

> [!NOTE]
> このチュートリアルでは、1 つの ILB IP アドレスを持つ 1 つのリスナーを作成する方法を示します。 1 つまたは複数の IP アドレスを使って 1 つまたは複数のリスナーを作成する方法については、[Azure での可用性グループ リスナーとロード バランサーの作成に関するページ](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)をご覧ください。
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>リスナーのポートを設定する

SQL Server Management Studio で、リスナー ポートを設定します。

1. SQL Server Management Studio を起動し、プライマリ レプリカに接続します。

1. **[AlwaysOn 高可用性]**  | 、 **[可用性グループ]**  | 、 **[可用性グループ リスナー]** の順に移動します。

1. フェールオーバー クラスター マネージャーで作成したリスナー名が表示されます。 リスナー名を右クリックし、 **[プロパティ]** をクリックします。

1. **[ポート]** ボックスで、可用性グループ リスナーのポート番号を指定します。 1433 が既定値です。 **[OK]** をクリックします。

これで、SQL Server 可用性グループが Resource Manager モードの Azure 仮想マシンにデプロイされました。

## <a name="test-connection-to-listener"></a>リスナーへの接続をテストする

接続をテストするには、次の手順に従います。

1. 同じ仮想ネットワークに存在する、レプリカを所有していない SQL Server に RDP で接続します。 クラスター内の他の SQL Server を使うことができます。

1. **sqlcmd** ユーティリティを使用して接続をテストします。 たとえば次のスクリプトは、Windows 認証を使用し、リスナー経由でプライマリ レプリカとの **sqlcmd** 接続を確立しています。

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   リスナーが既定のポート (1433) 以外のポートを使用している場合は、そのポートを接続文字列で指定します。 たとえば、次の sqlcmd コマンドは、ポート 1435 でリスナーに接続します。

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

SQLCMD 接続では、プライマリ レプリカをホストしている SQL Server インスタンスに対して自動的に接続されます。

> [!TIP]
> 指定したポートは、両方の SQL Server のファイアウォールで必ず開放してください。 使用する TCP ポートに対する入力方向の規則が両方のサーバーに必要となります。 詳しくは、「[ファイアウォール規則を追加または編集する](https://technet.microsoft.com/library/cc753558.aspx)」をご覧ください。

## <a name="next-steps"></a>次の手順

- [2 番目の可用性グループのロード バランサーに IP アドレスを追加](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP)します。

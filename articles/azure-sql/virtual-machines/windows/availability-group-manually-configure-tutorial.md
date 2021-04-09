---
title: チュートリアル:SQL Server Always On 可用性グループを構成する
description: このチュートリアルでは、Azure Virtual Machines に SQL Server Always On 可用性グループを作成する方法を説明します。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: feab48f32396bcc89621433930c9a9f4689d8286
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97355445"
---
# <a name="tutorial-manually-configure-an-availability-group-sql-server-on-azure-vms"></a>チュートリアル:可用性グループを手動で構成する (Azure VM 上の SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

このチュートリアルでは、Azure Virtual Machines (VM) に SQL Server の Always On 可用性グループを作成する方法を説明します。 チュートリアル全体で、2 つの SQL サーバー上にデータベース レプリカを使用して可用性グループを作成します。

この記事では可用性グループ環境を手動で構成しますが、[Azure portal](availability-group-azure-portal-configure.md)、[PowerShell や Azure CLI](availability-group-az-commandline-configure.md)、または [Azure クイックスタート テンプレート](availability-group-quickstart-template-configure.md)を使用して構成することもできます。 


**推定所要時間**: [前提条件](availability-group-manually-configure-prerequisites-tutorial.md)が満たされてから完了までに約 30 分かかります。


## <a name="prerequisites"></a>前提条件

このチュートリアルでは、SQL Server Always On 可用性グループの基本的な知識があることを前提としています。 詳しくは、「[Always On 可用性グループの概要 (SQL Server)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)」をご覧ください。

チュートリアルを始める前に、[Azure Virtual Machines で Always On 可用性グループを作成するための前提条件を満たす](availability-group-manually-configure-prerequisites-tutorial.md)必要があります。 これらの前提条件が既に満たされている場合は、「[クラスターを作成する](#CreateCluster)」に進んでかまいません。

次の表に、このチュートリアルを開始する前に完了している必要がある前提条件を示します。

| 要件 |説明 |
|----- |----- |----- |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **2 つの SQL Server インスタンス** | - Azure 可用性セット内 <br/> - 単一のドメイン内 <br/> - フェールオーバー クラスタリング機能インストール済み |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Windows Server** | クラスター監視用のファイル共有 |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **SQL Server サービス アカウント** | ドメイン アカウント |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **SQL Server エージェント サービス アカウント** | ドメイン アカウント |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **ファイアウォール ポートを開く** | - SQL Server:**1433** (既定インスタンス用) <br/> - データベース ミラーリング エンドポイント:**5022** または使用可能な任意のポート <br/> - 可用性グループ ロードバランサーの IP アドレスの正常性プローブ:**59999** または使用可能な任意のポート <br/> - クラスター コア ロードバランサーの IP アドレスの正常性プローブ:**58888** または使用可能な任意のポート |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **フェールオーバー クラスタリング機能を追加する** | 両方の SQL Server インスタンスにこの機能が必要です |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **インストール ドメイン アカウント** | - 各 SQL Server 上のローカル管理者 <br/> - SQL Server の各インスタンスの SQL Server sysadmin 固定サーバー ロールのメンバー  |

>[!NOTE]
> このチュートリアルで説明する手順の多くは、[Azure portal](availability-group-azure-portal-configure.md)、[PowerShell や Az CLI](./availability-group-az-commandline-configure.md)、[Azure クイックスタート テンプレート](availability-group-quickstart-template-configure.md)を使用して自動化できるようになりました。


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>

## <a name="create-the-cluster"></a>クラスターを作成する

前提条件が完了した後は、最初に、2 つの SQL Sever と監視サーバーを含む Windows Server フェールオーバー クラスターを作成します。

1. リモート デスクトップ プロトコル (RDP) を使用して、最初の SQL Server に接続します。 SQL Server と監視サーバー両方の管理者であるドメイン アカウントを使用します。

   >[!TIP]
   >[前提条件のドキュメント](availability-group-manually-configure-prerequisites-tutorial.md)に従っている場合、**CORP\Install** という名前のアカウントを作成しているはずです。 このアカウントを使います。

2. **[サーバー マネージャー]** ダッシュボードで、 **[ツール]** を選択し、 **[フェールオーバー クラスター マネージャー]** を選択します。
3. 左側のペインで、 **[フェールオーバー クラスター マネージャー]** を右クリックし、 **[クラスターの作成]** を選択します。

   ![クラスターの作成](./media/availability-group-manually-configure-tutorial/40-createcluster.png)

4. クラスターの作成ウィザードの各ページで、次の表の設定を使って、単一ノード クラスターを作成します。

   | ページ | 設定 |
   | --- | --- |
   | はじめに |既定値を使用 |
   | サーバーの選択 |1 番目の SQL Server の名前を **[サーバー名を入力してください]** に入力し、 **[追加]** を選択します。 |
   | 検証の警告 |**[いいえ、このクラスターに Microsoft のサポートは必要ありませんので、検証テストを実行しません。[次へ] を選択して、クラスターの作成を続行します。]** を選択します。 |
   | クラスター管理用のアクセス ポイント |**[クラスター名]** にクラスター名を入力します (例: **SQLAGCluster1**)。|
   | 確認 |記憶域スペースを使用している場合を除き、既定値を使用します。 この表の次の注を参照してください。 |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Windows Server フェールオーバー クラスターの IP アドレスを設定する

  > [!NOTE]
  > Windows Server 2019 では、クラスターは **クラスターネットワーク名** ではなく、**分散サーバー名** を作成します。 Windows Server 2019 を使用している場合は、このチュートリアルでクラスターのコア名を参照するすべての手順をスキップしてください。 クラスター ネットワーク名は、[PowerShell](failover-cluster-instance-storage-spaces-direct-manually-configure.md#create-failover-cluster) を使用して作成できます。 詳細については、「[フェールオーバー クラスター:クラスタ ネットワーク オブジェクト](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)」のブログを参照してください。 

1. **フェールオーバー クラスター マネージャー** で、 **[クラスター コア リソース]** まで下にスクロールして、クラスターの詳細を展開します。 **[名前]** と **[IP アドレス]** リソースの両方が **[失敗]** 状態で表示されます。 クラスターにコンピューター自体と同じ IP アドレスが割り当てられていて、アドレスが重複するため、IP アドレス リソースをオンラインにすることができません。

2. 失敗した **IP アドレス** リソースを右クリックし、 **[プロパティ]** を選択します。

   ![クラスターのプロパティ](./media/availability-group-manually-configure-tutorial/42_IPProperties.png)

3. **[静的 IP アドレス]** を選択し、仮想マシンと同じサブネットの使用可能なアドレスを指定します。

4. **[クラスター コア リソース]** セクションで、クラスター名を右クリックして、 **[オンラインにする]** を選択します。 両方のリソースがオンラインになるまで待ちます。 クラスター名リソースがオンラインになると、新しい Active Directory (AD) コンピューター アカウントでドメイン コントローラー (DC) サーバーが更新されます。 この AD アカウントは、後で可用性グループのクラスター化サービスを実行するときに使用します。

### <a name="add-the-other-sql-server-to-cluster"></a><a name="addNode"></a>他の SQL Server をクラスターに追加する

他の SQL Server をクラスターに追加します。

1. ブラウザー ツリーで、クラスターを右クリックし、 **[ノードの追加]** を選択します。

    ![クラスターにノードを追加](./media/availability-group-manually-configure-tutorial/44-addnode.png)

1. **ノードの追加ウィザード** で、 **[次へ]** を選択します。 **[サーバーの選択]** ページで、2 番目の SQL Server を追加します。 サーバー名を **[サーバー名を入力してください]** に入力し、 **[追加]** を選択します。 完了したら、 **[次へ]** を選択します。

1. **[検証の警告]** ページで **[いいえ]** を選択します (実際の運用シナリオでは、検証テストを実施する必要があります)。 次に、 **[次へ]** を選択します。

8. 記憶域スペースを使っている場合は、 **[確認]** ページの **[使用可能な記憶域をすべてクラスターに追加する]** チェック ボックスをオフにします。

   ![ノードの追加の確認](./media/availability-group-manually-configure-tutorial/46-addnodeconfirmation.png)

   >[!WARNING]
   >記憶域スペースを使っている場合に、 **[使用可能な記憶域をすべてクラスターに追加する]** をオフにしないと、Windows はクラスター作成処理中に仮想ディスクをデタッチします。 その結果、記憶域スペースがクラスターから削除され、PowerShell を使って再アタッチされるまで、仮想ディスクはディスク マネージャーやエクスプローラーに表示されなくなります。 記憶域スペースは、複数のディスクを記憶域プールにグループ化します。 詳細については、[記憶域スペース](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11))に関するページを参照してください。
   >

1. **[次へ]** を選択します。

1. **[完了]** を選択します。

   フェールオーバー クラスター マネージャーで、クラスターに新しいノードがあることが示され、そのノードが **[ノード]** コンテナーの一覧に表示されます。

10. リモート デスクトップ セッションからログアウトします。

### <a name="add-a-cluster-quorum-file-share"></a>クラスター クォーラム ファイル共有を追加する

この例では、Windows クラスターはファイル共有を使ってクラスター クォーラムを作成します。 このチュートリアルでは、"ノードおよびファイル共有マジョリティ" クォーラムを使います。 詳しくは、「[フェールオーバー クラスターのクォーラム構成について](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731739(v=ws.11))」をご覧ください。

1. リモート デスクトップ セッションでファイル共有監視メンバー サーバーに接続します。

1. **サーバー マネージャー** で、 **[ツール]** を選択します。 **[コンピューターの管理]** を開きます。

1. **[共有フォルダー]** を選択します。

1. **[共有]** を右クリックし、 **[新しい共有]** を選択します。

   ![[共有] を右クリックし、[新しい共有] を選択する](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   **共有フォルダーの作成ウィザード** を使って共有を作成します。

1. **[フォルダー パス]** で、 **[参照]** を選択して、共有フォルダーのパスを検索するか作成します。 **[次へ]** を選択します。

1. **[名前、説明および設定]** で、共有名とパスを確認します。 **[次へ]** を選択します。

1. **[共有フォルダーのアクセス許可]** で、 **[アクセス許可のカスタマイズ]** を設定します。 **[カスタム]** を選択します。

1. **[アクセス許可のカスタマイズ]** で、 **[追加]** を選択します。

1. クラスターの作成に使うアカウントがフル コントロールを持っていることを確認します。

   ![クラスターの作成に使うアカウントがフル コントロールを持っていることを確認する](./media/availability-group-manually-configure-tutorial/50-filesharepermissions.png)

1. **[OK]** を選択します。

1. **[共有フォルダーのアクセス許可]** で、 **[完了]** を選択します。 **[完了]** をもう一度選択します。  

1. サーバーからログアウトします。

### <a name="configure-the-cluster-quorum"></a>クラスター クォーラムを構成する

次に、クラスター クォーラムを設定します。

1. リモート デスクトップで 1 番目のクラスター ノードに接続します。

1. **フェールオーバー クラスター マネージャー** で、クラスターを右クリックし、 **[その他の操作]** をポイントして、 **[クラスター クォーラム設定の構成]** を選択します。

   ![[クラスター クォーラム設定の構成] を選択する](./media/availability-group-manually-configure-tutorial/52-configurequorum.png)

1. **クラスター クォーラム構成ウィザード** で、 **[次へ]** を選択します。

1. **[クォーラム構成オプションの選択]** で、 **[クォーラム監視を選択する]** を選択し、 **[次へ]** を選択します。

1. **[クォーラム監視の選択]** で、 **[ファイル共有監視を構成する]** を選択します。

   >[!TIP]
   >Windows Server 2016 では、クラウド監視がサポートされています。 この種類の監視を選んだ場合は、ファイル共有監視は必要ありません。 詳しくは、「[Deploy a cloud witness for a Failover Cluster](/windows-server/failover-clustering/deploy-cloud-witness)」(フェールオーバー クラスターにクラウド監視を展開する) をご覧ください。 このチュートリアルでは、以前のオペレーティング システムでサポートされているファイル共有監視を使います。
   >

1. **[ファイル共有監視の構成]** で、作成した共有のパスを入力します。 **[次へ]** を選択します。

1. **[確認]** で設定を確認します。 **[次へ]** を選択します。

1. **[完了]** を選択します。

クラスター コア リソースにファイル共有監視が構成されます。

## <a name="enable-availability-groups"></a>可用性グループを有効にする

次に、**AlwaysOn 可用性グループ** 機能を有効にします。 以下の手順は、両方の SQL Server に対して実行してください。

1. **スタート** 画面から **SQL Server 構成マネージャー** を起動します。
2. ブラウザー ツリーで、 **[SQL Server のサービス]** を選択し、 **[SQL Server (MSSQLSERVER)]** サービスを右クリックして、 **[プロパティ]** を選択します。
3. 次に示すように、 **[AlwaysOn 高可用性]** タブを選択し、 **[AlwaysOn 可用性グループを有効にする]** をオンにします。

    ![[AlwaysOn 可用性グループを有効にする]](./media/availability-group-manually-configure-tutorial/54-enableAlwaysOn.png)

4. **[適用]** を選択します。 ポップアップ ダイアログで **[OK]** を選択します。

5. SQL Server サービスを再起動します。

もう一方の SQL Server についても同じ手順を繰り返します。

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an availability group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the availability groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, select **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and select **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and select **Next**.
6. In the **Profile** page, accept the default settings and select **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then select **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>1 番目の SQL Server でデータベースを作成する

1. sysadmin 固定サーバー ロールのメンバーであるドメイン アカウントを使って、1 番目の SQL Server に対する RDP ファイルを起動します。
1. SQL Server Management Studio を開き、1 番目の SQL Server に接続します。
7. **オブジェクト エクスプローラー** で、 **[データベース]** を右クリックし、 **[新しいデータベース]** を選択します。
8. **[データベース名]** に「**MyDB1**」と入力し、 **[OK]** を選択します。

### <a name="create-a-backup-share"></a><a name="backupshare"></a>バックアップ共有を作成する

1. **サーバー マネージャー** の 1 番目の SQL Server で、 **[ツール]** を選択します。 **[コンピューターの管理]** を開きます。

1. **[共有フォルダー]** を選択します。

1. **[共有]** を右クリックし、 **[新しい共有]** を選択します。

   ![[新しい共有] を選択する](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   **共有フォルダーの作成ウィザード** を使って共有を作成します。

1. **[フォルダー パス]** で、 **[参照]** を選択して、データベース バックアップ共有フォルダーのパスを検索するか作成します。 **[次へ]** を選択します。

1. **[名前、説明および設定]** で、共有名とパスを確認します。 **[次へ]** を選択します。

1. **[共有フォルダーのアクセス許可]** で、 **[アクセス許可のカスタマイズ]** を設定します。 **[カスタム]** を選択します。

1. **[アクセス許可のカスタマイズ]** で、 **[追加]** を選択します。

1. SQL Server アカウントと SQL Server エージェント サービス アカウントの両方にフル コントロールがあることを確認します。

   ![SQL Server アカウントと SQL Server エージェント サービス アカウントの両方にフル コントロールがあることを確認します。](./media/availability-group-manually-configure-tutorial/68-backupsharepermission.png)

1. **[OK]** を選択します。

1. **[共有フォルダーのアクセス許可]** で、 **[完了]** を選択します。 **[完了]** をもう一度選択します。  

### <a name="take-a-full-backup-of-the-database"></a>データベースの完全バックアップを作成する

ログ チェーンを初期化するには、新しいデータベースをバックアップする必要があります。 新しいデータベースのバックアップを作成しないと、データベースを可用性グループに含めることはできません。

1. **オブジェクト エクスプローラー** で、データベースを右クリックし、 **[タスク]** をポイントして、 **[バックアップ]** を選択します。

1. **[OK]** を選択して、既定のバックアップ場所に完全バックアップを作成します。

## <a name="create-the-availability-group"></a>可用性グループを作成する

これで、次の手順を使用して可用性グループを構成する準備が整いました。

* 1 番目の SQL Server でデータベースを作成する
* データベースの完全バックアップとトランザクション ログ バックアップの両方を作成します。
* **NORECOVERY** オプションを使って、完全バックアップとログ バックアップを 2 番目の SQL Server に復元する。
* 同期コミット、自動フェールオーバー、読み取り可能なセカンダリのレプリカを含む可用性グループ (**AG1**) を作成します。

### <a name="create-the-availability-group"></a>可用性グループを作成する

1. 1 番目の SQL Server へのリモート デスクトップ セッションを開きます。 SSMS の **オブジェクト エクスプローラー** で、 **[AlwaysOn 高可用性]** を右クリックし、 **[新しい可用性グループ ウィザード]** を選択します。

    ![新しい可用性グループ ウィザードを起動する](./media/availability-group-manually-configure-tutorial/56-newagwiz.png)

2. **[説明]** ページで **[次へ]** を選択します。 **[可用性グループ名の指定]** ページで、 **[可用性グループ名]** に可用性グループの名前を入力します。 たとえば、「**AG1**」のように入力します。 **[次へ]** を選択します。

    ![新しい可用性グループ ウィザード、可用性グループ名の指定](./media/availability-group-manually-configure-tutorial/58-newagname.png)

3. **[データベースの選択]** ページで、対象のデータベースを選択し、 **[次へ]** を選択します。

   >[!NOTE]
   >対象とするプライマリ レプリカで完全バックアップを少なくとも 1 つは作成しているため、このデータベースは可用性グループの前提条件を満たしています。
   >

   ![新しい可用性グループ ウィザード、データベースの選択](./media/availability-group-manually-configure-tutorial/60-newagselectdatabase.png)

4. **[レプリカの指定]** ページで **[レプリカの追加]** を選択します。

   ![新しい可用性グループ ウィザード、レプリカの指定](./media/availability-group-manually-configure-tutorial/62-newagaddreplica.png)

5. **[サーバーに接続]** ダイアログが表示されます。 2 番目のサーバーの名前を **[サーバー名]** に入力します。 **[接続]** を選択します。

   **[レプリカの指定]** ページに戻ると、 **[可用性レプリカ]** の一覧に 2 番目のサーバーが表示されていることがわかります。 次に示すようにレプリカを構成します。

   ![新しい可用性グループ ウィザード、レプリカの指定 (完了)](./media/availability-group-manually-configure-tutorial/64-newagreplica.png)

6. **[エンドポイント]** を選択して、この可用性グループのデータベース ミラーリング エンドポイントを表示します。 [データベース ミラーリング エンドポイントのファイアウォール ルール](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall)を設定するときに使ったものと同じポートを使います。

    ![新しい可用性グループ ウィザード、最初のデータ同期を選択](./media/availability-group-manually-configure-tutorial/66-endpoint.png)

8. **[最初のデータ同期を選択]** ページで、 **[完全]** を選び、共有ネットワークの場所を指定します。 この場所としては、[先に作成したバックアップ共有](#backupshare)を使います。 この例では、 **\\\\<1 番目の SQL Server\>\Backup\\** でした。 **[次へ]** を選択します。

   >[!NOTE]
   >完全同期では、SQL Server の 1 番目のインスタンスにあるデータベースの完全バックアップが作成されて、2 番目のインスタンスに復元されます。 大規模なデータベースの場合、完全同期は長い時間がかかることがあるのでお勧めできません。 手動でデータベースのバックアップを作成し、`NO RECOVERY` で復元することにより、この時間を短縮できます。 可用性グループを構成する前に 2 番目の SQL Server のデータベースが `NO RECOVERY` で既に復元されている場合は、 **[参加のみ]** を選びます。 可用性グループを構成した後でバックアップを作成する場合は、 **[最初のデータ同期をスキップ]** を選びます。
   >

   ![[最初のデータの同期をスキップ] を選択する](./media/availability-group-manually-configure-tutorial/70-datasynchronization.png)

9. **[検証]** ページで **[次へ]** を選択します。 このページは次の図のようになります。

    ![新しい可用性グループ ウィザード、検証](./media/availability-group-manually-configure-tutorial/72-validation.png)

    >[!NOTE]
    >可用性グループ リスナーを構成していないため、リスナー構成に関する警告が表示されます。 Azure Virtual Machines で Azure Load Balancer を作成した後にリスナーを作成するので、この警告は無視してかまいません。

10. **[概要]** ページで、 **[完了]** を選択し、新しい可用性グループが構成されるまで待ちます。 **[進行状況]** ページで **[詳細]** を選択すると、進行状況が詳しく表示されます。 ウィザードが終了した後、**[結果]** ページを調べ、可用性グループが正常に作成されたことを確認します。

     ![新しい可用性グループ ウィザード、結果](./media/availability-group-manually-configure-tutorial/74-results.png)

11. **[閉じる]** を選択してウィザードを終了します。

### <a name="check-the-availability-group"></a>可用性グループを確認する

1. **オブジェクト エクスプローラー** で、 **[AlwaysOn 高可用性]** 、 **[可用性グループ]** の順に展開します。 このコンテナー内に新しい可用性グループが表示されます。 可用性グループを右クリックして **[ダッシュボードの表示]** をクリックします。

   ![可用性グループ ダッシュ ボードを表示する](./media/availability-group-manually-configure-tutorial/76-showdashboard.png)

   **AlwaysOn ダッシュボード** が、次のスクリーンショットのように表示されます。

   ![可用性グループ ダッシュ ボード](./media/availability-group-manually-configure-tutorial/78-agdashboard.png)

   レプリカ、各レプリカのフェールオーバー モード、および同期の状態を確認できます。

2. **フェールオーバー クラスター マネージャー** で、対象のクラスターを選択します。 **[役割]** を選びます。 使った可用性グループの名前は、クラスターでの役割です。 リスナーを構成していないため、その可用性グループにはクライアント接続用の IP アドレスがありません。 Azure Load Balancer を作成した後で、リスナーを構成します。

   ![フェールオーバー クラスター マネージャー内の可用性グループ](./media/availability-group-manually-configure-tutorial/80-clustermanager.png)

   > [!WARNING]
   > フェールオーバー クラスター マネージャーから、可用性グループのフェールオーバーを実行しないでください。 すべてのフェールオーバー操作は、SSMS の **AlwaysOn ダッシュボード** から実行する必要があります。 詳細については、[フェールオーバー クラスター マネージャーと可用性グループの使用に関する制限事項](/sql/database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server)のページを参照してください。
    >

この時点で、SQL Server の 2 つのインスタンス上にレプリカを含む可用性グループができています。 可用性グループは、インスタンス間で移動できます。 リスナーがないため、可用性グループにはまだ接続できません。 Azure Virtual Machines では、リスナーにはロード バランサーが必要です。 次に、Azure でロード バランサーを作成します。

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Azure Load Balancer を作成する

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Azure Virtual Machines では、SQL Server 可用性グループにはロード バランサーが必要です。 ロード バランサーには、可用性グループ リスナーと Windows Server フェールオーバー クラスターの IP アドレスが保持されます。 このセクションでは、Azure Portal でロード バランサーを作成する方法の概要を説明します。

Azure のロード バランサーは、Standard Load Balancer または Basic Load Balancer のいずれかです。 Standard Load Balancer には、Basic Load Balancer よりも多くの機能があります。 可用性グループで、(可用性セットではなく) 可用性ゾーンを使用する場合は、Standard Load Balancer が必要です。 ロード バランサーの SKU の違いについては、「[Load Balancer の SKU の比較](../../../load-balancer/skus.md)」を参照してください。

1. Azure portal で、対象の SQL Server が存在するリソース グループに移動し、 **[+ 追加]** を選択します。
1. 「**ロード バランサー**」を検索します。 Microsoft が公開しているロード バランサーを選びます。

   ![Microsoft が公開しているロード バランサーを選択する](./media/availability-group-manually-configure-tutorial/82-azureloadbalancer.png)

1. **［作成］** を選択します
1. ロード バランサーに関して次のパラメーターを構成します。

   | 設定 | フィールド |
   | --- | --- |
   | **名前** |ロード バランサーのテキスト名を使います (例: **sqlLB**)。 |
   | **Type** |内部 |
   | **Virtual Network** |Azure 仮想ネットワークの名前を使います。 |
   | **サブネット** |仮想マシンが存在するサブネットの名前を使います。  |
   | **IP アドレスの割り当て** |静的 |
   | **IP アドレス (IP address)** |サブネットで利用できるアドレスを使います。 可用性グループ リスナーにはこのアドレスを使用します。 これはクラスター IP アドレスと異なることに注意してください。  |
   | **サブスクリプション** |仮想マシンと同じサブスクリプションを使います。 |
   | **場所** |仮想マシンと同じ場所を使います。 |

   Azure Portal のブレードは次のようになります。

   ![Load Balancer の作成](./media/availability-group-manually-configure-tutorial/84-createloadbalancer.png)

1. **[作成]** を選択して、ロード バランサーを作成します。

ロード バランサーを構成するには、バックエンド プールとプローブを作成し、負荷分散規則を設定する必要があります。 これらは Azure Portal で行います。

### <a name="add-a-backend-pool-for-the-availability-group-listener"></a>可用性グループ リスナーのバックエンド プールを追加する

1. Azure Portal で、可用性グループに移動します。 新しく作成したロード バランサーを表示するため、ビューの更新が必要な場合があります。

   ![リソース グループでのロード バランサーの検索](./media/availability-group-manually-configure-tutorial/86-findloadbalancer.png)

1. ロード バランサーを選択し、 **[バックエンド プール]** を選択し、 **[+ 追加]** を選択します。

1. バックエンド プールの名前を入力します。

1. VM を含む可用性セットにバックエンド プールを関連付けます。

1. **[ターゲット ネットワーク IP 構成]** の下で、 **[仮想マシン]** を確認し、可用性グループ レプリカをホストする仮想マシンを両方とも選択します。 ファイル共有監視サーバーは含めないでください。

   >[!NOTE]
   >仮想マシンを両方とも指定しないと、プライマリ レプリカにしか接続できません。

1. **[OK]** を選択して、バックエンド プールを作成します。

### <a name="set-the-probe"></a>プローブを設定する

1. ロード バランサーを選択し、 **[正常性プローブ]** を選択し、 **[+ Add]** を選択します。

1. リスナーの正常性プローブを次のように設定します。

   | 設定 | 説明 | 例
   | --- | --- |---
   | **名前** | Text | SQLAlwaysOnEndPointProbe |
   | **プロトコル** | TCP を選びます | TCP |
   | **[ポート]** | 未使用の任意のポート | 59999 |
   | **間隔**  | プローブの試行の間隔 (秒単位) |5 |
   | **異常のしきい値** | 仮想マシンが異常と判断されるために必要な、連続したプローブ失敗の数  | 2 |

1. **[OK]** を選択して、正常性プローブを設定します。

### <a name="set-the-load-balancing-rules"></a>負荷分散規則を設定する

1. ロード バランサーを選択し、 **[負荷分散規則]** を選択し、 **[+ 追加]** を選択します。

1. 次のようにリスナーの負荷分散規則を設定します。

   | 設定 | 説明 | 例
   | --- | --- |---
   | **名前** | Text | SQLAlwaysOnEndPointListener |
   | **フロントエンド IP アドレス** | アドレスを選びます |ロード バランサーの作成時に作成したアドレスを使います。 |
   | **プロトコル** | TCP を選びます |TCP |
   | **[ポート]** | 可用性グループ リスナーのポートを使用する | 1433 |
   | **バックエンド ポート** | Direct Server Return に Floating IP を設定するときは、このフィールドは使われません | 1433 |
   | **プローブ** |プローブに指定した名前 | SQLAlwaysOnEndPointProbe |
   | **セッション永続化** | ドロップダウン リスト | **なし** |
   | **アイドル タイムアウト** | TCP 接続を開いたままにしておく時間 (分) | 4 |
   | **フローティング IP (ダイレクト サーバー リターン)** | |Enabled |

   > [!WARNING]
   > Direct Server Return は作成の間に設定されます。 この値は変更しないでください。
   >

1. **[OK]** を選択して、リスナーの負荷分散規則を設定します。

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Windows Server フェールオーバー クラスター (WSFC) のクラスターコア IP アドレスを追加する

WSFC の IP アドレスもロード バランサー上に存在する必要があります。

1. Azure portal で、同じ Azure ロード バランサーに移動します。 **[フロントエンド IP 構成]** を選択し、 **[+ 追加]** を選択します。 クラスター コア リソース内の WSFC 用に構成した IP アドレスを使用します。 この IP アドレスを静的として設定します。

1. ロード バランサーで、 **[正常性プローブ]** を選択し、 **[+ 追加]** を選択します。

1. WSFC クラスターのコア IP アドレスの正常性プローブを次のように設定します。

   | 設定 | 説明 | 例
   | --- | --- |---
   | **名前** | Text | WSFCEndPointProbe |
   | **プロトコル** | TCP を選びます | TCP |
   | **[ポート]** | 未使用の任意のポート | 58888 |
   | **間隔**  | プローブの試行の間隔 (秒単位) |5 |
   | **異常のしきい値** | 仮想マシンが異常と判断されるために必要な、連続したプローブ失敗の数  | 2 |

1. **[OK]** を選択して、正常性プローブを設定します。

1. 負荷分散規則を設定します。 **[負荷分散規則]** を選択し、 **[+ 追加]** を選択します。

1. クラスターのコア IP アドレスの負荷分散規則を次のように設定します。

   | 設定 | 説明 | 例
   | --- | --- |---
   | **名前** | Text | WSFCEndPoint |
   | **フロントエンド IP アドレス** | アドレスを選びます |WSFC の IP アドレスの構成時に作成したアドレスを使用します。 これはリスナーの IP アドレスとは異なります |
   | **プロトコル** | TCP を選びます |TCP |
   | **[ポート]** | クラスター IP アドレスのポートを使用します。 これは、リスナー プローブ ポートには使用されない使用可能なポートです。 | 58888 |
   | **バックエンド ポート** | Direct Server Return に Floating IP を設定するときは、このフィールドは使われません | 58888 |
   | **プローブ** |プローブに指定した名前 | WSFCEndPointProbe |
   | **セッション永続化** | ドロップダウン リスト | **なし** |
   | **アイドル タイムアウト** | TCP 接続を開いたままにしておく時間 (分) | 4 |
   | **フローティング IP (ダイレクト サーバー リターン)** | |Enabled |

   > [!WARNING]
   > Direct Server Return は作成の間に設定されます。 この値は変更しないでください。
   >

1. **[OK]** を選択して、負荷分散規則を設定します。

## <a name="configure-the-listener"></a><a name="configure-listener"></a> リスナーを構成する

次の手順は、フェールオーバー クラスター上の可用性グループ リスナーの構成です。

> [!NOTE]
> このチュートリアルでは、1 つの ILB IP アドレスを持つ 1 つのリスナーを作成する方法を示します。 1 つまたは複数の IP アドレスを使用して 1 つまたは複数のリスナーを作成する方法については、[Azure での可用性グループ リスナーとロード バランサーの作成](availability-group-listener-powershell-configure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>リスナーのポートを設定する

SQL Server Management Studio で、リスナー ポートを設定します。

1. SQL Server Management Studio を起動し、プライマリ レプリカに接続します。

1. **[AlwaysOn 高可用性]**  >  **[可用性グループ]**  > z **[可用性グループ リスナー]** の順に移動します。

1. フェールオーバー クラスター マネージャーで作成したリスナー名が表示されます。 リスナー名を右クリックし、 **[プロパティ]** を選択します。

1. **[ポート]** ボックスで、可用性グループ リスナーのポート番号を指定します。 既定値は 1433 です。 **[OK]** を選択します。

これで、SQL Server 可用性グループが Resource Manager モードの Azure 仮想マシンにデプロイされました。

## <a name="test-connection-to-listener"></a>リスナーへの接続をテストする

接続をテストするには、次の手順に従います。

1. RDP を使用して、同じ仮想ネットワークに存在する、レプリカを所有していない SQL Server に接続します。 クラスター内の他の SQL Server を使うことができます。

1. **sqlcmd** ユーティリティを使用して接続をテストします。 たとえば次のスクリプトは、Windows 認証を使用し、リスナー経由でプライマリ レプリカとの **sqlcmd** 接続を確立しています。

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   リスナーが既定のポート (1433) 以外のポートを使用している場合は、そのポートを接続文字列で指定します。 たとえば、次の `sqlcmd` コマンドでは、ポート 1435 でリスナーに接続します。

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

SQLCMD 接続では、プライマリ レプリカをホストしている SQL Server インスタンスに対して自動的に接続されます。

> [!TIP]
> 指定したポートは、両方の SQL Server のファイアウォールで必ず開放してください。 使用する TCP ポートに対する入力方向の規則が両方のサーバーに必要となります。 詳しくは、「[ファイアウォール規則を追加または編集する](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11))」をご覧ください。
>

## <a name="next-steps"></a>次のステップ

- [2 番目の可用性グループのロード バランサーに IP アドレスを追加](availability-group-listener-powershell-configure.md#Add-IP)します。
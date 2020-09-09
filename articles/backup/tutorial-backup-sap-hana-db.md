---
title: チュートリアル - Azure VM での SAP HANA データベースのバックアップ
description: このチュートリアルでは、Azure VM 上で稼働している SAP HANA データベースを Azure Backup Recovery Services コンテナーにバックアップする方法について学習します。
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: b43fd5c432b06902de0a898fc4bb0f114143b3ba
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89375280"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>チュートリアル:Azure VM での SAP HANA データベースのバックアップ

このチュートリアルでは、Azure VM 上で稼働している SAP HANA データベースを Azure Backup Recovery Services コンテナーにバックアップする方法について説明します。 この記事では、以下を行う方法について説明します。

> [!div class="checklist"]
>
> * コンテナーを作成して構成する
> * データベースを検出する
> * バックアップを構成する

現在サポートされているすべてのシナリオについては、[こちら](sap-hana-backup-support-matrix.md#scenario-support)を参照してください。

>[!NOTE]
>2020 年 8 月 1 日の時点で、RHEL (7.4、7.6、7.7、8.1) 用の SAP HANA バックアップは一般提供されています。

## <a name="prerequisites"></a>前提条件

バックアップを構成する前に、必ず、次の操作を行ってください。

* SAP HANA を実行する VM として、同じリージョンおよびサブスクリプションの [Recovery Services コンテナー](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault)を特定または作成します。
* VM からインターネットへの接続を許可して、Azure に到達できるようにします。以下の「[ネットワーク接続を設定する](#set-up-network-connectivity)」の手順を参照してください。
* SAP HANA Server VM 名とリソース グループ名とを組み合わせた長さが、Azure Resource Manager (ARM_ VM) の場合は 84 文字、クラシック VM の場合は 77 文字を超えないようにしてください。 この制限は、一部の文字がサービスによって予約されていることに起因します。
* 次の条件を満たすキーが **hdbuserstore** に存在する必要があります。
  * 既定の **hdbuserstore** に存在する。 既定値は、SAP HANA がインストールされている `<sid>adm` アカウントです。
  * MDC の場合、キーが **NAMESERVER** の SQL ポートを指すこと。 SDC の場合は、**INDEXSERVER** の SQL ポートを指す必要があります
  * ユーザーを追加したり削除したりするための資格情報があること。
  * このキーは、事前登録スクリプトを正常に実行した後に削除できることに注意してください
* HANA がインストールされている仮想マシンで、SAP HANA バックアップ構成スクリプト (事前登録スクリプト) をルート ユーザーとして実行します。 [このスクリプト](https://aka.ms/scriptforpermsonhana)で、バックアップに備えた HANA システムの準備を行うことができます。 事前登録スクリプトの詳細については、「[事前登録スクリプトで実行される処理](#what-the-pre-registration-script-does)」セクションを参照してください。
* HANA セットアップでプライベート エンドポイントが使用されている場合は、 [事前登録スクリプト](https://aka.ms/scriptforpermsonhana)を *-sn* または *--skip-network-checks* パラメーターを指定して実行します。

>[!NOTE]
>事前登録スクリプトは、RHEL (7.4、7.6、および 7.7) 上で実行されている SAP HANA ワークロードには **compat-unixODBC234** をインストールし、RHEL 8.1 には **unixODBC** をインストールします。 [このパッケージは、RHEL for SAP HANA (RHEL 7 Server の場合) の SAP ソリューション用更新サービス (RPM) リポジトリ](https://access.redhat.com/solutions/5094721)にあります。  Azure Marketplace RHEL イメージの場合、リポジトリは **rhui-rhel-sap-hana-for-rhel-7-server-rhui-e4s-rpms** になります。

## <a name="set-up-network-connectivity"></a>ネットワーク接続を設定する

すべての操作において、Azure VM で実行されている SAP HANA データベースには Azure Backup サービス、Azure Storage、Azure Active Directory への接続が必要です。 これは、プライベート エンドポイントを使用するか、必要なパブリック IP アドレスまたは FQDN へのアクセスを許可することによって実現できます。 必要な Azure サービスへの適切な接続を許可しないと、データベースの検出、バックアップの構成、バックアップの実行、データの復元などの操作の失敗につながる可能性があります。

次の表に、接続の確立に使用できるさまざまな選択肢を示します。

| **オプション**                        | **長所**                                               | **短所**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| プライベート エンドポイント                 | 仮想ネットワーク内のプライベート IP 経由でのバックアップを可能にする  <br><br>   ネットワークとコンテナーの側で詳細な制御を提供する | 標準のプライベート エンドポイント [コスト](https://azure.microsoft.com/pricing/details/private-link/)が発生する |
| NSG サービス タグ                  | 範囲の変更が自動的にマージされるため管理しやすい   <br><br>   追加のコストが発生しない | NSG でのみ使用可能  <br><br>    サービス全体へのアクセスを提供する |
| Azure Firewall の FQDN タグ          | 必要な FQDN が自動的に管理されるため管理しやすい | Azure Firewall でのみ使用可能                         |
| サービスの FQDN/IP へのアクセスを許可する | 追加のコストが発生しない   <br><br>  すべてのネットワーク セキュリティ アプライアンスとファイアウォールで動作する | 広範な IP または FQDN のセットへのアクセスが必要になる場合がある   |
| HTTP プロキシを使用する                 | VM に対するインターネット アクセスを単一の場所で実現                       | プロキシ ソフトウェアで VM を実行するための追加のコストが発生する         |

これらのオプションを使用する方法の詳細については、以下を参照してください。

### <a name="private-endpoints"></a>プライベート エンドポイント

プライベート エンドポイントを使用すると、仮想ネットワーク内のサーバーから Recovery Services コンテナーに安全に接続できます。 プライベート エンドポイントでは、お使いのコンテナーの VNET アドレス空間からの IP アドレスが使用されます。 仮想ネットワーク内のリソースとコンテナー間のネットワーク トラフィックは、仮想ネットワークと Microsoft のバックボーン ネットワーク上のプライベート リンクを経由します。 これにより、パブリック インターネットへの露出が排除されます。 [こちら](./private-endpoints.md)から、Azure Backup のプライベート エンドポイントの詳細を参照してください。

### <a name="nsg-tags"></a>NSG タグ

ネットワーク セキュリティ グループ (NSG) を使用する場合は、*AzureBackup* サービス タグを使用して、Azure Backup への発信アクセスを許可します。 Azure Backup タグに加えて、*Azure AD* および *Azure Storage* に対して同様の [NSG 規則](../virtual-network/security-overview.md#service-tags)を作成することによって、認証とデータ転送のための接続を許可する必要もあります。  次の手順では、Azure Backup タグの規則を作成するプロセスについて説明します。

1. **[すべてのサービス]** で、 **[ネットワーク セキュリティ グループ]** に移動して、ネットワーク セキュリティ グループを選択します。

1. **[設定]** で **[送信セキュリティ規則]** を選択します。

1. **[追加]** を選択します。 [セキュリティ規則の設定](../virtual-network/manage-network-security-group.md#security-rule-settings)の説明に従って、新しい規則を作成するために必要なすべての詳細を入力します。 オプション **[宛先]** が *[サービス タグ]* に、 **[宛先サービス タグ]** が *[AzureBackup]* に設定されていることを確認します。

1. **[追加]** を選択して、新しく作成した送信セキュリティ規則を保存します。

Azure Storage と Azure AD に対する NSG 送信セキュリティ規則も、同様に作成できます。 サービス タグの詳細については、[こちらの記事](../virtual-network/service-tags-overview.md)を参照してください。

### <a name="azure-firewall-tags"></a>Azure Firewall タグ

Azure Firewall を使用している場合は、*AzureBackup* [Azure Firewall FQDN タグ](../firewall/fqdn-tags.md)を使用してアプリケーション規則を作成します。 これにより、Azure Backup へのすべての発信アクセスが許可されます。

### <a name="allow-access-to-service-ip-ranges"></a>サービスの IP 範囲へのアクセスを許可する

サービスの IP へのアクセスを許可することを選択した場合は、[こちら](https://www.microsoft.com/download/confirmation.aspx?id=56519)から利用可能な IP 範囲の JSON ファイルを参照してください。 Azure Backup、Azure Storage、および Azure Active Directory に対応する IP へのアクセスを許可する必要があります。

### <a name="allow-access-to-service-fqdns"></a>サービスの FQDN へのアクセスを許可する

次の FQDN を使用することで、サーバーから必要なサービスへのアクセスを許可することもできます。

| サービス    | アクセスするドメイン名                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | [この記事](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)に従って、セクション 56 および 59 の FQDN へのアクセスを許可します |

### <a name="use-an-http-proxy-server-to-route-traffic"></a>トラフィックをルーティングするために HTTP プロキシ サーバーを使用する

Azure VM で実行されている SAP HANA データベースをバックアップする場合、VM 上のバックアップ拡張機能によって HTTPS API が使用され、管理コマンドが Azure Backup に送信されてデータが Azure Storage に送信されます。 また、バックアップ拡張機能では、認証に Azure AD を使用します。 HTTP プロキシ経由でこれらの 3 つのサービスのバックアップ拡張機能のトラフィックをルーティングします。 必要なサービスへのアクセスを許可するには、上記で説明した IP と FQDN の一覧を使用します。 認証済みプロキシ サーバーはサポートされません。

## <a name="what-the-pre-registration-script-does"></a>事前登録スクリプトで実行される処理

事前登録スクリプトでは、次の機能が実行されます。

* ご利用の Linux ディストリビューションに基づいて、スクリプトにより、Azure Backup エージェントに必要なすべてのパッケージがインストールまたは更新されます。
* Azure Backup サーバーと依存サービス (Azure Active Directory、Azure Storage など) に対するアウトバウンド ネットワーク接続チェックを実行します。
* [前提条件](#prerequisites)の 1 つでもあるユーザー キーを使用して HANA システムにログインします。 このユーザー キーは、バックアップ ユーザー (AZUREWLBACKUPHANAUSER) を HANA システムに作成するために使用されます。また、**このユーザー キーは、事前登録スクリプトが正常に実行された後に削除できます**。
* AZUREWLBACKUPHANAUSER には、次の必要なロールとアクセス許可が割り当てられます。
  * DATABASE ADMIN (MDC の場合) および BACKUP ADMIN (SDC の場合): 復元中に新しいデータベースを作成します。
  * CATALOG READ: バックアップ カタログを読み取ります。
  * SAP_INTERNAL_HANA_SUPPORT いくつかのプライベート テーブルにアクセスします。
* このスクリプトにより、HANA バックアップ プラグインですべての操作 (データベース クエリ、復元操作、バックアップの構成と実行) を処理するために AZUREWLBACKUPHANAUSER の **hdbuserstore** にキーが追加されます。

>[!NOTE]
> [前提条件](#prerequisites)の一環として記載されているユーザー キーは、事前登録スクリプトのパラメーターとして明示的に渡すことができます: `-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>このスクリプトで受け取るその他のパラメーターを確認するには、`bash msawb-plugin-config-com-sap-hana.sh --help` コマンドを使用してください。

キーの作成を確認するには、SIDADM 資格情報を使用して HANA コンピューター上で HDBSQL コマンドを実行します。

```hdbsql
hdbuserstore list
```

このコマンド出力には、AZUREWLBACKUPHANAUSER として示されるユーザーと共に {SID}{DBNAME} キーが表示されます。

>[!NOTE]
> `/usr/sap/{SID}/home/.hdb/` の下に固有の一連の SSFS ファイルがあることを確認してください。 このパスにはフォルダーが 1 つしか存在しません。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーは、経時的に作成されたバックアップと復旧ポイントを格納するエンティティです。 Recovery Services コンテナーには、保護される仮想マシンに関連付けられたバックアップ ポリシーも含まれます。

Recovery Services コンテナーを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com/) でサブスクリプションにサインインします。

2. 左側のメニューから、 **[すべてのサービス]** を選択します。

   ![[すべてのサービス] を選択する](./media/tutorial-backup-sap-hana-db/all-services.png)

3. **[すべてのサービス]** ダイアログ ボックスに、「**Recovery Services**」と入力します。 入力に従って、リソースの一覧がフィルター処理されます。 リソースの一覧から **[Recovery Services コンテナー]** を選択します。

   ![Recovery Services コンテナーを選択する](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. **[Recovery Services コンテナー]** ダッシュボードで **[追加]** を選択します。

   ![Recovery Services コンテナーを追加する](./media/tutorial-backup-sap-hana-db/add-vault.png)

   **[Recovery Services コンテナー]** ダイアログ ボックスが開きます。 **[名前]、[サブスクリプション]、[リソース グループ]** 、および **[場所]** に値を入力します。

   ![Recovery Services コンテナーの作成](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Name**:この名前は、Recovery Services コンテナーを識別するために使用されるため、Azure サブスクリプションに対して一意である必要があります。 2 文字以上で、50 文字以下の名前を指定します。 名前の先頭にはアルファベットを使用する必要があります。また、名前に使用できるのはアルファベット、数字、ハイフンのみです。 このチュートリアルでは、**SAPHanaVault** という名前を使用しています。
   * **サブスクリプション**:使用するサブスクリプションを選択します。 1 つのサブスクリプションのみのメンバーの場合は、その名前が表示されます。 どのサブスクリプションを使用すればよいかがわからない場合は、既定 (推奨) のサブスクリプションを使用してください。 職場または学校アカウントが複数の Azure サブスクリプションに関連付けられている場合に限り、複数の選択肢が存在します。 ここでは、**SAP HANA solution lab subscription** サブスクリプションを使用しています。
   * **[リソース グループ]** :既存のリソース グループを使用するか、新しいリソース グループを作成します。 ここでは、**SAPHANADemo** を使用しています。<br>
   サブスクリプションの使用可能なリソース グループの一覧を表示するには、 **[既存のものを使用]** を選択し、ドロップダウン リスト ボックスからリソースを選択します。 新しいリソース グループを作成するには、 **[新規作成]** を選択し、名前を入力します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)」を参照してください。
   * **[場所]** :コンテナーの地理的リージョンを選択します。 このコンテナーは、SAP HANA を実行している仮想マシンと同じリージョンにある必要があります。 ここでは、 **[米国東部 2]** を使用しています。

5. **[確認および作成]** を選択します。

   ![[確認および作成] を選択する](./media/tutorial-backup-sap-hana-db/review-create.png)

これで、Recovery Services コンテナーが作成されました。

## <a name="discover-the-databases"></a>データベースを検出する

1. コンテナーの **[作業の開始]** で、 **[バックアップ]** を選択します。 **[ワークロードはどこで実行されていますか?]** で、 **[Azure VM の SAP HANA]** を選択します。
2. **[検出の開始]** を選択します。 これで、コンテナー リージョン内の保護されていない Linux VM の検出が開始されます。 保護する Azure VM が表示されます。
3. **[仮想マシンの選択]** で、リンクを選択して、データベースの検出のために SAP HANA VM へのアクセス許可を Azure Backup サービスに与えるスクリプトをダウンロードします。
4. バックアップする SAP HANA データベースをホストしている VM 上でスクリプトを実行します。
5. VM 上でスクリプトを実行した後、 **[仮想マシンの選択]** で VM を選択します。 次に、 **[Discover DBs]\(データベースを検出\)** を選択します。
6. Azure Backup によって、VM 上のすべての SAP HANA データベースが検出されます。 検出中に、Azure Backup によって VM がコンテナーに登録され、VM に拡張機能がインストールされます。 エージェントはデータベースにインストールされません。

   ![データベースを検出する](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>バックアップの構成

バックアップするデータベースが検出されたら、バックアップを有効にしましょう。

1. **[バックアップの構成]** を選択します。

   ![バックアップの構成](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. **[Select items to back up]\(バックアップする項目の選択\)** で、保護するデータベースを 1 つ以上選択し、 **[OK]** を選択します。

   ![バックアップする項目を選択する](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. **[バックアップ ポリシー] > [バックアップ ポリシーの選択]** で、次のセクションの手順に従って、データベースの新しいバックアップ ポリシーを作成します。

   ![バックアップ ポリシーを選択する](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. ポリシーを作成した後、 **[バックアップ] メニュー**の **[バックアップの有効化]** を選択します。

   ![[バックアップの有効化] の選択](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. ポータルの **[通知]** 領域で、バックアップ構成の進行状況を追跡します。

## <a name="creating-a-backup-policy"></a>バックアップ ポリシーの作成

バックアップ ポリシーでは、バックアップが取得されるタイミングと、それらが保持される期間を定義します。

* ポリシーはコンテナー レベルで作成されます。
* 複数のコンテナーでは同じバックアップ ポリシーを使用できますが、各コンテナーにバックアップ ポリシーを適用する必要があります。

次のように、ポリシー設定を指定します。

1. **[ポリシー名]** に新しいポリシーの名前を入力します。 この場合は、「**SAPHANA**」と入力します。

   ![新しいポリシーの名前を入力する](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. **[Full Backup policy]\(完全バックアップのポリシー\)** で、 **[バックアップ頻度]** を選択します。 **[Daily]\(毎日\)** または **[毎週]** を選択できます。 このチュートリアルでは、 **[Daily]\(毎日\)** バックアップを選択しています。

   ![バックアップ頻度を選択する](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. **[リテンション期間]** で、完全バックアップのリテンション期間の設定を構成します。
   * 既定では、すべてのオプションが選択されています。 使用しない保持期間の制限をすべてクリアして、使用するものを設定します。
   * あらゆる種類のバックアップ (完全、差分、ログ) の最小保持期間は 7 日間です。
   * 復旧ポイントは、そのリテンション期間の範囲に基づいて、リテンション期間に対してタグ付けされます。 たとえば、日次での完全バックアップを選択した場合、日ごとにトリガーされる完全バックアップは 1 回だけです。
   * 特定の曜日のバックアップがタグ付けされ、週次でのリテンション期間と設定に基づいて保持されます。
   * 月次および年次のリテンション期間の範囲でも、同様の動作になります。
4. **完全バックアップのポリシー** メニューで、 **[OK]** を選択して設定を確定します。
5. 次に、 **[差分バックアップ]** を選択して、差分ポリシーを追加します。
6. **差分バックアップのポリシー**で、 **[有効]** を選択して頻度とリテンション期間の制御を開きます。 ここでは、毎週**日曜日**の**午前 2:00** に実行され、**30 日間**保持される差分バックアップを有効にしました。

   ![差分バックアップ ポリシー](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >増分バックアップは現在、サポートされていません。
   >

7. **[OK]** を選択してポリシーを保存し、 **[バックアップ ポリシー]** のメイン メニューに戻ります。
8. **[ログ バックアップ]** を選択し、トランザクション ログ バックアップ ポリシーを追加します。
   * **[ログ バックアップ]** は既定で **[有効]** に設定されています。 SAP HANA ではすべてのログ バックアップが管理されるため、これを無効にすることはできません。
   * ここでは、バックアップのスケジュールとして **2 時間**を設定し、保持期間として **15 日間**を設定しています。

    ![ログ バックアップのポリシー](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > ログ バックアップでは、1 回の完全バックアップが正常に完了した後にのみ、フローが開始されます。
   >

9. **[OK]** を選択してポリシーを保存し、 **[バックアップ ポリシー]** のメイン メニューに戻ります。
10. バックアップ ポリシーの定義が完了した後、 **[OK]** を選択します。

これで、SAP HANA データベースのバックアップが正常に構成されました。

## <a name="next-steps"></a>次の手順

* [Azure VM で稼働している SAP HANA データベースでオンデマンド バックアップを実行する](backup-azure-sap-hana-database.md#run-an-on-demand-backup)方法を学習する
* [Azure VM で稼働している SAP HANA データベースを復元する](sap-hana-db-restore.md)方法を学習する
* [Azure Backup を使用してバックアップされた SAP HANA データベースを管理する](sap-hana-db-manage.md)方法を学習する
* [SAP HANA データベースのバックアップ時に発生する一般的な問題をトラブルシューティングする](backup-azure-sap-hana-database-troubleshoot.md)方法を学習する

---
title: Azure Migrate に関する問題のトラブルシューティング | Microsoft Docs
description: Azure Migrate サービスの既知の問題についての概要を説明し、一般的なエラーのトラブルシューティングのヒントを提供します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: raynew
ms.openlocfilehash: 49c43d393ef0722424088e0073942b56787f1bc7
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067806"
---
# <a name="troubleshoot-azure-migrate"></a>Azure Migrate のトラブルシューティング

[Azure Migrate](migrate-services-overview.md) には、評価と移行のためのツールのほか、サードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが集約されています。 この記事は、Azure Migrate、Azure Migrate Server Assessment、および Azure Migrate Server Migration に関する問題のトラブルシューティングに役立ちます。


## <a name="find-a-project"></a>プロジェクトを探す

Azure Migrate には [2 つのバージョン](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)があります。


現在のバージョンの Azure Migrate で Azure Migrate プロジェクトを作成した場合は、次の手順のようにします。

1. [Azure portal](https://portal.azure.com) で **Azure Migrate** を検索します。
2. Azure Migrate ダッシュボードの **[サーバー]** で、右上隅の **[変更]** を選択します。

    ![既存の Azure Migrate プロジェクトに切り替える](./media/troubleshooting-general/switch-project.png)

3. 適切なサブスクリプションと Azure Migrate プロジェクトを選択します。


以前のバージョンの Azure Migrate でプロジェクトを作成した場合は、次の手順のようにします。

1. [Azure portal](https://portal.azure.com) で **Azure Migrate** を検索します。
2. 以前のバージョンでプロジェクトを作成した場合は、Azure Migrate ダッシュボードに古いプロジェクトを参照するバナーが表示されます。 そのバナーを選択します。

    ![既存のプロジェクトにアクセスする](./media/troubleshooting-general/access-existing-projects.png)

3. 古いプロジェクトの一覧を確認します。


## <a name="create-additional-projects"></a>追加のプロジェクトの作成

新しい Azure Migrate プロジェクトを作成するには、次のようにします。

1. [Azure portal](https://portal.azure.com) で **Azure Migrate** を検索します。
2. Azure Migrate ダッシュボードの **[サーバー]** で、右上隅の **[変更]** を選択します。

   ![Azure Migrate プロジェクトを変更する](./media/troubleshooting-general/switch-project.png)

3. 新しいプロジェクトを作成するには、 **[click here]\(ここをクリック\)** を選択します。

   ![2 つ目の Azure Migrate プロジェクトを作成する](./media/troubleshooting-general/create-new-project.png)

## <a name="review-supported-geographies"></a>サポートされている地域を確認する

サポートされている地域については、[VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) および [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects) を確認してください。

## <a name="delete-projectsworkspaces"></a>プロジェクト/ワークスペースを削除する

Azure Migrate プロジェクトと Log Analytics ワークスペースを削除するときは、次の点に注意してください。

- Azure Migrate プロジェクトを削除すると、プロジェクトと "*共に*" 検出されたコンピューターに関するメタデータも削除されます。
- Log Analytics ワークスペースを Server Assessment ツールに関連付けている場合、ワークスペースは自動的には削除されません。
- 同じ Log Analytics ワークスペースが、複数のシナリオで使用されている可能性があります。
- Log Analytics ワークスペースを削除する場合は、手動で行う必要があります。


### <a name="delete-a-project"></a>プロジェクトを削除する

現在のバージョンの Azure Migrate でプロジェクトを削除するには:

1. プロジェクトが作成された Azure リソース グループを開きます。
2. リソース グループ ページで **[非表示の型の表示]** を選択します。
3. 削除する移行プロジェクトを選択します。 リソースの種類は Microsoft.Migrate/migrateprojects であり、それを削除します。

古いバージョンの Azure Migrate でプロジェクトを削除するには:

1. プロジェクトが作成された Azure リソース グループを開きます。
2. 削除する移行プロジェクトを選択します。 リソースの種類は移行プロジェクトであり、それを削除します。


### <a name="delete-a-workspace"></a>ワークスペースを削除する

プロジェクトに関連付けられている Log Analytics ワークスペースを参照します。
* Azure Migrate プロジェクトを削除していない場合は、 **[Essentials]\(基礎\)**  >  **[Server Assessment]\(Server Assessment\)** でワークスペースへのリンクを見つけることができます。
       ![LA ワークスペース](./media/troubleshooting-general/loganalytics-workspace.png)

     * If you've already deleted the Azure Migrate project, select **Resource Groups** in the left pane of the Azure portal. Locate the workspace in the relevant resources group, and [follow the instructions](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) to delete it.


## <a name="error-requests-must-contain-user-identity-headers"></a>エラー "要求にはユーザー ID ヘッダーが含まれていなければなりません"

プロジェクトを作成するときに、このエラーで、組織の Azure Active Directory (Azure AD) テナントへのアクセス権がないことが示される可能性があります。

- ユーザーは、Azure AD テナントに初めて追加されるときに、テナントへの参加を求める招待メールを受け取ります。
- テナントに正しく追加されるためには、招待を承諾する必要があります。
    - メールが表示されない場合は、テナントへのアクセス権を持つユーザーに連絡し、[招待を再送信する](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)ように依頼してください。
    - 招待メールを受け取った後、メールを開き、リンクを選択して招待を承諾します。 その後、Azure portal からサインアウトし、改めてサインインします。 (ブラウザーの更新は機能しません。)これで、移行プロジェクトの作成を始めることができます。


## <a name="error-invalid-ovf-manifest-entry"></a>エラー "Invalid OVF manifest entry" (無効な OVF マニフェスト エントリ)

"The provided manifest file is invalid: Invalid OVF manifest entry" (指定されたマニフェスト ファイルが無効です: 無効な OVF マニフェスト エントリ) というエラーを受け取る場合は、次のようにします。

1. Azure Migrate アプライアンスの OVA ファイルが正常にダウンロードされていることを、そのハッシュ値をチェックして確認します。 [詳細情報](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware)。 ハッシュ値が一致しない場合は、OVA ファイルをもう一度ダウンロードしてデプロイを再試行してください。
2. それでもデプロイが失敗する場合で、VMware vSphere クライアントを使って OVF ファイルをデプロイしている場合は、vSphere Web クライアントでデプロイしてみてください。 展開がそれでも失敗する場合は、別の Web ブラウザーをお試しください。
3. vSphere Web クライアントを使用しており、vCenter Server 6.5 または 6.7 にそれをデプロイしようとしている場合は、ESXi ホストに直接 OVA をデプロイしてみてください。
   - Web クライアント (https://<*ホスト IP アドレス*>/ui) を使用して (vCenter Server ではなく) ESXi ホストに直接接続します。
   - **[ホーム]**  >  **[インベントリ]** で、 **[ファイル]**  >  **[Deploy OVF template]\(OVF テンプレートのデプロイ\)** を選択します。 OVA を参照して、デプロイを完了する。
4. それでもデプロイが失敗する場合は、Azure Migrate のサポートにお問い合わせください。

## <a name="appliance-cant-connect-to-the-internet"></a>アプライアンスがインターネットに接続できない

これは、アプライアンス マシンがプロキシの内側にある場合に発生する可能性があります。

- プロキシに承認資格情報が必要な場合は、それを提供します。
- URL ベースのファイアウォール プロキシを使用して送信接続を制御している場合は、以下の URL を許可リストに追加します。

    - [VMware 評価のための URL](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
    - [Hyper-V 評価のための URL](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
    - [VMware のエージェントレスの移行のための URL](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
    - [VMware のエージェント ベースの移行のための URL](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
    - [Hyper-V の移行のための URL](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

- インターネットへの接続にインターセプト プロキシを使用している場合は、[こちらの手順](https://docs.microsoft.com/azure/migrate/concepts-collector)を使用して、プロキシの証明書をアプライアンス VM にインポートします。

## <a name="errordatetime-synchronization"></a>エラー: 日付/時刻の同期

日付と時刻の同期に関するエラー (802) は、サーバー クロックが現在の時刻と同期しておらず、5 分以上ずれている可能性があることを示します。 コレクター VM のクロックの時刻を変更して、現在の時刻と一致させます。

1. VM で管理者用のコマンド プロンプトを開きます。
2. タイム ゾーンを確認するには、**w32tm /tz** を実行します。
3. 時刻を同期するには、**w32tm /resync** を実行します。


## <a name="error-unabletoconnecttoserver"></a>エラー:UnableToConnectToServer

この接続エラーを受け取る場合、vCenter Server <*サーバー名*>.com:9443 に接続できない可能性があります。 エラーの詳細では、 https://<*サーバー名*>.com:9443/sdk でリッスンしているエンドポイントで、メッセージを受け入れることができるものがないことが示されています。

- 最新バージョンのアプライアンスを実行しているかどうかを確認します。 そうでない場合は、アプライアンスを[最新バージョン](https://docs.microsoft.com/azure/migrate/concepts-collector)にアップグレードします。
- 最新バージョンでも引き続き問題が発生する場合は、指定された vCenter Server 名をアプライアンスで解決できないか、または指定されたポートが間違っている可能性があります。 ポートが指定されていない場合、コレクターでは既定でポート番号 443 への接続が試みられます。

    1. アプライアンスから <*サーバー名*>.com に対して ping を実行します。
    2. ステップ 1 が失敗する場合は、IP アドレスを使用して vCenter サーバーへの接続を試みます。
    3. vCenter Server に接続するための正しいポート番号を確認します。
    4. vCenter Server が起動されて実行中であることを確認します。


## <a name="error-appliance-might-not-be-registered"></a>エラー:アプライアンスが登録されていない可能性がある

- エラー 60052 "The appliance might not be registered successfully to the Azure Migrate project" (アプライアンスが Azure Migrate プロジェクトに正常に登録されていない可能性があります) は、アプライアンスの登録に使用された Azure アカウントに十分な権限がない場合に発生します。
    - アプライアンスの登録に使用する Azure ユーザー アカウントに、少なくともサブスクリプションの共同作成者アクセス許可があることを確認します。
    - 必要な Azure ロールとアクセス許可の[詳細を参照](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)してください。
- エラー 60039 "The appliance might not be registered successfully to the Azure Migrate project" (アプライアンスが Azure Migrate プロジェクトに正常に登録されていない可能性があります) は、アプライアンスの登録に使用された Azure Migrate プロジェクトが見つからない場合に発生する可能性があります。
    - Azure portal で、リソース グループにプロジェクトが存在するかどうかを確認してください。
    - プロジェクトが存在しない場合は、ご利用のリソース グループに新しい Azure Migrate プロジェクトを作成して、アプライアンスをもう一度登録してください。 新しいプロジェクトを作成する[方法を参照](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool)してください。

## <a name="error-key-vault-management-operation-failed"></a>エラー:Key Vault の管理操作が失敗した

"Azure Key Vault 管理操作に失敗しました" というエラー 60030 または 60031 を受け取る場合は、次のようにします。
- アプライアンスの登録に使用する Azure ユーザー アカウントに、少なくともサブスクリプションの共同作成者アクセス許可があることを確認します。
- エラー メッセージで指定されているキー コンテナーへのアクセス権がアカウントにあることを確認した後、操作を再試行します。
- 引き続き問題が発生する場合は、Microsoft サポートにお問い合わせください。
- 必要な Azure ロールとアクセス許可の[詳細を参照](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)してください。

## <a name="fix-discovery-couldnt-be-initiated"></a>解決策:検出を開始できなかった

エラー 60028: "Discovery couldn't be initiated because of an error. The operation failed for the specified list of hosts or clusters" (エラーが発生したため、検出を開始できませんでした。指定されたホストまたはクラスターのリストに対する操作に失敗しました) は、VM 情報のアクセスまたは取得で問題が発生したため、エラーで示されているホストで検出を開始できなかったことを示します。 残りのホストは正常に追加されました。

- **[ホストの追加]** オプションを使用して、エラーで示されているホストをもう一度追加します。
- 検証エラーが発生している場合は、修復のガイダンスを確認してエラーを修正し、 **[保存して検出を開始]** オプションをもう一度試します。

## <a name="fix-azure-ad-operation-failed-60025"></a>解決策:Azure AD の操作が失敗した (60025)

エラー 60025: "An Azure AD operation failed. The error occurred while creating or updating the Azure AD application" (Azure AD の操作が失敗しました。Azure AD アプリケーションを作成または更新しているときにエラーが発生しました) は、検出を開始するために使用された Azure ユーザー アカウントが、アプライアンスの登録に使用されたアカウントと異なる場合に発生します。 次のいずれかを実行します。

- 検出を開始するユーザー アカウントが、アプライアンスの登録に使用するものと同じであることを確認します。
- 検出操作が失敗しているユーザー アカウントに、Azure Active Directory アプリケーションのアクセス許可を提供します。
- Azure Migrate プロジェクト用に以前に作成したリソース グループを削除します。 別のリソース グループを作成して、もう一度開始します。
- Azure Active Directory アプリケーションのアクセス許可に関する[詳細を参照](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)してください。


## <a name="discovered-vms-not-in-portal"></a>ポータルで VM が検出されない

検出を開始して、**Server Assessment** および **Server Migration** に **[検出中]** と表示されたにもかかわらず、ポータルに VM が表示されない場合は、次の点に注意してください。

- アプライアンスから検出を開始した後、VMware VM の検出には約 15 分かかり、Hyper-V VM に追加された各ホストの検出には約 2 分かかります。
- これらの待ち時間が経過した後も引き続き **[検出中]** と表示される場合は、 **[サーバー]** タブの **[最新の情報に更新]** を選択します。これで、**Server Assessment** および **Server Migration** に検出されたサーバーの数が表示されるはずです。


## <a name="deleted-vms-in-the-portal"></a>ポータルで削除された VM

オンプレミスの環境を継続的に検出するアプライアンスをデプロイしても、削除された VM がポータルに表示される場合は、次の点に注意してください。  

- アプライアンスによって収集された検出データがポータルに反映されるまでに最大で 30 分かかります。
- 30 分後に最新の情報が表示されない場合は、次の手順に従ってデータを最新の情報に更新してください。

    1. **[サーバー]**  >  **[Azure Migrate Server Assessment]\(Azure Migrate Server Assessment\)** で、 **[概要]** を選択します。
    2. **[管理]** の下で、 **[Agent Health]\(エージェントの正常性\)** を選択します
    3. **[エージェントを更新する]** を選択します。
    1. 更新操作が完了するまで待ちます。 これで、最新の情報が表示されるはずです。

## <a name="vm-information-isnt-in-the-portal"></a>ポータルに VM の情報が表示されない

- アプライアンスによって収集された検出データがポータルに反映されるまでに最大で 30 分かかります。
- 30 分後に最新の情報が表示されない場合は、次の手順に従ってデータを最新の情報に更新してください。

    1. **[サーバー]**  >  **[Azure Migrate Server Assessment]\(Azure Migrate Server Assessment\)** で、 **[概要]** を選択します。
    2. **[管理]** の下で、 **[Agent Health]\(エージェントの正常性\)** を選択します
    3. **[エージェントを更新する]** を選択します。
    1. 更新操作が完了するまで待ちます。 これで、最新の情報が表示されるはずです。


## <a name="fix-cant-connect-to-host-or-cluster"></a>解決策:ホストまたはクラスターに接続できない

エラー 50004: "Can't connect to a host or cluster because the server name can't be resolved. (サーバー名を解決できないため、ホストまたはクラスターに接続できません。) WinRM エラー コード: 0x803381B9" は、アプライアンスの要求を処理する Azure DNS で、指定されたクラスターまたはホストの名前を解決できない場合に発生する可能性があります。

- クラスターでこのエラーが発生する場合は、クラスターの FQDN。
- クラスター内のホストについても、このエラーが表示されることがあります。 これは、アプライアンスはクラスターに接続できますが、クラスターから FQDN ではないホスト名が返されることを示します。 このエラーを解決するには、IP アドレスとホスト名のマッピングを追加することでアプライアンス上の hosts ファイルを更新します。
    1. 管理者としてメモ帳を開きます。
    2. C:\Windows\System32\Drivers\etc\hosts ファイルを開きます。
    3. IP アドレスとホスト名を 1 行で追加します。 このエラーが見られるホストまたはクラスターごとに繰り返します。
    4. hosts ファイルを保存して閉じます。
    5. アプライアンス管理アプリを使用して、アプライアンスからホストに接続できるかどうかを確認します。 30 分後、これらのホストの最新情報が Azure portal に表示されるようになるはずです。



## <a name="fix-assessment-readiness"></a>評価の準備状態を修正する

評価の準備状態を修正するには、次のようにします。

**問題** | **解決策**
--- | ---
サポートされていないブートの種類 | Azure では、ブートの種類が EFI の VM はサポートされません。 移行を実行する前に、ブートの種類を BIOS に変換することをお勧めします。 <br/><br/>Azure Migrate Server Migration を使用して、そのような VM の移行を処理することができます。 それにより、移行中に VM のブートの種類が BIOS に変換されます。
条件付きでサポートされる Windows オペレーティング システム | オペレーティング システムはサポート期間が終了しており、[Azure でのサポート](https://aka.ms/WSosstatement)のためにはカスタム サポート契約 (CSA) が必要です。 Azure に移行する前にアップグレードを検討してください。
サポートされていない Windows オペレーティング システム | Azure では、[特定の Windows OS バージョン](https://aka.ms/WSosstatement)のみがサポートされています。 Azure に移行する前に、コンピューターのアップグレードを検討してください。
条件付きで動作が保証されている Linux OS | Azure では、[特定の Linux OS バージョン](../virtual-machines/linux/endorsed-distros.md)のみがサポートされます。 Azure に移行する前に、コンピューターのアップグレードを検討してください。
動作が保証されていない Linux OS | マシンは Azure で起動する可能性がありますが、オペレーティング システムは Azure ではサポートされていません。 Azure に移行する前に、[サポートされている Linux バージョン](../virtual-machines/linux/endorsed-distros.md)へのアップグレードを検討してください。
オペレーティング システムが不明です | VM のオペレーティング システムが、vCenter Server で "その他" として指定されました。 この動作により、Azure Migrate による VM の Azure 対応性の検証はブロックされます。 コンピューターを移行する前に、オペレーティング システムが Azure によって[サポートされている](https://aka.ms/azureoslist)ことを確認してください。
サポートされていないビット バージョン | 32 ビット オペレーティング システムの VM は Azure で起動する可能性がありますが、Azure に移行する前に、64 ビットにアップグレードすることをお勧めします。
Microsoft Visual Studio のサブスクリプションが必要です | コンピューターで実行されている Windows クライアント オペレーティング システムは、Visual Studio のサブスクリプションによってのみサポートされます。
必要なストレージ パフォーマンスの VM が見つかりません | そのマシンに必要なストレージ パフォーマンス (1 秒あたりの入力/出力操作数 (IOPS) とスループット) が Azure VM のサポート範囲を超えています。 移行前に、そのマシンのストレージ要件を緩和します。
必要なネットワーク パフォーマンスの VM が見つかりません | そのマシンに必要なネットワーク パフォーマンス (入力/出力) が Azure VM のサポート範囲を超えています。 そのマシンのネットワーク要件を緩和します。
指定した場所で VM が見つかりません | 移行前に、別のターゲットの場所を指定します。
不適切なディスクが 1 つ以上あります | VM に接続されている 1 つ以上のディスクで、Azure の要件が満たされていません。<br/><br/> Azure Migrate: 現在、Server Assessment では、Ultra SSD ディスクはサポートされておらず、Premium マネージド ディスクのディスク制限 (32 TB) に基づいてディスクが評価されます。<br/><br/> VM に接続されている各ディスクについて、ディスクのサイズが (Ultra SSD ディスクでサポートされている) 64 TB 未満であることを確認します。<br/><br/> そうでない場合は、Azure に移行する前にディスクのサイズを減らすか、または Azure で複数のディスクを使用して[ストライピングし](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)、ストレージの上限を高くします。 各ディスクで必要とされるパフォーマンス (IOPS とスループット) が Azure の[仮想マシンの管理ディスク](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)でサポートされていることを確認してください。
不適切なネットワーク アダプターが 1 つ以上あります。 | 移行前に、マシンから未使用のネットワーク アダプターを削除します。
ディスクの数が制限を超えています | 移行前に、マシンから未使用のディスクを削除します。
ディスクのサイズが制限を超えています | Azure Migrate: 現在、Server Assessment では、Ultra SSD ディスクはサポートされておらず、Premium ディスクの制限 (32 TB) に基づいてディスクが評価されます。<br/><br/> ただし、Azure では (Ultra SSD ディスクでサポートされる) 最大 64 TB のディスク サイズまでサポートされています。 移行の前にディスクを 64 TB 未満に圧縮するか、または Azure で複数のディスクを使用して[ストライピングし](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)、ストレージの上限を高くします。
指定した場所でディスクを使用できません | 移行前に、ディスクがターゲットの場所にあることを確認します。
指定した冗長性でディスクを使用できません | 評価の設定 (既定では LRS) で定義されているストレージの冗長性の種類をディスクで使用する必要があります。
内部エラーが原因でディスクの適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
必要なコアとメモリを備えた VM が見つかりません | Azure が適切な VM の種類を見つけられなかったことになります。 移行前に、オンプレミスのマシンのメモリとコアの数を削減します。
内部エラーが原因で VM の適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
内部エラーが原因で 1 つ以上のディスクの適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
内部エラーが原因で 1 つ以上のネットワーク アダプターの適合性を決定できませんでした | そのグループの評価を新しく作成してみます。

## <a name="linux-vms-are-conditionally-ready"></a>Linux VM が "条件付きで対応" である

Server Assessment では、Server Assessment の既知のギャップのため、Linux VM が "条件付きで対応" とマークされます。

- ギャップのため、オンプレミスの VM にインストールされている Linux OS のマイナー バージョンを検出できません。
- たとえば、RHEL 6.10 の場合、現在の Server Assessment では、RHEL 6 のみが OS バージョンとして検出されます。
-  特定のバージョンの Linux のみが Azure によって動作保証されるため、Linux VM は現在 Server Assessment において条件付き対応とマークされます。
- オンプレミスの VM で実行されている Linux OS が Azure で動作保証済みかどうかは、[Azure Linux サポート](https://aka.ms/migrate/selfhost/azureendorseddistros)のページを参照して特定できます。
-  ディストリビューションの動作保証を確認した後は、この警告を無視してかまいません。

## <a name="azure-skus-exceed-on-premises-sizing"></a>Azure SKU がオンプレミスのサイズ設定を超えている

Azure Migrate Server Assessment では、評価の種類に基づいて、現在のオンプレミスの割り当てより多くのコアとメモリを備えた Azure VM SKU が推奨される場合があります。


- VM SKU の推奨は、評価のプロパティによって異なります。
- これは、Server Assessment で実行する評価の種類によって影響を受けます: "*パフォーマンスベース*" または "*オンプレミス*"。
- パフォーマンスベースの評価では、Server Assessment によりオンプレミスの VM の利用状況データ (CPU、メモリ、ディスク、ネットワークの利用状況) が考慮されて、オンプレミスの VM に対して適切なターゲット VM SKU が決定されます。 また、有効な使用率を決定するときに快適性係数が追加されます。
- オンプレミスのサイズ設定では、パフォーマンス データは考慮されず、オンプレミスの割り当てに基づいてターゲット SKU が推奨されます。

これが推奨に与える影響を確認するため、次の例を見てみましょう。

オンプレミスの VM は、4 つのコアと 8 GB のメモリを備え、CPU 使用率は 50%、メモリ使用率は 50%、指定された快適性係数は 1.3 です。

-  評価が**オンプレミス**の場合は、4 コアと 8 GB のメモリを備えた Azure VM SKU が推奨されます。
- 評価がパフォーマンスベースの場合は、CPU およびメモリの有効な使用率 (4 コアの 50% * 1.3 = 2.6 コア、8 GB メモリの 50% * 1.3 = 5.3 GB メモリ) に基づき、4 コア (最も近いサポートされるコア数) と 8 GB メモリ (最も近いサポートされるメモリ サイズ) の最も安価な VM SKU が推奨されます。
- 評価のサイズ決定に関する[詳細を参照](concepts-assessment-calculation.md#sizing)してください。

## <a name="azure-disk-skus-bigger-than-on-premises"></a>オンプレミスより大きい Azure ディスク SKU

Azure Migrate Server Assessment では、評価の種類に基づいて、より大きいディスクが推奨される場合があります。
- Server Assessment でのディスクのサイズ設定は、サイズ設定基準とストレージの種類という 2 つの評価プロパティに依存します。
- サイズ設定基準が**パフォーマンスベース**で、ストレージの種類が**自動**である場合、ディスクの IOPS とスループットの値を考慮して、ターゲット ディスクの種類 (Standard HDD、Standard SSD、または Premium) が特定されます。 その後、そのディスクの種類からディスク SKU が推奨され、この推奨ではオンプレミスのディスクのサイズ要件が考慮されます。
- サイズ設定基準が**パフォーマンスベース**で、ストレージの種類が **Premium** である場合、オンプレミスのディスクの IOPS、スループット、サイズの要件に基づいて、Azure の Premium ディスク SKU が推奨されます。 サイズ設定基準が**オンプレミス**で、ストレージの種類が **Standard HDD**、**Standard SSD**、または **Premium** である場合は、同じロジックを使ってディスクのサイズ設定が行われます。

たとえば、オンプレミスのディスクと 32 GB のメモリを使っていても、ディスクの読み取りと書き込みの集計 IOPS が 800 IOPS である場合、Server Assessment では Premium ディスク (高い IOPS 要件であるため) が推奨された後、必要な IOPS とサイズに対応できるディスク SKU が推奨されます。 この例では、適合する最も近いものは P15 (256 GB、1100 IOPS) になります。 オンプレミスのディスクで必要なサイズは 32 GB でしたが、オンプレミスのディスクの高い IOPS 要件により、Server Assessment ではさらに大きいディスクが推奨されます。

## <a name="fix-percentage-of-utilized-core-or-memory-missing"></a>解決策:コアまたはメモリの使用率の欠測

Azure Migrate アプライアンスで関連するオンプレミス VM のパフォーマンス データを収集できない場合、Server Assessment では "PercentageOfCoresUtilizedMissing" または "PercentageOfMemoryUtilizedMissing" が報告されます。

- これは、評価期間中に VM がオフにされた場合に発生する可能性があります。 アプライアンスでは、オフになっていた VM のパフォーマンス データを収集することはできません。
- メモリ カウンターのみが取得されず、Hyper-V VM を評価しようとしている場合は、これらの VM で動的メモリが有効になっているかどうかを確認してください。 Hyper-V VM のみの既知の問題のため、動的メモリが有効になっていない VM のメモリ使用率データは、Azure Migrate アプライアンスで収集できません。
- いずれかのパフォーマンス カウンターが不足している場合、Azure Migrate Server Assessment は割り当てられたコアとメモリにフォールバックし、対応する VM サイズが推奨されます。

## <a name="is-the-vm-os-license-cost-included-in-cost-assessment"></a>VM OS のライセンス コストはコスト評価に含まれているか

現在、Azure Migrate Server Assessment では、Windows マシンのオペレーティング システムのライセンス コストのみが考慮されます。 Linux マシンのライセンス コストは現在は考慮されません。

## <a name="performance-history-and-percentile-use"></a>パフォーマンスの履歴とパーセンタイルの使用

これらのプロパティは、Azure Migrate Server Assessment でのパフォーマンスベースのサイズ設定にのみ適用されます。

Server Assessment では、オンプレミスのマシンのパフォーマンス データを継続的に収集し、それを使って Azure で VM の SKU とディスクの SKU を推奨します。 このパフォーマンス データは、Server Assessment によって次のように収集されます。
- Azure Migrate アプライアンスでは、オンプレミスの環境が継続的にプロファイリングされて、VMware VM の場合は 20 秒ごと、Hyper-V VM の場合は 30 秒ごとに、使用状況データがリアルタイムで収集されます。
- このアプライアンスでは、20 秒または 30 秒でサンプルがロールアップされて、10 分ごとに 1 つのデータ ポイントが作成されます。 1 つのデータ ポイントを作成するために、アプライアンスでは 20 秒および 30 秒のすべてのサンプルからピーク値が選択されて、Azure に送信されます。
- Server Assessment で評価を作成するときは、パフォーマンス期間とパフォーマンス履歴のパーセンタイル値に基づいて、代表的な使用率の値が特定されます。 たとえば、パフォーマンス履歴が 1 週間で、パーセンタイル使用率が 95 番目の場合、Azure Migrate では過去 1 週間のすべての 10 分サンプル ポイントが昇順で並べ替えられて、代表的な値として 95 番目のパーセンタイルが選択されます。
- 95 番目のパーセンタイル値を設定することにより、99 番目のパーセンタイルを選択した場合に含まれる可能性がある外れ値はすべて無視されるようになります。
- 期間中のピーク使用率を選択し、外れ値を見逃さないようにする場合は、パーセンタイル使用率として 99 番目のパーセンタイルを選択する必要があります。



## <a name="i-cant-find-dependency-visualization-for-azure-government"></a>Azure Government で依存関係の視覚化が見つからない

Azure Migrate の依存関係視覚化機能は Service Map に依存しています。 現在、Azure Government では Service Map を使用できないため、Azure Government ではこの機能を使用できません。

## <a name="dependencies-dont-show-after-installing-agents"></a>エージェントのインストール後に依存関係が表示されない


オンプレミスの VM に依存関係視覚化エージェントをインストールした後、Azure Migrate ポータルで依存関係が表示されるまで 15 分から 30 分かかります。 30 分以上待っている場合は、Microsoft Monitoring Agent (MMA) が Log Analytics ワークスペースに接続できることを確認してください。

Windows VM の場合:
1. コントロール パネルで MMA を開始します。
2. **[Microsoft Monitoring Agent のプロパティ]**  >  **[Azure Log Analytics (OMS)]** で、ワークスペースの **[状態]** が緑色になっていることを確認します。
3. 状態が緑色でない場合は、ワークスペースを削除して、再度 MMA に追加してみてください。

      ![MMA のプロパティ ダイアログ ボックス](./media/troubleshooting-general/mma-status.png)

Linux VM の場合、MMA と依存関係エージェントのインストールに関するすべてのコマンドが正常に実行されたことを確認します。

## <a name="supported-mma-os"></a>サポートされている MMA OS

 サポートされている [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) オペレーティング システムと [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) オペレーティング システムを確認します。

## <a name="supported-dependency-agent-os"></a>サポートされている依存関係エージェント OS

サポートされている [Windows および Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) オペレーティング システムを確認します。

## <a name="dependencies-for-more-than-an-hour"></a>1 時間を超える依存関係

Azure Migrate を使用すると、過去 1 か月間の特定の日付に戻ることができますが、依存関係を視覚化できる期間は最大 1 時間です。

たとえば、依存関係マップにある期間機能を使用して、昨日の依存関係を表示することが可能ですが、1 時間の期間のみ表示できます。

ただし、Azure Monitor ログを使用すれば、長期間にわたって[依存関係データのクエリ](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)を実行できます。

## <a name="i-cant-visualize-dependencies-for-groups-with-more-than-10-vms"></a>10 個より多くの VM が含まれるグループの依存関係を視覚化できない

Azure Migrate Server Assessment では、最大 10 個の VM を含む[グループの依存関係を視覚化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)できます。 大きいグループの場合、依存関係を視覚化するには VM を小さいグループに分割することをお勧めします。

## <a name="machines-show-install-agent-not-view-dependencies"></a>マシンに "依存関係の表示" ではなく "エージェントのインストール" が表示される

依存関係の視覚化が有効になっているマシンを Azure に移行した後、マシンでは、次の動作により、"依存関係の表示" ではなく "エージェントのインストール" アクションが表示される場合があります。


- Azure に移行した後、オンプレミスのマシンはオフになり、同等の VM が Azure で起動されます。 これらのマシンは、別々の MAC アドレスを取得します。
- ユーザーがオンプレミスの IP アドレスを保持するかどうかにより、マシンは異なる IP アドレスも取得する場合があります。
- MAC と IP アドレスの両方がオンプレミスと異なる場合、Azure Migrate ではオンプレミスのマシンと Service Map 依存関係データの関連付けは行われません。 この場合、依存関係の表示ではなく、エージェントをインストールするオプションが表示されます。
- Azure へのテスト移行の後、オンプレミスのマシンでは想定どおりにオンの状態が維持されます。 Azure にスピン アップされる同等のマシンでは、異なる MAC アドレスが取得され、異なる IP アドレスが取得されることがあります。 これらのマシンから送信される Azure Monitor ログ トラフィックをブロックしない限り、Azure Migrate ではオンプレミスのマシンと Service Map 依存関係データの関連付けは行われず、依存関係が表示されるのではなく、エージェントをインストールするオプションが表示されます。


## <a name="collect-network-traffic-logs-in-portal"></a>ポータルでネットワーク トラフィック ログを収集する

ログを収集するには次のようにします。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. F12 キーを押して開発者ツールを起動します。 必要な場合は、 **[ナビゲーション時にエントリをクリア]** の設定をオフにします。
3. **[ネットワーク]** タブを選択し、ネットワーク トラフィックのキャプチャを開始します。
   - Chrome では、 **[Preserve log]\(ログの保持\)** を選択します。 自動で記録が開始されるはずです。 赤い円は、トラフィックがキャプチャされていることを示しています。 赤い円が表示されない場合は、黒い円を選択して開始します。
   - Microsoft Edge または Internet Explorer では、自動で記録が開始されるはずです。 開始されない場合は、緑色の再生ボタンを選択します。
4. エラーを再現してみます。
5. 記録中にエラーが発生したら、記録を停止し、記録されたアクティビティのコピーを保存します。
   - Chrome では、右クリックして **[Save as HAR with content]\(内容を HAR ファイルに保存する\)** を選択します。 この操作により、ログが .har ファイルとして圧縮されエクスポートされます。
   - Microsoft Edge または Internet Explorer で、 **[キャプチャしたトラフィックのエクスポート]** オプションを選択します。 この操作により、ログが圧縮されてエクスポートされます。
6. **[コンソール]** タブを選択し、いずれかの警告またはエラーを確認します。 コンソール ログを保存するには次の操作を行います。
   - Chrome では、コンソール ログのどこかを右クリックします。 **[名前を付けて保存]** を選択し、ログをエクスポートして zip 圧縮します。
   - Microsoft Edge または Internet Explorer では、エラーを右クリックして **[すべてコピー]** を選択します。
7. 開発者ツールを閉じます。

---
title: Azure Migrate に関する問題のトラブルシューティング | Microsoft Docs
description: Azure Migrate サービスの既知の問題についての概要を説明し、一般的なエラーのトラブルシューティングのヒントを提供します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: fa1e7fcf89ccc06e429831191ba5dfce3cf33797
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828323"
---
# <a name="troubleshoot-azure-migrate"></a>Azure Migrate のトラブルシューティング

[Azure Migrate](migrate-services-overview.md) には、評価と移行のためのツールのほか、サードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが集約されています。 この記事は、Azure Migrate、Azure Migrate Server Assessment、および Azure Migrate Server Migration に関するエラーのトラブルシューティングに役立ちます。

## <a name="azure-migrate-project-issues"></a>Azure Migrate プロジェクトの問題

### <a name="i-cant-find-my-existing-azure-migrate-project"></a>既存の Azure Migrate プロジェクトが見つかりません。

Azure Migrate には [2 つのバージョン](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)があります。 プロジェクトを作成したバージョンに応じて、次のいずれかの方法を使用します。

* Azure Migrate の以前のバージョン (以前のエクスペリエンス) で作成されたプロジェクトを探している場合は、次の手順に従ってプロジェクトを見つけます。

    1. [Azure portal](https://portal.azure.com) に移動して、**Azure Migrate** を検索します。
    2. Azure Migrate ダッシュボードに、以前のプロジェクトへのアクセスについて説明するバナーが表示されます。 このバナーが表示されるのは、以前のエクスペリエンスでプロジェクトを作成した場合だけです。 そのバナーを選択します。

       ![既存のプロジェクトにアクセスする](./media/troubleshooting-general/access-existing-projects.png)

    3. 以前のバージョンの Azure Migrate で作成された既存のプロジェクトの一覧が表示されます。

* 現在のバージョン (新しいエクスペリエンス) で作成されたプロジェクトを探している場合は、次の手順に従ってプロジェクトを見つけます。

    1. [Azure portal](https://portal.azure.com) に移動して、**Azure Migrate** を検索します。
    2. Azure Migrate ダッシュボードの左側のウィンドウにある **[サーバー]** ページに移動し、右上隅の **[変更]** を選択します。

       ![既存の Azure Migrate プロジェクトに切り替える](./media/troubleshooting-general/switch-project.png)

    3. 適切なサブスクリプションと Azure Migrate プロジェクトを選択します。

### <a name="how-do-i-create-a-second-azure-migrate-project"></a>2 つ目の Azure Migrate プロジェクトを作成するにはどうすればよいですか?

新しい Azure Migrate プロジェクトを作成するには、以下の手順のようにします。

1. [Azure portal](https://portal.azure.com) に移動して、**Azure Migrate** を検索します。
2. Azure Migrate ダッシュボードの左側のウィンドウにある **[サーバー]** ページに移動し、右上隅の **[変更]** を選択します。

   ![Azure Migrate プロジェクトを変更する](./media/troubleshooting-general/switch-project.png)

3. 新しいプロジェクトを作成するには、 **[click here]\(ここをクリック\)** を選択します。

   ![2 つ目の Azure Migrate プロジェクトを作成する](./media/troubleshooting-general/create-new-project.png)

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Azure Migrate では Azure のどの地域がサポートされていますか?

[VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) と [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects) の一覧をご覧ください。

### <a name="how-do-i-delete-azure-migrate-projects-and-associated-log-analytics-workspaces"></a>Azure Migrate プロジェクトと関連する Log Analytics ワークスペースを削除するにはどうすればよいですか?

Azure Migrate プロジェクトを削除すると、移行プロジェクトと "*共に*"、検出されたコンピューターに関するメタデータが削除されます。 ただし、Log Analytics ワークスペースを Server Assessment ツールに関連付けている場合、Log Analytics ワークスペースは自動的に削除されません。 (同じ Log Analytics ワークスペースが、複数のユース ケースで使用されている可能性があります。)Log Analytics ワークスペースも削除する場合は、手動で行う必要があります。

1. プロジェクトに関連付けられている Log Analytics ワークスペースを参照します。
     * 移行プロジェクトをまだ削除していない場合は、[基本] セクションに Server Assessment の概要ページからワークスペースへのリンクが見つかります。

       ![LA ワークスペース](./media/troubleshooting-general/loganalytics-workspace.png)

     * 既に移行プロジェクトを削除した場合は、Azure portal の左側のウィンドウの **[リソース グループ]** を選択します。 ワークスペースが作成されたリソース グループに移動し、それを参照します。
2. 「[Azure portal で Azure Log Analytics ワークスペースを削除する](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace)」の手順に従います。

### <a name="migration-project-creation-failed-with-a-requests-must-contain-user-identity-headers-error"></a>移行プロジェクトの作成が、"要求にはユーザー ID ヘッダーが含まれていなければなりません" というエラーで失敗しました。

この問題は、ユーザーが組織の Azure Active Directory (Azure AD) テナントへのアクセス権を持っていない場合に発生することがあります。 ユーザーは、Azure AD テナントに初めて追加されたときに、テナントへの参加を求める招待メールを受け取ります。 ユーザーがテナントに正しく追加されるためには、招待を承諾する必要があります。 メールを表示できない場合は、既にテナントへのアクセス権を持つユーザーに連絡し、「[招待をゲスト ユーザーに再送信する](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)」で指定されている手順を使用して、自分に招待を再送信するよう依頼します。

招待メールを受け取った後、メールを開き、メール内のリンクをクリックして招待を承諾する必要があります。 その後、Azure portal からサインアウトし、もう一度サインインする必要があります。 (ブラウザーの更新は機能しません。)これで、移行プロジェクトの作成を始めることができます。

## <a name="appliance-issues"></a>アプライアンスの問題

### <a name="deployment-of-an-azure-migrate-appliance-for-vmware-failed-with-a-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry-error"></a>VMware 用の Azure Migrate アプライアンスの展開が、次のエラーで失敗します: "The provided manifest file is invalid: Invalid OVF manifest entry" (指定されたマニフェスト ファイルが無効です: 無効な OVF マニフェスト エントリ)。

1. Azure Migrate アプライアンスの OVA ファイルが正常にダウンロードされていることを、そのハッシュ値をチェックして確認します。 方法については、「[評価および Azure への移行のために VMware VM を準備する](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware)」をご覧ください。 ハッシュ値が一致しない場合は、OVA ファイルをもう一度ダウンロードしてデプロイを再試行してください。
2. それでも展開が失敗する場合、および VMware vSphere クライアントを使って OVF ファイルを展開している場合は、vSphere Web クライアントで展開してみてください。 展開がそれでも失敗する場合は、別の Web ブラウザーをお試しください。
3. vSphere Web クライアントを使用しており、vCenter Server 6.5 または 6.7 にそれをデプロイしようとしている場合は、次の手順に従って ESXi ホストに直接 OVA を展開してみてください。
   - Web クライアント (https://<*ホスト IP アドレス*>/ui) を使用して、(vCenter Server ではなく) ESXi ホストに直接接続します。
   - **[ホーム]**  >  **[インベントリ]** に移動する。
   - **[ファイル]**  >  **[Deploy OVF template]\(OVF テンプレートの展開\)** の順に選択します。 OVA を参照して、デプロイを完了する。
4. それでも展開が失敗する場合は、Azure Migrate のサポートにお問い合わせください。

### <a name="the-appliance-cant-connect-to-the-internet"></a>アプライアンスがインターネットに接続できません。

お使いのコンピューターがプロキシの内側にある場合、この動作が発生することがあります。 プロキシに承認資格情報が必要な場合は、それを提供します。
URL ベースのファイアウォール プロキシを使用してアウトバウンド接続を制御している場合は、次の必須の URL を必ず許可リストに追加してください。

- [VMware のサーバー評価](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
- [Hyper-V のサーバー評価](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
- [VMware のサーバー移行 (エージェントレス)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
- [VMware のサーバー移行 (エージェントベース)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
- [Hyper-V のサーバー移行](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

インターネットへの接続にインターセプト プロキシを使用している場合、プロキシの証明書をアプライアンス VM にインポートする必要があります。 「[Azure Migrate アプライアンス](https://docs.microsoft.com/azure/migrate/concepts-collector)」の手順に従って、プロキシ証明書をインポートできます。

### <a name="error-802-date-and-time-synchronization-error"></a>エラー 802: 日付と時刻の同期エラー。

サーバー クロックが現在の時刻と同期しておらず、5 分以上ずれている可能性があります。 コレクター VM のクロックの時刻を変更して現在の時刻と一致させるには、次の手順に従います。

1. VM で管理者用のコマンド プロンプトを開きます。
2. タイム ゾーンを確認するには、**w32tm /tz** を実行します。
3. 時刻を同期するには、**w32tm /resync** を実行します。


###  <a name="connection-failed-error-unabletoconnecttoserver"></a>接続できませんでした。 エラー:UnableToConnectToServer。

vCenter Server <*サーバー名*>.com:9443 に接続できない可能性があります。 エラーの詳細では、メッセージを受け入れることができる https://<*サーバー名*>.com:9443/sdk でリッスンしているエンドポイントがないことが示されています。

このような状況では、最新バージョンのコレクター アプライアンスを実行しているかどうかを確認します。 そうでない場合は、アプライアンスを[最新バージョン](https://docs.microsoft.com/azure/migrate/concepts-collector)にアップグレードします。

最新バージョンでも引き続き問題が発生する場合は、コレクターのコンピューターで指定された vCenter Server 名を解決できないか、または指定されたポートが間違っている可能性があります。 ポートが指定されていない場合、コレクターでは既定でポート番号 443 への接続が試みられます。

1. コレクターのコンピューターから <*サーバー名*>.com に対して ping を実行します。
2. ステップ 1 が失敗する場合は、IP アドレスを使用して vCenter サーバーへの接続を試みます。
3. 正しいポート番号を識別して vCenter に接続します。
4. vCenter サーバーが起動されて実行中であることを確認します。


### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>The appliance might not be registered successfully to the Azure Migrate project (Error ID: 60052) (Azure Migrate プロジェクトにアプライアンスが正常に登録されていない可能性があります (エラー ID: 60052))。

このエラーは、アプライアンスの登録に使用された Azure アカウントに十分なアクセス許可がない場合に発生します。 アプライアンスの登録に使用する Azure ユーザー アカウントに、少なくともサブスクリプションの共同作成者アクセス許可があることを確認します。 必要な Azure ロールとアクセス許可の[詳細を参照](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)してください。

### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>The appliance might not be registered successfully to the Azure Migrate project (Error ID: 60052) (Azure Migrate プロジェクトにアプライアンスが正常に登録されていない可能性があります (エラー ID: 60039))。

アプライアンスを登録するために選択された Azure Migrate プロジェクトが見つからない可能性があります。 この場合、登録は失敗します。 Azure portal に移動して、お使いのリソース グループにプロジェクトが存在するかどうかを確認します。 プロジェクトが存在しない場合は、ご利用のリソース グループに新しい Azure Migrate プロジェクトを作成して、アプライアンスをもう一度登録してください。 新しい Azure Migrate プロジェクトの作成についての[詳細を参照](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool)してください。

### <a name="an-azure-key-vault-management-operation-failed-error-id-60030-60031"></a>Azure Key Vault の管理操作が失敗しました (エラー ID: 60030、60031)。

アプライアンスの登録に使用する Azure ユーザー アカウントに、少なくともサブスクリプションの共同作成者アクセス許可があることを確認します。 また、エラー メッセージに指定されたキー コンテナーへのアクセス権がアカウントにあるかどうかを確認してから、操作を再試行してください。 引き続き問題が発生する場合は、Microsoft サポートにお問い合わせください。 必要な Azure ロールとアクセス許可の[詳細を参照](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)してください。

### <a name="discovery-couldnt-be-initiated-because-of-an-error-the-operation-failed-for-the-specified-list-of-hosts-or-clusters-error-id-60028"></a>Discovery couldn't be initiated because of an error. The operation failed for the specified list of hosts or clusters (Error ID: 60028) (エラーが発生したため、検出を開始できませんでした。指定された一連のホストまたはクラスターに対する操作に失敗しました (エラー ID: 60028))。

VM 情報へのアクセスまたは取得で問題が発生したため、エラーに示されているホストで検出を開始できませんでした。 残りのホストは正常に追加されました。 **[ホストの追加]** オプションを使用して、エラーに記載されているホストをもう一度追加します。 検証エラーが発生している場合は、修復のガイダンスを確認してエラーを修正し、 **[保存して検出を開始]** オプションをもう一度試します。

### <a name="an-azure-ad-operation-failed-the-error-occurred-while-creating-or-updating-the-azure-ad-application-error-id-60025"></a>An Azure AD operation failed. The error occurred while creating or updating the Azure AD application (Error ID: 60025) (Azure AD の操作が失敗しました。Azure AD アプリケーションを作成または更新しているときにエラーが発生しました (エラー ID: 60025))。

このエラーは、検出を開始するのに使用する Azure ユーザー アカウントが、アプライアンスの登録に使用するアカウントと異なる場合に発生します。 次のいずれかの手順を行います。
1. 検出を開始するユーザー アカウントが、アプライアンスの登録に使用するものと同じであることを確認します。
1. 検出操作が失敗している他のユーザー アカウントに AAD アプリケーションのアクセス許可を提供します。
1. Azure Migrate プロジェクト用に以前作成したリソース グループを削除し、別のリソース グループを作成してもう一度開始します。

AAD アプリケーションのアクセス許可の[詳細を参照](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)してください。

## <a name="discovery-issues"></a>検出の問題

### <a name="i-started-discovery-but-dont-see-the-discovered-vms-on-the-azure-portal-the-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>検出を開始しましたが、検出された VM が Azure portal に表示されません。 **Server Assessment** および **Server Migration** のタイルに "検出中" という状態が表示されます。
アプライアンスから検出を開始した後、検出されたマシンが Azure portal に表示されるまでにしばらく時間がかかります。 VMware の検出では約 15 分、Hyper-V の検出では追加されたホストごとに約 2 分かかります。 これらの待機期間が経過した後も引き続き "検出中" と表示される場合は、 **[サーバー]** タブの **[更新]** を選択します。これで、検出されたサーバーの数が [Server Assessment] および [Server Migration] タイルに表示されるはずです。


### <a name="im-using-an-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-shown-in-the-portal"></a>オンプレミスの環境を継続的に検出するアプライアンスを使用していますが、オンプレミスの環境で削除された VM がまだポータルに表示されます。

アプライアンスによって収集された検出データがポータルに反映されるまでに最大で 30 分かかります。 30 分後になっても最新の情報が表示されない場合は、次の手順に従ってデータを最新の情報に更新してください。

1. **[サーバー]**  >  **[Azure Migrate Server Assessment]\(Azure Migrate Server Assessment\)** に移動し、 **[概要]** を選択します。
2. **[管理]** の下で、 **[Agent Health]\(エージェントの正常性\)** を選択します。
3. **[エージェントを更新する]** を選択します。 このオプションは、エージェントの一覧の下に表示されます。
4. 更新操作が完了するまで待ちます。 これで、ご利用の VM に関する最新情報が表示されます。

### <a name="i-dont-see-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>オンプレミスの VM に関する最新情報がポータルに表示されません。

アプライアンスによって収集された検出データがポータルに反映されるまでに最大で 30 分かかります。 30 分後になっても最新の情報が表示されない場合は、次の手順に従ってデータを最新の情報に更新してください。

1. **[サーバー]**  >  **[Azure Migrate Server Assessment]\(Azure Migrate Server Assessment\)** に移動し、 **[概要]** を選択します。
2. **[管理]** の下で、 **[Agent Health]\(エージェントの正常性\)** を選択します
3. **[エージェントを更新する]** を選択します。 このオプションは、エージェントの一覧の下に表示されます。
4. 更新操作が完了するまで待ちます。 これで、ご利用の VM に関する最新情報が表示されます。

### <a name="cant-connect-to-a-host-or-cluster-because-the-server-name-cant-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>Can't connect to a host or cluster because the server name can't be resolved. (サーバー名を解決できないため、ホストまたはクラスターに接続できません。) WinRM エラー コード: 0x803381B9 (エラー ID: 50004)。
このエラーは、アプライアンスの要求を処理する Azure DNS が、指定されたクラスターまたはホストの名前を解決できない場合に発生します。 クラスターでこのエラーが表示された場合は、そのクラスターの完全修飾ドメイン名 (FQDN) を指定してみてください。

クラスター内のホストについても、このエラーが表示されることがあります。 この場合、アプライアンスはクラスターに接続できますが、クラスターからは FQDN ではないホスト名が返されます。

このエラーを解決するには、IP アドレスとホスト名のマッピングを追加することでアプライアンス上の hosts ファイルを更新します。
1. 管理者としてメモ帳を開きます。 次に、C:\Windows\System32\Drivers\etc\hosts ファイルを開きます。
2. IP アドレスとホスト名を 1 行で追加します。 このエラーが見られるホストまたはクラスターごとに繰り返します。
3. hosts ファイルを保存して閉じます。
4. アプライアンス管理アプリを使用して、アプライアンスからホストに接続できるかどうかを確認します。 30 分後、それらのホストの最新情報を Azure portal で確認できるようになります。


## <a name="assessment-issues"></a>評価の問題

### <a name="azure-readiness-issues"></a>Azure 対応性の問題

問題 | Remediation
--- | ---
サポートされていないブートの種類 | Azure では、ブートの種類が EFI の VM はサポートされません。 移行を実行する前に、ブートの種類を BIOS に変換することをお勧めします。 <br/><br/>Azure Migrate Server Migration を使用して、そのような VM の移行を処理することができます。 それにより、移行中に VM のブートの種類が BIOS に変換されます。
条件付きでサポートされる Windows OS | この OS はサポート期間が終了しており、[Azure でのサポート](https://aka.ms/WSosstatement)のためにはカスタム サポート契約 (CSA) が必要です。 Azure に移行する前に OS のアップグレードを検討してください。
サポートされていない Windows OS | Azure では、[特定の Windows OS バージョン](https://aka.ms/WSosstatement)のみがサポートされています。 Azure に移行する前に、コンピューターの OS のアップグレードを検討してください。
条件付きで動作が保証されている Linux OS | Azure では、[特定の Linux OS バージョン](../virtual-machines/linux/endorsed-distros.md)のみがサポートされます。 Azure に移行する前に、コンピューターの OS のアップグレードを検討してください。
動作が保証されていない Linux OS | マシンは Azure で起動する場合がありますが、OS は Azure ではサポートされていません。 Azure に移行する前に、OS を[サポートされている Linux バージョン](../virtual-machines/linux/endorsed-distros.md)にアップグレードすることを検討してください。
オペレーティング システムが不明です | VM のオペレーティング システムが、vCenter Server で "その他" として指定されました。 この動作により、Azure Migrate による VM の Azure 対応性の検証はブロックされます。 マシンを移行する前に、マシンの OS が Azure によって[サポートされている](https://aka.ms/azureoslist)ことを確認してください。
サポートされていないビット数版の OS | 32 ビット OS の VM は Azure で起動する場合がありますが、Azure に移行する前に、VM の OS を 64 ビットにアップグレードすることをお勧めします。
Microsoft Visual Studio のサブスクリプションが必要です | マシンで実行されている Windows クライアント OS は、Visual Studio のサブスクリプションによってのみサポートされます。
必要なストレージ パフォーマンスの VM が見つかりません | そのマシンに必要なストレージ パフォーマンス (1 秒あたりの入力/出力操作数 (IOPS) とスループット) が Azure VM のサポート範囲を超えています。 移行前に、そのマシンのストレージ要件を緩和します。
必要なネットワーク パフォーマンスの VM が見つかりません | そのマシンに必要なネットワーク パフォーマンス (入力/出力) が Azure VM のサポート範囲を超えています。 そのマシンのネットワーク要件を緩和します。
指定した場所で VM が見つかりません | 移行前に、別のターゲットの場所を指定します。
不適切なディスクが 1 つ以上あります | VM に接続されている 1 つ以上のディスクが Azure の要件を満たしていません。 Azure Migrate: 現在、Server Assessment では、Ultra SSD ディスクはサポートされておらず、Premium マネージド ディスクのディスク制限 (32 TB) に基づいてディスクが評価されます。  VM に接続されている各ディスクについて、ディスクのサイズが (Ultra SSD ディスクでサポートされている) 64 TB 未満であることを確認します。そうでない場合は、Azure に移行する前にディスク サイズを小さくするか、Azure で複数のディスクを使用し、[それらをまとめてストライピング](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)してストレージの制限を高くします。 各ディスクで必要とされるパフォーマンス (IOPS とスループット) が Azure の[仮想マシンの管理ディスク](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)でサポートされていることを確認してください。
不適切なネットワーク アダプターが 1 つ以上あります。 | 移行前に、マシンから未使用のネットワーク アダプターを削除します。
ディスクの数が制限を超えています | 移行前に、マシンから未使用のディスクを削除します。
ディスクのサイズが制限を超えています | Azure Migrate: 現在、Server Assessment では、Ultra SSD ディスクはサポートされておらず、Premium ディスクの制限 (32 TB) に基づいてディスクが評価されます。 ただし、Azure では (Ultra SSD ディスクでサポートされる) 最大 64 TB のディスク サイズまでサポートされています。 移行の前にディスクを 64 TB 未満に圧縮するか、または Azure で複数のディスクを使用して[ストライピングし](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)、ストレージの上限を高くします。
指定した場所でディスクを使用できません | 移行前に、ディスクがターゲットの場所にあることを確認します。
指定した冗長性でディスクを使用できません | 評価の設定 (既定では LRS) で定義されているストレージの冗長性の種類をディスクで使用する必要があります。
内部エラーが原因でディスクの適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
必要なコアとメモリを備えた VM が見つかりません | Azure が適切な VM の種類を見つけられなかったことになります。 移行前に、オンプレミスのマシンのメモリとコアの数を削減します。
内部エラーが原因で VM の適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
内部エラーが原因で 1 つ以上のディスクの適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
内部エラーが原因で 1 つ以上のネットワーク アダプターの適合性を決定できませんでした | そのグループの評価を新しく作成してみます。

### <a name="i-cant-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>評価のプロパティで Azure プランとしてマイクロソフト エンタープライズ契約 (EA) を指定できません。
Azure Migrate Server Assessment では現在、マイクロソフト エンタープライズ契約 (EA) ベースの価格はサポートされていません。 この制限を回避するには、Azure プランとして**従量課金制**を使い、**割引**プロパティを使って、受け取るカスタム割引を指定します。 [評価をカスタマイズする方法の詳細を確認してください](https://aka.ms/migrate/selfhelp/eapricing)。

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready"></a>Server Assessment によって Linux VM が "条件付き対応" とマークされるのはなぜですか?
Server Assessment には、オンプレミスの VM にインストールされている Linux OS のマイナー バージョンを検出できない既知のギャップがあります (たとえば、RHEL 6.10 の場合、Server Assessment では現在、OS のバージョンが RHEL 6 としてのみ検出されます)。 特定のバージョンの Linux のみが Azure によって動作保証されるため、Linux VM は現在 Server Assessment において条件付き対応とマークされます。 オンプレミスの VM で実行されている Linux OS が Azure で動作保証済みかどうかは、[Azure Linux サポート ドキュメント](https://aka.ms/migrate/selfhost/azureendorseddistros)を参照して特定できます。 ディストリビューションの動作保証を確認した後は、この警告を無視してかまいません。

### <a name="why-does-the-vm-sku-recommended-by-server-assessment-have-more-cores-and-more-memory-than-whats-allocated-on-premises"></a>Server Assessment によって推奨される VM SKU のコアとメモリが、オンプレミスで割り当てられているものより大きいのはなぜですか?
Server Assessment での VM SKU の推奨事項は、評価のプロパティに依存します。 Server Assessment では、次の 2 種類の評価を作成できます: "*パフォーマンスベース*" と "*オンプレミス*"。 パフォーマンスベースの評価では、Server Assessment によりオンプレミスの VM の利用状況データ (CPU、メモリ、ディスク、ネットワークの利用状況) が考慮されて、オンプレミスの VM に対して適切なターゲット VM SKU が決定されます。 さらに、パフォーマンスベースのサイズ設定では、効果的な使用率を決定するときに快適性係数が考慮されます。 オンプレミスのサイズ設定では、パフォーマンス データは考慮されず、オンプレミスでの割り当てに基づいてターゲット SKU が推奨されます。

たとえば、4 つのコアと 8 GB のメモリを搭載した、CPU 使用率が 50%、メモリ使用率が 50% のオンプレミスの VM があり、評価で快適性係数 1.3 が指定されているとします。 評価のサイズ設定基準が**オンプレミス**の場合は、4 コアと 8 ギガバイト (GB) のメモリを備えた Azure VM SKU が推奨されます。

これに対し、サイズ設定基準がパフォーマンスベースの場合は次のようになります。 CPU およびメモリの有効な使用率 (4 コアの 50% * 1.3 = 2.6 コア、8 GB メモリの 50% * 1.3 = 5.3 GB メモリ) に基づき、4 コア (最も近いサポートされるコア数) と 8 GB メモリ (最も近いサポートされるメモリ サイズ) の最も安価な VM SKU が推奨されます。 [Server Assessment によってどのようにサイズ変更が行われるかを確認してください](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)。

### <a name="why-is-the-disk-sku-recommended-by-server-assessment-bigger-than-whats-allocated-on-premises"></a>Server Assessment によって推奨されるディスク SKU が、オンプレミスでの割り当てより大きいのはなぜですか?
Server Assessment でのディスクのサイズ設定は、サイズ設定基準とストレージの種類という 2 つの評価プロパティに依存します。 サイズ設定基準が**パフォーマンスベース**で、ストレージの種類が**自動**である場合、ディスクの IOPS とスループットの値を考慮して、ターゲット ディスクの種類 (Standard HDD、Standard SSD、または Premium ディスク) が特定されます。 その後、そのディスクの種類からディスク SKU が推奨され、この推奨ではオンプレミスのディスクのサイズ要件が考慮されます。 サイズ設定基準が**パフォーマンスベース**で、ストレージの種類が **Premium** である場合、オンプレミスのディスクの IOPS、スループット、サイズの要件に基づいて、Azure の Premium ディスク SKU が推奨されます。 サイズ設定基準が**オンプレミス**で、ストレージの種類が **Standard HDD**、**Standard SSD**、または **Premium** である場合は、同じロジックを使ってディスクのサイズ設定が行われます。

たとえば、オンプレミスのディスクと 32 GB のメモリを使っていても、ディスクの読み取りと書き込みの集計 IOPS が 800 IOPS である場合、Server Assessment では Premium ディスクの種類 (高い IOPS 要件であるため) が推奨された後、必要な IOPS とサイズに対応できるディスク SKU が推奨されます。 この例では、適合する最も近いものは P15 (256 GB、1100 IOPS) になります。 オンプレミスのディスクで必要なサイズは 32 GB でしたが、オンプレミスのディスクの高い IOPS 要件により、Server Assessment ではさらに大きいディスクが推奨されます。

### <a name="why-does-my-assessment-report-indicate-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>評価レポートで一部の VM に対して "PercentageOfCoresUtilizedMissing" または "PercentageOfMemoryUtilizedMissing" と示されるのはなぜですか?
これらの問題は、Azure Migrate アプライアンスでオンプレミス VM のパフォーマンス データを収集できない場合に報告されます。 この動作は、評価の作成対象期間 (過去 1 日/1 週間/1 か月) に VM の電源がオフになっていた場合に、発生する可能性があります。 アプライアンスでは、オフになっていた VM のパフォーマンス データを収集することはできません。 メモリ カウンターのみが取得されず、Hyper-V VM を評価しようとしている場合は、これらの VM で動的メモリが有効になっているかどうかを確認してください。 既知の問題のため、動的メモリが有効になっていない VM のメモリ使用率データは、Azure Migrate アプライアンスで収集できません。 この問題は、Hyper-V VM にのみ影響し、VMware VM には影響しません。 いずれかのパフォーマンス カウンターが不足している場合、Azure Migrate Server Assessment は割り当てられたコアとメモリにフォールバックし、対応する VM サイズが推奨されます。

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>Server Assessment で見積もられたコンピューティング コストには、VM の OS ライセンス コストは含まれますか?
現在、Server Assessment では、Windows マシンの OS ライセンス コストのみが考慮されます。 Linux マシンの OS ライセンス コストは現在は考慮されません。

### <a name="what-impact-do-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>パフォーマンス履歴と百分位数での使用率は、サイズの推奨にどのような影響を与えますか?
これらのプロパティは、パフォーマンスベースのサイズ設定にのみ適用されます。 Server Assessment では、オンプレミスのマシンのパフォーマンス データを継続的に収集し、それを使って Azure で VM の SKU とディスクの SKU を推奨します。 このパフォーマンス データは、Server Assessment によって次のように収集されます。
- Azure Migrate アプライアンスでは、オンプレミスの環境が継続的にプロファイリングされて、VMware VM の場合は 20 秒ごと、Hyper-V VM の場合は 30 秒ごとに、使用状況データがリアルタイムで収集されます。
- このアプライアンスでは、20 秒および 30 秒でサンプルがロールアップされて、10 分ごとに 1 つのデータ ポイントが作成されます。 1 つのデータ ポイントを作成するために、アプライアンスでは 20 秒および 30 秒のすべてのサンプルからピーク値が選択されて、Azure に送信されます。
- Server Assessment で評価を作成するときは、パフォーマンス期間とパフォーマンス履歴のパーセンタイル値に基づいて、代表的な使用率の値が特定されます。 たとえば、パフォーマンス履歴が 1 週間で、パーセンタイル使用率が 95 番目の場合、Azure Migrate では過去 1 週間のすべての 10 分サンプル ポイントが昇順で並べ替えられて、代表的な値として 95 番目のパーセンタイルが選択されます。
95 番目のパーセンタイル値を設定することにより、99 番目のパーセンタイルを選択した場合に含まれる可能性がある外れ値はすべて無視されるようになります。 期間中のピーク使用率を選択し、外れ値を見逃さないようにする場合は、パーセンタイル使用率として 99 番目のパーセンタイルを選択する必要があります。

## <a name="dependency-visualization-issues"></a>依存関係の視覚化の問題

### <a name="i-cant-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Azure Government プロジェクトの依存関係の視覚化機能が見つかりません。

Azure Migrate の依存関係視覚化機能は Service Map に依存しています。 現在、Azure Government では Service Map を使用できないため、Azure Government ではこの機能を使用できません。

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>オンプレミスのVM へMicrosoft Monitoring Agent（MMA） と依存関係エージェントをインストールしましたが、依存関係が Azure Migrate ポータルに表示されています。

エージェントをインストールした後、Azure Migrate ポータルで依存関係が表示されるまで 15 分から 30 分かかります。 30 分以上待っている場合は、次の手順に従って MMA が OMS ワークスペースと通信できることを確認してください。

Windows VM：
1. [コントロール パネル] に移動して Microsoft Monitoring Agent を起動します。
2. **[Microsoft Monitoring Agent のプロパティ]** ダイアログ ボックスの **[Azure Log Analytics (OMS)]** タブで、ワークスペースの **[状態]** が緑色になっていることを確認します。
3. ステータスが緑色でない場合、ワークスペースを削除して、再度MMAへ追加してみてください。

      ![MMA のプロパティ ダイアログ ボックス](./media/troubleshooting-general/mma-status.png)

Linux VM の場合、MMA と依存関係エージェントのインストールに関するすべてのコマンドが正常に実行されたことを確認します。

### <a name="what-operating-systems-does-mma-support"></a>MMA ではどのようなオペレーティング システムがサポートされていますか?

 [MMA でサポートされる Windows オペレーティング システムの一覧はこちらです](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)。
また、[MMA でサポートされる Linux オペレーティング システムの一覧はこちらです](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)。

### <a name="what-operating-systems-does-the-dependency-agent-support"></a>依存関係エージェントではどのようなオペレーティング システムがサポートされていますか?

[依存関係エージェントでサポートされる Windows オペレーティング システムの一覧はこちらです](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)。 また、[依存関係エージェントでサポートされる Linux オペレーティング システムの一覧はこちらです](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)。

### <a name="i-cant-visualize-dependencies-in-azure-migrate-for-more-than-a-one-hour-duration"></a>Azure Migrate で 1 時間を超えて依存関係を視覚化することができません。
Azure Migrate では、依存関係を視覚化できる期間は、最大 1 時間です。 Azure Migrate を使用すると、過去 1 か月間の特定の日付に戻ることができますが、依存関係を視覚化できる期間は最大 1 時間です。

たとえば、依存関係マップにある期間機能を使用して、昨日の依存関係を表示することが可能ですが、1 時間の期間のみ表示できます。 ただし、Azure Monitor ログを使用すれば、長期間にわたって[依存関係データのクエリ](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)を実行できます。

### <a name="i-cant-visualize-dependencies-for-groups-that-have-more-than-10-vms"></a>10 個より多くの VM を含むグループの依存関係を視覚化できません。
[グループの依存関係を視覚化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)できるのは、VM が 10 個以下の場合です。グループに含まれる VM が 10 個 を超える場合は、そのグループを小さなグループに分割して、依存関係を視覚化することをお勧めします。

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>エージェントをインストールし、依存関係の視覚エフェクトを使用してグループを作成しました。 現在、フェールオーバー後にマシンで [依存関係の表示] ではなく [エージェントのインストール] アクションが表示されます
* 計画的なフェールオーバー、または計画外のフェールオーバーの後、オンプレミスのマシンはオフになり、同等のマシンが Azure にスピン アップされます。 これらのマシンは、別々の MAC アドレスを取得します。 ユーザーがオンプレミスの IP アドレスを保持するか保持しないかによって、異なる IP アドレスを取得する場合があります。

  MAC と IP アドレスの両方が異なる場合、Azure Migrate ではオンプレミスのマシンと Service Map 依存関係データの関連付けは行われません。 依存関係が表示される代わりに、ユーザーに対してエージェントのインストールが要求されます。
* テスト フェールオーバー後、オンプレミスのマシンでは想定どおりにオンの状態が維持されます。 Azure にスピン アップされる同等のマシンでは、異なる MAC アドレスが取得され、異なる IP アドレスが取得されることがあります。 これらのマシンから送信される Azure Monitor ログ トラフィックをユーザーがブロックしない限り、Azure Migrate ではオンプレミスのマシンと Service Map 依存関係データの関連付けは行われず、依存関係を表示する代わりにエージェントをインストールするようユーザーに要求されます。

## <a name="collect-azure-portal-logs"></a>Azure portal のログを収集する

**Azure portal のネットワーク トラフィック ログを収集するにはどうすればよいですか?**

1. ブラウザーを開き、[ポータル](https://portal.azure.com)に移動してサインインします。
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

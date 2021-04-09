---
title: MABS を使用して SharePoint ファームを Azure にバックアップする
description: Azure Backup Server を使用して SharePoint データをバックアップおよび復元します。 この記事では、目的のデータを Azure に保存できるように SharePoint ファームを構成するための情報を提供します。 ディスクまたは Azure から保護対象の SharePoint データを復元できます。
ms.topic: conceptual
ms.date: 04/26/2020
ms.openlocfilehash: 837aabf739431eebaa6406770620329fe6345eb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89375399"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-mabs"></a>MABS を使用して SharePoint ファームを Azure にバックアップする

Microsoft Azure Backup Server (MABS) を使用して SharePoint ファームを Microsoft Azure にバックアップする方法は、他のデータ ソースのバックアップとよく似ています。 Azure Backup ではバックアップのスケジュールを柔軟に設定して日、週、月、年の単位でバックアップ ポイントを作成でき、さまざまなバックアップ ポイントに対応する保有ポリシー オプションがあります。 MABS では、目標復旧時間 (RTO) 短縮のためにはローカル ディスク コピーを保存でき、コスト効率に優れた長期リテンション期間のためには Azure にコピーできます。

MABS を使用して SharePoint を Azure にバックアップするプロセスは、SharePoint をローカルで DPM (Data Protection Manager) にバックアップする場合と似ています。 この記事では、Azure に関する特定の考慮事項について説明します。

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint のサポートされるバージョンと関連する保護シナリオ

サポートされている SharePoint のバージョンと、それらのバックアップに必要な MABS のバージョンの一覧については、[MABS 保護マトリックス](./backup-mabs-protection-matrix.md)に関するページを参照してください

## <a name="before-you-start"></a>開始する前に

SharePoint ファームを Azure にバックアップする前に、確認する必要がある点がいくつかあります。

### <a name="whats-not-supported"></a>サポートされていないもの

* SharePoint ファームを保護する MABS では、検索インデックスまたはアプリケーション サービス データベースは保護されません。 これらのデータベースの保護は別に構成する必要があります。

* MABS では、スケールアウト ファイル サーバー (SOFS) 共有でホストされている SharePoint SQL Server データベースのバックアップはサポートしていません。

### <a name="prerequisites"></a>前提条件

先に進む前に、Microsoft Azure Backup を使用してワークロードを保護する上で必要なすべての [前提条件](backup-azure-dpm-introduction.md#prerequisites-and-limitations) が満たされていることを確認します。 前提条件を満たすための作業として、バックアップ コンテナーの作成、コンテナー資格情報のダウンロード、Azure Backup エージェントのインストール、コンテナーへの Azure Backup Server の登録などがあります。

追加の前提条件と制限事項:

* 既定では、SharePoint を保護すると、すべてのコンテンツ データベース (および SharePoint_Config と SharePoint_AdminContent* データベース) が保護されます。 検索インデックス、テンプレートまたはアプリケーション サービス データベース、ユーザー プロファイル サービスなどのカスタマイズを追加する場合は、これらを個別に保護するように構成する必要があります。 この種の機能やカスタマイズ ファイルを含むすべてのフォルダーに対して保護が有効になっていることを確認してください。

* SharePoint データベースを SQL Server データ ソースとして保護することはできません。 ファームのバックアップから個々のデータベースを回復することができます。

* MABS が **ローカル システム** として実行されている状態で、SQL Server データベースをバックアップする場合は、SQL Server のアカウントに対する sysadmin 特権が必要になることに注意してください。 バックアップする SQL Server NT AUTHORITY\SYSTEM を **sysadmin** に設定してください。

* MABS フォルダーが存在するボリュームには、ファーム内の 1,000 万項目ごとに 2 GB 以上の容量が必要です。 この容量はカタログ生成のために必要です。 MABS を使用して特定の項目 (サイト コレクション、サイト、リスト、ドキュメント ライブラリ、フォルダー、個々のドキュメント、およびリスト項目) の回復を実行できるようにするために、カタログ生成では各コンテンツ データベース内に含まれる URL のリストが作成されます。 MABS 管理者コンソールの回復タスク領域の [回復可能な項目] ウィンドウで、URL の一覧を確認できます。

* SharePoint ファームで、SQL Server 別名を使用して構成されている SQL Server データベースがある場合は、MABS で保護するフロントエンド Web サーバーに SQL Server クライアント コンポーネントをインストールしてください。

* アプリケーション ストアの項目の保護は、SharePoint 2013 ではサポートされていません。

* MABS では、リモート FILESTREAM の保護はサポートしていません。 FILESTREAM はデータベースの一部でなければなりません。

## <a name="configure-backup"></a>バックアップの構成

SharePoint ファームをバックアップするには、ConfigureSharePoint.exe を使用して SharePoint の保護を構成し、MABS で保護グループを作成します。

1. **ConfigureSharePoint.exe** を実行します。このツールは、SharePoint VSS ライター サービス \(WSS\) を構成し、保護エージェントに SharePoint ファームの資格情報を提供します。 保護エージェントを展開すると、ConfigureSharePoint.exe ファイルがフロントエンド Web サーバー上の `<MABS Installation Path\>\bin` フォルダーに配置されます。  複数の WFE サーバーがある場合でも、インストールする必要があるのはそれらのサーバーのいずれかのみになります。 次のように実行します。

    * WFE サーバーで、コマンド プロンプトを使用して `\<MABS installation location\>\\bin\\` に移動し、`ConfigureSharePoint \[\-EnableSharePointProtection\] \[\-EnableSPSearchProtection\] \[\-ResolveAllSQLAliases\] \[\-SetTempPath <path>\]` を実行します。それぞれ以下の内容を表します。

        * **EnableSharePointProtection** では、SharePoint ファームの保護を有効にして、VSS ライターを有効にし、DCOM アプリケーション WssCmdletsWrapper の ID を登録して、このオプションで資格情報を入力するユーザーとして実行するようにします。 このアカウントは、ファーム管理者であり、フロントエンド Web サーバーのローカル管理者でもある必要があります。

        * **EnableSPSearchProtection** では、フロントエンド Web サーバーで HKLM\\Software\\Microsoft\\ Microsoft Data Protection Manager\\Agent\\2.0\\ にあるレジストリ キー SharePointSearchEnumerationEnabled を使用して、WSS 3.0 SP Search の保護を有効にし、DCOM アプリケーション WssCmdletsWrapper の ID を登録して、このオプションで資格情報を入力するユーザーとして実行するようにします。 このアカウントは、ファーム管理者であり、フロントエンド Web サーバーのローカル管理者でもある必要があります。

        * **ResolveAllSQLAliases** は、SharePoint VSS ライターが報告するエイリアスをすべて表示し、対応する SQL Server に対してそれらを解決します。 また、解決されたインスタンス名も表示します。 サーバーがミラー化されている場合は、そのサーバーも表示します。 SQL Server に対して解決されていないすべてのエイリアスを報告します。

        * **SetTempPath** は、指定されたパスに環境変数 TEMP と TMP を設定します。 サイズの大きなサイト コレクション、サイト、リスト、または項目が回復中であり、ファーム管理者の一時フォルダーで空き容量が不足している場合、項目レベルの回復が失敗します。 このオプションにより、一時ファイルのフォルダー パスを、回復しているサイト コレクションまたはサイトを保存するために十分なボリュームへと変更できます。

    * ファーム管理者の資格情報を入力します。 このアカウントは、WFE サーバーのローカル管理者グループのメンバーである必要があります。 ファーム管理者がローカル管理者ではない場合は、WFE サーバーで次の権限を付与します。

        * MABS フォルダー (`%Program Files%\Data Protection Manager\DPM\`) に対するフル コントロールを **WSS_Admin_WPG** グループに付与します。

        * MABS レジストリ キー (`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager`) に対する読み取りアクセスを **WSS_Admin_WPG** グループに付与します。

        ConfigureSharePoint.exe を実行した後で、SharePoint ファーム管理者の資格情報に変更がある場合は、これを再実行する必要があります。

1. 保護グループを作成するには、MABS コンソールで **[保護]**  >  **[アクション]**  >  **[保護グループの作成]** の順に選択して **[新しい保護グループの作成]** ウィザードを開きます。

1. **[保護グループの種類の選択]** で **[サーバー]** を選択します。

1. **[グループ メンバーの選択]** で、WFE ロールを保持しているサーバーを展開します。 複数の WFE サーバーがある場合は、ConfigureSharePoint.exe をインストールしたサーバーを選択します。

    SharePoint サーバーを展開すると、MABS は VSS を照会して MABS で保護できるデータを確認します。  SharePoint データベースがリモートである場合、MABS はそれに接続します。 SharePoint データ ソースが表示されない場合は、VSS ライターが SharePoint サーバーといずれかのリモート SQL Server で実行されていることを確認し、SharePoint サーバーとリモート SQL Server の両方で MABS エージェントがインストールされていることを確認します。 さらに、SharePoint データベースが SQL Server データベースとして他の場所で保護されていないことを確認してください。

1. **[データの保護方法の選択]** で、短期と長期のバックアップの処理方法を指定します。 短期のバックアップでは、常にまずディスクにバックアップされますが、Azure Backup を使用してディスクから Azure クラウドにバックアップするオプションがあります \(短期または 長期\)。

1. **[短期的な目標の選択]** で、ディスク上の短期記憶域へのバックアップ方法を指定します。   **[保有期間の範囲]** で、ディスクにデータを保持する期間を指定します。 **[同期の頻度]** で、ディスクへの増分バックアップを実行する頻度を指定します。 バックアップ間隔を設定しない場合は、[回復ポイントの直前] を有効にし、各回復ポイントがスケジュールされる直前に MABS が高速完全バックアップを実行するように指定できます。

1. [ディスク割り当ての確認] ページで、保護グループに割り当てられる記憶域プールのディスク領域を確認します。

    **[合計データ サイズ]** は、バックアップするデータのサイズです。 **[Disk space to be provisioned on MABS]\(MABS にプロビジョニングするディスク領域\)** は、MABS が保護グループに推奨する領域です。 MABS では、設定に基づいて理想的なバックアップ ボリュームが選択されます。 ただし、 **[Disk allocation details]\(ディスク割り当ての詳細\)** でバックアップ ボリュームの選択を編集できます。 ワークロードの場合、ドロップダウン メニューで、優先ストレージを選択します。 編集すると、 **[利用できるディスク ストレージ]** ウィンドウの **[ストレージの合計]** と **[空きストレージ]** の値が変わります。 過小にプロビジョニングされた領域とは、今後もスムーズなバックアップを確実に行うためにボリュームに追加することを MABS から提案されるストレージの量です。

1. **[レプリカの作成方法の選択]** で、最初の全データのレプリケーションを処理する方法を選択します。  ネットワーク経由でのレプリケーションを選択する場合は、ピーク時以外の時間帯を選択することをお勧めします。 データが大量にある場合や、ネットワークの状態が最適でない場合は、リムーバブル メディアを使用してオフラインでデータをレプリケートすることを検討してください。

1. **[整合性チェック オプションの選択]** で、整合性チェックを自動化する方法を選択します。 レプリカ データに不整合が生じた場合にのみ、またはスケジュールに従ってチェックを実行することができます。 自動整合性チェックを構成しない場合は、MABS コンソールの **[保護]** 領域で保護グループを右クリックし、 **[整合性チェックの実行]** を選択すると、いつでも手動のチェックを実行できます。

1. Azure Backup でクラウドにバックアップすることを選択した場合は、 **[オンライン保護するデータの指定]** ページで、Azure にバックアップするワークロードが選択されていることを確認します。

1. **[オンライン バックアップ スケジュールの指定]** で、Azure への増分バックアップを行う頻度を指定します。 毎日、毎週、毎月、毎年というタイミングでバックアップをスケジュールできます。また、実行する日時を選択できます。 バックアップは、最大 1 日に 2 回実行できます。 バックアップが実行されるたびに、Azure で MABS ディスクに保存されているバックアップ データのコピーからデータの回復ポイントが作成されます。

1. **[オンライン保持ポリシーの指定]** では、毎日、毎週、毎月、毎年のバックアップから作成される回復ポイントを Azure に保持する方法を指定できます。

1. **[オンライン レプリケーションの選択]** で、最初の全データのレプリケーションを実行する方法を指定します。 ネットワーク経由でのレプリケーションまたはオフライン バックアップ (オフライン シード処理) を実行できます。 オフライン バックアップは、Azure Import 機能を使用します。 詳細については、[こちら](./backup-azure-backup-import-export.md)を参照してください。

1. **[概要]** ページで、設定を確認します。 **[グループの作成]** を選択した後、データの初期レプリケーションが実行されます。 終了すると、 **[状態]** ページに保護グループの状態が **[OK]** と表示されます。 保護グループの設定に沿ってバックアップが実行されます。

## <a name="monitoring"></a>監視

保護グループが作成されると、初期レプリケーションが行われ、MABS は SharePoint データのバックアップと同期を開始します。 MABS は最初の同期とそれ以降のバックアップを監視します。  SharePoint データは、次のいくつかの方法で監視できます。

* アラートを発行し通知を構成することで、既定の MABS の監視を使用して、プロアクティブな監視向けの通知を設定できます。 インスタンス化された回復の重大、警告、情報の各アラートおよび状態の通知を電子メールで送信できます。

* Operations Manager を使用すると、アラートを一元的に発行できます。

### <a name="set-up-monitoring-notifications"></a>監視通知の設定

1. MABS 管理コンソールで、 **[監視]**  >  **[アクション]**  >  **[オプション]** の順に選択します。

2. **[SMTP サーバー]** を選択し、通知の送信元のサーバー名、ポート、メール アドレスを入力します。 アドレスは有効である必要があります。

3. **[認証済み SMTP サーバー]** で、ユーザー名とパスワードを入力します。 ユーザー名とパスワードは、前の手順で "差出人" アドレスが入力されたユーザーのドメイン アカウント名にする必要があります。 そうでない場合、通知は配信できません。

4. SMTP サーバーの設定をテストするには、 **[テスト電子メールの送信]** を選択し、MABS がテスト メッセージを送信する先の電子メール アドレスを入力して **[OK]** を選択します。 **[オプション]**  >  **[通知]** の順に選択し、受信者に通知するアラートの種類を選択します。 **[受信者]** に、MABS から通知のコピーを送信する各宛先の電子メール アドレスを入力します。

### <a name="publish-operations-manager-alerts"></a>Operations Manager アラートの発行

1. MABS 管理コンソールで、 **[監視]**  >  **[アクション]**  >  **[オプション]**  >  **[アラートの発行]**  >  **[アクティブなアラートの発行]** の順に選択します。

2. **[アラートの発行]** を有効にすると、ユーザーの操作が必要となる既存のすべての MABS アラートは、**MABS アラート** イベント ログに発行されます。 その後、MABS サーバーにインストールされている Operations Manager エージェントがこれらのアラートを Operations Manager に発行し、新しいアラートが生成されるたびにコンソールを更新し続けます。

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>MABS を使用したディスクからの SharePoint アイテムの復元

次の例では、 *Recovering SharePoint item* が誤って削除され、回復する必要があります。
![MABS の SharePoint 保護 4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. **MABS 管理者コンソール** を開きます。 MABS によって保護されているすべての SharePoint ファームが、 **[保護]** タブに表示されます。

    ![MABS の SharePoint 保護 3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. アイテムの回復を始めるには、 **[回復]** タブを選択します。

    ![MABS の SharePoint 保護 5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. 回復ポイントの範囲内でワイルドカード ベースの検索を使用して、SharePoint で *Recovering SharePoint item* を検索できます。

    ![MABS の SharePoint 保護 6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. 検索結果から適切な回復ポイントを選択し、アイテムを右クリックして、 **[回復]** を選択します。
5. さまざまな回復ポイントを参照して、回復するデータベースまたはアイテムを選択できます。 **日付、回復時刻** の順に選択した後、正しい **データベース、SharePoint ファーム、回復ポイント、アイテム** を順番に選択します。

    ![MABS の SharePoint 保護 7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. アイテムを右クリックして **[回復]** を選択し、**回復ウィザード** を開きます。 **[次へ]** を選択します。

    ![回復の選択の確認](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. 実行する回復の種類を選択し、 **[次へ]** を選択します。

    ![回復の種類](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > 上図のように **[元のサイトに回復する]** を選択すると、アイテムは元の SharePoint サイトに回復します。
   >
   >
8. 使用する **回復プロセス** を選択します。

   * SharePoint ファームが変更されておらず、復元される回復ポイントと同じである場合は、 **[回復ファームを使用しないで回復する]** を選択します。
   * 回復ポイントの作成後に SharePoint ファームが変更された場合は、 **[回復ファームを使用して回復する]** を選択します。

     ![回復プロセス](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. 一時的にデータベースを回復するためのステージング SQL Server インスタンスの場所を指定し、MABS 上と SharePoint を実行するサーバー上でアイテムを回復するためのステージング ファイル共有を指定します。

    ![ステージングの場所 1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS は、SharePoint アイテムをホストしているコンテンツ データベースを一時的な SQL Server インスタンスにアタッチします。 MABS は、コンテンツ データベースからアイテムを回復し、MABS 上のステージング ファイルの場所に格納します。 次に、ステージングの場所に回復されたアイテムを、SharePoint ファーム上のステージング場所にエクスポートする必要があります。

    ![ステージングの場所 2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. **[回復オプションの指定]** を選択し、SharePoint ファームに対するセキュリティ設定を適用するか、または回復ポイントのセキュリティ設定を適用します。 **[次へ]** を選択します。

    ![回復オプション](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > ネットワーク帯域幅の使用量を調整できます。 これにより、業務時間中の運用サーバーへの影響を最小限にします。
    >
    >
11. 概要情報を確認し、 **[回復]** を選択してファイルの回復を開始します。

    ![回復の概要](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. **MABS 管理者コンソール** の **[監視]** タブを選択して、回復の **状態** を確認できます。

    ![回復の状態](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > ファイルが復元されます。 SharePoint サイトを更新して、復元されたファイルを確認すできます。
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-mabs"></a>MABS を使用した Azure からの SharePoint データベースの復元

1. SharePoint コンテンツ データベースを回復するには、さまざまな回復ポイントを参照して (前述したように)、復元する回復ポイントを選択します。

    ![MABS の SharePoint 保護 8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. SharePoint の回復ポイントをダブルクリックして、使用可能な SharePoint カタログ情報を表示します。

   > [!NOTE]
   > SharePoint ファームは Azure では長期保有期間用に保護されているので、MABS サーバーには使用可能なカタログ情報 (メタデータ) がありません。 その結果、特定時点の SharePoint コンテンツ データベースを回復する必要があるときは常に、SharePoint ファームを再カタログ化する必要があります。
   >
   >
3. **[再カタログ化]** を選択します。

    ![MABS の SharePoint 保護 10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    **[クラウドの再カタログ化]** ステータス ウィンドウが開きます。

    ![MABS の SharePoint 保護 11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    カタログ化が完了すると、ステータスが *[成功]* に変わります。 **[閉じる]** を選択します。

    ![MABS の SharePoint 保護 12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. MABS の **[回復]** タブに表示される SharePoint オブジェクトを選択して、コンテンツ データベースの構造を取得します。 アイテムを右クリックし、 **[回復]** を選択します。

    ![MABS の SharePoint 保護 13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. この時点で、この記事で前述した回復手順に従ってディスクから SharePoint コンテンツ データベースを回復します。

## <a name="switching-the-front-end-web-server"></a>フロントエンド Web サーバーの切り替え

複数のフロントエンド Web サーバーがあり、MABS でファームの保護に使用するサーバーを切り替えたい場合は、次の手順に従ってください。

次の手順では、2 台のフロントエンド Web サーバー (*Server1* と *Server2*) によるサーバー ファームの例を使用します。 MABS は *Server1* を使用してファームを保護します。 ファームから *Server1* を削除できるように、MABS が使用するフロントエンド Web サーバーを *Server2* に変更する必要があります。

> [!NOTE]
> ファームの保護のために MABS が使用するフロントエンド Web サーバーが使用できない場合、次の手順の 4 から開始してフロントエンド Web サーバーを変更してください。

### <a name="to-change-the-front-end-web-server-that-mabs-uses-to-protect-the-farm"></a>ファームの保護のために MABS が使用するフロント エンド Web サーバーを変更するには

1. コマンド プロンプトで次のコマンドを実行して、*Server1* の SharePoint VSS ライター サービスを停止します。

    ```CMD
    stsadm -o unregisterwsswriter
    ```

1. *Server1* でレジストリ エディターを開き、次のキーに移動します。

   **HKLM\System\CCS\Services\VSS\VssAccessControl**

1. VssAccessControl サブキーに表示されるすべての値を確認します。 いずれかのエントリに 0 の値データが含まれ、関連するアカウントの資格情報で別の VSS ライターが実行されている場合は、値データを 1 に変更してください。

1. *Server2* に保護エージェントをインストールします。

   > [!WARNING]
   > 両方のサーバーが同じドメインにある場合にのみ、Web フロントエンド サーバーを切り替えることができます。

1. *Server2* のコマンド プロンプトで、ディレクトリを `_MABS installation location_\bin\` に変更し、**ConfigureSharepoint** を実行します。 ConfigureSharePoint の詳細については、「[バックアップの構成](#configure-backup)」を参照してください。

1. サーバー ファームが属している保護グループを選択し、 **[保護グループの変更]** を選択します。

1. グループの変更ウィザードの **[グループ メンバーの選択]** ページで *Server2* を展開し、サーバー ファームを選択して、ウィザードを完了します。

   整合性チェックが開始します。

1. 手順 6 を実行した場合は、保護グループからボリュームを削除できるようになります。

## <a name="next-steps"></a>次のステップ

[Exchange サーバーのバックアップ](backup-azure-exchange-mabs.md)について確認してください。
[SQL Server のバックアップ](backup-azure-sql-mabs.md)について確認してください。

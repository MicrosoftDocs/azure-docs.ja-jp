---
title: Azure Backup Server を使用して Azure VMware Solution の VM をバックアップする
description: Azure Backup Server を使用して仮想マシンをバックアップするように Azure VMware Solution 環境を構成します。
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 163065556b7dbc979d68613744ea827c209d9fda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618918"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>Azure Backup Server を使用して Azure VMware Solution の VM をバックアップする

この記事では、Azure Backup Server を使用して、Azure VMware Solution で実行されている VMware 仮想マシン (VM) をバックアップします。 まず、「[Azure VMware Solution 向けに Azure Backup Server を設定する](set-up-backup-server-for-azure-vmware-solution.md)」をよくお読みください。

その後、次の操作を実行するために必要なすべての手順について説明します。

> [!div class="checklist"] 
> * Azure Backup Server が HTTPS 経由で VMware サーバーと通信できるように、セキュリティで保護されたチャネルを設定します。 
> * Azure Backup Server にアカウントの資格情報を追加します。 
> * Azure Backup Server に vCenter を追加します。 
> * バックアップする VMware VM が含まれている保護グループを設定し、バックアップの設定を指定して、バックアップをスケジュールします。

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>vCenter Server へのセキュリティで保護された接続を作成する

既定では、Azure Backup Server は HTTPS 経由で VMware サーバーと通信します。 HTTPS 接続を設定するには、VMware 証明機関 (CA) 証明書をダウンロードして、Azure Backup Server にインポートします。

### <a name="set-up-the-certificate"></a>証明書を設定する

1. ブラウザーで Azure Backup Server マシンに移動し、vSphere Web Client の URL を入力します。

   > [!NOTE] 
   > VMware の **[Getting Started]\(使用開始\)** ページが表示されない場合は、接続とブラウザーのプロキシ設定を確認してから、もう一度試します。

1. VMware の **[Getting Started]\(使用開始\)** ページで、 **[Download trusted root CA certificates]\(信頼されたルート CA 証明書をダウンロードする\)** を選択します。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="vSphere Web Client":::

1. **download.zip** ファイルを Azure Backup Server マシンに保存し、その内容を **certs** フォルダーに抽出します。次のものが含まれています。

   - .0 や .1 などの連番で始まる拡張子が付いたルート証明書ファイル。
   - .r0 や .r1 などの連番で始まる拡張子が付いた CRL ファイル。

1. **certs** フォルダーで、ルート証明書ファイルを右クリックし、 **[名前の変更]** を選択して、拡張子を「 **.crt**」に変更します。

   ファイルのアイコンが、ルート証明書を表すものに変わります。

1. ルート証明書を右クリックし、 **[証明書のインストール]** を選択します。

1. **証明書のインポート ウィザード** で、証明書のインポート先として **[ローカル コンピューター]** を選択し、 **[次へ]** を選択します。

   ![ウィザードの [ようこそ] ページ](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > 求められたら、コンピューターへの変更を許可することを確認します。

1. **[証明書をすべて次のストアに配置する]** を選択し、 **[参照]** を選択して証明書ストアを選択します。

   ![証明書ストレージ](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. インポート先フォルダーとして **[信頼されたルート証明機関]** を選択し、 **[OK]** を選択します。

1. 設定を確認し、 **[完了]** を選択して証明書のインポートを開始します。

   ![証明書が適切なフォルダーにあることを確認する](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. 証明書のインポートが完了した後、vCenter Server にサインインし、接続がセキュリティ保護されていることを確認します。

### <a name="enable-tls-12-on-azure-backup-server"></a>Azure Backup Server で TLS 1.2 を有効にする

VMware 6.7 以降では、TLS が通信プロトコルとして有効になっています。 

1. 次のレジストリ設定をコピーし、メモ帳に貼り付けます。 その後、.txt 拡張子を除いた TLS.REG としてファイルを保存します。

   ```
   
   Windows Registry Editor Version 5.00
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   ```

1. TLS.REG ファイルを右クリックし、 **[統合する]** または **[開く]** を選択して、レジストリに設定を追加します。


## <a name="add-the-account-on-azure-backup-server"></a>Azure Backup Server でアカウントを追加する

1. Azure Backup Server を開き、Azure Backup Server コンソールで **[管理]**  >  **[運用サーバー]**  >  **[VMware の管理]** を選択します。

   ![Azure Backup Server コンソール](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. **[資格情報の管理]** ダイアログ ボックスで、 **[追加]** を選択します。

   ![[資格情報の管理] ダイアログ ボックスで、 [追加] を選択します。](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. **[資格情報の追加]** ダイアログ ボックスで、新しい資格情報の名前と説明を入力します。 VMware サーバーで定義したユーザー名とパスワードを指定します。

   > [!NOTE] 
   > VMware サーバーと Azure Backup Server が同じドメイン内にない場合は、 **[ユーザー名]** ボックスでドメインを指定します。

   ![Azure Backup Server の [資格情報の追加] ダイアログ ボックス](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. **[追加]** を選択して、新しい資格情報を追加します。

   ![Azure Backup Server の [資格情報の管理] ダイアログ ボックスのスクリーンショット。新しい資格情報が表示されています。](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Azure Backup Server に vCenter Server を追加する

1. Azure Backup Server コンソールで、 **[管理]**  >  **[運用サーバー]**  >  **[追加]** を選択します。

   ![運用サーバーの追加ウィザードを開く](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. **[VMware サーバー]** を選択し、 **[次へ]** を選択します。

   ![運用サーバーの追加ウィザード](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. vCenter の IP アドレスを指定します。

   ![VMware サーバーを指定する](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. **[SSL ポート]** ボックスに、vCenter との通信に使用するポートを入力します。

   > [!TIP] 
   > ポート 443 が既定のポートですが、vCenter で別のポートがリッスンされている場合は変更できます。

1. **[資格情報の指定]** ボックスで、前のセクションで作成した資格情報を選択します。

1. **[追加]** を選択してサーバーの一覧に vCenter を追加し、 **[次へ]** を選択します。

   ![VMware サーバーと資格情報を追加する](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. **[概要]** ページで、 **[追加]** を選択して vCenter を Azure Backup Server に追加します。

   新しいサーバーがすぐに追加されます。 vCenter にはエージェントは必要ありません。

   ![Azure Backup Server への VMware サーバーの追加](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. **[完了]** ページで設定を確認して、 **[閉じる]** を選択します。

   ![[完了] ページ](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   **[運用サーバー]** の下に次のような vCenter サーバーが表示されます。
   - [種類] が **[VMware サーバー]** 
   - [エージェントの状態] が **[OK]** 
   
      **[エージェント ステータス]** が **[不明]** と表示される場合は、 **[更新]** を選択します。

## <a name="configure-a-protection-group"></a>保護グループの構成

保護グループでは、複数の VM が収集されて、グループ内のすべての VM に、同じデータ保持とバックアップの設定が適用されます。

1. Azure Backup Server コンソールで、 **[保護]**  >  **[新規]** を選択します。

   ![新しい保護グループの作成ウィザードを開く](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. **新しい保護グループの作成** ウィザードのようこそページで、 **[次へ]** を選択します。

   ![[新しい保護グループの作成ウィザード] ダイアログ ボックス](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. **[保護グループの種類の選択]** ページで、 **[サーバー]** を選択し、 **[次へ]** を選択します。 **[グループ メンバーの選択]** ページが表示されます。

1. **[グループ メンバーの選択]** ページで、バックアップする VM (または VM フォルダー) を選択し、 **[次へ]** を選択します。

   > [!NOTE]
   > フォルダーを選択すると、そのフォルダー内の VM またはフォルダーもバックアップ対象に選択されます。 バックアップしたくないフォルダーや VM はオフにすることができます。 VM またはフォルダーが既にバックアップされている場合、それを選択することはできません。これにより、VM に対して重複する回復ポイントが作成されなくなります。

   ![グループ メンバーの選択](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. **[データの保護方法の選択]** ページで、保護グループの名前と保護の設定を入力します。 

1. 短期的な保護を **[ディスク]** に設定し、オンライン保護を有効にして、 **[次へ]** を選択します。

   ![データ保護方法の選択](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. ディスクにバックアップされたデータを保持する期間を指定します。

   - **[保持期間]** : ディスク回復ポイントを保持する日数。
   - **[高速完全バックアップ]** : ディスク回復ポイントを取得する頻度。 短期バックアップを実行する時刻または日付を変更するには、 **[変更]** を選択します。

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="ディスク ベースの保護の短期的な目標を指定する":::

1. **[ディスク記憶域の割り当ての確認]** ページで、VM バックアップ用に提供されているディスク領域を確認します

   - 推奨されるディスク割り当ては、指定した保有期間の範囲、ワークロードの種類、および保護データのサイズに基づきます。 必要な変更を行い、 **[次へ]** を選択します。
   - **データ サイズ:** 保護グループ内のデータのサイズです。
   - **ディスク領域:** 保護グループに対して推奨されるディスク領域の量。 この設定を変更する場合は、各データ ソースの増加の見積もり量よりもやや大きい領域を選択します。
   - **ストレージ プールの詳細:** 記憶域プールの状態が表示されます。合計や残りのディスク サイズが含まれます。

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="記憶域プールに指定されているディスク領域を確認する":::

   > [!NOTE]
   > 一部のシナリオでは、報告されるデータ サイズが実際の VM サイズより大きくなります。 この問題は確認されており、現在調査中です。

1. **[レプリカの作成方法の選択]** ページで、初期バックアップの作成方法を指定し、 **[次へ]** を選択します。

   - 既定値は、 **[自動 (ネットワーク経由)]** と **[今すぐ]** です。 既定値を使用する場合は、ピーク時以外を指定します。 **[後で実行]** を選択した場合は、日時を指定します。
   - データが大量にある場合や、ネットワークの状態が最適でない場合は、リムーバブル メディアを使用してオフラインでデータをレプリケートすることを検討してください。

   ![レプリカ作成方法の選択](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. **[整合性チェック オプション]** で、整合性チェックを自動化する方法とタイミングを選択し、 **[次へ]** を選択します。

   - 整合性チェックは、レプリカ データが不整合になったときに実行することや、設定したスケジュールで実行することができます。
   - 自動整合性チェックを構成しない場合は、保護グループを右クリックし、 **[整合性チェックの実行]** を選択することで、手動チェックを実行できます。

1. **[オンライン保護するデータの指定]** ページで、バックアップする VM または VM フォルダーを選択し、 **[次へ]** を選択します。 

   > [!TIP]
   > メンバーを個別に選択するか、 **[すべて選択]** を選択してすべてのメンバーを選択することができます。

   ![オンライン保護データの指定](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. **[オンライン バックアップ スケジュールの指定]** ページで、ローカル ストレージから Azure にデータをバックアップする頻度を指定します。 

   - スケジュールに従って、データのクラウド回復ポイントが生成されます。 
   - 回復ポイントが生成された後、Azure の Recovery Services コンテナーに転送されます。

   ![オンライン バックアップ スケジュールの指定](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. **[オンライン保持ポリシーの指定]** ページで、Azure へのバックアップから作成された回復ポイントを保持する期間を指定します。

   - Azure にデータを保持できる期間に制限はありません。
   - 唯一の制限は、保護されたインスタンスあたりの回復ポイントの数が 9,999 までであることです。 この例では、保護するインスタンスは VMware サーバーです。

   ![オンライン保持ポリシーの指定](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. **[概要]** ページで設定を確認して、 **[グループの作成]** を選択します。

   ![保護グループのメンバーと設定の概要](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>Azure Backup Server コンソールで監視する

Azure VMware Solution の VM をバックアップするように保護グループを構成した後は、Azure Backup Server コンソールを使用して、バックアップ ジョブとアラートの状態を監視できます。 監視できるものは次のとおりです。

- **[監視]** タスク領域:
   - **[アラート]** では、エラー、警告、および一般情報を監視できます。  アクティブおよび非アクティブなアラートを表示し、メール通知を設定できます。
   - **[ジョブ]** では、特定の保護されたデータ ソースまたは保護グループに対して、Azure Backup Server によって開始されたジョブを表示できます。 ジョブの進行状況を追跡したり、ジョブによって消費されるリソースを確認したりできます。
- **[保護]** タスク領域では、保護グループ内のボリュームと共有の状態を確認できます。 また、回復の設定、ディスクの割り当て、バックアップのスケジュールなどの構成設定を確認することもできます。
- **[管理]** タスク領域では、 **[ディスク]、[オンライン]** 、 **[エージェント]** の各タブを表示して、記憶域プール内のディスクの状態、Azure への登録、デプロイされた DPM エージェントの状態を確認できます。

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="Azure Backup Server でバックアップ ジョブの状態を監視する":::

## <a name="restore-vmware-virtual-machines"></a>VMware 仮想マシンの復元

Azure Backup Server 管理者コンソールでは、2 つの方法で回復可能なデータを見つけることができます。 検索または参照することができます。 データを回復するとき、データまたは VM を同じ場所に復元したい場合と、そうでない場合があります。 そのため、Azure Backup Server では、VMware VM のバックアップに関して 3 つの回復オプションがサポートされています。

- **元の場所への回復 (OLR)** : 保護された VM を元の場所に復元するには、OLR を使用します。 VM を元の場所に復元できるのは、バックアップが実行されてからディスクが追加も削除もされていない場合だけです。 ディスクが追加または削除されている場合は、"別の場所への回復" を使用する必要があります。
- **別の場所への回復 (ALR)** : 元の VM が見つからない場合、または元の VM に支障をきたさないようにする場合に使用します。 ESXi ホストの場所、リソース プール、フォルダー、およびストレージのデータストアとパスを指定します。 復元された VM と元の VM を区別するため、Azure Backup Server によって VM の名前の末尾に *"-Recovered"* が追加されます。
- **個々のファイルの場所への回復 (ILR)** : 保護された VM が Windows Server VM の場合、Azure Backup Server の ILR 機能を使用して、VM 内の個々のファイルまたはフォルダーを回復することができます。 個々のファイルを復旧する方法については、この記事の後半の手順を参照してください。 Windows VM とディスク回復ポイントの場合のみ、VM から個別ファイルを回復できます。

### <a name="restore-a-recovery-point"></a>復旧ポイントを復元する

1. Azure Backup Server 管理者コンソールで、 **[回復]** ビューを選択します。 

1. **[参照]** ウィンドウを使用して、復旧する VM を選択するか、フィルターで検索します。 VM またはフォルダーを選択すると、使用可能な回復ポイントが **[回復ポイント] ペインに表示されます。

   ![使用可能な復旧ポイント](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. **[回復ポイント]** ペインで、回復ポイントが取得された日付を選択します。 太字のカレンダー日付には、使用可能な復旧ポイントがあります。 または、VM を右クリックして **[すべての回復ポイントを表示]** を選択し、一覧から回復ポイントを選択します。

   > [!NOTE] 
   > 短期間の保護では、迅速な回復のためにディスクベースの回復ポイントを選択します。 短期的な回復ポイントの有効期限が切れると、回復する **オンライン** 回復ポイントのみが表示されます。

1. オンライン回復ポイントから回復する前に、回復する VM の圧縮されていない完全なサイズを格納するのに十分な空き領域がステージングの場所にあることを確認します。 ステージングの場所を表示または変更するには、**サブスクリプション設定の構成ウィザード** を実行します。

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="Azure Backup Server の回復フォルダーの設定":::

1. **[回復]** を選択して、**回復ウィザード** を開きます。

   ![回復ウィザード、[回復の選択の確認] ページ](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. **[次へ]** を選択して、 **[回復オプションの指定]** 画面に進みます。 もう一度 **[次へ]** を選択して、 **[回復の種類の選択]** 画面に進みます。 

   > [!NOTE]
   > VMware ワークロードでは、ネットワーク帯域幅調整の有効化はサポートされていません。

1. **[回復の種類の選択]** ページで、元のインスタンスまたは新しい場所に回復します。

   - **[Recover to original instance]\(元のインスタンスに復旧する\)** を選択した場合、ウィザードではそれ以上何も選択する必要はありません。 元のインスタンスのデータが使用されます。
   - **[仮想マシンとして任意のホストに回復します]** を選択した場合、 **[宛先の指定]** 画面で、 **[ESXi ホスト]** 、 **[リソース プール]** 、 **[フォルダー]** 、 **[パス]** の情報を指定します。

   ![[回復の種類の選択] ページ](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. **[概要]** ページで設定を確認し、 **[回復]** を選択して回復プロセスを開始します。 

   **[Recovery status]\(復旧の状態\)** 画面に復旧操作の進行状況が表示されます。

### <a name="restore-an-individual-file-from-a-vm"></a>VM から個々のファイルを復元する

保護された VM の復旧ポイントから個々のファイルを復元することができます。 この機能は Windows Server VM でのみ使用できます。 個々のファイルの復元は VM 全体の復元に似ていますが、回復プロセスを開始する前に VMDK を参照して目的のファイルを見つける点が異なります。 

> [!NOTE]
> Windows VM とディスク回復ポイントの場合のみ、VM から個別ファイルを回復できます。

1. Azure Backup Server 管理者コンソールで、 **[回復]** ビューを選択します。

1. **[参照]** ウィンドウを使用して、復旧する VM を選択するか、フィルターで検索します。 VM またはフォルダーを選択すると、使用可能な回復ポイントが **[回復ポイント] ペインに表示されます。

   ![使用できる回復ポイント](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. **[回復ポイント]** ペインで、カレンダーを使用して、目的の回復ポイントが含まれる日付を選択します。 バックアップポリシーの構成方法によっては、複数の回復ポイントを日付に含めることができます。 

1. 回復ポイントが取得された日付を選択した後、正しい **[回復時刻]** が選択されていることを確認します。 

   > [!NOTE]
   > 選択した日付に複数の回復ポイントがある場合は、使用する回復ポイントを **[回復時刻]** ドロップダウン メニューから選択します。 

   回復ポイントを選択すると、回復可能な項目の一覧が **[パス]** ペインに表示されます。

1. 回復するファイルを見つけるには、 **[パス]** ペインで、 **[回復可能な項目]** 列内の項目をダブルクリックして開きます。 回復するファイルまたはフォルダーを選択します。 複数の項目を選択するには、**Ctrl** キーを押しながら各項目を選択します。 **[パス]** ペインを使用して、 **[回復可能な項目]** 列に表示されているファイルまたはフォルダーの一覧を検索します。
    
   > [!NOTE]
   > **[以下の一覧を検索]** では、サブフォルダーの中までは検索されません。 サブフォルダーまで検索するには、フォルダーをダブルクリックします。 **[上へ]** を使用して、子フォルダーから親フォルダーへ移動します。 複数の項目 (ファイルとフォルダー) を選択できますが、それらは同じ親フォルダーに含まれている必要があります。 同じ回復ジョブで複数のフォルダー内の項目を回復することはできません。

   ![回復の選択の確認](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. 回復する項目を選択したら、管理者コンソールのツール リボンで **[回復]** を選択して **[回復ウィザード]** を開きます。 **回復ウィザード** の **[回復の選択の確認]** 画面には、回復することを選択した項目が表示されます。

1. **[回復オプションの指定]** 画面で、次の手順のいずれかを行います。

   - ネットワーク帯域幅の調整を有効にするには、 **[変更]** を選択します。 **[調整]** ダイアログ ボックスで、 **[ネットワークの使用帯域幅の調整を有効にする]** を選択してオンにします。 有効にしたら、 **[設定]** と **[Work Schedule]\(動作スケジュール\)** を構成します。
   - ネットワークの調整を無効のままにするには、 **[次へ]** を選択します。

1. **[回復の種類の選択]** 画面で、 **[次へ]** を選択します。 ファイルまたはフォルダーは、ネットワーク フォルダーにのみ回復できます。

1. **[宛先の指定]** 画面で、 **[参照]** を選択して、ファイルまたはフォルダーがあるネットワークの場所を探します。 Azure Backup Server により、回復されたすべての項目がコピーされるフォルダーが作成されます。 フォルダー名には MABS_day-month-year というプレフィックスが付きます。 回復されたファイルまたはフォルダーの場所を選択すると、その場所の詳細が表示されます。

   ![ファイルを復旧する場所を指定する](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. **[回復オプションの指定]** 画面で、適用するセキュリティ設定を選択します。 ネットワーク帯域幅の使用量調整を変更することもできますが、既定では調整は無効になっています。 **[SAN 回復]** および **[通知]** も有効になっていません。

1. **[概要]** 画面で設定を確認し、 **[回復]** を選択して回復プロセスを開始します。 **[Recovery status]\(復旧の状態\)** 画面に復旧操作の進行状況が表示されます。

## <a name="next-steps"></a>次のステップ

ここでは Azure Backup Server を使用した Azure VMware Solution VM のバックアップについて説明したので、次は以下について学習してください。 

- [Azure Backup Server でバックアップを設定する場合のトラブルシューティング](../backup/backup-azure-mabs-troubleshoot.md)。
- [Azure VMware Solution VM のライフサイクル管理](lifecycle-management-of-azure-vmware-solution-vms.md)。

---
title: Azure Database for PostgreSQL のバックアップ
description: 長期保有を指定した Azure Database for PostgreSQL のバックアップ (プレビュー) について説明します。
ms.topic: conceptual
ms.date: 09/08/2020
ms.custom: references_regions
ms.openlocfilehash: 1e2d83d4a5e21ed747ec9d4dcf2fa03d1e3935cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737574"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>長期保有を指定した Azure Database for PostgreSQL のバックアップ (プレビュー)

Azure Backup と Azure Database Services を連携させることで、バックアップを最大 10 年間保持する、Azure Database for PostgreSQL サーバー向けのエンタープライズ クラスのバックアップ ソリューションが構築されます。

長期保有以外にも、このソリューションには次のような多くの機能があります。

- Azure Active Directory とマネージド サービス ID (MSI) 認証を使用した、データベースへの Azure ロールベースのアクセス制御 (Azure RBAC)。
- お客様の管理による個々のデータベース レベルでのスケジュール バックアップとオンデマンド バックアップ。
- Postgres サーバーへのデータベース レベルでの復元、または BLOB ストレージへのデータベース レベルでの直接復元。
- 長期保有。
- すべての操作とジョブの一元的な監視。
- バックアップは、個別のセキュリティ ドメインと障害ドメインに保存されます。 そのため、ソース サーバーがセキュリティ侵害を受けたり、強制終了されたりしても、[バックアップ コンテナー](backup-vault-overview.md)内のバックアップは安全なままです。
- **pg_dump** を使用すると、復元の柔軟性を高めることができ、データベースのバージョン間で復元することや、バックアップの一部のみを復元することができます。

このソリューションは個別に使用できます。また、データ保有期間が最大 35 日の Azure PostgreSQL 提供のネイティブ バックアップ ソリューションに加えて使用することもできます。 ネイティブ ソリューションは、最新のバックアップから復旧する場合など、運用復旧に適しています。 Azure Backup ソリューションを使用すると、コンプライアンスのニーズを満たし、より細かく柔軟なバックアップと復元を行うことができます。

## <a name="support-matrix"></a>サポート マトリックス

|サポート  |詳細  |
|---------|---------|
|サポートされているデプロイ   |  [Azure Database for PostgreSQL - 単一サーバー](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|サポート対象の Azure リージョン |  米国東部、米国東部 2、米国中部、米国中南部、米国西部、米国西部 2、米国中西部、ブラジル南部、カナダ中部、北ヨーロッパ、西ヨーロッパ、英国南部、英国西部、ドイツ中西部、スイス北部、スイス西部、東アジア、東南アジア、東日本、西日本、韓国中部、韓国南部、インド中部、オーストラリア東部、オーストラリア中部、オーストラリア中部 2、アラブ首長国連邦北部  |
|サポートされる Azure PostgreSQL のバージョン    |   9.5、9.6、10、11      |

## <a name="feature-considerations-and-limitations"></a>機能の考慮事項と制限事項

- すべての操作は、Azure portal からのみサポートされます。
- データベースの最大サイズの上限として 400 GB が推奨されています。
- リージョン間のバックアップはサポートされていません。 したがって、Azure PostgreSQL サーバーは別のリージョンのコンテナーにバックアップできません。 同様に、バックアップは、コンテナーと同じリージョン内のサーバーにのみ復元できます。
- 復元時に復旧されるのはデータのみです。 "ロール" は復元されません。
- プレビューでは、テスト環境でのみソリューションを実行することをお勧めします。

## <a name="backup-process"></a>バックアップ プロセス

1. このソリューションでは、**pg_dump** を使用して、Azure PostgreSQL データベースのバックアップが作成されます。

2. バックアップする Azure PostgreSQL データベースを指定すると、サーバーとデータベースでバックアップ操作を実行するための適切な一連のアクセス許可とアクセス権があるかの検証が行われます。

3. Azure Backup により、バックアップ拡張機能がインストールされている worker ロールがスピンアップされます。 この拡張機能は、Postgres サーバーと通信します。

4. この拡張機能は、コーディネーターと Azure Postgres プラグインで構成されています。 コーディネーターは、バックアップの構成、バックアップ、復元など、さまざまな操作のワークフローをトリガーすることを担当し、プラグインは実際のデータ フローを担当します。
  
5. 選択したデータベースに対して保護の構成をトリガーすると、バックアップ サービスにより、コーディネーターに対してバックアップ スケジュールとその他のポリシーの詳細が設定されます。

6. スケジュールされた時刻になると、コーディネーターはプラグインと通信し、**pg_dump** を使用して Postgres サーバーからバックアップ データのストリーミングを開始します。

7. プラグインはバックアップ コンテナーにデータを直接送信するため、ステージングの場所は必要ありません。 データは Microsoft マネージド キーを使用して暗号化され、Azure Backup サービスによってストレージ アカウントに保存されます。

8. データ転送が完了すると、コーディネーターはバックアップ サービスを使用してコミットを確認します。

    ![バックアップ プロセス](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Azure PostgreSQL データベースでバックアップを構成する

複数の Azure PostgreSQL サーバーにある複数のデータベースのバックアップを構成できます。 サービスで Postgres サーバーをバックアップするために必要な[前提条件のアクセス許可](#prerequisite-permissions-for-configure-backup-and-restore)が既に構成されていることを確認します。

次の手順は、Azure Backup を使用して Azure PostgreSQL データベースのバックアップを構成するためのステップ バイ ステップのガイドです。

1. プロセスを開始するには、次の 2 つの方法があります。

    1. [バックアップ センター](backup-center-overview.md) ->  **[概要]**  ->  **[バックアップ]** に移動します。

        ![バックアップ センターに移動する](./media/backup-azure-database-postgresql/backup-center.png)

        **[開始: バックアップの構成]** で、 **[データソースの種類]** として **[Azure Database for PostgreSQL]** を選択します。

        ![[開始: バックアップの構成] で、データソースの種類を選択する](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. または、[[バックアップ コンテナー]](backup-vault-overview.md) ->  **[バックアップ]** に直接移動することもできます。

        ![[バックアップ コンテナー] に移動する](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![[バックアップ コンテナー] で [バックアップ] を選択する](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. **[バックアップの構成]** で、Postgres データベースをバックアップする先の **バックアップ コンテナー** を選択します。 既にコンテナーのコンテキスト内である場合、この情報は事前に入力されます。

    ![[バックアップの構成] でバックアップ コンテナーを選択する](./media/backup-azure-database-postgresql/configure-backup.png)

1. **バックアップ ポリシー** を選択または作成します。

    ![バックアップ ポリシーを選択する](./media/backup-azure-database-postgresql/backup-policy.png)

1. バックアップするリソースまたは Postgres データベースを選択します。

    ![バックアップするリソースを選択する](./media/backup-azure-database-postgresql/select-resources.png)

1. Azure PostgreSQL サーバーがコンテナーと同じリージョン内に存在する場合、すべてのサブスクリプションの Azure PostgreSQL サーバーの一覧から選択できます。 矢印を展開して、サーバー内のデータベースの一覧を表示します。

    ![サーバーを選択する](./media/backup-azure-database-postgresql/choose-servers.png)

1. サービスによって、選択したデータベースに対してこれらのチェックが実行され、選択した Postgres サーバーとデータベースをバックアップするためのアクセス許可がコンテナーにあるかどうかが検証されます。
    1. 続行するには、すべてのデータベースの **[バックアップの準備]** が **[成功]** にならなければなりません。
    1. エラーがある場合、そのエラーを **修正** して **再検証** するか、選択からデータベースを削除します。

    ![修正する検証のエラー](./media/backup-azure-database-postgresql/validation-errors.png)

1. **[レビューと構成]** ですべての詳細を確認し、 **[バックアップの構成]** を選択して操作を送信します。

    ![[レビューと構成] で詳細を確認する](./media/backup-azure-database-postgresql/review-and-configure.png)

1. **[バックアップの構成]** 操作がトリガーされると、バックアップ インスタンスが作成されます。 バックアップ センターまたはコンテナー ビューの [[バックアップ インスタンス]](backup-center-monitor-operate.md#backup-instances) ペインで操作の状態を追跡できます。

    ![バックアップ インスタンス](./media/backup-azure-database-postgresql/backup-instances.png)

1. バックアップは、ポリシーで定義されているバックアップ スケジュールに従ってトリガーされます。 ジョブは [[バックアップ ジョブ]](backup-center-monitor-operate.md#backup-jobs) で追跡できます。 現時点では、過去 7 日間のジョブを表示できます。

    ![バックアップ ジョブ](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>バックアップ ポリシーを作成する

1. **[バックアップ センター]**  ->  **[バックアップ ポリシー]**  ->  **[追加]** に移動します。 または、 **[バックアップ コンテナー]**  ->  **[バックアップ ポリシー]**  ->  **[追加]** に移動します。

    ![バックアップ ポリシーを追加する](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. 新しいポリシーの **名前** を入力します。

    ![ポリシー名を入力する](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. バックアップ スケジュールを定義します。 現時点では、**週単位** のバックアップがサポートされています。 単一または複数の曜日にバックアップをスケジュールできます。

    ![バックアップ スケジュールを定義する](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. **保持** の設定を定義します。 保持規則を 1 つ以上追加できます。 各保持規則は、特定のバックアップの入力と、それらのバックアップのデータ ストアと保持期間を前提とします。

1. バックアップの格納先として、次の 2 つのデータ ストア (または階層) のいずれかを選択できます。**バックアップ データ ストア** (Standard レベル) または **アーカイブ データ ストア** (プレビュー)。 次の **2 つの階層化オプション** のいずれかを選択することで、2 つのデータ ストア間でバックアップを階層化するタイミングを定義できます。

    - バックアップとアーカイブの両方のデータ ストアに同時にバックアップ コピーを作成する場合は、 **[今すぐ]** コピーすることを選択します。
    - バックアップ データ ストアでバックアップが期限切れになったときにそのバックアップをアーカイブ データ ストアに移動する場合は、 **[On-expiry]\(期限切れ時\)** に移動することを選択します。

1. 他の保持規則が存在しない場合、**既定の保持規則** が適用されます。既定値は 3 か月です。

    - **バックアップ データ ストア** では、保持期間の範囲は 7 日から 10 年です。
    - **アーカイブ データ ストア** では、保持期間の範囲は 6 か月から 10 年です。

    ![保持期間を編集する](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>保持規則は、事前に決められた優先順位で評価されます。 この優先順位は、高い方から順に **年単位** の規則、**月単位** の規則、**週単位** の規則となります。 他の規則が適用されない場合、既定の保持設定が適用されます。 たとえば、最初に成功した週単位のバックアップと、最初に成功した月単位のバックアップで、復旧ポイントが同じになる場合があります。 ただし、月単位の規則の優先順位は週単位の規則よりも高いため、最初に成功した月単位のバックアップに対応する保持が適用されます。

## <a name="restore"></a>復元

ターゲット サーバーに対する適切な一連のアクセス許可がサービスにある場合は、同じサブスクリプション内の任意の Azure PostgreSQL サーバーにデータベースを復元できます。 Postgres サーバーをバックアップするためにサービスに必要な[前提条件のアクセス許可](#prerequisite-permissions-for-configure-backup-and-restore)が既に構成されていることを確認します。

次のステップ バイ ステップのガイドに従って復元をトリガーします。

1. 復元プロセスを開始するには、次の 2 つの方法があります。
    1. [バックアップ センター](backup-center-overview.md) ->  **[概要]**  ->  **[復元]** に移動します。

    ![バックアップ センターで [復元] を選択する](./media/backup-azure-database-postgresql/backup-center-restore.png)

    **[開始: 復元]** で、 **[データ ソースの種類]** として **[Azure Database for PostgreSQL]** を選択します。 **[バックアップ インスタンス]** を選択します。

    ![[開始: 復元] で [データ ソースの種類] を選択する](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. または、 **[バックアップ コンテナー]**  ->  **[バックアップ インスタンス]** に直接移動することもできます。 復元するデータベースに対応する **バックアップ インスタンス** を選択します。

    ![復元用のバックアップ インスタンス](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![バックアップ インスタンスの一覧](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![[復元] を選択](./media/backup-azure-database-postgresql/select-restore.png)

1. 選択したバックアップ インスタンスで使用できるすべての完全バックアップの一覧から **復旧ポイントを選択** します。 既定では、最新の復旧ポイントが選択されます。

    ![復旧ポイントを選択する](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![復旧ポイントの一覧](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. **復元のパラメーター** を入力します。 この時点で、次の 2 種類の復元から選択できます。 **[Restore as Database]\(データベースとして復元\)** と **[ファイルとして復元]** 。

1. **データベースとして復元**: バックアップ データを復元して、ターゲット PostgreSQL サーバーに新しいデータベースを作成します。

    - ターゲット サーバーは、ソース サーバーと同じにすることができます。 ただし、元のデータベースの上書きはサポートされていません。
    - すべてのサブスクリプションのサーバーから、コンテナーと同じリージョン内のサーバーを選択できます。
    - **[レビューと復元]** を選択します。 これにより検証がトリガーされ、ターゲット サーバーに対する適切な復元のアクセス許可がサービスにあるかどうかが確認されます。

    ![データベースとして復元](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **ファイルとして復元**: バックアップ ファイルをターゲット ストレージ アカウント (BLOB) にダンプします。

    - すべてのサブスクリプションのストレージ アカウントから、コンテナーと同じリージョン内のストレージ アカウントを選択できます。
    - 選択したストレージ アカウントに対してフィルター処理されたコンテナーの一覧からターゲット コンテナーを選択します。
    - **[レビューと復元]** を選択します。 これにより検証がトリガーされ、ターゲット サーバーに対する適切な復元のアクセス許可がサービスにあるかどうかが確認されます。

    ![ファイルとして復元](./media/backup-azure-database-postgresql/restore-as-files.png)

1. 情報を確認し、 **[復元]** を選択します。 これにより、対応する復元ジョブがトリガーされます。このジョブは **[バックアップ ジョブ]** で追跡できます。

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>バックアップと復元を構成するための前提条件のアクセス許可

Azure Backup は、厳密なセキュリティ ガイドラインに準拠しています。 ネイティブの Azure サービスでありながら、リソースに対するアクセス許可は想定されておらず、ユーザーが明示的に指定する必要があります。  同様に、データベースに接続するための資格情報は保存されません。 これは、データを保護するために重要です。 代わりに、Azure Active Directory 認証が使用されます。

自動スクリプトと関連する指示を入手するために、[こちらのドキュメントをダウンロード](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx)してください。 これにより、バックアップと復元のために、Azure PostgreSQL サーバーへの適切な一連のアクセス許可が付与されます。

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>バックアップされた Azure PostgreSQL データベースを管理する

**バックアップ インスタンス** で実行できる管理操作を次に示します。

### <a name="on-demand-backup"></a>オンデマンド バックアップ

ポリシーで指定されたスケジュールにないバックアップをトリガーするには、 **[バックアップ インスタンス]**  ->  **[今すぐバックアップ]** に移動します。
関連するバックアップ ポリシーで定義された保持規則の一覧から選択します。

![今すぐバックアップをトリガーする](./media/backup-azure-database-postgresql/backup-now.png)

![保持規則の一覧から選択する](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>保護の停止

バックアップ項目の保護を停止できます。 これにより、そのバックアップ項目に関連付けられている復旧ポイントも削除されます。 既存の復旧ポイントを保持しながら、保護を停止するオプションはまだ提供されていません。

![保護の停止](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>ポリシーを変更する

バックアップ インスタンスに関連付けられているポリシーを変更できます。

1. **[バックアップ インスタンス]**  ->  **[ポリシーの変更]** を選択します。

    ![ポリシーを変更する](./media/backup-azure-database-postgresql/change-policy.png)

1. データベースに適用する新しいポリシーを選択します。

    ![ポリシーを再割り当てする](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>トラブルシューティング

このセクションには、Azure Backup を使用した Azure PostgreSQL データベースのバックアップに関するトラブルシューティング情報を記載します。

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

バックアップ コンテナー MSI に対して、バックアップまたは復元する PG サーバーの **読み取り** アクセス権を付与します。

PostgreSQL データベースへのセキュリティで保護された接続を確立するために、Azure Backup では、[マネージド サービス ID (MSI)](../active-directory/managed-identities-azure-resources/overview.md) 認証モデルが使用されます。 したがって、バックアップ コンテナーは、ユーザーによって明示的にアクセス許可が付与されているリソースにのみアクセスできます。

システム MSI は、作成時に自動的にコンテナーに割り当てられます。 このコンテナー MSI に、データベースのバックアップ元となる PostgreSQL サーバーへのアクセス権を付与する必要があります。

手順:

1. Postgres サーバーで、 **[アクセス制御 (IAM)]** ペインに移動します。

    ![[アクセス制御] ペイン](./media/backup-azure-database-postgresql/access-control-pane.png)

1. **[ロールの割り当てを追加する]** を選択します。

    ![ロールの割り当ての追加](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. 開いた右側のコンテキスト ペインで、次の情報を入力します。<br>

    **役割:** Reader<br>
    **[アクセスの割り当て先]** : **[バックアップ コンテナー]** を選択します<br>
    ドロップダウン リストに **[バックアップ コンテナー]** オプションが表示されない場合は、 **[Azure AD のユーザー、グループ、サービス プリンシパル]** オプションを選択します。<br>

    ![ロールの選択](./media/backup-azure-database-postgresql/select-role.png)

    **選択:** このサーバーとそのデータベースをバックアップする先のバックアップ コンテナーの名前を入力します。<br>

    ![バックアップ コンテナーの名前を入力する](./media/backup-azure-database-postgresql/enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Azure Active Directory で認証できるデータベース バックアップ ユーザーを作成します。

このエラーは、PostgreSQL サーバーの Azure Active Directory 管理者が存在しない場合、または Azure Active Directory を使用して認証できるバックアップ ユーザーが存在しない場合に発生する可能性があります。

手順:

OSS サーバーに Active Directory 管理者を追加します。

パスワードの代わりに Azure Active Directory で認証できるユーザーを使用してデータベースに接続する場合、この手順が必要です。 Azure Database for PostgreSQL の Azure AD 管理者ユーザーは **azure_ad_admin** ロールを所有します。 Azure AD で認証できる新しいデータベース ユーザーを作成できるのは、**azure_ad_admin** ロールのみです。

1. サーバー ビューの左側のナビゲーション ウィンドウにある [Active Directory 管理者] タブに移動し、Active Directory 管理者として自分 (または他のユーザー) を追加します。

    ![Active Directory 管理者を設定する](./media/backup-azure-database-postgresql/set-admin.png)

1. 必ず、AD 管理者ユーザー設定を **保存** してください。

    ![Active Directory 管理者ユーザー設定を保存する](./media/backup-azure-database-postgresql/save-admin-setting.png)

アクセス許可の付与手順を完了するために実行する必要がある手順の一覧については、[こちらのドキュメント](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx)をご覧ください。

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

サーバー ビューで **[Azure サービスへのアクセス許可]** フラグを有効にすることで、ネットワーク接続を確立ます。 サーバー ビューの **[接続のセキュリティ]** ペインで、 **[Azure サービスへのアクセス許可]** フラグを **[はい]** に設定します。

![Azure サービスへのアクセス許可](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>ファイルとして復元するときにストレージ アカウント コンテナーに復元するアクセス許可

1. Azure portal を使用して、ストレージ アカウント コンテナーにアクセスするためのアクセス許可をバックアップ コンテナー MSI に付与します。
    1. **[ストレージ アカウント]**  ->  **[アクセス制御]**  ->  **[ロールの割り当ての追加]** に移動します。
    1. **[ストレージ BLOB データ共同作成者]** ロールをバックアップ コンテナー MSI に割り当てます。

    ![[ストレージ BLOB データ共同作成者] ロールを割り当てる](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. または、Azure CLI で [az role assignment create](/cli/azure/role/assignment) コマンドを使用して、復元先の特定のコンテナーに詳細なアクセス許可を付与します。

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. assignee パラメーターをコンテナーの MSI の **アプリケーション ID** に置き換え、scope パラメーターで特定のコンテナーを参照します。
    1. コンテナー MSI の **アプリケーション ID** を取得するには、 **[アプリケーションの種類]** で **[すべてのアプリケーション]** を選択します。

        ![[すべてのアプリケーション] を選択する](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. コンテナー名を検索し、アプリケーション ID をコピーします。

        ![コンテナー名を検索する](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>次のステップ

- [バックアップ コンテナーの概要](backup-vault-overview.md)
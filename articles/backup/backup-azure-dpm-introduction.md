---
title: DPM サーバーを準備して ワークロードを Azure にバックアップする
description: DPM データを Azure Recovery Services コンテナーにバックアップする手順の概要。
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: 09c324fa1c24c56ede431fab8e175b166a52b438
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466565"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>System Center DPM を使用して Azure にワークロードをバックアップするための準備

この記事では、Azure Backup サービスを使用して、Azure への System Center Data Protection Manager (DPM) バックアップを準備する方法について説明します。

この記事では、次の内容について説明します。

- Azure Backup による DPM のデプロイの概要。
- DPM で Azure Backup を使用するための前提条件と制限。
- Recovery Services バックアップ コンテナーの設定、コンテナーの Azure ストレージの種類の変更 (オプション) など Azure を準備する手順。
- コンテナーの資格情報のダウンロード、Azure Backup エージェントのインストール、コンテナーへの DPM サーバーの登録を含む DPM サーバーを準備する手順。
- 一般的なエラーのトラブルシューティングのヒント。


## <a name="why-back-up-dpm-to-azure"></a>DPM を Azure にバックアップする理由

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) は、ファイルとアプリケーション データをバックアップします。 DPM は、Azure Backup と次のように対話します。

* **物理サーバーまたはオンプレミスの VM で実行されている DPM** — ディスクおよびテープへのバックアップに加えて、Azure でバックアップ コンテナーにデータをバックアップできます。
* **Azure VM で実行されている DPM** — System Center 2012 R2 の更新プログラム 3 以降からは、Azure VM に DPM をデプロイできます。 VM に接続された Azure ディスクにデータをバックアップできます。または Azure Backup を使用してデータをバックアップ コンテナーにバックアップできます。

DPM サーバーを Azure にバックアップするメリットは、次のとおりです。

* オンプレミスの DPM では、Azure Backup はテープへの長期的なデプロイの代替手段を用意しています。
* Azure VM で実行されている DPM では、Azure Backup は Azure ディスクからストレージをオフロードできます。 バックアップ コンテナーに古いデータを格納することで、ディスクに新しいデータを格納してビジネスをスケールアップできます。

## <a name="prerequisites-and-limitations"></a>前提条件と制限事項

**設定** | **要件**
--- | ---
Azure VM 上の DPM | System Center 2012 R2 の DPM 2012 R2 更新プログラムのロールアップ 3 以降。
物理サーバー上の DPM | System Center 2012 SP1 以降。System Center 2012 R2。
HYPER-V VM 上の DPM | System Center 2012 SP1 以降。System Center 2012 R2。
VMware VM 上の DPM | System Center 2012 R2 の更新プログラムのロールアップ 5 以降。
コンポーネント | DPM サーバーには、Windows PowerShell および .NET Framework 4.5 がインストール済みである必要があります。
サポート対象アプリ | DPM がバックアップできるものについて[学習](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix)します。
サポートされるファイルの種類 | Azure Backup では次のファイルの種類をバックアップできます。暗号化ファイル (完全バックアップのみ)、圧縮ファイル (増分バックアップがサポートされる)、スパース ファイル (増分バックアップがサポートされる)、圧縮されたスパース ファイル (スパースとして処理)。
サポートされていないファイルの種類 | 大文字小文字を区別するファイル システム上のサーバー、ハード リンク (スキップされる)、再解析ポイント (スキップされる)、暗号化されている圧縮ファイル (スキップされる)。暗号化されているスパース ファイル (スキップされる)、圧縮ストリーム、解析ストリーム。
ローカル ストレージ | バックアップする各マシンは、バックアップするデータのサイズの 5% 以上の空きローカル ストレージが必要です。 たとえば、100 GB のデータをバックアップするには、スクラッチ場所に少なくとも 5 GB の空き領域が必要です。
コンテナー ストレージ | Azure Backup コンテナーにバックアップできるデータ量に制限はありませんが、データ ソース (仮想マシンやデータベースなど) のサイズは 54,400 GB を超えないようにする必要があります。
Azure ExpressRoute | Azure ExpressRoute がプライベートまたは Microsoft ピアリングで構成されている場合は、それを使用してデータを Azure にバックアップできません。<br/><br/> Azure ExpressRoute がパブリック ピアリングで構成されている場合は、それを使用してデータを Azure にバックアップできます。<br/><br/> **注:** パブリック ピアリングは、新しい回線では非推奨です。
Azure Backup エージェント | DPM が System Center 2012 SP1 で実行されている場合、DPM SP1 のロールアップ 2 以降をインストールします。 これはエージェントのインストールに必要です。<br/><br/> この記事では、Microsoft Azure Recovery Service (MARS) エージェントとも呼ばれる、Azure Backup エージェントの最新バージョンをデプロイする方法について説明します。 以前のバージョンがデプロイされている場合、最新バージョンに更新し、バックアップが期待どおりに動作するようにしてください。

開始する前に、Azure Backup 機能が有効になっている Azure アカウントが必要です。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 [Azure Backup の料金](https://azure.microsoft.com/pricing/details/backup/)を参照してください。

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>ストレージ設定の変更

geo 冗長ストレージとローカル冗長ストレージのどちらかを選択できます。

- 既定では、コンテナーには geo 冗長ストレージがあります。
- コンテナーがプライマリ バックアップの場合は、オプションの設定を geo 冗長ストレージのままにします。 耐久性が十分でなくても低コストなバックアップが必要な場合は、以下の手順を使用して、ローカル冗長ストレージを構成します。
- [Azure ストレージ](../storage/common/storage-redundancy.md)、[geo 冗長](../storage/common/storage-redundancy-grs.md)ストレージおよび[ローカル冗長](../storage/common/storage-redundancy-lrs.md)ストレージの各オプションについて学習します。
- 初期バックアップの前にストレージ設定を変更します。 項目をバックアップ済みの場合、ストレージ設定を変更する前にコンテナーへのバックアップを停止します。

ストレージ レプリケーション設定を編集するには、次の手順を実行します。

1. コンテナー ダッシュボードを開きます。

2. **[管理]** で、 **[バックアップ インフラストラクチャ]** をクリックします。

3. **[バックアップ構成]** メニューで、コンテナーのストレージ オプションを選択します。

    ![バックアップ コンテナーの一覧](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>コンテナー資格情報のダウンロード

DPM サーバーをコンテナーに登録する場合、コンテナーの資格情報を使用します。

- コンテナーの資格情報ファイルは、各バックアップコンテナーごとにポータルによって生成される証明書です。
- ポータルは公開キーを Access Control Service (ACS) にアップロードします。
- マシン登録ワークフローの中で、ユーザーは、証明書の秘密キーを使用できるようになります。この秘密キーによって、マシンが認証されます。
- 認証に基づき、Azure Backup サービスは、指定されたコンテナーにデータを送信します。

### <a name="best-practices-for-vault-credentials"></a>コンテナーの資格情報のベスト プラクティス

資格情報を取得するには、Azure ポータルからセキュリティで保護されたチャネルを介してコンテナーの資格情報ファイルをダウンロードします。

- コンテナーの資格情報は登録ワークフロー中しか使用されません。
- コンテナーの資格情報ファイルが安全で漏えいしないようにするのはユーザーの責任です。
    - 資格情報のコントロールが失われた場合は、コンテナーの資格情報を使用して、他のマシンをコンテナーに登録できます。
    - ただし、バックアップ データは顧客に属するパスフレーズを使用して暗号化されているため、既存のバックアップ データが漏えいすることはありません。
- ファイルは、DPM サーバーからアクセスできる場所に保存してください。 ファイル共有 / SMB に格納されている場合は、アクセス許可を確認します。
- コンテナー資格情報は 48 時間後に有効期限が切れます。 新しいコンテナー資格情報は、必要な回数だけダウンロードできます。 ただし、登録ワークフローでは、最新のコンテナー資格情報ファイルのみを使用してください。
- Azure Backup サービスでは、証明書の秘密キーは認識されません。また、ポータルまたはサービスでは、この秘密キーは使用できません。

次の手順に従って、コンテナーの資格情報ファイルをローカル コンピューターにダウンロードします。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. DPM サーバーを登録するコンテナーを開きます。
3. **[設定]** で、 **[プロパティ]** をクリックします。

    ![コンテナー メニューを開く](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. **[プロパティ]**  >  **[バックアップ資格情報]** で、 **[ダウンロード]** をクリックします。 コンテナー名と現在の日付の組み合わせを使用してポータルがコンテナーの資格情報ファイルを生成し、ダウンロードできるようにします。

    ![ダウンロード](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. **[保存]** をクリックしてフォルダーにコンテナーの資格情報をダウンロードするか、または **[名前を付けて保存]** をクリックして保存場所を指定します。 ファイルの生成には最大で 1 分かかります。


## <a name="install-the-backup-agent"></a>Backup エージェントのインストール

Azure Backup によってバックアップされるすべてのマシンに、Backup エージェント (Microsoft Azure Recovery Service (MARS) エージェントとも呼ばれます) をインストールする必要があります。 次のように、DPM サーバーにエージェントをインストールします。

1. DPM サーバーを登録するコンテナーを開きます。
2. **[設定]** で、 **[プロパティ]** をクリックします。

    ![コンテナー メニューを開く](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. **[プロパティ]** ページで、Azure Backup エージェントをダウンロードします。

    ![ダウンロード](./media/backup-azure-dpm-introduction/azure-backup-agent.png)


4. ダウンロードした後、MARSAgentInstaller.exe を実行し、 DPM マシンにエージェントをインストールします。
5. エージェントのインストール フォルダーとキャッシュ フォルダーを選択します。 キャッシュの場所の空き領域は、バックアップ データの 5% 以上である必要があります。
6. プロキシ サーバーを使用してインターネットに接続する場合、 **[プロキシ構成]** 画面で、プロキシ サーバーの詳細を入力します。 認証済みのプロキシを使用する場合は、この画面でユーザー名とパスワードの詳細を入力します。
7. Azure Backup エージェントは、.NET Framework 4.5 と Windows PowerShell を (インストールされていない場合は) インストールして、インストールを完了します。
8. エージェントがインストールされたら、ウィンドウを **閉じます**。

    ![閉じます](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>DPM サーバーのコンテナーへの登録

1. DPM 管理者コンソール > **[管理]** で、 **[オンライン]** をクリックします。 **[登録]** を選択します。 これにより、サーバーの登録ウィザードが開きます。
2. **[プロキシ構成]** で、必要に応じてプロキシ設定を指定します。

    ![[プロキシ構成]](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. **[Backup コンテナー]** で、ダウンロードしたコンテナー資格情報ファイルを参照して選択します。

    ![コンテナー資格情報](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

10. **[使用帯域幅の調整]** で、必要に応じてバックアップの帯域幅調整を有効にできます。 速度制限を設定して作業時間と日数を指定できます。

    ![[使用帯域幅の調整]](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. **[回復先フォルダーの設定]** で、データの回復中に使用できる場所を指定します。

    - Azure Backup は、回復するデータを一時的に保持する領域としてこの場所を使用します。
    - データ復旧完了後、Azure Backup はこの領域のデータをクリーンアップします。
    - この場所は、並列復旧が見込まれる項目を保持するのに十分な領域が必要です。

    ![[回復先フォルダーの設定]](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. **[暗号化設定]** で、パスフレーズを生成または指定します。

    - パスフレーズは、クラウドへのバックアップを暗号化する際に使用されます。
    - 16 文字以上指定します。
    - 安全な場所にファイルを保存します。これは回復のために必要です。

    ![暗号化](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > 暗号化のパスフレーズはユーザーが所有しており、Microsoft はこれを確認できません。
    > パスフレーズを紛失または忘れてしまった場合、Microsoft はバックアップ データの回復を支援することはできません。

13. **[登録]** をクリックしてコンテナーに DPM サーバーを登録します。

サーバーがコンテナーに正常に登録されると、Microsoft Azure へのバックアップを開始できるようになります。

## <a name="troubleshoot-vault-credentials"></a>コンテナーの資格情報のトラブルシューティング

### <a name="expiration-error"></a>期限切れエラー

コンテナーの資格情報ファイルは (ポータルからダウンロード後) 48 時間のみ有効です。 この画面でなんらかのエラー (例: "指定されたコンテナーの資格情報ファイルは期限切れです。") が発生した場合は、Azure ポータルにログインし、コンテナーの資格情報ファイルを再度ダウンロードします。

### <a name="access-error"></a>アクセス エラー

セットアップ アプリケーションがアクセスできる場所に、コンテナーの資格情報ファイルがあることを確認します。 アクセス関連のエラーが発生した場合は、コンテナーの資格情報ファイルをこのコンピューターの一時的な場所にコピーし、操作をやり直してください。

### <a name="invalid-credentials-error"></a>無効な資格情報エラー

コンテナーの資格情報が無効であるというエラー (例: "無効なコンテナーの資格情報が指定されました。ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません。") が発生した場合、ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません。

- ポータルから新しいコンテナーの資格情報ファイルをダウンロードしてから操作をやり直してください。
- このエラーは通常、ユーザーが Azure ポータルで **[コンテナー資格情報のダウンロード]** オプションを連続で 2 回クリックした場合に発生します。 この場合、2 番目のコンテナーの資格情報ファイルだけが有効です。

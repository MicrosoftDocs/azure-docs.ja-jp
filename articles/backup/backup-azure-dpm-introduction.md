---
title: "DPM を使用してワークロードを Azure Portal にバックアップする | Microsoft Docs"
description: "Azure Backup サービスを使用した DPM サーバーのバックアップの概要"
services: backup
documentationcenter: 
author: Nkolli1
manager: shreeshd
editor: 
keywords: "System Center Data Protection Manager, Data Protection Manager, DPM バックアップ"
ms.assetid: c8c322cf-f5eb-422c-a34c-04a4801bfec7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: adigan;giridham;jimpark;markgal;trinadhk
translationtype: Human Translation
ms.sourcegitcommit: 2224ddf52283d7da599b1b4842ca617d28b28668
ms.openlocfilehash: 973730bfdd4d13714ce7d0256a32af9eb8183e7a
ms.lasthandoff: 01/27/2017


---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>DPM を使用して Azure へのワークロードをバックアップするための準備
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Azure Backup Server (クラシック)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (クラシック)](backup-azure-dpm-introduction-classic.md)
>
>

この記事では、Microsoft Azure Backup を使用して System Center Data Protection Manager (DPM) サーバーとワークロードを保護する方法について概説します。 この記事を読むと次のことが理解できます。

* Azure DPM サーバーのバックアップの仕組み
* スムーズなバックアップ操作を実現するための前提条件
* 一般的に発生するエラーとその対処法
* サポートされるシナリオ

> [!NOTE]
> Azure には、リソースの作成と操作に関して&2; 種類のデプロイメント モデルがあります。[Resource Manager デプロイメント モデルとクラシック デプロイメント モデル](../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、Resource Manager モデルを使用してデプロイされた VM を復元するための情報および手順を示しています。
>
>

System Center DPM は、ファイルとアプリケーション データをバックアップします。 DPM にバックアップされるデータは、テープやディスクに保存することも、Microsoft Azure Backup を使って Azure にバックアップすることもできます。 DPM は、Azure Backup と次のように対話します。

* **物理サーバーまたはオンプレミス仮想マシンとしてデプロイされている DPM** — DPM が物理サーバーまたはオンプレミス Hyper-V 仮想マシンとしてデプロイされている場合、データはディスクやテープだけでなく、Recovery Services コンテナーにバックアップすることができます。
* **Azure の仮想マシンとしてデプロイされている DPM** — System Center 2012 R2 Update 3 以降、DPM は Azure 仮想マシンとしてデプロイすることができます。 DPM が Azure 仮想マシンとしてデプロイされている場合、DPM Azure 仮想マシンにアタッチされている Azure ディスクにデータをバックアップすることができます。またはデータを Recovery Services コンテナーにバックアップして、データ ストレージをオフロードすることができます。

## <a name="why-backup-from-dpm-to-azure"></a>DPM から Azure にバックアップする理由
DPM サーバーのバックアップに Azure Backup を使用するビジネス上のメリットは、次のとおりです。

* オンプレミスの DPM デプロイでは、Azure はテープへの長期的なデプロイの代替手段として使用できます。
* Azure の DPM デプロイでは、Azure Backup を使用して Azure ディスクからストレージをオフロードし、Recovery Services コンテナーに古いデータを、ディスクに新しいデータを格納してスケールアップすることができます。

## <a name="prerequisites"></a>前提条件
DPM データをバックアップするために Azure Backup を準備するには、次のようにします。

1. **Recovery Services コンテナーの作成** — Azure ポータルでコンテナーを作成します。
2. **コンテナーのダウンロード** — Recovery Services コンテナーに DPM サーバーを登録するために使用する資格情報をダウンロードします。
3. **Azure Backup エージェントのインストール** — Azure Backup から、各 DPM サーバーにエージェントをインストールします。
4. **サーバーの登録** — Recovery Services コンテナーに DPM サーバーを登録します。

### <a name="1-create-a-recovery-services-vault"></a>1.Recovery Services コンテナーの作成
Recovery Services コンテナーを作成するには、次の手順に従います。

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. ハブ メニューで **[参照]** をクリックし、リソースの一覧で「**Recovery Services**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Recovery Services コンテナー]**をクリックします。

    ![Create Recovery Services Vault step 1](./media/backup-azure-dpm-introduction/open-recovery-services-vault.png)

    Recovery Services コンテナーの一覧が表示されます。
3. **[Recovery Services コンテナー]** メニューの **[追加]** をクリックします。

    ![Create Recovery Services Vault step 2](./media/backup-azure-dpm-introduction/rs-vault-menu.png)

    [Recovery Services コンテナー] ブレードが開き、**[名前]**、**[サブスクリプション]**、**[リソース グループ]**、**[場所]** を指定するよう求められます。

    ![Create Recovery Services vault step 5](./media/backup-azure-dpm-introduction/rs-vault-attributes.png)
4. **[名前]**ボックスに、コンテナーを識別する表示名を入力します。 名前は Azure サブスクリプションに対して一意である必要があります。 2 ～ 50 文字の名前を入力します。 名前の先頭にはアルファベットを使用する必要があります。また、名前に使用できるのはアルファベット、数字、ハイフンのみです。
5. **[サブスクリプション]** をクリックして、使用可能なサブスクリプションの一覧を表示します。 どのサブスクリプションを使用すればよいかがわからない場合は、既定 (または推奨) のサブスクリプションを使用してください。 組織のアカウントが複数の Azure サブスクリプションに関連付けられている場合に限り、複数の選択肢が存在します。
6. **[リソース グループ]** をクリックして、使用可能なリソース グループを表示するか、**[新規]** をクリックして、新しいリソース グループを作成します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」をご覧ください。
7. **[場所]** をクリックして、コンテナーの地理的リージョンを選択します。
8. **[作成]**をクリックします。 Recovery Services コンテナーの作成に時間がかかることがあります。 ポータルの右上隅で、状態の通知を監視します。
   コンテナーが作成されると、ポータルで開かれます。

### <a name="set-storage-replication"></a>ストレージ レプリケーションの設定
ストレージ レプリケーション オプションでは、geo 冗長ストレージとローカル冗長ストレージのどちらかを選択できます。 既定では、コンテナーには geo 冗長ストレージがあります。 プライマリ バックアップの場合は、オプションが geo 冗長ストレージに設定されているままにします。 冗長性を犠牲にしても低コストなバックアップが必要な場合は、ローカル冗長ストレージを選択します。 [geo 冗長](../storage/storage-redundancy.md#geo-redundant-storage)ストレージ オプションと[ローカル冗長](../storage/storage-redundancy.md#locally-redundant-storage)ストレージ オプションの詳細については、[Azure Storage のレプリケーションの概要](../storage/storage-redundancy.md)に関する記事をご覧ください。

ストレージ レプリケーション設定を編集するには、次の手順を実行します。

1. コンテナーを選択して、コンテナーのダッシュボードと [設定] ブレードを開きます。 **[設定]** ブレードが開かない場合は、コンテナーのダッシュボードで **[すべての設定]** をクリックします。
2. **[設定]** ブレードで、**[バックアップ インフラストラクチャ]** > **[バックアップ構成]** をクリックして、**[バックアップ構成]** ブレードを開きます。 **[バックアップ構成]** ブレードで、コンテナーのストレージ レプリケーション オプションを選択します。

    ![バックアップ資格情報コンテナーの一覧](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    コンテナーのストレージ オプションを選択したら、VM をコンテナーに関連付けることができます。 関連付けを開始するには、Azure 仮想マシンを検出して登録する必要があります。

### <a name="2-download-vault-credentials"></a>2.コンテナー資格情報のダウンロード
資格情報コンテナーの資格情報ファイルは、各バックアップ資格情報コンテナーごとにポータルによって生成される証明書です。 ポータルは公開キーを Access Control Service (ACS) にアップロードします。 ユーザーは、マシン登録ワークフローの入力として指定したワークフローの一部として、証明書の秘密キーを使用できます。 これにより、コンピューターは Azure Backup サービスで識別された資格情報のコンテナーにバックアップ データを送信できるようになります。

資格情報コンテナーの資格情報は登録ワークフロー中しか使用されません。 資格情報コンテナーの資格情報ファイルが漏えいしないようにするのはユーザーの責任です。 悪意のあるユーザーが入手した場合、資格情報コンテナーの資格情報ファイルを使用し、同じ資格情報コンテナーに対してその他のコンピューターが登録されてしまう可能性があります。 ただし、バックアップ データは顧客に属するパスフレーズを使用して暗号化されているため、既存のバックアップ データが漏えいすることはありません。 この問題をなくすため、資格情報コンテナーの資格情報は 48 時間で期限切れになるよう設定されています。 Recovery Services の資格情報コンテナーの資格情報は何回でもダウンロードできますが、登録ワークフロー時には最新の資格情報コンテナーの資格情報ファイルだけが適用できます。

資格情報コンテナーの資格情報ファイルは、セキュリティで保護されたチャネルを介して Azure ポータルからダウンロードされます。 Azure Backup サービスは証明書の秘密キーを認識せず、ポータルまたはサービスでは秘密キーは保持されません。 資格情報コンテナーの資格情報ファイルをローカル コンピューターにダウンロードするには、次の手順を使用します。

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. DPM を登録する Recovery Services コンテナーを開きます。
3. 既定では、設定ブレードが開きます。 設定ブレードが閉じている場合は、コンテナー ダッシュ ボードの **[設定]** をクリックして設定ブレードを開きます。 設定ブレードの **[プロパティ]**をクリックします。

    ![Open vault blade](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
4. [プロパティ] ページで、**[バックアップ資格情報]** の **[ダウンロード]** をクリックします。 ポータルは、ダウンロードで使用可能な資格情報コンテナーの資格情報ファイルを生成します。

    ![ダウンロード](./media/backup-azure-dpm-introduction/vault-credentials.png)

ポータルは、資格情報コンテナーの名前と現在の日付の組み合わせを使用して、資格情報コンテナーの資格情報を生成します。 **[保存]** をクリックして、資格情報コンテナーの資格情報をローカル アカウントのダウンロード フォルダーにダウンロードするか、[保存] メニューから [名前を付けて保存] を選択して、保存場所を指定します。 ファイルの生成には最大で&1; 分かかります。

### <a name="note"></a>注
* 資格情報コンテナーの資格情報ファイルは、使用するコンピューターからアクセスできる場所に保存してください。 ファイル共有 / SMB に格納されている場合は、アクセス許可を確認します。
* 資格情報コンテナーの資格情報ファイルは登録ワークフロー中しか使用されません。
* 資格情報コンテナーの資格情報ファイルの有効期限は 48 時間であり、ポータルからダウンロードすることができます。

### <a name="3-install-backup-agent"></a>3.Backup エージェントのインストール
Azure Backup コンテナーを作成したら、エージェントを各 Windows コンピューター (Windows Server、Windows クライアント、System Center Data Protection Manager サーバー、Azure Backup サーバー コンピューター) にインストールする必要があります。このエージェントにより、データやアプリケーションを Azure にバックアップできるようになります。

1. DPM を登録する Recovery Services コンテナーを開きます。
2. 既定では、設定ブレードが開きます。 設定ブレードが閉じている場合は、 **[設定]** をクリックして設定ブレードを開きます。 設定ブレードの **[プロパティ]**をクリックします。

    ![Open vault blade](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. [設定] ページで、**[Azure Backup エージェント]** の下の **[ダウンロード]** をクリックします。

    ![ダウンロード](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

   エージェントがダウンロードされたら、MARSAgentInstaller.exe をダブルクリックして Azure Backup エージェントのインストールを開始します。 エージェントに必要なインストール フォルダーとスクラッチ フォルダーを選択します。 指定されたキャッシュの場所には、バックアップ データの 5% 以上の空き領域が必要です。
4. プロキシ サーバーを使用してインターネットに接続する場合、 **[プロキシ構成]** 画面で、プロキシ サーバーの詳細を入力します。 認証済みのプロキシを使用する場合は、この画面でユーザー名とパスワードの詳細を入力します。
5. Azure Backup エージェントは、.NET Framework 4.5 と Windows PowerShell を (まだ使用可能でない場合は) インストールして、インストールを完了します。
6. エージェントがインストールされたら、ウィンドウを **閉じます** 。

   ![閉じます](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)
7. 資格情報コンテナーに **DPM サーバーを登録する**には、**[管理]** タブで **[オンライン]** をクリックします。 次に、 **[登録]**を選択します。 これにより、Register Setup (セットアップの登録) ウィザードが開きます。
8. プロキシ サーバーを使用してインターネットに接続する場合、 **[プロキシ構成]** 画面で、プロキシ サーバーの詳細を入力します。 認証済みのプロキシを使用する場合は、この画面でユーザー名とパスワードの詳細を入力します。

    ![[プロキシ構成]](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. [資格情報コンテナーの資格情報] 画面で、以前にダウンロードされた資格情報コンテナーの資格情報ファイルを参照して選択します。

    ![コンテナー資格情報 ](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

    資格情報コンテナーの資格情報ファイルは (ポータルからダウンロード後) 48 時間のみ有効です。 この画面でなんらかのエラー (例: "指定されたコンテナーの資格情報ファイルは期限切れです。") が発生した場合は、Azure ポータルにログインし、コンテナーの資格情報ファイルを再度ダウンロードします。

    セットアップ アプリケーションがアクセスできる場所に、資格情報コンテナーの資格情報ファイルがあることを確認します。 アクセス関連のエラーが発生した場合は、資格情報コンテナーの資格情報ファイルをこのコンピューターの一時的な場所にコピーし、操作をやり直してください。

    コンテナーの資格情報が無効であるというエラー (例: "無効なコンテナーの資格情報が指定されました。ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません。") が発生した場合、ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません。 ポータルから新しい資格情報コンテナーの資格情報ファイルをダウンロードしてから操作をやり直してください。 このエラーは通常、ユーザーが Azure ポータルで **[資格情報コンテナー資格情報のダウンロード]** オプションを連続でクリックした場合に発生します。 この場合、2 番目の資格情報コンテナーの資格情報ファイルだけが有効です。
10. 営業時間内外のネットワーク帯域幅の使用を制御するには、 **[使用帯域幅の調整]** 画面で、帯域幅の使用の制限を設定し、営業時間と非営業時間を定義できます。

    ![[使用帯域幅の調整]](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)
11. **[回復先フォルダーの設定]** 画面で、Azure からダウンロードされたファイルを一時的にステージングするフォルダーを参照します。

    ![[回復先フォルダーの設定]](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)
12. **[暗号化の設定]** 画面で、パスフレーズを生成するか、パスフレーズ (最小 16 文字) を指定することができます。 必ず安全な場所にパスフレーズを保存してください。

    ![暗号化](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > パスフレーズを紛失または忘れてしまった場合、Microsoft はバックアップ データの回復を支援することはできません。 エンド ユーザーは暗号化パスフレーズを所有していますが、Microsoft はエンド ユーザーが使用するパスフレーズを確認できません。 回復操作中に必要になる場合もあるため、ファイルは安全な場所に保存してください。
    >
    >
13. **[登録]** ボタンをクリックすると、コンピューターはコンテナーに正常に登録され、Microsoft Azure へのバックアップを開始できるようになります。
14. Data Protection Manager を使用する場合は、**[管理]** タブで **[オンライン]** を選択して **[構成]** オプションをクリックすることによって、登録ワークフロー時に指定した設定を変更できます。

## <a name="requirements-and-limitations"></a>要件 (および制限)
* DPM は、物理サーバーとして、または System Center 2012 SP1 か System Center 2012 R2 にインストールされている Hyper-V 仮想マシンとして実行することができます。 さらに、DPM は、System Center 2012 R2 (少なくとも DPM 2012 R2 更新プログラム ロールアップ 3 が適用されているもの) で実行する Azure 仮想マシンとして、または System Center 2012 R2 (少なくとも更新プログラム ロールアップ 5 が適用されているもの) で実行する VMWare の Windows 仮想マシンとして実行することもできます。
* DPM を System Center 2012 SP1 で実行する場合は、System Center Data Protection Manager SP1 用の更新プログラム ロールアップ 2 をインストールする必要があります。 この手順は、Azure Backup エージェントをインストールする前に実行する必要があります。
* DPM サーバーには、Windows PowerShell および .Net Framework 4.5 がインストール済みである必要があります。
* DPM は、ほとんどのワークロードを Azure Backup にバックアップできます。 サポート対象の詳細な一覧については、次に示す Azure Backup サポートの項目を参照してください。
* Azure Backup に格納されているデータは、"テープにコピー" オプションでは回復できません。
* Azure Backup 機能が有効になっている Azure アカウントを使用する必要があります。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 [Azure Backup の料金](https://azure.microsoft.com/pricing/details/backup/)を参照してください。
* Azure Backup を使用するには、バックアップ対象のサーバーに Azure Backup エージェントがインストールされていることが必要です。 各サーバーをローカル ストレージとして使用するには、バックアップするデータのサイズの 5% 以上の空き領域が必要です。 たとえば、100 GB のデータをバックアップするには、スクラッチ場所に少なくとも 5 GB の空き領域が必要です。
* データは、Azure コンテナー ストレージに格納されます。 Azure Backup コンテナーにバックアップできるデータ量に制限はありませんが、データ ソース (仮想マシンやデータベースなど) のサイズは 54,400 GB を超えないようにする必要があります。

Azure へのバックアップがサポートされているファイルの種類は、次のとおりです。

* 暗号化ファイル (完全バックアップのみ)
* 圧縮ファイル (増分バックアップがサポートされる)
* スパース ファイル (増分バックアップがサポートされる)
* 圧縮されたスパース ファイル (スパースとして処理)

次のものはサポートされていません。

* 大文字と小文字を区別するファイル システムのサーバーはサポートされません。
* ハード リンク (スキップされる)
* 再解析ポイント (スキップされる)
* 暗号化されている圧縮ファイル (スキップされる)
* 暗号化されているスパース ファイル (スキップされる)
* 圧縮ストリーム
* スパース ストリーム

> [!NOTE]
> System Center 2012 DPM SP1 以降では、Microsoft Azure Backup を使用して、DPM で保護されているワークロードを Azure にバックアップすることができます。
>
>


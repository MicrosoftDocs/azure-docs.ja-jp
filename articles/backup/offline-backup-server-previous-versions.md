---
title: Data Protection Manager (DPM) および Microsoft Azure Backup Server (MABS) のオフライン バックアップ - 以前のバージョン
description: Azure Backup では、Azure Import/Export サービスを使用してネットワークからデータを送信できます。 この記事では、以前のバージョンの DPM と Azure Backup Server でのオフライン バックアップ ワークフローについて説明します。
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 0405ab66b7714f00349419e94bb064267ca711a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98702187"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-previous-versions"></a>DPM および Azure Backup Server のオフライン バックアップのワークフロー (以前のバージョン)

>[!IMPORTANT]
>これらの手順は、DPM 2019 RTM およびそれ以前のバージョンと、MABS v3 RTM およびそれ以前のバージョンに適用されます。

Azure Backup はさまざまな面で効率性に優れ、Azure への初回完全バックアップ時にネットワークとストレージのコストを抑えます。 初回完全バックアップでは通常、大量のデータが転送されます。その後の差分/増分のみを転送するバックアップと比べると、多くのネットワーク帯域幅が必要です。 Azure Backup では、初回バックアップが圧縮されます。 オフライン シード処理プロセスによって、Azure Backup でディスクを使用し、圧縮済みの初回バックアップ データをオフラインで Azure にアップロードすることができます。

Azure Backup のオフライン シード処理プロセスは [Azure Import/Export サービス](../import-export/storage-import-export-service.md) と緊密に統合されています。 このサービスでは、ディスクを使用してデータを Azure に転送できます。 待ち時間が長く、低帯域幅のネットワークで転送する必要がある初回バックアップ データが数 TB (テラバイト) ある場合は、オフライン シード処理ワークフローを使用して、1 台以上のハード ドライブ上にある初回バックアップ コピーを Azure データセンターに発送できます。 この記事では、System Center Data Protection Manager (DPM) および Microsoft Azure Backup Server (MABS) のこのワークフローを完了するための概要と手順について説明します。

> [!NOTE]
> Microsoft Azure Recovery Services (MARS) エージェントのオフライン バックアップのプロセスは、DPM と MABS とは異なります。 MARS エージェントのオフライン バックアップの使用の詳細については、「[Azure Backup でのオフライン バックアップのワークフロー](backup-azure-backup-import-export.md)」を参照してください。 Azure Backup エージェントを使用して行われたシステム状態のバックアップでは、オフライン バックアップはサポートされていません。
>

## <a name="overview"></a>概要

Azure Backup のオフライン シード処理機能と Azure Import/Export サービスを使用すると、ディスクを使ってオフラインで Azure にデータを簡単にアップロードできます。 オフライン バックアップ プロセスには次の手順があります。

> [!div class="checklist"]
>
> * バックアップ データは、ネットワーク経由で送信されずにステージング場所に書き込まれます。
> * その後、ステージング場所のデータは *AzureOfflineBackupDiskPrep* ユーティリティを使用して、1 つまたは複数の SATA ディスクに書き込まれます。
> * Azure インポート ジョブがユーティリティによって自動的に作成されます。
> * SATA ドライブが最寄りの Azure データセンターに送付されます。
> * Azure へのバックアップ データのアップロードが完了した後、Azure Backup は、バックアップ データをバックアップ コンテナーにコピーし、増分バックアップをスケジュールします。

## <a name="supported-configurations"></a>サポートされている構成

オフライン バックアップは、オンプレミスから Microsoft Cloud にデータをバックアップする Azure Backup のすべてのデプロイ モデルでサポートされます。 モデルには以下のものがあります。

> [!div class="checklist"]
>
> * MARS エージェントまたは Azure Backup エージェントでのファイルとフォルダーのバックアップ。
> * DPM を使用した、すべてのワークロードとファイルのバックアップ。
> * MABS を使用した、すべてのワークロードとファイルのバックアップ。

>[!NOTE]
>Azure CSP サブスクリプションは、DPM 2019 RTM およびそれ以前のバージョンと、MABS v3 RTM およびそれ以前のバージョンのオフライン シード処理での使用がサポートされていません。 ネットワーク経由のオンライン バックアップは、引き続きサポートされます。

## <a name="prerequisites"></a>前提条件

オフライン バックアップ ワークフローを開始する前に、次の前提条件を満たしていることをご確認ください。

* [Recovery Services コンテナー](backup-azure-recovery-services-vault-overview.md)が作成されていること。 作成するには、「[Recovery Services コンテナーを作成する](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)」の手順に従います。
* Azure Backup エージェントまたは MABS、または DPM が Windows Server または Windows クライアントに適宜インストールされ、Recovery Services コンテナーにコンピューターが登録されていること。 [最新バージョンの Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) のみが使用されていることを確認します。
* データのバックアップを計画しているコンピューターで [Azure 発行設定ファイルをダウンロード](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade)します。 発行設定ファイルをダウンロードするサブスクリプションは、Recovery Services コンテナーを含むサブスクリプションとは別のサブスクリプションにすることができます。 サブスクリプションがソブリン Azure クラウドにある場合は、Azure 発行設定ファイルのダウンロードに適した次のリンクを使用します。

    | ソブリン クラウドのリージョン | Azure 発行設定ファイルのリンク |
    | --- | --- |
    | United States | [リンク](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | 中国 | [リンク](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Resource Manager デプロイ モデルを使用する Azure Storage アカウントが、発行設定ファイルをダウンロードしたサブスクリプションに作成されていること。 ストレージ アカウントで、コピー先として使用する新しい BLOB コンテナーを作成します。

  ![Resource Manager 開発によるストレージ アカウントの作成](./media/offline-backup-dpm-mabs-previous-versions/storage-account-resource-manager.png)

* ステージング場所 (ネットワーク共有、または最初のコピーを保持するのに十分なディスク領域がある内部または外部コンピューター上の追加ドライブ) が作成されていること。 たとえば、500 GB のファイル サーバーをバックアップする場合は、500 GB 以上のステージング領域を確保します。 たとえば、500 GB のファイル サーバーをバックアップする場合は、ステージング領域が 500 GB 以上あることを確認します (圧縮処理により、使用量はこれよりも少なくなります)。
* Azure に送信されるディスクに関して、2.5 インチ SSD、または 2.5 インチか 3.5 インチの SATA II/III 内蔵ハード ドライブが使用されていることを確認します。 最大 10 TB のハード ドライブを使用できます。 サービスでサポートされている最新のドライブについては、[Azure Import/Export サービスのドキュメント](../import-export/storage-import-export-requirements.md#supported-hardware)をご覧ください。
* SATA ドライブは、ステージング場所から SATA ドライブへのバックアップ データのコピーが行われるコンピューター ("*コピー用コンピューター*" と呼ばれます) に接続されている必要があります。 コピー用コンピューターで BitLocker が有効になっていることを確認します。

## <a name="prepare-the-server-for-the-offline-backup-process"></a>オフライン バックアップ プロセスのためのサーバーの準備

>[!NOTE]
> MARS エージェントのインストールで、*AzureOfflineBackupCertGen.exe* などのユーティリティが一覧表示されない場合は、AskAzureBackupTeam@microsoft.com にメールを送付すると、アクセスできるようになります。

* サーバーで管理者特権でのコマンド プロンプトを開いて、次のコマンドを実行します。

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    Azure Offline Backup Active Directory アプリケーションが存在しない場合は、このツールによって作成されます。

    アプリケーションが既にある場合は、この実行可能ファイルにより、テナント内のアプリケーションに証明書を手動でアップロードするように要求されます。 [このセクション](#manually-upload-an-offline-backup-certificate)の手順に従って、アプリケーションに証明書を手動でアップロードします。

* *AzureOfflineBackupCertGen.exe* ツールは、*OfflineApplicationParams.xml* ファイルを生成します。 このファイルを MABS サーバーまたは DPM サーバーにコピーします。
* DPM インスタンスまたは Azure Backup サーバーに[最新の MARS エージェント](https://aka.ms/azurebackup_agent)をインストールします。
* サーバーを Azure に登録します。
* 次のコマンドを実行します。

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname to be used for offline backup>
    ```

* 上記のコマンドにより、`C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml` ファイルが作成されます。

## <a name="manually-upload-an-offline-backup-certificate"></a>オフライン バックアップ証明書の手動アップロード

オフライン バックアップ用として以前に作成された Azure Active Directory アプリケーションにオフライン バックアップ証明書を手動でアップロードするには、次の手順に従います。

1. Azure portal にサインインします。
1. **[Azure Active Directory]**  >  **[アプリの登録]** に移動します。
1. **[所有しているアプリケーション]** タブで、表示名の形式が `AzureOfflineBackup _<Azure User Id` のアプリケーションを見つけます。

    ![[所有しているアプリケーション] タブでアプリケーションを見つける](./media/offline-backup-dpm-mabs-previous-versions/owned-applications.png)

1. アプリケーションを選択します。 左側のウィンドウの **[管理]** で、 **[証明書とシークレット]** に移動します。
1. 既存の証明書または公開キーを確認します。 存在しない場合は、アプリケーションの **[概要]** ページにある **[削除]** ボタンを選択して、アプリケーションを安全に削除できます。 こうれにより、[オフライン バックアップ プロセスのためのサーバーの準備](#prepare-the-server-for-the-offline-backup-process)の手順を再試行して、以下の手順をスキップすることができます。 それ以外の場合は、オフライン バックアップを構成する DPM インスタンスまたは Azure Backup サーバーから、次の手順を続行します。
1. **[スタート]** – **[実行]** で、「*Certlm.msc*」と入力します。 **[証明書 - ローカル コンピューター]** ウィンドウで、 **[証明書 – ローカル コンピューター]**  >  **[個人]** タブの順に選択します。`CB_AzureADCertforOfflineSeeding_<ResourceId>` という名前の証明書を探します。
1. 証明書を選択し、 **[すべてのタスク]** を右クリックして、証明書を .cer 形式として秘密キーなしで **[エクスポート]** を選択します。
1. Azure portal で Azure オフライン バックアップ アプリケーションに移動します。
1. **[管理]**  >  **[証明書とシークレット]**  >  **[証明書のアップロード]** の順に選択します。 前の手順でエクスポートした証明書をアップロードします。

    ![証明書をアップロードする](./media/offline-backup-dpm-mabs-previous-versions/upload-certificate.png)

1. サーバーの実行ウィンドウに「**regedit**」と入力して、レジストリを開きます。
1. レジストリ エントリ *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider* に移動します。
1. **CloudBackupProvider** を右クリックして、`AzureADAppCertThumbprint_<Azure User Id>` という名前の新しい文字列値を追加します。

    >[!NOTE]
    > Azure ユーザー ID を見つけるには、次の手順のいずれかを実行します。
    >
    >* Azure に接続された PowerShell から、`Get-AzureRmADUser -UserPrincipalName "Account Holder's email as appears in the portal"` コマンドを実行します。
    >* `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;` レジストリ パスにアクセスします。

1. 前の手順で追加した文字列を右クリックして、 **[変更]** を選択します。 値として、手順 7 でエクスポートした証明書の拇印を入力します。 **[OK]** をクリックします。
1. 拇印の値を取得するには、証明書をダブルクリックします。 **[詳細]** タブを選択し、[拇印] フィールドが表示されるまで下にスクロールします。 **[拇印]** を選択して、値をコピーします。

    ![[拇印] フィールドの値をコピーする](./media/offline-backup-dpm-mabs-previous-versions/thumbprint-field.png)

1. 「[ワークフロー](#workflow)」セクションに進んで、オフライン バックアップ プロセスを続行します。

## <a name="workflow"></a>ワークフロー

このセクションの情報は、オフライン バックアップ ワークフローを完了するためのものなので、このデータを Azure データセンターに配信したり、Azure Storage にアップロードしたりできます。 インポート サービスやプロセスの他の側面について質問がある場合は、上記で参照した[サービスの概要に関するページ](../import-export/storage-import-export-service.md)を参照してください。

### <a name="initiate-offline-backup"></a>オフライン バックアップを開始する

1. バックアップをスケジュールすると、Windows Server、Windows クライアント、または DPM に次のページが表示されます。

    ![インポート ページ](./media/offline-backup-dpm-mabs-previous-versions/offline-backup-screen-inputs.png)

    DPM の対応するページを次に示します。 <br/>

    ![DPM および Azure Backup Server のインポート ページ](./media/offline-backup-dpm-mabs-previous-versions/dpm-offline.png)

    入力するボックスは次のとおりです。

   * **ステージングの場所**:初回バックアップ コピーが書き込まれる一時的なストレージの場所。 ステージング場所には、ネットワーク共有またはローカル コンピューター上の場所を使用できます。 コピー用コンピューターとソース コンピューターが異なる場合は、ステージング場所の完全なネットワーク パスを指定します。
   * **Azure 発行設定**:発行設定ファイルへのローカル パス。
   * **Azure インポート ジョブ名**:ディスクを使用して Azure に送信されたデータを、Azure Import/Export サービスと Azure Backup が追跡するときに使用する一意の名前。
   * **Azure サブスクリプション ID**:Azure 発行設定ファイルをダウンロードしたサブスクリプションの Azure サブスクリプション ID。
   * **Azure Storage アカウント**:Azure 発行設定ファイルに関連付けられる Azure サブスクリプションのストレージ アカウントの名前。
   * **Azure ストレージ コンテナー**:バックアップ データをインポートする先の Azure ストレージアカウントのストレージ BLOB の名前。

   **ステージング場所** と、指定した **Azure Import ジョブの名前** を保存します。 ディスクを準備する必要があります。

1. ワークフローを終了します。 オフラインのバックアップ コピーを開始するには、Azure Backup エージェント管理コンソールで **[今すぐバックアップ]** を選択します。 初期バックアップが、この手順の一部としてステージング領域に書き込まれます。

    ![今すぐバックアップ](./media/offline-backup-dpm-mabs-previous-versions/backup-now.png)

    DPM または Azure Backup Server で対応するワークフローを完了するには、 **[保護グループ]** を右クリックします。 **[回復ポイントの作成]** オプションを選択します。 次に、 **[オンライン保護]** オプションを選択します。

    ![DPM と MABS の今すぐバックアップ](./media/offline-backup-dpm-mabs-previous-versions/dpm-backup-now.png)

    操作が完了すると、ステージング場所を使用してディスクを準備できるようになります。

    ![Backup progress](./media/offline-backup-dpm-mabs-previous-versions/op-backup-now.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA ドライブの準備と Azure への発送

*AzureOfflineBackupDiskPrep* ユーティリティを使用して、最寄りの Azure データセンターに送る SATA ドライブを準備します。 このユーティリティは、次のパスの Recovery Services エージェントのインストール ディレクトリにあります。

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. このディレクトリに移動し、*AzureOfflineBackupDiskPrep* ディレクトリを、準備する SATA ドライブが接続されているコピー用コンピューターにコピーします。 次のことを確認します。

   * コピー用コンピューターが、「オフライン バックアップの開始」セクションのワークフロー中に指定されたのと同じネットワーク パスを使用して、オフライン シード処理ワークフローのステージング場所にアクセスできる。
   * コピー用コンピューターで BitLocker が有効になっている。
   * コピー用コンピューターで Azure Portal にアクセスできる。 必要に応じて、コピー用コンピューターをソース コンピューターと同じにすることができます。

     > [!IMPORTANT]
     > ソース コンピューターが仮想マシンの場合は、別の物理サーバーまたはクライアント マシンをコピー用コンピューターとして使用する必要があります。

1. コピー用コンピューターで、*AzureOfflineBackupDiskPrep* ユーティリティのディレクトリを現在のディレクトリとして使用して管理者特権でのコマンド プロンプトを開きます。 次のコマンドを実行します。

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | パラメーター | 説明 |
    | --- | --- |
    | s:&lt;*ステージング場所のパス*&gt; |必須の入力。「オフライン バックアップの開始」セクションのワークフローで入力したステージング場所へのパスを指定します。 |
    | p:&lt;*PublishSettingsFile へのパス*&gt; |省略可能。「オフライン バックアップの開始」セクションのワークフローで入力した Azure 発行設定ファイルへのパスを指定します。 |

    > [!NOTE]
    > コピー用コンピューターとソース コンピューターが異なる場合は、&lt;Azure PublishSettingsFile へのパス&gt;の値を必ず入力する必要があります。
    >
    >

    コマンドを実行すると、準備する必要があるドライブに対応する Azure インポート ジョブを選択するよう求められます。 指定されたステージング場所に関連付けられているインポート ジョブが 1 つのみの場合は、このようなページが表示されます。

    ![Azure Disk Preparation ツールの入力](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-drive-input.png) <br/>

1. Azure への転送を準備する、マウント済みディスクのドライブ文字を入力します (末尾のコロンを除く)。 メッセージが表示されたら、ドライブをフォーマットすることを確認します。

    これにより、ディスクとバックアップ データのコピーの準備が開始されます。 指定したディスクにバックアップ データを格納するための領域が十分にない場合は、ツールによりメッセージが表示されます。その場合は、追加のディスクを接続する必要があります。 <br/>

    ツールが正常に完了したら、指定した 1 つ以上のディスクを Azure に発送する準備は完了です。 また、「オフライン バックアップの開始」セクションのワークフローで指定した名前のインポート ジョブも Azure に作成されます。 最後に、ディスクの送付先である Azure データセンターの住所が表示されます。

    ![Azure ディスクの準備完了](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-success.png)<br/>

1. コマンドの実行の最後にも、発送情報を更新するオプションが表示されます。

    ![発送情報の更新オプション](./media/offline-backup-dpm-mabs-previous-versions/update-shipping-utility.png)<br/>

1. すぐに詳細を入力することができます。 このツールでは、一連の入力に関連するプロセスについて説明します。 追跡番号やその他の発送関連の詳細などの情報がない場合は、セッションを終了することができます。 発送の詳細を後で更新する手順をこの記事で示します。

1. ツールから提供されたアドレスにディスクを発送します。 後で参照できるよう、追跡番号を控えておきます。

   > [!IMPORTANT]
   > 2 つの Azure インポート ジョブが同じ追跡番号を持つことはできません。 単一の Azure インポート ジョブでユーティリティによって準備されたドライブが単一のパッケージで発送されることと、パッケージに 1 つの一意の追跡番号があることを確認します。 単一パッケージ内の異なる Azure インポート ジョブの一部として準備されたドライブは結合しないでください。

1. 追跡番号情報がある場合は、インポート ジョブの完了を待機しているソース コンピューターに移動します。 管理者特権でのコマンド プロンプトで *AzureOfflineBackupDiskPrep* ユーティリティのディレクトリを現在のディレクトリとして使用して次のコマンドを実行します。

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   必要に応じて、*AzureOfflineBackupDiskPrep* ユーティリティのディレクトリを現在のディレクトリとして使用して、コピー用コンピューターなどの別のコンピューターから次のコマンドを実行することもできます。

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | パラメーター | 説明 |
    | --- | --- |
    | u: | 必須の入力。Azure インポート ジョブの発送詳細の更新に使用されます。 |
    | s:&lt;*ステージング場所のパス*&gt; | 必須の入力。コマンドがソース コンピューターで実行されていない場合に使用されます。 「オフライン バックアップの開始」セクションのワークフローで入力したステージング場所へのパスを指定します。 |
    | p:&lt;*PublishSettingsFile へのパス*&gt; | 必須の入力。コマンドがソース コンピューターで実行されていない場合に使用されます。 「オフライン バックアップの開始」セクションのワークフローで入力した Azure 発行設定ファイルへのパスを指定するために使用されます。 |

    ユーティリティは、ソース コンピューターが待機しているインポート ジョブまたはコマンドが別のコンピューターで実行されているときにステージング場所に関連付けられているインポート ジョブを自動的に検出します。 一連の入力を通じて発送情報を更新するオプションが提供されます。

    ![発送情報の入力](./media/offline-backup-dpm-mabs-previous-versions/shipping-inputs.png)<br/>

1. すべての入力を指定したら、詳細情報を十分に確認し、「**yes**」と入力して指定した発送情報をコミットします。

    ![発送情報の確認](./media/offline-backup-dpm-mabs-previous-versions/review-shipping-information.png)<br/>

1. 発送情報が正常に更新されると、入力した発送情報が保存されているローカルの場所がユーティリティによって提供されます。

    ![発送情報の保存](./media/offline-backup-dpm-mabs-previous-versions/storing-shipping-information.png)<br/>

   > [!IMPORTANT]
   > *AzureOfflineBackupDiskPrep* ユーティリティを使用して発送情報を指定してから 2 週間以内にドライブが Azure データセンターに届くようにしてください。 そうしないと、ドライブが処理されないことがあります。

前の手順を完了すると、Azure データセンターはドライブを受け取る準備ができ、さらにドライブからユーザーが作成したクラシック タイプの Azure ストレージ アカウントにバックアップ データを転送する処理を行います。

### <a name="time-to-process-the-drives"></a>ドライブの処理の所要時間

Azure インポート ジョブの処理にかかる時間は状況に応じて異なります。 処理時間は、発送時刻、ジョブの種類、コピーするデータの種類とサイズ、提供されるディスクのサイズなどの要因によって異なります。 Azure Import/Export サービスには SLA はありません。 ディスクの到着後 7 日から 10 日以内に Azure ストレージ アカウントへのバックアップ データ コピーが完了するよう努力しております。 次のセクションでは、Azure インポート ジョブの状態を監視する方法について詳しく説明します。

### <a name="monitor-azure-import-job-status"></a>Azure インポート ジョブの状態の監視

ストレージ アカウントにコピーするドライブが輸送中または Azure データセンターに保管されている間、ソース コンピューター上の Azure Backup エージェント、DPM または MABS コンソールには、スケジュールされたバックアップについて次のジョブ状態が表示されます。

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

インポート ジョブの状態を確認するには、次の操作を行います。

1. ソース コンピューターで管理者特権でのコマンド プロンプトを開き、次のコマンドを実行します。

     `AzureOfflineBackupDiskPrep.exe u:`

1. 出力に、インポート ジョブの現在の状態が示されます。

    ![インポート ジョブの状態の確認](./media/offline-backup-dpm-mabs-previous-versions/import-job-status-reporting.png)<br/>

Azure インポート ジョブの各種の状態について詳しくは、「[Azure Import/Export ジョブの状態を表示する](../import-export/storage-import-export-view-drive-status.md)」を参照してください。

### <a name="finish-the-workflow"></a>ワークフローの完了

インポート ジョブが完了すると、初回バックアップ データをストレージ アカウントで使用できます。 次回のスケジュール バックアップでは、Azure Backup はストレージ アカウントのデータのコンテンツを Recovery Services コンテナーにコピーします。

   ![Recovery Services コンテナーへのデータのコピー](./media/offline-backup-dpm-mabs-previous-versions/copying-from-storage-account-to-azure-backup.png)<br/>

次回のスケジュール バックアップでは、Azure Backup は、初回バックアップ コピーに対する増分バックアップを実行します。

## <a name="next-steps"></a>次のステップ

* Azure Import/Export サービス ワークフローについて質問がある場合は、[Microsoft Azure Import/Export サービスを使用した BLOB ストレージへのデータの転送](../import-export/storage-import-export-service.md)に関するページを参照してください。

---
title: Azure Data Box を使用したオフライン バックアップ
description: Azure Data Box を使用して、MARS エージェントから Recovery Services コンテナーに、大規模な初期バックアップ データをオフラインでシード処理する方法について説明します。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: e789b6c9f4ff2e8cd168e6b5c138d423911d4743
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96752585"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Azure Data Box を使用した Azure Backup のオフライン バックアップ

[Azure Data Box](../databox/data-box-overview.md) を使うと、Microsoft Azure Recovery Services (MARS) の大規模な初期バックアップをオフラインで (ネットワークを使用せずに) Recovery Services コンテナーにシードできます。 このプロセスにより、待機時間の長いネットワークを介して大量のバックアップ データをオンラインで移動する場合に消費される時間とネットワーク帯域幅の両方を節約することができます。 この拡張機能は、現在プレビュー段階です。 Azure Data Box に基づくオフライン バックアップには、[Azure Import/Export サービスに基づくオフライン バックアップ](./backup-azure-backup-import-export.md)に比べて、次の 2 つの利点があります。

- Azure と互換性のある独自のディスクとコネクタを用意する必要がありません。 Azure Data Box では、選択された [Data Box SKU](https://azure.microsoft.com/services/databox/data/) に関連するディスクを発送します。
- Azure Backup (MARS エージェント) は、Azure Data Box のサポートされている SKU に直接バックアップ データを書き込むことができます。 この機能を使うと、初期バックアップ データのステージング場所をプロビジョニングする必要がなくなります。 また、そのデータをフォーマットしてディスクにコピーするためのユーティリティも必要ありません。

## <a name="azure-data-box-with-the-mars-agent"></a>MARS エージェントを使用した Azure Data Box

この記事では、Azure Data Box を使用して、MARS エージェントから Recovery Services コンテナーに、大規模な初期バックアップ データをオフラインでシード処理する方法について説明します。

## <a name="supported-platforms"></a>サポートされているプラットフォーム

Azure Data Box を使用して MARS エージェントからデータをシード処理するプロセスは、次の Windows SKU でサポートされています。

| **OS**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **ワークステーション**                        |                                                              |
| Windows 10 64 ビット                     | Enterprise、Pro、Home                                       |
| Windows 8.1 64 ビット                    | Enterprise、Pro                                             |
| Windows 8 64 ビット                      | Enterprise、Pro                                             |
| Windows 7 64 ビット                      | Ultimate、Enterprise、Professional、Home Premium、Home Basic、Starter |
| **[サーバー]**                             |                                                              |
| Windows Server 2019 64 ビット            | Standard、Datacenter、Essentials                            |
| Windows Server 2016 64 ビット            | Standard、Datacenter、Essentials                            |
| Windows Server 2012 R2 64 ビット         | Standard、Datacenter、Foundation                            |
| Windows Server 2012 64 ビット            | Datacenter、Foundation、Standard                            |
| Windows Storage Server 2016 64 ビット    | Standard、Workgroup                                         |
| Windows Storage Server 2012 R2 64 ビット | Standard、Workgroup、Essential                              |
| Windows Storage Server 2012 64 ビット    | Standard、Workgroup                                         |
| Windows Server 2008 R2 SP1 64 ビット     | Standard、Enterprise、Datacenter、Foundation                |
| Windows Server 2008 SP2 64 ビット        | Standard、Enterprise、Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>バックアップ データのサイズとサポートされている Data Box SKU

| サーバー 1 台あたりのバックアップ データ サイズ (MARS による圧縮後)* | サポートされている Azure Data Box SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 7\.2 TB 以下                                                    | [Azure Data Box ディスク](../databox/data-box-disk-overview.md) |
| 7\.2 TB 超かつ 80 TB 以下**                                      | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

\* 一般的な圧縮率は 10% から 20% の間になります <br>
** 1 台の MARS サーバーの初期バックアップ データが 80 TB を超えると予想される場合は、[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) にご連絡ください。

>[!IMPORTANT]
>1 台のサーバーの初期バックアップ データは、1 つの Azure Data Box インスタンスまたは Azure Data Box ディスク内に含まれている必要があり、同じまたは異なる SKU の複数のデバイス間で共有することはできません。 ただし、Azure Data Box デバイスには、複数のサーバーからの初期バックアップを含めることができます。

## <a name="prerequisites"></a>前提条件

### <a name="azure-subscription-and-required-permissions"></a>Azure サブスクリプションと必要なアクセス許可

- このプロセスには、Azure サブスクリプションが必要です。
- このプロセスでは、オフライン バックアップ ポリシーを実行するよう指定されたユーザーが Azure サブスクリプションの "所有者" である必要があります。
- Data Box ジョブと、(データをシード処理する必要がある) Recovery Services コンテナーは、同じサブスクリプション内に存在する必要があります。
- Azure Data Box ジョブと Recovery Services コンテナーに関連するターゲット ストレージ アカウントは、同じリージョンに配置することをお勧めします。 ただし、これは必須ではありません。

### <a name="get-azure-powershell-370"></a>Azure PowerShell 3.7.0 を入手する

*これは、このプロセスにとって最も重要な前提条件です*。 Azure PowerShell バージョン 3.7.0 をインストールする前に、次のチェックを実行します。

#### <a name="step-1-check-the-powershell-version"></a>手順 1:PowerShell のバージョンを確認する

1. Windows PowerShell を開き、次のコマンドを実行します。

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. 出力に 3.7.0 よりも後のバージョンが表示される場合は、「手順 2」を実行します。 それ以外の場合は「手順 3」に進みます。

#### <a name="step-2-uninstall-the-powershell-version"></a>手順 2:PowerShell のバージョンをアンインストールする

PowerShell の現在のバージョンをアンインストールします。

1. PowerShell で次のコマンドを実行して、依存モジュールを削除します。

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. すべての依存モジュールが正常に削除されたことを確認するには、次のコマンドを実行します。

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>手順 3:PowerShell バージョン 3.7.0 をインストールする

AzureRM モジュールが存在しないことを確認したら、次のいずれかの方法を使用してバージョン 3.7.0 をインストールします。

- GitHub から[このリンク](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)を使用します。

または、次のことができます。

- PowerShell ウィンドウで次のコマンドを実行します。

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell は、msi ファイルを使用してインストールされている可能性もあります。 それを削除するには、コントロール パネルの **[プログラムのアンインストール]** オプションを使用してアンインストールします。

### <a name="order-and-receive-the-data-box-device"></a>Data Box デバイスを注文して受け取る

MARS と Azure Data Box を使用したオフライン バックアップ プロセスでは、MARS エージェントを使用してオフライン バックアップをトリガーする前に、Data Box デバイスが配送完了状態になっている必要があります。 要件に最も適した SKU を注文するには、「[バックアップ データ サイズとサポートされる Data Box SKU](#backup-data-size-and-supported-data-box-skus)」を参照してください。 「[チュートリアル: Azure Data Box Disk を注文する](../databox/data-box-disk-deploy-ordered.md)」の手順に従って Data Box デバイスを注文して受け取ります。

> [!IMPORTANT]
> **[アカウントの種類]** として *[BlobStorage]* を選択しないでください。 MARS エージェントには、ページ BLOB をサポートするアカウントが必要ですが、これは、 *[BlobStorage]* が選択されているとサポートされません。 Azure Data Box ジョブのターゲット ストレージ アカウントを作成するときに **[アカウントの種類]** として **[Storage V2 (general purpose v2)]\(Storage V2 (汎用 V2)\)** を選択します。

![インスタンスの詳細でアカウントの種類を選択する](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>MARS エージェントのインストールと設定

1. 以前にインストールした MARS エージェントをすべてアンインストールします。
1. [この Web サイト](https://aka.ms/azurebackup_agent)から最新の MARS エージェントをダウンロードします。
1. *MARSAgentInstaller.exe* を実行し、[エージェントをインストールしてバックアップの格納先の Recovery Services コンテナーに登録する](./install-mars-agent.md#install-and-register-the-agent)手順 "*のみ*" を実行します。

   > [!NOTE]
   > Recovery Services コンテナーは、Azure Data Box ジョブと同じサブスクリプションに含まれている必要があります。

   エージェントが Recovery Services コンテナーに登録されたら、次のセクションの手順に従います。

## <a name="set-up-azure-data-box-devices"></a>Azure Data Box デバイスを設定する

注文した Azure Data Box SKU に応じて、次の該当するセクションで説明されている手順を実行します。 この手順では、MARS エージェントで初期バックアップ データを識別して転送できるように、Data Box デバイスを設定して準備する方法を示します。

### <a name="set-up-azure-data-box-disks"></a>Azure Data Box ディスクを設定する

1 つ以上の Azure Data Box ディスク (それぞれ最大 8 TB) を注文した場合は、ここに記載されている手順に従って、[Data Box ディスクを開梱、接続、ロック解除](../databox/data-box-disk-deploy-set-up.md)してください。

>[!NOTE]
>MARS エージェントがあるサーバーに USB ポートがない可能性があります。 そのような状況では、Azure Data Box ディスクを別のサーバーまたはクライアントに接続し、デバイスのルートをネットワーク共有として公開することができます。

### <a name="set-up-azure-data-box"></a>Azure Data Box を設定する

Azure Data Box インスタンス (最大 100 TB) を注文した場合は、この手順に従って [Data Box インスタンスを設定](../databox/data-box-deploy-set-up.md)します。

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Azure Data Box インスタンスをローカル システムとしてマウントする

MARS エージェントはローカル システム コンテキストで動作するため、Azure Data Box インスタンスが接続されているマウント パスに提供されるのと同じレベルの特権が必要です。

NFS プロトコルを使用して Data Box デバイスをローカル システムとしてマウントできることを確認するには:

1. MARS エージェントがインストールされている Windows サーバーで、NFS クライアント機能を有効にします。 代替ソース *WIM:D:\Sources\Install.wim:4* を指定します。
1. MARS Agent がインストールされているサーバーに [Sysinternals](/sysinternals/downloads/psexec) ページから PsExec をダウンロードします。
1. 管理者特権でのコマンド プロンプトを開き、*PSExec.exe* を含むディレクトリを現在のディレクトリとして次のコマンドを実行します。

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   上のコマンドによって開かれるコマンド ウィンドウは、ローカル システム コンテキストにあります。 このコマンド ウィンドウを使用して、Azure ページ BLOB 共有をネットワーク ドライブとして Windows サーバーにマウントする手順を実行します。
1. 「[Data Box に接続する](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box)」の手順に従って、NFS 経由で MARS エージェントがインストールされたサーバーを Data Box デバイスに接続します。 ローカル システムのコマンド プロンプトで次のコマンドを実行して、Azure ページ BLOB 共有をマウントします。

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   共有がマウントされたら、サーバーから X: にアクセスできるかどうかを確認します。 できる場合は、この記事の次のセクションに進んでください。

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Azure Data Box デバイスに初期バックアップ データを転送する

1. お使いのサーバーで **Microsoft Azure Backup** アプリケーションを開きます。
1. **[操作]** ペインで、 **[バックアップのスケジュール]** を選択します。

    ![[バックアップのスケジュール] を選択する](./media/offline-backup-azure-data-box/schedule-backup.png)

1. **バックアップのスケジュール ウィザード** の手順に従います。

1. **[項目の追加]** ボタンを選択して項目を追加します。 項目の合計サイズを、[注文して受け取った Azure Data Box SKU でサポートされているサイズの制限](#backup-data-size-and-supported-data-box-skus)内に収めます。

    ![項目をバックアップに追加する](./media/offline-backup-azure-data-box/add-items.png)

1. **[File およびフォルダー]** と **[システム状態]** の適切なバックアップ スケジュールとアイテム保持ポリシーを選択します。 システム状態は Windows サーバーにのみ適用され、Windows クライアントには適用されません。
1. ウィザードの **[初期バックアップの種類の選択 (ファイルとフォルダー)]** ページで、オプション **[Transfer using Microsoft Azure Data Box disks]\(Microsoft Azure Data Box ディスクを使用して転送する\)** を選択し、 **[次へ]** を選択します。

    ![初期バックアップの種類を選択する](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. メッセージが表示されたら、Azure サブスクリプションの所有者アクセス権があるユーザー資格情報を使用して Azure にサインインします。 この操作に成功すると、このようなページが表示されます。

    ![リソースを作成し、必要なアクセス許可を適用する](./media/offline-backup-azure-data-box/creating-resources.png)

   次に、MARS エージェントによって、配送完了状態にあるサブスクリプション内の Data Box ジョブがフェッチされます。

    ![サブスクリプション ID の Data Box ジョブをフェッチする](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Data Box ディスクを開梱し、接続し、ロックを解除した、正しい Data Box の注文を選択します。 **[次へ]** を選択します。

    ![Data Box の注文を選択する](./media/offline-backup-azure-data-box/select-databox-order.png)

1. **[Data Box デバイスの検出]** ページで **[デバイスの検出]** を選択します。 この操作により、ローカルに接続された Azure Data Box ディスクが MARS エージェントによってスキャンされ、検出されます。

    ![Data Box デバイスの検出](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Azure Data Box インスタンスをネットワーク共有として接続した場合 (USB ポートを使用できないため、または 100 TB の Data Box デバイスを注文してマウントしたため)、最初の検出は失敗します。 Data Box デバイスのネットワーク パスを入力するオプションが表示されます。

    ![ネットワーク パスを入力する](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Azure Data Box ディスクのルート ディレクトリへのネットワーク パスを指定します。 このディレクトリには、*PageBlob* という名前のディレクトリが含まれている必要があります。
    >
    >![Azure Data Box ディスクのルート ディレクトリ](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >たとえば、ディスクのパスが `\\mydomain\myserver\disk1\` で、*disk1* に *PageBlob* というディレクトリが含まれている場合、MARS エージェント ウィザード ページで入力するパスは `\\mydomain\myserver\disk1\` になります。
    >
    >[Azure Data Box 100 TB デバイスを設定](#set-up-azure-data-box-devices)した場合は、デバイスのネットワーク パスとして「`\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`」と入力します。

1. **[次へ]** を選択し、次のページで **[完了]** を選択して、Azure Data Box を使用したオフライン バックアップの構成を含むバックアップとアイテム保持ポリシーを保存します。

   次のページで、ポリシーが正常に保存されていることを確認します。

    ![ポリシーが正常に保存された](./media/offline-backup-azure-data-box/policy-saved.png)

1. 前のページで **[閉じる]** を選択します。

1. MARS エージェント コンソールの **[アクション]** ペインで、 **[今すぐバックアップ]** を選択します。 ウィザード ページの **[バックアップ]** を選択します。

    ![今すぐバックアップ ウィザード](./media/offline-backup-azure-data-box/backup-now.png)

MARS エージェントによって、選択したデータの Azure Data Box デバイスへのバックアップが開始されます。 このプロセスには数時間から数日かかることがあります。 この時間の長さは、ファイル数と、MARS エージェントがインストールされたサーバーと Azure Data Box ディスク間の接続速度によって変わります。

データのバックアップが完了すると、MARS エージェントにこのようなページが表示されます。

![バックアップの進行状況の表示](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>バックアップ後の手順

このセクションでは、Azure Data Box ディスクへのデータのバックアップが正常に完了した後に実行する手順について説明します。

- こちらの記事の手順に従って、[Azure Data Box Disk を Azure に発送](../databox/data-box-disk-deploy-picked-up.md)します。 Azure Data Box の 100 TB のデバイスを使用した場合は、こちらの手順に従って [Azure Data Box デバイスを Azure に発送](../databox/data-box-deploy-picked-up.md)してください。

- Azure portal で [Data Box ジョブを監視](../databox/data-box-disk-deploy-upload-verify.md)します。 Azure Data Box ジョブが完了すると、MARS エージェントによって、次回のスケジュールされたバックアップ時に、ストレージ アカウントから Recovery Services コンテナーにデータが自動的に移動されます。 その後、回復ポイントが正常に作成されると、そのバックアップ ジョブは "*完了したジョブ*" としてマークされます。

    >[!NOTE]
    >MARS エージェントにより、ポリシー作成時にスケジュールされた時刻にバックアップがトリガーされます。 これらのジョブでは、ジョブが完了するまで "Azure Data Box ジョブの完了を待機中" というフラグが立ちます。

- MARS エージェントによって最初のバックアップに対応する回復ポイントが正常に作成されたら、Azure Data Box ジョブに関連するストレージ アカウントまたは特定のコンテンツを削除できます。

## <a name="troubleshooting"></a>トラブルシューティング

Microsoft Azure Recovery Services (MARS) エージェントによって、テナントに Azure Active Directory (Azure AD) アプリケーションが自動的に作成されます。 このアプリケーションには、オフライン シード処理ポリシーを構成しているときに作成およびアップロードされる認証用の証明書が必要です。 Azure PowerShell を使用して証明書を作成し、Azure AD アプリケーションにアップロードします。

### <a name="problem"></a>問題

オフライン バックアップを構成すると、Azure PowerShell コマンドレットのバグが原因で問題が発生する場合があります。 MAB エージェントによって作成された同じ Azure AD アプリケーションに複数の証明書を追加できない場合があります。 この問題は、同じサーバーまたは異なるサーバーにオフライン シード処理ポリシーを構成した場合に影響します。

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>この特定の根本原因によってこの問題が引き起こされたかどうかを確認する

問題が前述のものと同じかどうかを確認するには、次のいずれかの手順を実行します。

#### <a name="step-1-of-verification"></a>検証の手順 1

オフライン バックアップを構成したときに、MAB コンソールに次のエラー メッセージが表示されるかどうかを確認します。

![Unable to create Offline Backup policy for the current Azure account (現在の Azure アカウントのオフライン バックアップ ポリシーを作成できません)](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>検証の手順 2

1. インストール パスの **Temp** フォルダーを開きます。 既定の一時フォルダーのパスは、*C:\Program Files\Microsoft Azure Recovery Services Agent\Temp* です。*CBUICurr* ファイルを探し、そのファイルを開きます。

1. *CBUICurr* ファイルで、最後の行までスクロールし、エラー メッセージ `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` の問題と同じかどうかを確認します。

### <a name="workaround"></a>回避策

この問題を解決する回避策として、次の手順を実行し、ポリシーの構成を再試行します。

#### <a name="step-1-of-workaround"></a>回避策の手順 1

Data Box ジョブが作成されるサブスクリプションで、管理者アクセス権がある別のアカウントを使用して、MAB UI に表示される PowerShell にサインインします。

#### <a name="step-2-of-workaround"></a>回避策の手順 2

他のサーバーでオフライン シード処理が構成されておらず、他のサーバーが `AzureOfflineBackup_<Azure User Id>` アプリケーションに依存していない場合は、このアプリケーションを削除します。 **[Azure portal]**  >  **[Azure Active Directory]**  >  **[アプリの登録]** を選択します。

>[!NOTE]
> アプリケーション `AzureOfflineBackup_<Azure User Id>` に他のオフライン シード処理が構成されていないこと、およびこのアプリケーションに依存している他のサーバーがないことを確認します。 **[公開鍵]** セクションの **[設定]**  >  **[キー]** に移動します。 他の公開キーは追加しないでください。 参考のために次のスクリーンショットをご覧ください。
>
>![公開キー](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>手順 3.

オフライン バックアップを構成しようとしているサーバーから、次の操作を実行します。

1. **[Manage computer certificate application]\(コンピューター証明書の管理アプリケーション\)**  >  **[個人用]** タブに移動し、`CB_AzureADCertforOfflineSeeding_<ResourceId>` という名前の証明書を探します。

2. 証明書を選択し、 **[すべてのタスク]** を右クリックして、証明書を .cer 形式として秘密キーなしで **[エクスポート]** を選択します。

3. 手順 2 に記載されている Azure オフライン バックアップ アプリケーションにアクセスします。 **[設定]**  >  **[キー]**  >  **[公開キーのアップロード]** を選択します。 前の手順でエクスポートした証明書をアップロードします。

    ![公開キーのアップロード](./media/offline-backup-azure-data-box/upload-public-key.png)

4. サーバーの実行ウィンドウに「**regedit**」と入力して、レジストリを開きます。

5. レジストリ *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider* に移動します。 **CloudBackupProvider** を右クリックして、`AzureADAppCertThumbprint_<Azure User Id>` という名前の新しい文字列値を追加します。

    >[!NOTE]
    > Azure ユーザー ID を取得するには、次のいずれかの操作を実行します。
    >
    >- Azure に接続された PowerShell から、`Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` コマンドを実行します。
    > - *CurrentUserId* という名前のレジストリ パス `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` に移動します。

6. 前の手順で追加した文字列を右クリックして、 **[変更]** を選択します。 値として、手順 2 でエクスポートした証明書の拇印を入力します。 **[OK]** を選択します。

7. 拇印の値を取得するには、証明書をダブルクリックします。 **[詳細]** タブを選択し、[拇印] フィールドが表示されるまで下にスクロールします。 **[拇印]** を選択して、値をコピーします。

    ![証明書の [サムプリント] フィールド](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>疑問がある場合

発生した問題に関する質問や説明については、[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) にお問い合わせください。

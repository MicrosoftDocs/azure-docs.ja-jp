---
title: DPM および MABS の Azure Data Box を使用したオフライン バックアップ
description: Azure Data Box を使用して、DPM および MABS からオフラインで初期バックアップ データをシード処理することができます。
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 1cfd9131099ad6a8ccd3d43e93f3d97641514f03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96752551"
---
# <a name="offline-seeding-using-azure-data-box-for-dpm-and-mabs-preview"></a>DPM および MABS の Azure Data Box を使用したオフライン シード処理 (プレビュー)

> [!NOTE]
> この機能は、Data Protection Manager (DPM) 2019 UR2 以降に適用されます。<br><br>
> この機能は現在、Microsoft Azure Backup Server (MABS) でプレビュー段階にあります。 MABS でのオフライン シード処理に Azure Data Box を使用することに関心をお持ちの場合は、[systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) にご連絡ください。

この記事では、Azure Data Box を使用して、DPM および MABS から Azure Recovery Services コンテナーに初期バックアップ データをオフラインでシード処理する方法について説明します。

[Azure Data Box](../databox/data-box-overview.md) を使うと、大規模な初期 DPM または MABS バックアップをオフラインで (ネットワークを使用せずに) Recovery Services コンテナーにシード処理できます。 このプロセスにより、待機時間の長いネットワークを介して大量のバックアップ データをオンラインで移動する場合に消費される時間とネットワーク帯域幅の両方を節約することができます。 現在、この機能はプレビュー段階にあります。

Azure Data Box に基づくオフライン バックアップには、[Azure Import/Export サービスに基づくオフライン バックアップ](backup-azure-backup-server-import-export.md)に比べて、次の 2 つの利点があります。

- Azure と互換性のある独自のディスクとコネクタを調達する必要はありません。 Azure Data Box では、選択された [Data Box SKU](https://azure.microsoft.com/services/databox/data/) に関連するディスクを発送します。

- Azure Backup (MARS エージェント) は、Azure Data Box のサポートされている SKU に直接バックアップ データを書き込むことができます。 この機能を使うと、初期バックアップ データのステージング場所をプロビジョニングする必要がなくなります。 また、そのデータをフォーマットしてディスクにコピーするためのユーティリティも必要ありません。

## <a name="supported-platforms"></a>サポートされているプラットフォーム

次のプラットフォームがサポートされています。

- Windows Server 2019 64 ビット (Standard、Datacenter、Essentials)
- Windows Server 2016 64 ビット (Standard、Datacenter、Essentials)

## <a name="backup-data-size-and-supported-data-box-skus"></a>バックアップ データのサイズとサポートされている Data Box SKU

次の Data Box SKU がサポートされています。

| サーバー 1 台あたりのバックアップ データ サイズ (MARS による圧縮後)\* | サポートされている Azure Data Box SKU |
| --- | --- |
| \<= 7.2 TB | [Azure Data Box Disk](../databox/data-box-disk-overview.md) |
| > 7.2 TB かつ <= 80 TB\*\* | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

\* 一般的な圧縮率は 10% から 20% の間で変化します <br>
\*\* 1 つのデータ ソースの初期バックアップ データが 80 TB を超えると予想される場合は、[SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) にご連絡ください。

> [!IMPORTANT]
> 1 つのデータ ソースの初期バックアップ データは、1 つの Azure Data Box または Azure Data Box Disk 内に含まれている必要があり、同じまたは異なる SKU の複数のデバイス間で共有することはできません。 しかし、Azure Data Box には、複数のデータ ソースからの初期バックアップが含まれている場合があります。

## <a name="before-you-begin"></a>始める前に

DPM または MABS で実行されている MARS エージェントは、[最新バージョン](https://aka.ms/azurebackup_agent) (2.0.9171.0 以降) にアップグレードする必要があります。

以下のことを確認してください。

### <a name="azure-subscription-and-required-permissions"></a>Azure サブスクリプションと必要なアクセス許可

- 有効な Azure サブスクリプション。
- オフライン バックアップ ポリシーを実行しようとしているユーザーは、Azure サブスクリプションの所有者である必要があります。
- Data Box ジョブと Recovery Services コンテナー (データのシード処理が必要) は、同じサブスクリプションで使用できる必要があります。
    > [!NOTE]
    > ターゲット ストレージ アカウントと Recovery Services コンテナーを同じリージョンに配置することをお勧めします。 しかし、これは必須ではありません。

### <a name="order-and-receive-the-data-box-device"></a>Data Box デバイスを注文して受け取る

オフライン バックアップをトリガーする前に、必要な Data Box デバイスが確実に "*配信済み*" の状態になるようにします。 要件に最適な SKU を注文するには、「[バックアップ データのサイズとサポートされている Data Box SKU](#backup-data-size-and-supported-data-box-skus)」を参照してください。 Data Box デバイスを注文して受け取るには、[こちらの記事](../databox/data-box-disk-deploy-ordered.md)の手順に従ってください。

> [!IMPORTANT]
> **[アカウントの種類]** として *[BlobStorage]* を選択しないでください。 DPM または MABS サーバーには、ページ BLOB をサポートするアカウントが必要ですが、これは、 *[BlobStorage]* が選択されているとサポートされません。 Azure Data Box ジョブのターゲット ストレージ アカウントを作成するときは、 **[アカウントの種類]** として **[Storage V2 (general purpose v2)]\(Storage V2 (汎用 V2)\)** を選択します。

![Azure Data Box をセットアップする](./media/offline-backup-azure-data-box-dpm-mabs/setup-azure-databox.png)

## <a name="setup-azure-data-box-devices"></a>Azure Data Box デバイスをセットアップする

Azure Data Box デバイスを受け取ったら、注文した Azure Data Box SKU に応じて、以下の該当するセクションの手順を行い、DPM または MABS サーバーで初期バックアップ データを特定して転送するための Data Box デバイスを設定して準備します。

### <a name="setup-azure-data-box-disk"></a>Azure Data Box Disk をセットアップする

1 つまたは複数の Azure Data Box Disk (それぞれ最大 8 TB) を注文した場合は、[こちら](../databox/data-box-disk-deploy-set-up.md)に記載されている手順に従って、Data Box Disk を開梱、接続、ロック解除してください。

> [!NOTE]
> DPM または MABS サーバーに USB ポートがない可能性があります。 そのようなシナリオでは、Azure Data Box Disk を別のサーバーまたはクライアントに接続し、デバイスのルートをネットワーク共有として公開することができます。

## <a name="setup-azure-data-box"></a>Azure Data Box をセットアップする

Azure Data Box (最大 100 TB) を注文した場合は、[こちら](../databox/data-box-deploy-set-up.md)に記載されている手順に従って Data Box を設定してください。

### <a name="mount-your-azure-data-box-as-local-system"></a>ローカル システムとして Azure Data Box をマウントする

DPM または MABS サーバーはシステム コンテキストで動作するため、Azure Data Box が接続されているマウント パスに提供されるのと同じレベルの特権が必要です。 以下の手順に従って、確実に、Data Box デバイスを NFS プロトコルを使用してローカル システムとしてマウントできるようにします。

1. DPM または MABS サーバーで NFS のクライアント機能を有効にします。
代替ソースを指定します: *WIM:D:\Sources\Install.wim:4*
2. [https://download.sysinternals.com/files/PSTools.zip](https://download.sysinternals.com/files/PSTools.zip) から DPM または MABS サーバーに **PSExec** をダウンロードします。
3. 管理者特権でのコマンド プロンプトを開き、*PSExec.exe* を含むディレクトリを現在のディレクトリとして次のコマンドを実行します。

   ```cmd
   psexec.exe  -s  -i  cmd.exe
   ```

4. 上記のコマンドの結果として開かれるコマンド ウィンドウは、ローカル システム コンテキストにあります。 このコマンド ウィンドウを使用して、Azure ページ BLOB 共有をネットワーク ドライブとして Windows サーバーにマウントする手順を行います。
5. [こちら](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box)の手順に従って、DPM または MABS サーバーを NFS 経由で Data Box デバイスに接続し、ローカル システムのコマンド プロンプトで次のコマンドを実行して、Azure ページ BLOB 共有をマウントします。

    ```cmd
    mount -o nolock \\<DeviceIPAddres>\<StorageAccountName_PageBlob X:
    ```

6. マウントしたら、ご利用のサーバーから X: にアクセスできるかどうかを確認します。 できる場合は、この記事の次のセクションに進んでください。

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Azure Data Box デバイスに初期バックアップ データを転送する

1. DPM または MABS サーバーで、[新しい保護グループを作成する](/system-center/dpm/create-dpm-protection-groups)手順に従います。 既存の保護グループにオンライン保護を追加する場合は、既存の保護グループを右クリックし、 **[オンライン保護の追加]** を選択して、**手順 8** から開始します。
2. **[グループ メンバーの選択]** ページで、バックアップするコンピューターとソースを指定します。
3. **[データの保護方法の選択]** ページで、短期および長期のバックアップの処理方法を指定します。 必ず、 **[オンライン保護を利用する]** を選択してください。

   ![新しい保護グループを作成する](./media/offline-backup-azure-data-box-dpm-mabs/create-new-protection-group.png)

4. **[短期的な目標値の選択]** ページで、ディスク上の短期記憶域へのバックアップ方法を指定します。
5. **[ディスク割り当ての確認]** ページで、保護グループに割り当てられる記憶域プールのディスク領域を確認します。
6. **[レプリカの作成方法の選択]** ページで、 **[自動でネットワーク経由]** を選択します。
7. **[整合性チェック オプションの選択]** ページで、整合性チェックを自動化する方法を選択します。
8. **[オンライン保護データの指定]** ページで、オンライン保護を有効にするメンバーを選択します。

    ![オンライン保護データの指定](./media/offline-backup-azure-data-box-dpm-mabs/specify-online-protection-data.png)

9. **[オンライン バックアップ スケジュールの指定]** ページで、Azure への増分バックアップの頻度を指定します。
10. **[オンライン保持ポリシーの指定]** ページでは、毎日、毎週、毎月、毎年のバックアップから作成される回復ポイントを Azure に保持する方法を指定します。
11. ウィザードの **[オンライン レプリケーションの選択]** 画面で、 **[Microsoft 所有のディスクを使用して転送]** オプションを選び、 **[次へ]** を選択します。

    ![初期オンライン レプリケーションを選ぶ](./media/offline-backup-azure-data-box-dpm-mabs/choose-initial-online-replication.png)

    >[!NOTE]
    > **[Microsoft 所有のディスクを使用して転送]** を選択するオプションは、その機能がプレビュー段階であるため、MABS v3 では使用できません。 MABS v3 でこの機能を使用する場合は、[systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) にご連絡ください。

12. メッセージが表示されたら、Azure サブスクリプションの所有者アクセス権があるユーザー資格情報を使用して Azure にサインインします。 サインインが成功した後、次の画面が表示されます。

    ![ログインが成功した後](./media/offline-backup-azure-data-box-dpm-mabs/after-successful-login.png)

     その後、"*配送済み*" 状態にあるサブスクリプション内の Data Box ジョブが、DPM または MABS サーバーによってフェッチされます。

     > [!NOTE]
     > 初回のサインインには、通常よりも時間がかかります。 Azure PowerShell モジュールがバックグラウンドでインストールされ、また、Azure AD アプリケーションが登録されます。
     >
     >  - 次の PowerShell モジュールがインストールされます。<br>
          - AzureRM.Profile     *5.8.3*<br>
          - AzureRM.Resources   *6.7.3*<br>
          - AzureRM.Storage     *5.2.0*<br>
          - Azure.Storage       *4.6.1*<br>
     >  - Azure AD アプリケーションは *AzureOfflineBackup_\<object GUID of the user>* として登録されます。

13. Data Box ディスクを開梱し、接続し、ロックを解除した、正しい Data Box の注文を選択します。 **[次へ]** を選択します。

    ![Data Box を選択する](./media/offline-backup-azure-data-box-dpm-mabs/select-databox.png)

14. **[Data Box の検出]** 画面で、Data Box デバイスのパスを入力してから、 **[デバイスを検出する]** を選択します。

    ![ネットワーク パスを入力する](./media/offline-backup-azure-data-box-dpm-mabs/enter-network-path.png)

    > [!IMPORTANT]
    > Azure Data Box ディスクのルート ディレクトリへのネットワーク パスを指定します。 このディレクトリには、次に示すように、*PageBlob* という名前のディレクトリが含まれている必要があります。
    >
    > ![USB ドライブ](./media/offline-backup-azure-data-box-dpm-mabs/usb-drive.png)
    >
    > たとえば、ディスクのパスが `\\mydomain\myserver\disk1\` で、*disk1* に *PageBlob* というディレクトリが含まれている場合、DPM または MABS サーバー ウィザードで指定するパスは `\\mydomain\myserver\disk1\` になります。
    > [Azure Data Box の 100 TB デバイスをセットアップ](./offline-backup-azure-data-box.md#set-up-azure-data-box)する場合は、デバイスへのネットワーク パスとして `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` を指定します。

15. **[次へ]** を選択します。 **[概要]** ページで、設定を確認し、 **[グループの作成]** を選択します。

    ![Data Box を検出する](./media/offline-backup-azure-data-box-dpm-mabs/detect-databox.png)

    次の画面で、保護グループが正常に作成されていることを確認します。

    ![作成された保護グループ](./media/offline-backup-azure-data-box-dpm-mabs/protection-group-created.png)

16. 上の画面で **[閉じる]** を選択します。

    これにより、データの初期レプリケーションが DPM または MABS ディスクに対して発生します。 保護が終了すると、 **[状態]** ページにグループの保護状態が **[OK]** と表示されます。

17. Azure Data Box デバイスへのオフラインバックアップ コピーを開始するには、 **[保護グループ]** を右クリックしてから、 **[回復ポイントの作成]** オプションを選択します。 次に、 **[オンライン保護]** オプションを選択します。

    ![[回復ポイントの作成]](./media/offline-backup-azure-data-box-dpm-mabs/create-recovery-point.png)

    ![回復ポイント](./media/offline-backup-azure-data-box-dpm-mabs/recovery-point.png)

   DPM または MABS サーバーによって、選択したデータの Azure Data Box デバイスへのバックアップが開始されます。 これには、データのサイズや、DPM または MABS サーバーと Azure Data Box Disk の間の接続速度に応じて、数時間から数日かかることがあります。

   ジョブの状態は **[監視]** ペインで監視できます。 データのバックアップが完了すると、以下のような画面が表示されます。

   ![管理者コンソール](./media/offline-backup-azure-data-box-dpm-mabs/administrator-console.png)

## <a name="post-backup-steps"></a>バックアップ後の手順

Azure Data Box Disk へのデータのバックアップに成功したら、これらの手順に従います。

- [こちらの記事](../databox/data-box-disk-deploy-picked-up.md)の手順に従って、Azure Data Box Disk を Azure に発送します。 Azure Data Box 100 TB デバイスを使用した場合は、[これらの手順](../databox/data-box-deploy-picked-up.md)に従って、Azure Data Box を Azure に発送します。
- Azure portal で [Data Box ジョブを監視](../databox/data-box-disk-deploy-upload-verify.md)します。 Azure Data Box ジョブが "*完了*" になると、DPM または MABS サーバーによって、次回のスケジュールされたバックアップ時に、ストレージ アカウントから Recovery Services コンテナーにデータが自動的に移動されます。 その後、回復ポイントが正常に作成されると、そのバックアップ ジョブは "*完了したジョブ*" としてマークされます。

  > [!NOTE]
  > DPM または MABS サーバーによって、保護グループの作成時にスケジュールされた時刻にバックアップがトリガーされます。 しかし、これらのジョブでは、ジョブが完了するまで "*Azure Data Box ジョブの完了を待機中*" というフラグが設定されます。

- DPM または MABS サーバーによって、初期バックアップに対応する回復ポイントが正常に作成された後、Azure Data Box ジョブに関連するストレージ アカウント (または特定のコンテンツ) を削除できます。

## <a name="troubleshooting"></a>トラブルシューティング

DPM サーバー上の Microsoft Azure Backup (MAB) エージェントによって、テナントに自動的に Azure AD アプリケーションが作成されます。 このアプリケーションには、オフライン シード処理ポリシーを構成しているときに作成およびアップロードされる認証用の証明書が必要です。

証明書の作成と Azure AD アプリケーションへのアップロードには、Azure PowerShell を使用します。

### <a name="issue"></a>問題

オフライン バックアップを構成するときに、Azure PowerShell コマンドレットの既知のコード障害が原因で、MAB エージェントによって作成された同じ Azure AD アプリケーションに複数の証明書を追加できません。 これは、同じまたは別のサーバーに対してオフライン シード処理ポリシーを構成している場合に影響があります。

### <a name="verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>問題がこの特定の根本原因によって発生しているかどうかを確認する

エラーが確実に上記の[問題](#issue)が原因で発生していることを確認するには、次のいずれかの手順を行います。

#### <a name="step-1"></a>ステップ 1

オフライン バックアップの構成時に、DPM または MABS コンソールに次のエラー メッセージが表示されるかどうかを確認します。

![Azure Recovery Services エージェント](./media/offline-backup-azure-data-box-dpm-mabs/azure-recovery-services-agent.png)

#### <a name="step-2"></a>手順 2.

1. インストール パスの **Temp** フォルダーを開きます (既定の一時フォルダーのパスは、*C:\Program Files\Microsoft Azure Recovery Services Agent\Temp* です)。*CBUICurr* ファイルを見つけて、そのファイルを開きます。
2. *CBUICurr* ファイルで、最後の行までスクロールし、エラーの原因が "Unable to create an Azure AD application credential in customer's account. 例外:Update to existing credential with KeyId \<some guid> isn't allowed (お客様のアカウントには Azure AD アプリケーション資格情報を作成できません。例外: KeyId が <some guid> の既存の資格情報を更新することはできません)" であるかどうかを確認します。

### <a name="workaround"></a>回避策

この問題を解決するには、次の手順を行い、ポリシーの構成を再試行します。

1. Data Box ジョブが作成されるサブスクリプションで、管理者アクセス権がある別のアカウントを使用して、DPM または MABS サーバー UI に表示される Azure サインイン ページにサインインします。
2. オフライン シード処理が構成されている他のサーバーがなく、`AzureOfflineBackup_<Azure User Id>` アプリケーションに依存する他のサーバーがない場合は、**Azure portal > [Azure Active Directory] > [アプリの登録]** からこのアプリケーションを削除します。

   > [!NOTE]
   > アプリケーション `AzureOfflineBackup_<Azure User Id>` に他のオフライン シード処理が構成されていないこと、およびこのアプリケーションに依存している他のサーバーがないことを確認します。 [公開鍵] セクションの **[設定] > [キー]** に移動します。 他の **公開キー** は追加しないでください。 参考のために次のスクリーンショットをご覧ください。
   >
   > ![公開キー](./media/offline-backup-azure-data-box-dpm-mabs/public-keys.png)

#### <a name="step-3"></a>手順 3.

オフライン バックアップを構成しようとしている DPM または MABS サーバーから、次の操作を行います。

1. **[Manage computer certificate application]\(コンピューター証明書の管理アプリケーション\)**  >  **[個人用]** タブの順に開き、`CB_AzureADCertforOfflineSeeding_<ResourceId>` という名前の証明書を探します。
2. 上記の証明書を選択し、 **[すべてのタスク]** を右クリックして、秘密キーなしの .cer 形式で **[エクスポート]** します。
3. **ポイント 2** に記載されている Azure オフライン バックアップ アプリケーションにアクセスします。 **[設定]**  >  **[キー]**  >  **[公開キーのアップロード]** で、上の手順でエクスポートされた証明書をアップロードします。

   ![公開キーをアップロードする](./media/offline-backup-azure-data-box-dpm-mabs/upload-public-keys.png)

4. サーバーで、 **[ファイル名を指定して実行]** ウィンドウに「**regedit**」と入力して、レジストリを開きます。
5. レジストリ *Computer\HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider* に移動します。 **CloudBackupProvider** を右クリックし、`AzureADAppCertThumbprint_<Azure User Id>` という名前の新しい文字列値を追加します。

    >[!NOTE]
    > Azure ユーザー ID を取得するには、次のいずれかの操作を実行します。
    >
    >- Azure に接続された PowerShell から、`Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` コマンドを実行します。
    > - *CurrentUserId* という名前のレジストリ パス `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` に移動します。

6. 前の手順で追加された文字列を右クリックして、 **[変更]** を選択します。 値として、**ポイント 2** でエクスポートした証明書の拇印を入力し、 **[OK]** を選択します。
7. 拇印の値を取得するには、証明書をダブルクリックして、 **[詳細]** を選択し、拇印フィールドが表示されるまで下方向にスクロールします。 **[拇印]** を選択して、値をコピーします。

   ![拇印の値](./media/offline-backup-azure-data-box-dpm-mabs/certificate.png)

## <a name="next-steps"></a>次のステップ

- [独自のディスクを使用したオフライン シード処理 (Azure Import/Export サービスを使用)](backup-azure-backup-server-import-export.md)

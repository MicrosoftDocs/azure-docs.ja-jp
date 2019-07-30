---
title: Azure Backup Server からデータを回復する
description: Recovery Services コンテナーに保護しているデータを、そのコンテナーに登録されている任意の Azure Backup Server から回復します。
author: kasinh
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: kasinh
ms.openlocfilehash: aaa2efa706822bee85dc867ad35bc312f4c700a1
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466913"
---
# <a name="recover-data-from-azure-backup-server"></a>Azure Backup Server からデータを回復する
Azure Backup Server を使用して、Recovery Services コンテナーにバックアップしたデータを回復することができます。 そのためのプロセスは、Azure Backup Server 管理コンソールに統合されており、他の Azure Backup コンポーネントの回復ワークフローに似ています。

> [!NOTE]
> この記事は、[最新の Azure Backup エージェント](https://aka.ms/azurebackup_agent)と組み合わされた [System Center Data Protection Manager 2012 R2 UR7 以降](https://support.microsoft.com/en-us/kb/3065246)に適用されます。
>
>

Azure Backup Server からデータを回復するには:

1. Azure Backup Server 管理コンソールの **[回復]** タブで、画面の左上にある **[外部 DPM の追加]** をクリックします。   
    ![外部 DPM の追加](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. データを回復する **Azure Backup Server** に関連付けられているコンテナーから新しい**コンテナー資格情報**をダウンロードし、Recovery Services コンテナーに登録されている Azure Backup Server の一覧から Azure Backup Server を選択し、データを回復する Azure Backup Server に関連付けられている**暗号化パスフレーズ**を指定します。

    ![外部 DPM の資格情報](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > 同じ登録コンテナーに関連付けられている Azure Backup Server のみ互いのデータを復元できます。
   >
   >

    外部 Azure Backup Server が追加されたら、 **[回復]** タブから外部 Azure Backup Server とローカル Azure Backup Server のデータを閲覧できます。
3. 外部 Azure Backup Server で保護されている本稼働サーバーの一覧を閲覧し、該当するデータ ソースを選択します。

    ![外部 DPM サーバーの参照](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. **[回復ポイント]** ドロップダウンから **[月/年]** を選択し、回復ポイントが作成された **[回復日]** を選択し、 **[回復時刻]** を選択します。

    ファイルとフォルダーの一覧が下のページに表示されるので、任意の場所を参照し、そこに回復できます。

    ![外部 DPM サーバーの回復ポイント](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. 該当する項目を右クリックし、 **[回復]** をクリックします。

    ![外部 DPM 回復](./media/backup-azure-alternate-dpm-server/recover.png)
6. **[回復の選択]** を確認します。 回復するバックアップ コピーの日時とバックアップ コピーの作成元を確認します。 選択が間違っている場合、 **[キャンセル]** をクリックして [回復] タブに戻り、適切な回復ポイントを選択します。 選択が正しければ、 **[次へ]** をクリックします。

    ![外部 DPM 回復の概要](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. **[別の場所に回復する]** を選択します。 **[参照]** で適切な回復場所を開きます。

    ![外部の DPM 回復の別場所](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. **[コピーの作成]** 、 **[スキップ]** 、 **[上書き]** に関連付けられているオプションを選択します。

   * **[コピーの作成]** - 名前の競合がある場合、ファイルのコピーを作成します。
   * **スキップ** - 名前の競合がある場合、ファイルを回復せず、元のファイルを残します。
   * **上書き** - 名前の競合がある場合、ファイルの既存のコピーを上書きします。

     **[セキュリティの復元]** のオプションを選択します。 データを復元する復元先コンピューターのセキュリティ設定または回復ポイントが作成された時点で生成物に適用されたセキュリティを適用できます。

     回復が正常に完了したら**通知**を送信するかどうかを確認します。

     ![外部の DPM 回復の通知](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. **概要** 画面には、これまでに選択したオプションが一覧表示します。 **[回復]** をクリックすると、該当するオンプレミスの場所にデータが回復されます。

    ![外部 DPM 回復のオプション概要](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Azure Backup Server の **[監視]** タブで回復ジョブを監視できます。
   >
   >

    ![回復の監視](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. DPM サーバーの **[回復]** タブの **[外部 DPM の消去]** をクリックし、外部 DPM サーバーのビューを削除できます。

    ![外部 DPM の消去](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>エラー メッセージのトラブルシューティング
| No. | エラー メッセージ | トラブルシューティングの手順 |
|:---:|:--- |:--- |
| 1. |このサーバーは資格情報コンテナーが指定するコンテナーに登録されていません。 |**原因:** 選択したコンテナー資格情報ファイルが回復対象の Azure Backup Server に関連付けられている Recovery Services コンテナーに属さないとき、このエラーが表示されます。 <br> **解決策:** Azure Backup Server が登録されている Recovery Services コンテナーからコンテナー資格情報ファイルをダウンロードします。 |
| 2. |回復可能なデータがないか、選択したサーバーが DPM サーバーではありません。 |**原因:** 他の Azure Backup Server が Recovery Services コンテナーに登録されていないか、サーバーがまだメタデータをアップロードしていないか、選択したサーバーが (Windows Server または Windows クライアントを使用する) Azure Backup Server ではありません。 <br> **解決策:** 他の Azure Backup Server が Recovery Services コンテナーに登録されている場合、最新の Azure Backup エージェントがインストールされていることを確認します。 <br>他の Azure Backup Server が Recovery Services コンテナーに登録されている場合、インストール後 1 日待ってから、回復プロセスを開始します。 クラウドに保護されたすべてのバックアップのメタデータを夜間ジョブがアップロードします。 このデータを回復に利用できます。 |
| 手順 3. |このコンテナーには他の DPM サーバーが登録されていません。 |**原因:** 他の Azure Backup Server が回復元のコンテナーに登録されていません。<br>**解決策:** 他の Azure Backup Server が Recovery Services コンテナーに登録されている場合、最新の Azure Backup エージェントがインストールされていることを確認します。<br>他の Azure Backup Server が Recovery Services コンテナーに登録されている場合、インストール後 1 日待ってから、回復プロセスを開始します。 保護されたすべてのバックアップのメタデータを夜間ジョブがクラウドにアップロードします。 このデータを回復に利用できます。 |
| 4. |入力した暗号化パスフレーズが、サーバー: **\<サーバー名>** に関連付けられているパスフレーズと一致しません |**原因:** 回復対象の Azure Backup Server のデータからデータを暗号化する過程で使用された暗号化パスフレーズが指定した暗号化パスフレーズに一致しません。 エージェントはデータを復号できません。 そのため、回復に失敗します。<br>**解決策:** データを回復する Azure Backup Server に関連付けられている暗号化パスフレーズとまったく同じものを指定してください。 |

## <a name="next-steps"></a>次の手順

その他のよく寄せられる質問をお読みください。

- Azure VM バックアップについて[よく寄せられる質問](backup-azure-vm-backup-faq.md)
- Azure Backup エージェントについて[よく寄せられる質問](backup-azure-file-folder-backup-faq.md)

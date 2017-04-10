---
title: "Azure Backup Server からデータを復元する | Microsoft Docs"
description: "コンテナーに登録されている Azure Backup Server から、Azure Backup コンテナーに保護しているデータを復元します。"
services: backup
documentationcenter: 
author: nkolli1
manager: shreeshd
editor: 
ms.assetid: a55f8c6b-3627-42e1-9d25-ed3e4ab17b1f
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: adigan;giridham;trinadhk;markgal
translationtype: Human Translation
ms.sourcegitcommit: 14cc190a7d1cde1181a6f26ef83095bc601f7fbb
ms.openlocfilehash: 36c4e1865c99dd1c55be798e1d310a02f2af0864
ms.lasthandoff: 01/28/2017


---
# <a name="recovering-data-from-another-azure-backup-server-in-the-backup-vault"></a>バックアップ コンテナーで別の Azure Backup Server からデータを復元する
コンテナーに登録されている Azure Backup Server から、Azure Backup コンテナーに保護しているデータを復元できるようになりました。 この復元プロセスは ABS 管理コンソールに完全統合されており、その他の復元ワークフローと似ています。

> [!NOTE]
> この記事と次の手順は、[最新の Azure Backup エージェント](http://aka.ms/azurebackup_agent)を使用する [System Center Data Protection Manager UR7] (https://support.microsoft.com/en-us/kb/3065246) にも適用されます。
>
>

## <a name="recover-data-from-another-azure-backup-server"></a>別の Azure Backup Server からデータを復元する
別の Azure Backup Server からデータを復元するには:

1. ABS 管理コンソールの **[復元]** タブで、(画面の左上にある) **[外部 DPM の追加]** をクリックします。   
    ![外部 DPM の追加](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. データを復元する **Azure Backup Server** に関連付けられているコンテナーから新しい**コンテナー資格情報**をダウンロードし、バックアップ コンテナーに登録されている Azure Backup Server の一覧から Azure Backup Server を選択し、データを復元する Azure Backup Server に関連付けられている**暗号化パスフレーズ**を指定します。

    ![外部 DPM の資格情報](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > 同じ登録コンテナーに関連付けられている Azure Backup Server のみ互いのデータを復元できます。
   >
   >

    外部 Azure Backup Server が追加されたら、**[回復]** タブから外部 Azure Backup Server とローカル Azure Backup Server のデータを閲覧できます。
3. 外部 Azure Backup Server で保護されている本稼働サーバーの一覧を閲覧し、該当するデータ ソースを選択します。

    ![外部 DPM サーバーの参照](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. **[回復ポイント]** ドロップダウンから **[月/年]** を選択し、回復ポイントが作成された **[回復日]** を選択し、**[回復時刻]** を選択します。

    ファイルとフォルダーの一覧が下のページに表示されるので、閲覧し、任意の場所に復元できます。

    ![外部 DPM サーバーの回復ポイント](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. 該当する項目を右クリックし、 **[回復]**をクリックします。

    ![外部 DPM 回復](./media/backup-azure-alternate-dpm-server/recover.png)
6. **[回復の選択]**を確認します。 回復するバックアップ コピーの日時とバックアップ コピーの作成元を確認します。 選択が間違っている場合、 **[キャンセル]** をクリックして [回復] タブに戻り、適切な回復ポイントを選択します。 選択が正しければ、 **[次へ]**をクリックします。

    ![外部 DPM 回復の概要](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. **[別の場所に回復する]**を選択します。 **[参照]** で適切な回復場所を開きます。

    ![外部の DPM 回復の別場所](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. **[コピーの作成]**、**[スキップ]**、**[上書き]** に関連付けられているオプションを選択します。

   * **[コピーの作成]** は、名前の競合がある場合、ファイルのコピーを作成します。
   * **[スキップ]** は、名前の競合がある場合、ファイルの復元を省略します。
   * **[上書き]** は、名前の競合がある場合、指定された場所に既存のコピーを上書きします。

     **[セキュリティの復元]**のオプションを選択します。 データを復元する復元先コンピューターのセキュリティ設定または回復ポイントが作成された時点で生成物に適用されたセキュリティを適用できます。

     回復が完了したら、 **通知** を送信するかどうかを確認します。

     ![外部の DPM 回復の通知](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. **概要** 画面には、これまでに選択したオプションが一覧表示します。 **[回復]**をクリックすると、該当するオンプレミスの場所にデータが復元されます。

    ![外部 DPM 回復のオプション概要](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Azure Backup Server の **[監視]** タブで回復ジョブを監視できます。
   >
   >

    ![回復の監視](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. DPM サーバーの **[回復]** タブの **[外部 DPM の消去]** をクリックし、外部 DPM サーバーのビューを削除できます。

    ![外部 DPM の消去](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>エラー メッセージのトラブルシューティング
| いいえ。 | エラー メッセージ | トラブルシューティングの手順 |
|:---:|:--- |:--- |
| 1. |このサーバーは資格情報コンテナーが指定するコンテナーに登録されていません。 |**原因:** 選択した資格情報コンテナー ファイルが回復対象の Azure Backup Server に関連付けられているバックアップ コンテナーに属さないとき、このエラーが表示されます。 <br> **解決策:** Azure Backup Server が登録されているバックアップ コンテナーから資格情報コンテナー ファイルをダウンロードします。 |
| 2. |回復可能なデータがないか、選択したサーバーが DPM サーバーではありません。 |**原因:** Azure Backup Server が他にバックアップ コンテナーに登録されていないか、Azure Backup Server でメタデータがまだアップロードされていないか、選択したサーバーが Azure Backup Server (別名、Windows Server または Windows Client)ではありません。 <br> **解決策:** 他の Azure Backup Server がバックアップ コンテナーに登録されている場合、最新の Azure Backup エージェントがインストールされていることを確認します。 <br>他の Azure Backup Server がバックアップ コンテナーに登録されている場合、インストール後、1 日待ってから回復プロセスを開始します。 クラウドに保護されたすべてのバックアップのメタデータを夜間ジョブがアップロードします。 このデータを回復に利用できます。 |
| 3. |このコンテナーには他の DPM サーバーが登録されていません。 |**原因:** 他の Azure Backup Server が回復元のコンテナーに登録されていません。<br>**解決策:** 他の Azure Backup Server がバックアップ コンテナーに登録されている場合、最新の Azure Backup エージェントがインストールされていることを確認します。<br>他の Azure Backup Server がバックアップ コンテナーに登録されている場合、インストール後、1 日待ってから回復プロセスを開始します。 クラウドに保護されたすべてのバックアップのメタデータを夜間ジョブがアップロードします。 このデータを回復に利用できます。 |
| 4. |指定した暗号化パスフレーズが次のサーバーに関連付けられているパスフレーズと一致しません。**<server name>** |**原因:** 回復対象の Azure Backup Server のデータからデータを暗号化する過程で使用された暗号化パスフレーズが指定した暗号化パスフレーズに一致しません。 エージェントはデータを復号できません。 そのため、回復に失敗します。<br>**解決策:** データを回復する Azure Backup Server に関連付けられている暗号化パスフレーズとまったく同じものを指定してください。 |

## <a name="frequently-asked-questions"></a>よく寄せられる質問:
1. **UR7 と最新の Azure Backup エージェントをインストールしましたが、別の DPM サーバーから外部 DPM サーバーを追加できません。なぜですか。(SC DPM 2012 R2 を使用している場合)**

    A) (更新プログラムのロールアップ 7 以前の更新プログラムのロールアップを利用し) データ ソースがクラウドに保護されている既存の DPM サーバーの場合、UR7 と最新の Azure Backup エージェントをインストールした後、1 日待ってから *外部 DPM サーバーの追加*を開始する必要があります。 これは DPM 保護グループのメタデータを Azure にアップロードするために必要です。 最初にこれは夜間ジョブで行われます。
2. **Azure Backup エージェントのバージョンの最小要件は何ですか。**

    A) この機能を有効にするには 2.0.8719.0 以降の Azure Backup エージェントが必要です。  [コントロール パネル] **>** [すべてのコントロール パネル項目] **>** [プログラムと機能] **>** [Microsoft Azure Recovery Services Agent] の順に進むと、Azure Backup エージェントのバージョンを確認できます。 バージョンが 2.0.8719.0 以前の場合、 [最新の Azure Backup エージェント](https://go.microsoft.com/fwLink/?LinkID=288905) をダウンロードし、インストールしてください。

    ![外部 DPM の消去](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## <a name="next-steps"></a>次のステップ:
•   [Azure Backup の FAQ](backup-azure-backup-faq.md)


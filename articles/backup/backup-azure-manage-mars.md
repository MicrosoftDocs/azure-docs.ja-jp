---
title: MARS エージェントのバックアップを管理および監視する
description: Azure Backup サービスを使用して Microsoft Azure Recovery Services (MARS) エージェントのバックアップを管理および監視する方法について説明します。
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 25f0c41b535f9403d0a7027687cc5261cd437275
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97368598"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Azure Backup サービスを使用して Microsoft Azure Recovery Services (MARS) エージェントのバックアップを管理する

この記事では、Microsoft Azure Recovery Services エージェントによってバックアップされたファイルやフォルダーを管理する方法について説明します。

## <a name="modify-a-backup-policy"></a>バックアップ ポリシーを変更する

バックアップ ポリシーを変更すると、新しい項目の追加、バックアップからの既存項目の削除、[除外の設定] を使用したバックアップ対象からのファイルの除外が可能です。

- **[項目の追加]** : このオプションは、新しい項目をバックアップに追加する場合にのみ使用します。 既存の項目を削除するには、 **[項目の削除]** または **[除外の設定]** オプションを使用します。  
- **[項目の削除]** : このオプションは、バックアップ対象から項目を削除するために使用します。
  - ボリューム内のすべての項目を削除する場合は、 **[項目の削除]** ではなく **[除外の設定]** を使用します。
  - ボリューム内のすべての選択を解除すると、項目の以前のバックアップが、最終バックアップ時の保持設定に従って保持されます (変更のスコープなし)。
  - これらの項目を再選択すると、最初の完全バックアップが行われ、新しいポリシーの変更は以前のバックアップには適用されません。
  - ボリューム全体の選択を解除すると、保持ポリシーを変更するためのスコープがなくても過去のバックアップが保持されます。
- **[除外の設定]** : 特定の項目をバックアップ対象から除外するには、このオプションを使用します。

### <a name="add-new-items-to-existing-policy"></a>既存のポリシーに新しい項目を追加する

1. **[アクション]** で、 **[バックアップのスケジュール]** を選択します。

    ![Schedule a Windows Server backup](./media/backup-configure-vault/schedule-first-backup.png)

2. **[ポリシー項目の選択]** タブで、 **[ファイルとフォルダーのバックアップ スケジュールを変更する]** を選択し、 **[次へ]** を選択します。

    ![ポリシー項目の選択](./media/backup-azure-manage-mars/select-policy-items.png)

3. **[バックアップ スケジュールの変更または停止]** タブで、 **[バックアップ項目または時刻を変更する]** を選択し、 **[次へ]** を選択します。

    ![バックアップの変更またはスケジュール](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. **[バックアップする項目の選択]** タブで **[項目の追加]** を選択し、バックアップする項目を追加します。

    ![バックアップの変更またはスケジュールでの [項目の追加]](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. **[項目の選択]** ウィンドウで、追加するファイルまたはフォルダーを選択し、 **[OK]** を選択します。

    ![項目の選択](./media/backup-azure-manage-mars/select-item.png)

6. 次の手順を完了し、 **[完了]** を選択して操作を完了します。

### <a name="add-exclusion-rules-to-existing-policy"></a>既存のポリシーに除外ルールを追加する

除外ルールを追加して、バックアップ対象にしないファイルやフォルダーをスキップできます。 これは、新しいポリシーの定義時または既存ポリシーの変更時に実行できます。

1. 操作ウィンドウから **[バックアップのスケジュール]** を選択します。 **[バックアップする項目の選択]** に移動し、 **[除外の設定]** を選択します。

    ![[除外の設定]](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. **[除外の設定]** で **[除外の追加]** を選択します。

    ![除外の追加](./media/backup-azure-manage-mars/add-exclusion.png)

3. **[除外する項目の選択]** から、ファイルやフォルダーを参照し、除外する項目を選択して **[OK]** を選択します。

    ![除外する項目の選択](./media/backup-azure-manage-mars/select-items-exclude.png)

4. 既定では、選択したフォルダー内のすべての **サブフォルダー** が除外されます。 これは、 **[はい]** または **[いいえ]** を選択することで変更できます。 下に示すように、除外するファイルの種類を編集および指定することができます。

    ![サブフォルダーの種類の選択](./media/backup-azure-manage-mars/subfolders-type.png)

5. 次の手順を完了し、 **[完了]** を選択して操作を完了します。

### <a name="remove-items-from-existing-policy"></a>既存のポリシーから項目を削除する

1. 操作ウィンドウから **[バックアップのスケジュール]** を選択します。 **[バックアップする項目の選択]** に移動します。 一覧で、バックアップ スケジュールから削除するファイルやフォルダーを選択し、 **[項目の削除]** を選択します。

    ![削除する項目の選択](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > ポリシーからボリュームを完全に削除するときには、注意して操作を進めてください。  もう一度追加する必要がある場合、それは新しいボリュームとして扱われます。 次のスケジュールされたバックアップでは、増分バックアップではなく初回バックアップ (完全バックアップ) が実行されます。 後で項目を一時的に削除して追加する必要がある場合は、完全バックアップではなく増分バックアップとなるように、 **[項目の削除]** ではなく **[除外の設定]** を使用することをお勧めします。

2. 次の手順を完了し、 **[完了]** を選択して操作を完了します。

## <a name="stop-protecting-files-and-folder-backup"></a>ファイルおよびフォルダーのバックアップの保護を停止する

ファイルおよびフォルダーのバックアップの保護を停止する方法は 2 つあります。

- **保護を停止してバックアップ データを保持します**。
  - このオプションでは、今後、バックアップ ジョブによる保護がすべて停止されます。
  - 既存のすべての復旧ポイントは、引き続き Azure Backup サービスによって保持されます。  
  - 有効期限になっていない復旧ポイントのバックアップ データを復元することができます。
  - 保護を再開する場合は、*バックアップ スケジュールを再有効化する* オプションを使用できます。 その後は、新しいアイテム保持ポリシーに基づいてデータが保持されます。
- **保護を停止してバックアップ データを削除します**。
  - このオプションでは、今後、バックアップ ジョブによるデータの保護がすべて停止され、すべての復旧ポイントが削除されます。
  - ユーザーは、バックアップ データの削除に関するアラート メールを受信します。このメールには、"*Your data for this Backup item has been deleted. (このバックアップ項目のデータは削除されました。)This data will be temporarily available for 14 days, after which it will be permanently deleted (このデータは 14 日間一時的に使用できるようになり、その後、完全に削除されます)* " というメッセージと、"*Reprotect the Backup item within 14 days to recover your data. (データを復旧するには、14 日以内にバックアップ項目を再保護してください。)* という推奨される操作が記載されています。
  - 保護を再開するには、削除操作から 14 日以内に再保護します。

### <a name="stop-protection-and-retain-backup-data"></a>保護を停止してバックアップ データを保持する

1. MARS 管理コンソールを開き、 **[アクション]** ウィンドウに移動して、 **[バックアップのスケジュール]** を選択します。

    ![[バックアップのスケジュール] の選択](./media/backup-azure-manage-mars/mars-actions.png)
1. **[ポリシー項目の選択]** ページで、 **[ファイルとフォルダーのバックアップ スケジュールを変更します]** を選択し、 **[次へ]** を選択します。

    ![ポリシー項目の選択](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. **[スケジュールされたバックアップの変更または停止]** ページで、 **[Stop using this backup schedule, but keep the stored backups until a schedule is activated again] (このバックアップ スケジュールの使用を中止するが、スケジュールが再度アクティブ化されるまで保存されているバックアップを保持する)** を選択します。 次に、 **[次へ]** を選択します。

    ![スケジュールされたバックアップを停止する。](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. **[スケジュールされたバックアップの一時停止]** で情報を確認し、 **[完了]** を選択します。

    ![スケジュールされたバックアップを一時停止します。](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. **[バックアップの進行状況の変更]** で、スケジュールのバックアップの一時停止が成功の状態であることを確認し、 **[閉じる]** を選択して終了します。

### <a name="stop-protection-and-delete-backup-data"></a>保護を停止してバックアップ データを削除する

1. MARS 管理コンソールを開き、 **[アクション]** ウィンドウに移動して、 **[バックアップのスケジュール]** を選択します。
2. **[スケジュールされたバックアップの変更または停止]** ページで、 **[このバックアップ スケジュールの使用を中止して、保存されているバックアップをすべて削除する]** を選択します。 次に、 **[次へ]** を選択します。

    ![スケジュールされたバックアップを変更または停止する。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. **[スケジュールされたバックアップの停止]** ページで、 **[完了]** を選択します。

    ![スケジュールされたバックアップの停止と完了の選択](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. セキュリティ PIN (暗証番号) の入力を求められます。これは手動で生成する必要があります。 これを行うには、最初に Azure portal にサインインします。
5. **[Recovery Services コンテナー]**  >  **[設定]**  >  **[プロパティ]** の順に移動します。
6. **[セキュリティ PIN]** の下にある **[生成]** を選択します。 この PIN をコピーします。 この PIN は 5 分間だけ有効です。
7. 管理コンソールに PIN を貼り付け、 **[OK]** を選択します。

    ![セキュリティ PIN を生成する。](./media/backup-azure-delete-vault/security-pin.png)

8. **[バックアップの進行状況の変更]** ページに、次のメッセージが表示されます。"*削除されたバックアップ データは 14 日間保持されます。14 日が経過すると、バックアップ データが完全に削除されます。* " と表示されます。  

    ![バックアップの進行状況の変更](./media/backup-azure-delete-vault/deleted-backup-data.png)

オンプレミスのバックアップ アイテムを削除したら、ポータルから次の手順を実行します。

## <a name="re-enable-protection"></a>保護を再有効化する

データを保持しつつ保護を停止し、保護を再開することにした場合は、バックアップ ポリシーの変更を利用してバックアップ スケジュールを再有効化することができます。

1. **[アクション]** で、 **[バックアップのスケジュール]** を選択します。
1. **[バックアップ スケジュールを再有効化します。バックアップ項目または時刻を変更することもできます]** を選択し、 **[次へ]** を選択します。<br>

    ![バックアップ スケジュールの再有効化](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. **[バックアップする項目の選択]** で、 **[次へ]** を選択します。

    ![バックアップする項目の選択](./media/backup-azure-manage-mars/re-enable-next.png)
1. **[バックアップ スケジュールの選択]** でバックアップ スケジュールを指定し、 **[次へ]** を選択します。
1. **[保有期間ポリシーの選択]** ページで、保有期間を指定し、 **[次へ]** を選択します。
1. 最後に **[確認]** 画面で、ポリシーの詳細を確認し、 **[完了]** を選択します。

## <a name="re-generate-passphrase"></a>パスフレーズを再生成する

パスフレーズは、MARS エージェントを使用して Azure との間でオンプレミスまたはローカル マシンをバックアップまたは復元するときに、データを暗号化および復号化するために使用されます。 パスフレーズを紛失した場合または忘れた場合は、次のステップに従ってパスフレーズを再生成できます (マシンが Recovery Services コンテナーにまだ登録され、バックアップが構成されている場合)。

1. MARS エージェント コンソールで、 **[操作] ペイン** >  **[プロパティの変更]** に移動します。 次に、 **[暗号化] タブ** に移動します。<br>
1. **[パスフレーズの変更]** チェックボックスをオンにします。<br>
1. 新しいパスフレーズを入力するか、 **[パスフレーズの生成]** を選択します。
1. **[参照]** を選択して新しいパスフレーズを保存します。

    ![パスフレーズを生成します。](./media/backup-azure-manage-mars/passphrase.png)

1. **[OK]** を選択して変更を適用します。  Azure portal 上で Recovery Services コンテナーの [[セキュリティ機能]](./backup-azure-security-feature.md#enable-security-features) が有効な場合、[セキュリティ PIN] の入力を求められます。 PIN を受信するには、こちらの[記事](./backup-azure-security-feature.md#authentication-to-perform-critical-operations)に記載されている手順に従います。<br>
1. ポータルからセキュリティ PIN を貼り付けて、 **[OK]** を選択して変更を適用します。<br>

    ![セキュリティ PIN の貼り付け](./media/backup-azure-manage-mars/passphrase2.png)
1. パスフレーズは、(ソース マシンではない) 別の場所、できれば Azure Key Vault に安全に保存するようにします。 複数のマシンが MARS エージェントでバックアップされている場合は、すべてのパスフレーズを追跡します。

## <a name="managing-backup-data-for-unavailable-machines"></a>使用できないマシンのバックアップ データを管理する

このセクションでは、MARS で保護されていたソース マシンが、削除、破損、マルウェア/ランサムウェアの感染、または使用停止になったことにより、使用できなくなったシナリオについて説明します。

これらのマシンでは、バックアップ ポリシーで指定された保持規則に従って、最新の復旧ポイントが期限切れにならない (つまり、排除されない) ことが、Azure Backup サービスによって保証されます。 そのため、マシンを安全に復元できます。  バックアップされたデータに対して実行できる次のシナリオを考えてみましょう。

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>シナリオ 1:ソース マシンが使用できなくなり、バックアップ データを保持する必要がなくなった

- [この記事](backup-azure-delete-vault.md#delete-protected-items-on-premises)に記載されている手順に従って、Azure portal からバックアップ データを削除できます。

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>シナリオ 2: ソース マシンは使用できなくなったが、バックアップ データを保持する必要がある

MARS のバックアップ ポリシーの管理は、ポータルではなく、MARS コンソールを使用して行われます。 期限切れになる前に既存の復旧ポイントの保有期間の設定を延長する必要がある場合は、マシンを復元し、MARS コンソールをインストールして、ポリシーを延長する必要があります。

- マシンを復元するには、次の手順を実行します。
  1. [代替のターゲット マシンに VM を復元します](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. ソース マシンと同じホスト名でターゲット マシンを再作成します
  1. エージェントをインストールし、同じパスフレーズを使用して同じコンテナーに再登録します
  1. MARS クライアントを起動して、要件に応じて保有期間を延長します
- 新しく復元されたマシンは、MARS で保護され、引き続きバックアップを実行します。  

## <a name="configuring-antivirus-for-the-mars-agent"></a>MARS エージェントのウイルス対策を構成する

MARS エージェントの動作との競合を避けるために、ウイルス対策ソフトウェアは次のように構成することをお勧めします。

1. **パスの除外の追加**:パフォーマンスの低下を防ぎ、発生する可能性がある競合を回避するには、ウイルス対策ソフトウェアによるリアルタイム監視から次のパスを除外します。
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent` およびそのサブフォルダー
    1. **スクラッチ フォルダー**:スクラッチ フォルダーが標準の場所にない場合は、これも除外対象に追加します。  スクラッチ フォルダーの場所を特定する手順については、[こちらを参照](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)してください。
1. **バイナリ除外の追加**:バックアップとコンソールの動作が低下しないようにするには、ウイルス対策ソフトウェアによるリアルタイムの監視から、次のバイナリのプロセスを除外します。
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\cbengine.exe`

>[!NOTE]
>ほとんどのウイルス対策ソフトウェアでは、これらのパスを除外するだけで十分ですが、一部では MARS エージェントの操作が引き続き妨げられる場合があります。 予期しないエラーが発生する場合は、一時的にウイルス対策ソフトウェアをアンインストールして、問題が解決しないかどうかを監視します。 これによって問題が解決される場合は、製品の適切な構成について、ウイルス対策ソフトウェア ベンダーにお問い合わせください。

## <a name="next-steps"></a>次のステップ

- サポートされるシナリオと制限事項については、[MARS エージェントのサポート マトリックス](./backup-support-matrix-mars-agent.md)に関するページを参照してください。
- [オンデマンド バックアップ ポリシーの保持動作](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)の詳細を確認します。
- よく寄せられる質問については、「[MARS エージェントに関する FAQ](backup-azure-file-folder-backup-faq.md)」を参照してください。

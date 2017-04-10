---
title: "Resource Manager によってデプロイされた仮想マシン バックアップの監視 | Microsoft Docs"
description: "Resource Manager によってデプロイされた仮想マシンのバックアップのイベントとアラートを監視します。 アラートに基づいて電子メールを送信します。"
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: 
ms.assetid: fed32015-2db2-44f8-b204-d89f6fd1bea2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2016
ms.author: markgal;trinadhk;giridham;
translationtype: Human Translation
ms.sourcegitcommit: e12d533ac5befe020c0aad4aa64ca9ed50349c3d
ms.openlocfilehash: aefc65d42e3c23e35793be8af1751bb48dfaa84f
ms.lasthandoff: 02/17/2017


---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Azure 仮想マシンのバックアップ アラートの監視
アラートはサービスから返され、イベントのしきい値が満たされていること、またはしきい値を超えたことを示します。 ビジネス コストを抑えるには、問題が発生したときに、それを把握することが重要です。 通常、アラートはスケジュールに基づいて発生するわけではないため、発生後にできるだけ早く通知されると便利です。 たとえば、バックアップ ジョブまたは復元ジョブが失敗した場合、エラーの発生後&5; 分以内にアラートが発生します。 コンテナーのダッシュボードの [バックアップ アラート] タイルには、重大なアラートと警告レベルのアラートが発生しているイベントが表示されます。 [バックアップ アラート] 設定では、すべてのイベントを確認することができますが、 別の問題に対応しているときに、アラートが発生したらどうでしょう。 アラートが発生したことを知らなくても、大きな問題にはならないかもしれませんが、データのセキュリティ侵害につながることもあります。 アラートが発生したことを、適切なユーザーに確実に知らせるには、アラート通知を電子メールで送信するようにサービスを構成します。 電子メール通知の設定の詳細については、「[通知の構成](backup-azure-monitor-vms.md#configure-notifications)」を参照してください。

## <a name="how-do-i-find-information-about-the-alerts"></a>アラートに関する情報の見つけ方
アラートが生成されたイベントに関する情報を表示するには、[バックアップ アラート] ブレードを開く必要があります。 [バックアップ アラート] ブレードは、コンテナーのダッシュボードの [バックアップ アラート] タイルから開くか、[アラートとイベント] ブレードから開きます。

[バックアップ アラート] タイルから [バックアップ アラート] ブレードを開くには:

* ダッシュボードの **[バックアップ アラート]** タイルで **[重大]** または **[警告]** をクリックして、その重大度レベルの操作イベントを表示します。

    ![[バックアップ アラート] タイル](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

[アラートとイベント] ブレードから [バックアップ アラート] ブレードを開くには:

1. コンテナーのダッシュボードから、 **[すべての設定]**をクリックします。 ![[すべての設定] ボタン](./media/backup-azure-monitor-vms/all-settings-button.png)
2. **[設定]** ブレードで **[アラートとイベント]** をクリックします。 ![[アラートとイベント] ボタン](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. **[アラートとイベント]** ブレードで、**[バックアップ アラート]** をクリックします。 ![[バックアップ アラート] ボタン](./media/backup-azure-monitor-vms/backup-alerts.png)

    **[バックアップ アラート]** ブレードが開き、フィルター処理されたアラートが表示されます。

    ![[バックアップ アラート] タイル](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. 特定のアラートに関する詳しい情報を表示するには、イベントの一覧から目的のアラートをクリックして、その **[詳細]** ブレードを開きます。

    ![イベントの詳細](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    一覧に表示される属性をカスタマイズするには、「 [追加のイベント属性の表示](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>通知の構成
 過去&1; 時間に発生したアラート、または特定の種類のイベントの発生時に生成されたアラートに対して、電子メール通知を送信するようにサービスを構成できます。

アラートの電子メール通知を設定するには

1. [バックアップ アラート] メニューで、 **通知の構成**

    ![[バックアップ アラート] メニュー](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    [通知の構成] ブレードが開きます。

    ![[構成の通知] ブレード](./media/backup-azure-monitor-vms/configure-notifications.png)
2. [通知の構成] ブレードで、電子メール通知に対して **[オン]**をクリックします。

    受信者と重大度は必須情報であるため、そのダイアログの横には星マークが付いています。 1 つ以上の電子メール アドレスを指定し、1 つ以上の重大度を選択します。
3. **[受信者 (メール)]** ダイアログ ボックスに、通知を受け取る電子メール アドレスを、 username@domainname.com の形式で入力します。 複数の電子メール アドレスを入力する場合は、セミコロン (;) で区切ります。
4. 指定したアラートの発生時に通知を送信するには、**[通知]** 領域で **[アラートごと]** を選択します。過去&1; 時間の概要を送信するには、**[1 時間ごとのダイジェスト]** を選択します。
5. **[重大度]** ダイアログ ボックスで、電子メール通知をトリガーする&1; つ以上のレベルを選択します。
6. [ **Save**] をクリックします。

   ### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Azure IaaS VM のバックアップに使用できるアラートの種類
   | アラート レベル | 送信されるアラート |
   | --- | --- |
   | 重大 |バックアップの失敗、回復エラー |
   | [警告] |なし |
   | 情報 |なし |

### <a name="are-there-situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>通知が構成されていても電子メールが送信されない場合はあるか
通知が正しく構成されているにもかかわらずアラートが送信されないことがあります。 アラートのノイズを回避する目的で、次のような状況では電子メール通知は送信されません。

* 通知頻度が [1 時間ごとのダイジェスト] に設定されており、アラートが発生してから&1; 時間以内に解決した。
* ジョブが取り消された。
* バックアップ ジョブがトリガーされ失敗した後、別のバックアップ ジョブが進行中である。
* Resource Manager に対応する VM のスケジュールされたバックアップ ジョブが開始されたが、VM が存在しない。

## <a name="customize-your-view-of-events"></a>イベントのビューのカスタマイズ
**[監査ログ]** 設定には、操作イベント情報を示す事前定義済みの一連のフィルターと列が用意されています。 イベントのビューは、 **[イベント]** ブレードを開いたときに必要な情報が表示されるように、カスタマイズすることができます。

1. [資格情報コンテナーのダッシュボード](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard)で **[監査ログ]** に移動してクリックし、**[イベント]** ブレードを開きます。

    ![Audit Logs](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    **[イベント]** ブレードには、現在のコンテナーに該当する操作イベントのみが表示されます。

    ![Audit Logs Filter](./media/backup-azure-monitor-vms/audit-logs-filter.png)

    このブレードには、過去&1; 週間に発生した重大イベント、エラー イベント、警告イベント、情報イベントが一覧表示されます。 この期間は、 **[フィルター]**に設定されている既定値です。 **[イベント]** ブレードには、イベントがいつ発生したかを追跡する棒グラフも表示されます。 棒グラフを非表示にするには、**[イベント]** メニューの **[グラフの非表示]** をクリックしてグラフをオフに切り替えます。 イベントの既定のビューには、操作、レベル、状態、リソース、および時刻の情報が表示されます。 追加のイベント属性の表示については、 [イベント情報の展開](backup-azure-monitor-vms.md#view-additional-event-attributes)に関するセクションをご覧ください。
2. 操作イベントの詳細については、**[操作]** 列で操作イベントをクリックして、ブレードを開きます。 ブレードにはイベントに関する詳細情報が含まれています。 イベントは、関連付け ID によってグループ化されています。また、特定の期間に発生したイベントの一覧も表示されます。

    ![操作の詳細](./media/backup-azure-monitor-vms/audit-logs-details-window.png)
3. 特定のイベントに関する詳しい情報を表示するには、イベントの一覧から目的のイベントをクリックして、その **[詳細]** ブレードを開きます。

    ![イベントの詳細](./media/backup-azure-monitor-vms/audit-logs-details-window-deep.png)

    イベント レベルの情報は、得られた内容がそのままの詳細度で表示されます。 各イベントに関するこの大量の情報を確認し、その詳細情報を **[イベント]** ブレードに追加する必要がある場合は、 [イベント情報の展開](backup-azure-monitor-vms.md#view-additional-event-attributes)に関するセクションをご覧ください。

## <a name="customize-the-event-filter"></a>イベント フィルターのカスタマイズ
**フィルター** を使用して、特定のブレードに表示される情報を調整または選択します。 イベント情報をフィルター処理するには:

1. [資格情報コンテナーのダッシュボード](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard)で **[監査ログ]** に移動してクリックし、**[イベント]** ブレードを開きます。

    ![Audit Logs](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    **[イベント]** ブレードには、現在のコンテナーに該当する操作イベントのみが表示されます。

    ![Audit Logs Filter](./media/backup-azure-monitor-vms/audit-logs-filter.png)
2. **[イベント]** メニューで、**[フィルター]** をクリックして、そのブレードを開きます。

    ![open filter blade](./media/backup-azure-monitor-vms/audit-logs-filter-button.png)
3. **[フィルター]** ブレードで、**[レベル]**、**[期間]**、**[呼び出し元]** の各フィルターを調整します。 その他のフィルターは、現在の Recovery Services コンテナーの情報を提供するように設定されているため利用できません。

    ![Audit Logs-query details](./media/backup-azure-monitor-vms/filter-blade.png)

    イベントの **レベル** には、重大、エラー、警告、情報のいずれかを指定できます。 イベントのレベルは複数選んで組み合わせることができますが、少なくとも&1; つは選択する必要があります。 目的のレベルのオン/オフを指定してください。 イベントの収集対象期間は、 **[期間]** フィルターで指定できます。 カスタムの期間を使用する場合は、開始と終了のタイミングを設定できます。
4. フィルターを使って操作ログを照会する準備が整ったら、 **[更新]**をクリックします。 その結果が **[イベント]** ブレードに表示されます。

    ![操作の詳細](./media/backup-azure-monitor-vms/edited-list-of-events.png)

### <a name="view-additional-event-attributes"></a>追加のイベント属性の表示
**[列]** ボタンを使用して、**[イベント]** ブレード上の一覧に追加のイベント属性を表示できます。 イベントの既定の一覧には、操作、レベル、状態、リソース、および時刻の情報が表示されます。 追加の属性を有効にするには:

1. **[イベント]** ブレードで、**[列]** をクリックします。

    ![列を開く](./media/backup-azure-monitor-vms/audi-logs-column-button.png)

    **[列の選択]** ブレードが開きます。

    ![[列] ブレード](./media/backup-azure-monitor-vms/columns-blade.png)
2. 属性を選択するには、チェック ボックスをクリックします。 属性のチェック ボックスのオンとオフを切り替えます。
3. **[イベント]** ブレードで、**[リセット]** をクリックして属性の一覧をリセットします。 一覧で属性を追加または削除したら、 **[リセット]** を使用して、新しいイベント属性の一覧を表示します。
4. **[更新]** をクリックして、イベント属性のデータを更新します。 次の表は、各属性の情報を示しています。

| 列名 | Description |
| --- | --- |
| 操作 |操作の名前 |
| Level |操作のレベル。値は、情報、警告、エラー、重大のいずれかです |
| 状態 |操作の状態の説明 |
| リソース |リソースを識別する URL。リソース ID とも呼ばれます |
| Time |イベントが発生した時間。現在の時間から測定します |
| Caller |イベントを呼び出した、またトリガーしたシステムまたはユーザー |
| Timestamp |イベントがトリガーされた時間 |
| リソース グループ |関連付けられているリソース グループ |
| リソースの種類 |Resource Manager によって使用される内部リソースの種類 |
| サブスクリプション ID |関連付けられているサブスクリプション ID |
| カテゴリ |イベントのカテゴリ |
| 関連付け ID |関連するイベントの共通の ID |

## <a name="use-powershell-to-customize-alerts"></a>PowerShell を使用したアラートのカスタマイズ
ポータルで、ジョブのカスタム アラート通知を受け取ることができます。 ジョブの通知を受け取るには、操作ログ イベントに対して PowerShell ベースのアラート ルールを定義します。 " *PowerShell Version 1.3.0 以降*" を使用します。

バックアップに失敗した場合に警告するカスタム通知を定義するには、次のスクリプトのようなコマンドを使用します。

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.RecoveryServices/recoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/Microsoft.RecoveryServices/vaults/trinadhVault -Actions $actionEmail
```

**ResourceId** : ResourceId は監査ログから取得できます。 ResourceId は操作ログの Resource 列に記録されている URL です。

**OperationName**: OperationName の形式は "Microsoft.RecoveryServices/recoveryServicesVault/<*イベント名*>" です。ここで、<*イベント名*> は次のいずれかです:<br/>

* [登録] <br/>
* Unregister  <br/>
* ConfigureProtection  <br/>
* Backup  <br/>
* Restore  <br/>
* StopProtection  <br/>
* DeleteBackupData  <br/>
* CreateProtectionPolicy  <br/>
* DeleteProtectionPolicy  <br/>
* UpdateProtectionPolicy  <br/>

**Status** : サポートされる値は、Started、Succeeded、または Failed です。

**ResourceGroup** : 対象リソースが属しているリソース グループです。 生成するログに Resource Group 列を追加することができます。 リソース グループは、入手できるイベント情報の種類の&1; つです。

**Name** : アラート ルールの名前です。

**CustomEmail** : アラート通知を送信する先のカスタム電子メール アドレスを指定します

**SendToServiceOwners** : このオプションを指定すると、サブスクリプションの管理者と共同管理者すべてにアラート通知が送信されます。 これは、 **New-AzureRmAlertRuleEmail** コマンドレットで使用できます

### <a name="limitations-on-alerts"></a>アラートに関する制限事項
イベント ベースのアラートには、次の制限事項が適用されます。

1. アラートは、Recovery Services コンテナー内のすべての仮想マシン上でトリガーされます。 Recovery Services コンテナーの仮想マシンのサブセットに対して、アラートをカスタマイズすることはできません。
2. この機能はプレビュー段階にあります。 [詳細情報](../monitoring-and-diagnostics/insights-powershell-samples.md#create-alert-rules)
3. "alerts-noreply@mail.windowsazure.com" からアラートが送信されます。 現時点で、電子メールの送信者を変更することはできません。

## <a name="next-steps"></a>次のステップ
イベント ログは、バックアップ操作の事後分析や監査を行うのに役立ちます。 次の操作が記録されます。

* Register 
* Unregister 
* 保護の構成
* バックアップ (オンデマンド バックアップとスケジュールされたバックアップの両方)
* Restore 
* 保護の停止
* バックアップ データの削除
* ポリシーの追加
* ポリシーの削除
* ポリシーの更新
* ジョブの取り消し

Azure サービス全般のイベント、操作、および監査ログの概要については、「 [View events and audit logs (イベントと監査ログの表示)](../monitoring-and-diagnostics/insights-debugging-with-events.md)」を参照してください。

復旧ポイントからの仮想マシンの再作成については、 [Azure VM の復元](backup-azure-restore-vms.md)に関するページをご覧ください。 仮想マシンの保護については、 [Recovery Services コンテナーへの VM のバックアップ](backup-azure-vms-first-look-arm.md)に関するページをご覧ください。 VM バックアップの管理タスクについては、 [Azure 仮想マシンのバックアップの管理](backup-azure-manage-vms.md)に関するページをご覧ください。


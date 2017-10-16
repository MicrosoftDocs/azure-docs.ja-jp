---
title: "Backup コンテナーを Recovery Services コンテナーにアップグレードする (プレビュー) | Microsoft Docs"
description: "Azure Backup コンテナーを Recovery Services コンテナーにアップグレードするための手順とサポート情報です。"
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
ms.assetid: 228fef19-2f6b-4067-acc3-fb6e501afb88
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/03/2017
ms.author: sogup;markgal;arunak
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 28e8b0be0b69f279685109d611fbfb369b96101a
ms.contentlocale: ja-jp
ms.lasthandoff: 09/20/2017

---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Backup コンテナーを Recovery Services コンテナーにアップグレードする

この記事では、Backup コンテナーを Recovery Services コンテナーにアップグレードする方法について説明します。 このアップグレード プロセスは、実行中のバックアップ ジョブに影響を与えず、バックアップ データが失われることもありません。 Backup コンテナーを Recovery Services コンテナーにアップグレードする主な理由は次のとおりです。
- Backup コンテナーのすべての機能は、Recovery Services で保持されます。
- Recovery Services コンテナーは、Backup コンテナーよりも多くの機能 (強化されたセキュリティ、統合された監視、高速復元、アイテム レベルの復元など) を備えています。
- 改良され、簡素化されたポータルからバックアップ アイテムを管理します。
- 新機能は、Recovery Services コンテナーにのみ適用されます。

## <a name="impact-to-operations-during-upgrade"></a>アップグレード中の操作への影響

Backup コンテナーを Recovery Services コンテナーにアップグレードする際、データ プレーンの操作に影響を与えることはありません。 すべてのバックアップ ジョブは通常どおり続行され、アクティブな復元ジョブも中断なしで続行されます。 アップグレード中に、管理操作によって短いダウンタイムが発生します。新しい項目を保護することも、アドホック バックアップ ジョブを作成することもできません。 IaaS VM の復元ジョブは、アップグレード中は実行されません。 コンテナーのアップグレードは、1 時間以内に完了します。 終了すると、Recovery Services コンテナーが Backup コンテナーに置き換えられます。

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>アップグレード後のオートメーションおよびツールの変更

コンテナーをアップグレードするためにインフラストラクチャーを準備するときに、既存のオートメーションまたはツールがアップグレード後も引き続き動作できるように、それらをアップグレードする必要があります。
[Service Manager デプロイメント モデル](backup-client-automation-classic.md)と [Resource Manager デプロイメント モデル](backup-client-automation.md)用の PowerShell コマンドレット リファレンスを参照してください。


## <a name="before-you-upgrade"></a>アップグレードする前に

Backup コンテナーを Recovery Service コンテナーにアップグレードする前に、次の問題を調べてください。

- **エージェントの最小バージョン**: コンテナーをアップグレードするには、Microsoft Azure Recovery Services (MARS) エージェントのバージョンが少なくとも 2.0.9083.0 であることを確認します。 MARS エージェントが 2.0.9083.0 よりも古い場合は、アップグレード プロセスを開始する前にエージェントを更新してください。
- **インスタンス ベースの課金モデル**: Recovery Service コンテナーは、インスタンス ベースの課金モデルのみをサポートしています。 古いストレージ ベース課金モデルを使用しているバックアップ コンテナーがある場合は、アップグレード中に課金モデルを変換してください。
- **進行中のバックアップ構成操作がない**: アップグレード中は、管理プレーンへのアクセスは制限されています。 すべての管理プレーンの操作を完了してからアップグレードを開始してください。

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>PowerShell スクリプトを使用したコンテナーのアップグレード

PowerShell スクリプトを使用して、Backup コンテナーを Recovery Services コンテナーにアップグレードできます。 コンテナーのアップグレードを開始するために必要な PowerShell コンポーネントがあることを確認してください。 Backup コンテナー用の PowerShell スクリプトは、Recovery Services コンテナーでは動作しません。 コンテナーをアップグレードできるように環境を準備します。

1. [Windows Management Framework (WMF) のバージョン 5](https://www.microsoft.com/download/details.aspx?id=50395) 以降をインストールするか、そのバージョンにアップグレードします。
2. [Azure PowerShell MSI をインストール](https://github.com/Azure/azure-powershell/releases/download/v3.8.0-April2017/azure-powershell.3.8.0.msi)します。
3. コンテナーをアップグレードするための [PowerShell スクリプト](https://aka.ms/vaultupgradescript2)をダウンロードします。

### <a name="run-the-powershell-script"></a>PowerShell スクリプトの実行

次のスクリプトを使用して、コンテナーをアップグレードします。 次のサンプル スクリプトには、パラメーターの説明があります。

RecoveryServicesVaultUpgrade-1.0.2.ps1 **-SubscriptionID** `<subscriptionID>` **-VaultName** `<vaultname>` **-Location** `<location>` **-ResourceType** `BackupVault` **-TargetResourceGroupName** `<rgname>`

**SubscriptionID** - アップグレードするコンテナーのサブスクリプション ID 番号。<br/>
**VaultName** - アップグレードする Backup コンテナーの名前。<br/>
**Location** - アップグレードするコンテナーの場所。<br/>
**ResourceType** - BackupVault を使用します。<br/>
**TargetResourceGroupName** - コンテナーは Resource Manager ベースのデプロイにアップグレードされるため、リソース グループを指定します。 既存のリソース グループを使用することも、新しい名前を指定して作成することもできます。 リソース グループの名前のスペルを間違えた場合は、新しいリソース グループが作成されます。 リソース グループの詳細については、[こちら](../azure-resource-manager/resource-group-overview.md#resource-groups)のリソース グループの概要に関する記事を参照してください。

>[!NOTE]
> リソース グループの名前には制約があります。 必ずこのガイダンスに従ってください。従わなかった場合、コンテナーのアップグレードが失敗する可能性があります。
>
>**Azure US Government** のお客様は、スクリプトの実行中に環境を "AzureUSGovernment" に設定する必要があります。

次のコード スニペットは、PowerShell コマンドの例を示しています。

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

スクリプトはパラメーターなしで実行することもできます。この場合は、すべての必須パラメーターを入力することを求められます。

PowerShell スクリプトによって、資格情報を入力することを求められます。 資格情報は 2 回入力します。1 回目の入力は Service Manager アカウント用であり、2 回目は Resource Manager アカウント用です。

### <a name="pre-requisites-checking"></a>前提条件のチェック
Azure の資格情報を入力すると、Azure によって、環境が次の前提条件を満たしていることがチェックされます。

- **エージェントの最小バージョン** - バックアップ コンテナーを Recovery Services コンテナーにアップグレードするには、MARS エージェントのバージョンが少なくとも 2.0.9083.0 である必要があります。 2.0.9083.0 より前のエージェントを使用してバックアップ コンテナーに登録した項目がある場合、前提条件のチェックは失敗します。 前提条件のチェックが失敗した場合は、エージェントを更新し、コンテナーのアップグレードをやり直してください。 エージェントの最新バージョンは、[http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe](http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) からダウンロードできます。
- **進行中の構成ジョブ**: アップグレードするように設定した Backup コンテナーに対するジョブの構成または項目の登録が進行中の場合、前提条件のチェックは失敗します。 構成を完了するか、項目の登録を終了した後、コンテナーのアップグレード プロセスを開始してください。
- **ストレージ ベースの課金モデル**: Recovery Services コンテナーは、インスタンス ベースの課金モデルをサポートします。 ストレージ ベースの課金モデルを使用している Backup コンテナーでコンテナーのアップグレードを実行した場合、コンテナーと共に課金モデルもアップグレードするよう求められます。 または、課金モデルを更新した後でコンテナーのアップグレードを実行できます。
- Recovery Services コンテナーのリソース グループを識別します。 Resource Manager デプロイ機能を活用するには、Recovery Services コンテナーをリソース グループ内に配置する必要があります。 使用するリソース グループがわからない場合は、名前を指定すると、アップグレード プロセスによってリソース グループが作成されます。 さらに、アップグレード プロセスは、新しいリソース グループにコンテナーを関連付けます。

アップグレード プロセスで前提条件のチェックが完了すると、コンテナーのアップグレードを開始することに求められます。 確認後に実行されるアップグレード プロセスは、通常は約 15 ～ 20 分で完了しますが、時間はコンテナーのサイズによって異なります。 大規模なコンテナーでは、アップグレードは最大 90 分かかる可能性があります。

## <a name="managing-your-recovery-services-vaults"></a>Recovery Services コンテナーの管理

次の画面は、Azure ポータルで Backup コンテナーからアップグレードされた新しい Recovery Services コンテナーを示しています。 最初の画面は、コンテナーの重要なエンティティを表示するコンテナー ダッシュボードを示しています。

![Backup コンテナーからアップグレードされた Recovery Services コンテナーの例](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

2 番目の画面は、Recovery Services コンテナーの使用を開始するために役立つリンク ヘルプを示しています。

![[クイック スタート] ブレードのヘルプ リンク](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>アップグレード後の手順
Recovery Services コンテナーでは、バックアップ ポリシーでのタイム ゾーン情報の指定をサポートしています。 コンテナーが正常にアップグレードされたら、コンテナー設定メニューからバックアップ ポリシーに移動し、コンテナーで構成されているポリシーごとにタイム ゾーン情報を更新します。 この画面には、ポリシーの作成時に使用したローカル タイム ゾーンに従って指定されたバックアップのスケジュール時刻が既に表示されています。 

## <a name="enhanced-security"></a>強化されたセキュリティ

Backup コンテナーを Recovery Services コンテナーにアップグレードすると、そのコンテナーのセキュリティ設定が自動的に有効になります。 セキュリティ設定が有効になると、バックアップの削除やパスフレーズの変更などの特定の操作で [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) の PIN が必要になります。 強化されたセキュリティの詳細については、[ハイブリッド バックアップを保護するためのセキュリティ機能](backup-azure-security-feature.md)に関する記事をご覧ください。 

強化されたセキュリティを有効にすると、データは、復旧ポイント情報がコンテナーから削除されてから最大 14 日間保持されます。 このセキュリティ データのストレージに対して課金されます。 セキュリティ データのリテンション期間は、Azure Backup エージェント、Azure Backup Server、System Center Data Protection Manager に対して作成された復旧ポイントに適用されます。 

## <a name="gather-data-on-your-vault"></a>コンテナー上のデータの収集

Recovery Services コンテナーにアップグレードしたら、Azure Backup のレポート (IaaS VM および Microsoft Azure Recovery Services (MARS) 用) を構成し、Power BI を使用してレポートにアクセスします。 データの収集の詳細については、記事「[Azure Backup のレポートを構成する](backup-azure-configure-reports.md)」を参照してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**アップグレード プランは進行中のバックアップに影響を与えますか?**</br>
いいえ。 進行中のバックアップは、アップグレード中もアップグレード後も、中断なく続行されます。

**近日中にアップグレードする予定がない場合、コンテナーはどうなりますか?**</br>
すべての新機能は Recovery Services コンテナーにのみ適用されるので、コンテナーをアップグレードすることをお勧めします。 Microsoft は、最終的にクラシック ポータルを廃止する予定です。 2017 年 9 月 1 日から、Microsoft は Recovery Services コンテナーへのバックアップ コンテナーの自動アップグレードを開始します。 アップグレード プロセスは、2017 年 11 月 1 日までに完了する予定です。 コンテナーは、9 月または 10 月中にいつでも自動的にアップグレードできます。 できるだけ早急にコンテナーをアップグレードすることをお勧めします。

**このアップグレードは、既存のツールにとってどのような意味がありますか?**</br>
ツールを Resource Manager デプロイメント モデルに更新します。 Recovery Services コンテナーは、Resource Manager デプロイメント モデルで使用するように作成されています。 Resource Manager デプロイメント モデルの計画を立て、コンテナーの違いを考慮することが重要です。 

**アップグレード中のダウンタイムはどれくらいですか?**</br>
アップグレードされるリソースの数によって異なります。 小規模なデプロイ (保護されたインスタンスが数十) では、アップグレード全体は 20 分未満で実行されます。 大規模なデプロイでは、最大 1 時間かかります。

**アップグレード後にロールバックすることはできますか?**</br>
いいえ。 リソースが正常にアップグレードされた後のロールバックはサポートされていません。

**サブスクリプションまたはリソースがアップグレードが可能かどうかを確認できますか?**</br>
はい。 アップグレードの最初の手順は、リソースがアップグレード可能であることを検証することです。 前提条件の検証が失敗した場合は、アップグレードを完了できない理由をすべて通知するメッセージが表示されます。

**コンテナーのアップグレードを開始するために必要なアクセス許可は何ですか?**</br>
コンテナーのアップグレードを実行するには、Azure クラシック ポータルにサブスクリプションの共同管理者として追加されている必要があります。 これは、Azure ポータルに所有者として既に表示されている場合でも必要です。 試しに、Azure クラシック ポータルでサブスクリプションの共同管理者の追加を実行して、自分がサブスクリプションの共同管理者になっているかどうかを確認してください。 共同管理者を追加できない場合は、サブスクリプションのサービス管理者または共同管理者に連絡して、共同管理者として追加してもらってください。

**CSP ベースの Backup コンテナーはアップグレードできますか?**</br>
いいえ。 現時点では、CSP ベースのバックアップ コンテナーはアップグレードできません。 次のリリースで、CSP ベースのバックアップ コンテナーのアップグレードのサポートが追加される予定です。

**アップグレード後に、従来のコンテナーを表示できますか?**</br>
いいえ。 アップグレード後は、従来のコンテナーは、表示することも管理することもできません。 コンテナーのすべての管理操作は、新しい Azure ポータルを使用してのみ実行できます。

**アップグレードに失敗しましたが、更新する必要があるエージェントを保持するコンピューターがもう存在しません。このような場合はどうすればよいでしょうか?**</br>
コンピューターのバックアップを長期間保存する必要がある場合は、コンテナーをアップグレードすることはできません。 今後のリリースで、このようなコンテナーのアップグレードのサポートが追加される予定です。
コンピューターのバックアップを保存する必要がなくなった場合は、コンテナーからそのコンピューターの登録を解除してから、アップグレードをやり直してください。

**アップグレード後にリソースのジョブ情報が表示されないのはなぜですか?**</br>
バックアップの監視 (MARS エージェントおよび IaaS) は、バックアップ コンテナーを Recovery Services コンテナーにアップグレードした後で利用できる新しい機能です。 監視情報がサービスと同期されるまで、最大 12 時間かかります。

**問題はどのようにレポートすればよいですか?**</br>
コンテナーのアップグレードの一部が失敗した場合は、エラーに示されている OperationId を書き留めます。 Microsoft サポートが問題の解決に積極的に取り組みます。 サポートを受けるには、サブスクリプション ID、コンテナー名、OperationId を記載した電子メールを rsvaultupgrade@service.microsoft.com にお送りください。 Microsoft は、問題をできるだけ速やかに解決するよう努力します。 Microsoft による明確な指示がない限り、操作をやり直さないでください。


## <a name="next-steps"></a>次のステップ
次に関する記事を参照してください。</br>
[IaaS VM のバックアップ](backup-azure-arm-vms-prepare.md)</br>
[Azure Backup Server のバックアップ](backup-azure-microsoft-azure-backup.md)</br>
[Windows Server のバックアップ](backup-configure-vault.md).


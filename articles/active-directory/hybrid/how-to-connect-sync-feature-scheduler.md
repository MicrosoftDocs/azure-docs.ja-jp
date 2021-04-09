---
title: 'Azure AD Connect 同期: スケジューラ |Microsoft Docs'
description: このトピックでは、Azure AD Connect 同期の組み込みのスケジューラ機能について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad7b0039602add7f4cd3cdd300bd829c4f148a79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90084738"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Azure AD Connect 同期: Scheduler
このトピックでは、Azure AD Connect 同期の組み込みのスケジューラ (同期エンジン) について説明します。

この機能は、ビルド 1.1.105.0 (2016 年 2 月リリース) で導入されました。

## <a name="overview"></a>概要
Azure AD Connect 同期は、オンプレミス ディレクトリで発生した変更を、スケジューラを使用して同期します。 スケジューラが行う処理は 2 つあり、1 つはパスワードの同期で、もう 1 つはオブジェクト/属性の同期と、メンテナンス タスクです。 このトピックでは、後者について説明します。

以前のリリースでは、オブジェクトと属性のスケジューラは同期エンジンの外部にあり、 Windows タスク スケジューラまたは別の Windows サービスを使用して、同期プロセスをトリガーしました。 スケジューラは、1.1 リリースで同期エンジンに組み込まれ、いくつかのカスタマイズが可能になりました。 新しい既定の同期頻度は、30 分です。

スケジューラは、次の 2 つのタスクを担当します。

* **同期サイクル**。 変更をインポート、同期、およびエクスポートする処理です。
* **メンテナンス タスク**。 パスワードのリセットと Device Registration Service (DRS) のためにキーと証明書を更新します。 操作ログの古いエントリを消去します。

スケジューラ自体は常に実行されていますが、これらのタスクの 1 つだけを実行したり、どれも実行しないように構成することができます。 たとえば、独自の同期サイクル処理が必要な場合は、スケジューラのこのタスクを無効にし、メンテナンス タスクは実行するように指定できます。

>[!IMPORTANT]
>既定では、30 分おきに同期サイクルが実行されます。 同期サイクルを変更した場合、少なくとも 7 日に 1 回は同期サイクルが実行されるようにする必要があります。 
>
>* 差分同期は、最後の差分同期から 7 日以内に実行されることが必要です。
>* (完全同期に続く) 差分同期は、完全同期が最後に完了してから 7 日以内に実行されることが必要です。
>
>この条件が満たされなかった場合、解決に完全同期の実行を要する同期の問題が発生する可能性があります。 これはステージング モードのサーバーにも当てはまります。

## <a name="scheduler-configuration"></a>スケジューラの構成
現在の構成設定を確認するには、PowerShell に移動し、 `Get-ADSyncScheduler`を実行します。 次のような設定が表示されます。

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

このコマンドレットを実行するときに「 **sync コマンドまたはコマンドレットを使用できません** 」と表示される場合、PowerShell モジュールが読み込まれません。 この問題は、PowerShell 制限レベルが既定の設定よりも高いドメイン コント ローラーまたはサーバーで Azure AD Connect を実行する場合に発生する可能性があります。 このエラーが発生する場合、 `Import-Module ADSync` を実行してコマンドレットを使用できるようにします。

* **AllowedSyncCycleInterval**。 Azure AD で利用できる同期サイクルの最短の時間間隔です。 この設定よりも頻繁に同期してサポートを受けることはできません。
* **CurrentlyEffectiveSyncCycleInterval**。 現時点で有効になっているスケジュールです。 AllowedSyncInterval よりも高い頻度でない場合は、CustomizedSyncInterval と同じ値です (設定されている場合)。 1\.1.281 より前のビルドを使用している場合、CustomizedSyncCycleInterval を変更すると、次回の同期サイクルの後に有効になります。 ビルド 1.1.281 以降を使用している場合、変更はすぐに反映されます。
* **CustomizedSyncCycleInterval**。 既定の 30 分以外の頻度でスケジューラを実行する場合は、この設定を構成します。 上の図では、スケジューラは 1 時間ごとに実行されるように設定されています。 この設定を AllowedSyncInterval より低い値に設定すると、その値が使用されます。
* **NextSyncCyclePolicyType**。 Delta または Initial です。 次回の実行で、変更の差分だけを処理するのか、完全なインポートと同期を行って、新規の規則と変更した規則のすべてを再処理するのかを定義します。
* **NextSyncCycleStartTimeInUTC**。 スケジューラが次回の同期サイクルを開始する時刻です。
* **PurgeRunHistoryInterval**。 操作ログを保持する時間です。 ログは Synchronization Service Manager で参照できます。 既定では、ログは 7 日間保持されます。
* **SyncCycleEnabled**。 スケジューラが、その操作の一部としてインポート、同期、およびエクスポート処理を実行しているかどうかを示します。
* **MaintenanceEnabled**。 メンテナンス処理が有効になっているかどうかを示します。 メンテナンス処理では、証明書/キーの更新と、操作ログの消去が行われます。
* **StagingModeEnabled**。 [ステージング モード](how-to-connect-sync-staging-server.md) が有効になっているかどうかを示します。 この設定が有効になっている場合、エクスポートは実行されませんが、インポートと同期は引き続き実行されます。
* **SchedulerSuspended**。 アップグレード中にスケジューラの実行を一時的にブロックするために Connect によって設定されます。

これらの設定のいくつかは、 `Set-ADSyncScheduler`を使用して変更できます。 次のパラメーターに変更を加えることができます。

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

Azure AD Connect の以前のビルドでは、**isStagingModeEnabled** は Set-ADSyncScheduler で公開されていました。 このプロパティの設定は **サポートされていません**。 プロパティ **SchedulerSuspended** は Connect でのみ変更する必要があります。 PowerShell で直接これを設定することは **サポートされていません**。

スケジューラの構成は Azure AD に保存されます。 ステージング サーバーがある場合、プライマリ サーバーでの変更はステージング サーバーにも影響します (IsStagingModeEnabled を除く)。

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
構文: `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - 日数、HH - 時間数、mm - 分数、ss - 秒数

例: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
スケジューラが 3 時間ごとに実行されるように変更されます。

例: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
スケジューラが毎日実行されるように変更されます。

### <a name="disable-the-scheduler"></a>スケジューラを無効にする  
構成を変更する必要がある場合は、スケジューラを無効にします。 たとえば、[フィルタ リングを構成](how-to-connect-sync-configure-filtering.md)したり、[同期規則を変更](how-to-connect-sync-change-the-configuration.md)したりするときです。

スケジューラを無効にするには、`Set-ADSyncScheduler -SyncCycleEnabled $false` を実行します。

![スケジューラを無効にする](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

変更したら、`Set-ADSyncScheduler -SyncCycleEnabled $true` で忘れずにスケジューラを有効にしてください。

## <a name="start-the-scheduler"></a>スケジューラの開始
スケジューラは、既定では 30 分ごとに実行されます。 場合によっては、スケジュールされたサイクル間に同期サイクルを実行したり、別の種類を実行する必要があったりします。

### <a name="delta-sync-cycle"></a>差分同期サイクル
差分同期サイクルには、以下の手順が含まれています。


- すべてのコネクタでの差分インポート
- すべてのコネクタでの差分同期
- すべてのコネクタでのエクスポート

### <a name="full-sync-cycle"></a>完全同期サイクル
完全同期サイクルの手順は、次のとおりです。

- すべてのコネクタでのフル インポート
- すべてのコネクタでの完全同期
- すべてのコネクタでのエクスポート

緊急な変更が発生し、直ちに同期しなければならないことがあります。そのような場合は、サイクルを手動で実行する必要があります。 

同期サイクルを手動で実行する必要がある場合は、PowerShell で `Start-ADSyncSyncCycle -PolicyType Delta` を実行します。

完全同期サイクルを開始するには、PowerShell プロンプトで `Start-ADSyncSyncCycle -PolicyType Initial` を実行します。   

完全同期サイクルを実行すると非常に時間がかかる場合があります。このプロセスを最適化する方法について、次のセクションをご覧ください。

### <a name="sync-steps-required-for-different-configuration-changes"></a>さまざまな構成の変更に必要な同期手順
すべてのオブジェクトに変更を確実に正しく適用するために、異なる構成の変更には異なる同期手順が必要です。

- (同期規則の追加/変更により) ソース ディレクトリからインポートされるオブジェクトまたは属性を追加した
    - そのソース ディレクトリのコネクタでフル インポートが必要です
- 同期規則を変更した
    - 変更した同期規則のコネクタで完全同期が必要です
- 異なる数のオブジェクトが含まれるように [フィルター処理](how-to-connect-sync-configure-filtering.md) を変更した
    - 同期エンジンにインポート済みの属性に基づいた属性ベースのフィルター処理を使用している場合を除いて、各 AD コネクタのコネクタでフル インポートが必要です

### <a name="customizing-a-sync-cycle-run-the-right-mix-of-delta-and-full-sync-steps"></a>差分同期と完全同期の手順の適切な組み合わせを実行する同期サイクルのカスタマイズ
完全同期の実行を回避するために、次のコマンドレットを使用して、完全同期の手順を実行する特定のコネクタにマークを付けることができます。

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullSyncRequired $true`

`Get-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid>` 

例:コネクタ "AD Forest A" の同期規則に対して、新しい属性のインポートを必要としない変更を加えた場合、次のコマンドレットを実行して、このコネクタに対する完全同期手順も実行する差分同期サイクルを実行します。

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`

例:コネクタ "AD Forest A" の同期規則に対して、今度は新しい属性のインポートが必要な変更を加えた場合、次のコマンドレットを実行して、このコネクタに対するフル インポートと完全同期手順も実行する差分同期サイクルを実行します。

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`


## <a name="stop-the-scheduler"></a>スケジューラの停止
スケジューラが同期サイクルを実行中に、停止しなければならない場合があります。 たとえば、インストール ウィザードを開始して、次のエラーが発生したとします。

![スクリーンショットは、構成を変更できないというエラー メッセージを示しています。](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

同期サイクルの実行中は、構成を変更できません。 スケジューラが処理を終了するまで待つこともできますが、すぐに変更を行うことができるように、スケジューラを停止することもできます。 現在のサイクルを停止しても害はなく、保留になった変更は次回の実行で処理されます。

1. まず、PowerShell コマンドレット `Stop-ADSyncSyncCycle` で、現在のサイクルを停止するようスケジューラに指示します。
2. 1\.1.281 より前のビルドを使用している場合は、スケジューラを停止しても、現在のコネクタの現在のタスクは停止しません。 コネクタを強制的に停止させるには、以下の操作を行います。

   ![スクリーンショットでは、Sychronization Service Manager の [コネクタ] が選択されています。実行中の 1 つのコネクタが強調表示されて、停止アクションが選択されています。](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)

   * [スタート] メニューから **[Synchronization Service (同期サービス)]** を起動します。 **[コネクタ]** に移動し、状態が **[実行中]** のコネクタを強調表示して、[操作] で **[停止]** を選択します。

スケジューラはアクティブなままで、次の機会に再度開始されます。

## <a name="custom-scheduler"></a>カスタム スケジューラ
このセクションに記載されているコマンドレットは、[1.1.130.0](reference-connect-version-history.md) 以降でのみ利用できます。

組み込みのスケジューラで自社の要件を満たすことができない場合は、PowerShell を使用してコネクタをスケジュールすることができます。

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
次の方法でコネクタのプロファイルを開始できます。

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

[コネクタ名](how-to-connect-sync-service-manager-ui-connectors.md)と[実行プロファイル名](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles)に使用する名前は、[Synchronization Service Manager UI](how-to-connect-sync-service-manager-ui.md) で確認できます。

![Invoke Run Profile](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

`Invoke-ADSyncRunProfile` コマンドレットは同期的なコマンドレットです。つまり、コネクタが操作に成功するかエラーが発生することで操作を終えるまで制御を返しません。

コネクタをスケジュールする際は、次の順序でスケジュールすることをお勧めします。

1. (完全/差分) Active Directory などのオンプレミスのディレクトリからインポートする
2. (完全/差分) Azure AD からインポートする
3. (完全/差分) Active Directory などのオンプレミスのディレクトリから同期する
4. (完全/差分) Azure AD から同期する
5. Azure AD にエクスポートする
6. Active Directory などのオンプレミスのディレクトリにエクスポートする

組み込みのスケジューラの場合も、コネクタはこの順序で実行されます。

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
同期エンジンがビジー状態かアイドル状態かを監視することができます。 同期エンジンがアイドル状態で、コネクタが実行されていない場合、このコマンドレットは空の結果を返します。 コネクタが実行されている場合は、コネクタの名前を返します。

```
Get-ADSyncConnectorRunStatus
```

![Connector Run Status](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
上の図の 1 行目は、同期エンジンがアイドル状態であることを示しています。 2 行目は、Azure AD コネクタが実行されていることを示しています。

## <a name="scheduler-and-installation-wizard"></a>スケジューラとインストール ウィザード
インストール ウィザードを開始すると、スケジューラは一時的に中断されます。 この動作は、構成が変更されても、同期エンジンがアクティブに実行されていると、この設定を適用できないためです。 このようなしくみになっているので、インストール ウィザードを開いたままにしないでください。開いたままにすると、同期エンジンが同期操作を実行できません。

## <a name="next-steps"></a>次のステップ
[Azure AD Connect Sync](how-to-connect-sync-whatis.md) の構成に関するページをご覧ください。

「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。

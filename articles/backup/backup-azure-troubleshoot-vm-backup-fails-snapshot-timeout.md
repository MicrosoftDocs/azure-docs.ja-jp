---
title: "Azure Backup エラー &quot;スナップショット VM サブタスクのタイムアウト&quot; のトラブルシューティング | Microsoft Docs"
description: "Azure Backup エラー &quot;スナップショットの状態について VM エージェントと通信できませんでした。スナップショット VM サブタスクがタイムアウトしました&quot; の症状、原因、および解決策"
services: backup
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: genli;markgal;
translationtype: Human Translation
ms.sourcegitcommit: 26ea5c6f867165a25dd5aecb01d0a0ce3b213a51
ms.openlocfilehash: 707d666eb6c23fb926c31711daddfb22979513bc
ms.lasthandoff: 01/25/2017

---

# <a name="troubleshoot-azure-backup-failure-snapshot-vm-sub-task-timed-out"></a>Azure Backup エラー "スナップショット VM サブタスクのタイムアウト" のトラブルシューティング
## <a name="summary"></a>まとめ
Azure Backup サービスに VM を登録して、スケジュール設定すると、Backup サービスは、VM のバックアップ拡張機能と通信してジョブを開始し、ポイントインタイム スナップショットを作成します。 スナップショットをトリガーできず、それがバックアップ エラーにつながる条件は&4; つあります。 この記事では、スナップショットのタイムアウト エラーに関連する Backup エラーを解決するためのトラブルシューティング手順について説明します。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>症状
サービスとしてのインフラストラクチャ (IaaS) VM に対する Azure Backup が失敗し、[Azure Portal](https://portal.azure.com/) のジョブ エラーの詳細で "スナップショットの状態について VM エージェントと通信できませんでした - スナップショット VM サブタスクがタイムアウトしました" というエラー メッセージが表示されます。

## <a name="cause-1-the-vm-has-no-internet-access"></a>原因 1: VM がインターネットにアクセスできない
デプロイ要件によっては、VM がインターネットにアクセスできない場合や、設定されている制限により、Azure インフラストラクチャにアクセスできない場合があります。

バックアップ拡張機能が正常に機能するには、Azure のパブリック IP アドレスへの接続が必要です。 この拡張機能が、VM のスナップショットを管理するコマンドを、Azure Storage エンドポイント (HTTP URL) に送信するためです。 拡張機能がパブリック インターネットにアクセスできない場合は、Backup は最終的に失敗します。

### <a name="solution"></a>解決策
この問題を解決するには、次の方法のいずれかを試してください。
#### <a name="allow-access-to-the-azure-datacenter-ip-ranges"></a>Azure データセンターの IP 範囲へのアクセスを許可する

1. アクセスを許可する [Azure データセンターの IP の一覧](https://www.microsoft.com/download/details.aspx?id=41653)を取得します。
2. 管理者特権の PowerShell ウィンドウで、Azure VM 内で **New-NetRoute** コマンドレットを実行して、IP のブロックを解除します。 コマンドレットは、管理者として実行してください。
3. IP へのアクセスを許可するには、規則をネットワーク セキュリティ グループに追加します (ネットワーク セキュリティ グループがある場合)。

#### <a name="create-a-path-for-http-traffic-to-flow"></a>フローに対する HTTP トラフィック用のパスを作成する

1. ネットワーク制限 (ネットワーク セキュリティ グループなど) を設定している場合は、トラフィックをルーティングするための HTTP プロキシ サーバーをデプロイします。
2. HTTP プロキシからインターネットへのアクセスを許可するには、規則をネットワーク セキュリティ グループに追加しましす (ネットワーク セキュリティ グループがある場合)。

VM バックアップの HTTP プロキシを設定する方法については、「[Azure 仮想マシンをバックアップする環境の準備](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups)」を参照してください。

## <a name="cause-2-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>原因 2: VM にインストールされているエージェントが古くなっている (Linux VM の場合)

### <a name="solution"></a>解決策
Linux VM の場合、エージェントに関連するエラーまたは拡張機能に関連するエラーのほとんどは、古い VM エージェントに影響する問題が原因で発生します。 この問題のトラブルシューティングを行うには、次の一般的なガイドラインに従います。

1. [Linux VM エージェントを更新](../virtual-machines/virtual-machines-linux-update-agent.md)する手順に従います。

 >[!NOTE]
 >ディストリビューション リポジトリを通してのみエージェントを更新することを "*強くお勧め*" します。 エージェント コードを直接 GitHub からダウンロードして、更新することはお勧めしません。 最新のエージェントをディストリビューションで使用できない場合は、そのエージェントをインストールする方法をディストリビューション サポートにお問い合わせください。 最新のエージェントを確認するには、GitHub リポジトリの [Windows Azure Linux エージェント](https://github.com/Azure/WALinuxAgent/releases)のページをご覧ください。

2. `ps -e` コマンドを実行して、Azure エージェントが VM で実行されていることを確認します。

 このプロセスが実行されていない場合は、次のコマンドを使用してプロセスを再起動します。

 * Ubuntu の場合: `service walinuxagent start`
 * その他のディストリビューションの場合: `service waagent start`

3. [エージェントの自動再起動を構成します](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)。
4. 新しいテスト バックアップを実行します。 エラーが解決しない場合は、お客様の VM から次のログを収集してください。

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

waagent の詳細ログが必要な場合は、次の手順に従います。

1. /etc/waagent.conf ファイルで、次の行を見つけます: **Enable verbose logging (y|n)**
2. **Logs.Verbose** の値を *n* から *y* に変更します。
3. 変更を保存した後、このセクションの前の手順に従って waagent を再起動します。

## <a name="cause-3-the-backup-extension-fails-to-update-or-load"></a>原因 3: バックアップ拡張機能の更新または読み込みに失敗した
拡張機能を読み込むことができないと、スナップショットを作成できないため、Backup が失敗します。

### <a name="solution"></a>解決策

Windows ゲストの場合:

iaasvmprovider サービスが有効になっていて、スタートアップの種類が "*自動*" になっていることを確認します。 サービスがこのように構成されていない場合は、そのサービスを有効にして、次のバックアップが成功するかどうかを確認します。

Linux ゲストの場合:

最新バージョンの VMSnapshot for Linux (Backup で使用されている拡張機能) は 1.0.91.0 です。

バックアップ拡張機能の更新または読み込みがまだ失敗する場合は、VMSnapshot 拡張機能をインストールして強制的に再度読み込まれるようにします。 次回のバックアップ時に、拡張機能が再度読み込まれます。

拡張機能をアンインストールするには、以下の手順を実行します。

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。
2. バックアップの問題が発生している VM を見つけます。
3. **[設定]**をクリックします。
4. **[拡張機能]** をクリックします。
5. **[Vmsnapshot 拡張機能]** をクリックします。
6. **[アンインストール]** をクリックします。  

この手順により、次回のバックアップ時に拡張機能が再インストールされます。

## <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>原因 4: スナップショットの状態を取得できなかったか、スナップショットを作成できなかった
VM のバックアップは、基礎となるストレージ アカウントへのスナップショット コマンドの発行に依存します。 ストレージ アカウントにアクセスできないか、スナップショット タスクの実行が遅れているために、Backup が失敗することがあります。

### <a name="solution"></a>解決策
次の場合にスナップショットのタスクが失敗することがあります。

| 原因 | 解決策 |
| --- | --- |
| VM で SQL Server のバックアップが構成されている。 | 既定では、VM のバックアップは Windows VM で VSS 完全バックアップとして実行されます。 SQL Server ベースのサーバーを実行し、SQL Server のバックアップが構成されている VM では、スナップショットの実行の遅延が発生する場合があります。<br><br>スナップショットに関する問題により Backup エラーが発生する場合は、次のレジストリ キーを設定してください。<br><br>**[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE"** |
| VM が RDP でシャットダウンされているため、VM の状態が正しく報告されない。 | リモート デスクトップ プロトコル (RDP) で VM をシャットダウンした場合は、ポータルでその VM の状態が正しいかどうかを確認します。 正しくない場合は、VM のダッシュボードの **[シャットダウン]** オプションを使用して、ポータルで VM をシャットダウンします。 |
| 同じクラウド サービスから多数の VM が同時にバックアップするように構成されている。 | 同じクラウド サービスの VM については、バックアップ スケジュールを分散させることをお勧めします。 |
| VM の CPU またはメモリの使用率が高くなっている。 | VM の CPU 使用率が高い (90% を超える) 場合、またはメモリ使用率が高い場合、スナップショット タスクがキューに配置され、遅延し、最終的にタイムアウトになります。 この場合は、オンデマンド バックアップを試してください。 |
| VM が DHCP からホスト/ファブリック アドレスを取得できない。 | IaaS VM バックアップが正しく機能するには、ゲスト内で DHCP が有効になっている必要があります。  VM が DHCP 応答 245 からホスト/ファブリック アドレスを取得できない場合は、拡張機能をダウンロードしたり実行したりできません。 静的プライベート IP が必要な場合は、プラットフォームを通じて構成する必要があります。 VM 内の DHCP オプションは有効のままにしておいてください。 詳細については、[静的内部プライベート IP の設定](../virtual-network/virtual-networks-reserved-private-ip.md)に関するページをご覧ください。 |


---
title: Azure VM のシステム再起動について | Microsoft Docs
description: VM の再起動につながる可能性のある一連のイベントを掲載しています
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: a536eb89f2040333617329e963ce00f5c6b1ba7a
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086972"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Azure VM のシステム再起動について

Azure 仮想マシン (VM) は、ユーザーが再起動操作を開始した事実も、はっきりした理由もないのに再起動することがあります。 この記事では、VM の再起動を発生させる可能性がある操作とイベントを示し、予期しない再起動の問題を回避する方法、またはそのような問題の影響を軽減する方法について洞察します。

## <a name="configure-the-vms-for-high-availability"></a>仮想マシンを高可用性を実現するように構成する

Azure で実行中のアプリケーションを VM の再起動とダウンタイムから保護する最善の方法は、高可用性を実現するよう仮想マシンを構成することです。

このレベルの冗長性をアプリケーションに提供するには、2 台以上の VM を可用性セットにグループ化することをお勧めします。 このような構成により、計画的または計画外のメンテナンス イベント中に、少なくとも 1 つの VM が利用可能となり、99.95% の [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/) が満たされます。

可用性セットの詳細については、次の記事を参照してください。

- [VM の可用性の管理](../windows/manage-availability.md)
- [VM の可用性の構成](../windows/classic/configure-availability.md)

## <a name="resource-health-information"></a>Resource Health の情報

Azure Resource Health は個々の Azure リソースの正常性を明らかにし、問題をトラブルシューティングするために実行できる操作をアドバイスするサービスです。 サーバーやインフラストラクチャの要素に直接アクセスすることができないクラウド環境での Resource Health の目標は、ユーザーがトラブルシューティングに費やす時間を短縮することです。 特に、問題の原因がアプリケーションにあるのか、Azure プラットフォームの内部で発生したイベントにあるのかを判別するために費やされる時間を短縮することが目標となります。 詳細については、[Resource Health の概要と使用方法](../../resource-health/resource-health-overview.md)に関するページを参照してください。

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>VM の再起動の原因となる可能性がある操作とイベント

### <a name="planned-maintenance"></a>計画済みのメンテナンス

Microsoft Azure は、世界各地で定期的に更新を行い、VM の基盤となるホスト インフラストラクチャの信頼性、パフォーマンス、セキュリティの向上に努めています。 これらの更新の多くは、VM やクラウド サービスに影響を及ぼさずに実行されます (メモリ保護更新など)。

ただし、再起動が必要な更新があります。 そのようなケースでは、インフラストラクチャに修正プログラムが適用されている間、VM はシャットダウンされ、その後再起動されます。

Azure の計画的メンテナンスの概要と、それが Linux VM の可用性に及ぼす影響については、次の記事を参照してください。 これらの記事は、Azure の計画的メンテナンス プロセスの背景と、影響を軽減するために計画的メンテナンスのスケジュールを設定する方法を示しています。

- [Azure での VM の計画的メンテナンス](../windows/planned-maintenance.md)
- [Azure VM の計画的メンテナンスのスケジュールを設定する方法](../windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>メモリ保護更新

Microsoft Azure のこのクラスの更新では、実行中の VM に関して、ユーザーが気付くほどの影響が生じることはありません。 これらの更新の多くは、実行中のインスタンスに干渉することがなく更新可能なコンポーネントまたはサービスを対象にしています。 一部はホスト オペレーティング システムのプラットフォーム インフラストラクチャの更新であり、VM の再起動を必要とせずに適用できます。

これらのメモリ保護更新は、インプレース ライブ マイグレーションを実現するテクノロジによって完了します。 更新中は、VM の状態が "*一時停止*" になります。 この状態で、RAM 内のメモリが保護され、基礎となるホスト オペレーティング システムが必要な更新プログラムと修正プログラムを受信します。 VM は、30 秒以内の一時停止で再開されます。 再開後、VM のクロックは自動的に同期されます。

一時停止の期間が短いことを考えると、このメカニズムによる更新のデプロイは、VM への影響を大幅に軽減します。 ただし、この方法ですべての更新をデプロイできるわけではありません。 

複数インスタンスの更新 (可用性セット内の VM) は、一度に 1 つの更新ドメインに適用されます。

> [!NOTE]
> カーネルのバージョンが古い Linux マシンでは、この更新方法では、カーネル パニックによる影響が発生します。 この問題を回避するには、カーネルのバージョンを 3.10.0-327.10.1 以降に更新します。 詳細については、[3.10 ベースのカーネルで実行中の Azure Linux VM でホスト ノードのアップグレード後に発生するパニック](https://support.microsoft.com/help/3212236)に関するページを参照してください。

### <a name="user-initiated-reboot-or-shutdown-actions"></a>ユーザーが開始した再起動/シャットダウン操作

再起動を Azure Portal、Azure PowerShell、コマンド ライン インターフェイス、または Reset API から実行した場合は、[Azure アクティビティ ログ](../../azure-monitor/platform/activity-logs-overview.md)でそのイベントを見つけることができます。

VM のオペレーティング システムから操作を実行した場合は、システム ログでそのイベントを見つけることができます。

VM の再起動の原因となる一般的なその他のシナリオとして、複数の構成を変更する操作があります。 通常、特定の操作を実行すると VM の再起動が発生することを示す警告メッセージが表示されます。 たとえば、VM のサイズ変更操作、管理アカウントのパスワードの変更、静的 IP アドレスの設定などの操作があります。

### <a name="azure-security-center-and-windows-update"></a>Azure Security Center と Windows の更新プログラム

Azure Security Center では、オペレーティング システムに不足している更新プログラムがないかどうかを確認するために、Windows と Linux の VM の監視を毎日行っています。 Security Center は、Windows VM に構成されているサービスに応じて、Windows Update または Windows Server Update Services (WSUS) から利用可能なセキュリティ更新プログラムと重要な更新プログラムの一覧を取得します。 また、Linux システムの最新の更新プログラムについてもチェックしています。 VM でシステムの更新プログラムが不足している場合、Security Center は、それらを適用することを推奨します。 これらのシステムの更新プログラムの適用は、Azure Portal で Security Center を通して制御されます。 一部の更新プログラムでは、その適用後に VM の再起動が必要になります。 詳細については、「[Azure Security Center でシステムの更新プログラムを適用する](../../security-center/security-center-apply-system-updates.md)」を参照してください。

Windows VM は、オンプレミスのサーバーと同じように、ユーザーによって管理されることを想定しているため、Azure 側からそれらの VM に Windows の更新プログラムをプッシュすることはありません。 ただし、自動 Windows Update の設定は有効のままにしておくことをお勧めします。 Windows Update による更新プログラムの自動インストールでも、更新プログラムの適用後に再起動が発生する可能性があります。 詳細については、「[Windows Update: FAQ](https://support.microsoft.com/help/12373/windows-update-faq)」を参照してください。

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>VM の可用性に影響するその他の状況

Azure によって VM の使用が能動的に停止される場合があります。 ユーザーは、この操作が行われる前に電子メール通知を受信するため、基になる問題を解決するチャンスがあります。 VM の可用性に影響する問題の例としては、セキュリティ違反や、支払いの有効期限切れがあります。

### <a name="host-server-faults"></a>ホスト サーバー エラー

VM は、Azure データセンター内で稼働している物理サーバーでホストされています。 物理サーバーでは、ホスト エージェントと呼ばれるエージェントと、他のいくつかの Azure コンポーネントが実行されています。 物理サーバー上のこれらの Azure ソフトウェア コンポーネントが応答しなくなると、復旧を試行するために、監視システムによってホスト サーバーの再起動がトリガーされます。 通常、VM は、5 分以内に再度利用可能になり、前と同じホスト上で動作を続行します。

サーバー エラーは通常、ハード ディスクやソリッドステート ドライブの障害など、ハードウェアの障害によって発生します。 Azure は、これらの発生を継続的に監視し、基になるバグが特定し、軽減策を実装してテストした後、更新をロールアウトします。

一部のホスト サーバー エラーはサーバー固有のエラーである可能性があるため、VM の再起動が繰り返し発生する場合は、別のホスト サーバーに VM を手動で再デプロイすると状況が改善することがあります。 この操作は、VM の詳細ページで **[再デプロイ]** オプションを使用するか、Azure Portal で VM を停止して再起動することでトリガーできます。

### <a name="auto-recovery"></a>自動復旧

ホスト サーバーが何らかの理由で再起動できない場合、Azure プラットフォームは、詳細な調査を行うために、障害のあるホスト サーバーをローテーションから除外する自動復旧操作を開始します。 

ホスト上のすべての VM は、正常に稼働している別のホスト サーバーに自動的に再配置されます。 このプロセスは、通常は 15 分以内に完了します。 自動復旧プロセスの詳細については、[VM の自動復旧](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines)に関するページを参照してください。

### <a name="unplanned-maintenance"></a>計画外のメンテナンス

まれなことではありますが、Azure の運用チームが、Azure プラットフォームの全体的な正常性を保証するために、メンテナンス アクティビティを実施しなければならないことがあります。 この行動が VM の可用性に影響を与える場合があります。通常は、前述と同じ自動復旧操作が発生します。  

計画外のメンテナンスには、次のものがあります。

- 緊急のノードの最適化
- 緊急のネットワーク スイッチの更新

### <a name="vm-crashes"></a>VM のクラッシュ

VM は、VM 自体の問題が原因で再起動することがあります。 VM で実行中のワークロードまたはロールによって、ゲスト オペレーティング システム内のバグ チェックがトリガーされる場合があります。 クラッシュの原因を判断する手掛かりとしては、Windows VM のアプリケーション ログや Linux VM のシリアル ログを参照してください。

### <a name="storage-related-forced-shutdowns"></a>ストレージ関連の強制シャットダウン

Azure の VM は、オペレーティング システムの仮想ディスクと Azure Storage インフラストラクチャでホストされているデータ ストレージに依存しています。 VM と関連する仮想ディスクとの間の可用性または接続が 120 秒を超えて影響を受けた場合、Azure プラットフォームは、データの破損を回避するために、常に VM の強制シャットダウンを実行します。 ストレージの接続が回復すると、VM は自動的に復旧します。 

シャットダウンの期間は 5 分ほどですが、大幅に長くなる可能性があります。 ストレージ関連の強制シャットダウンに関連する特殊なケースの 1 つを次に示します。 

**制限を超える IO**

1 秒あたりの I/O 操作の量 (IOPS) がディスクの I/O の上限を超えているために、I/O 要求のスロットルが連続的に行われていると、VM が一時的にシャットダウンされることがあります (たとえば Standard ディスク ストレージは 500 IOPS に制限されています)。この問題を軽減するには、ワークロードに応じて、ディスク ストライピングを使用するか、ゲスト VM 内に記憶域スペースを構成します。 詳細については、「[Configuring Azure VMs for Optimal Storage Performance](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)」(最適なストレージ パフォーマンスのための Azure Virtual Machines の構成) を参照してください。

### <a name="other-incidents"></a>その他のインシデント

まれな状況ですが、拡散する問題によって、Azure データセンターの複数のサーバーが影響を受けることがあります。 このような問題が発生した場合、Azure チームは、影響を受けるサブスクリプションに電子メール通知を送信します。 [Azure サービス正常性ダッシュボード](https://azure.microsoft.com/status/)と Azure Portal で、継続中の停止と過去のインシデントをチェックすることができます。

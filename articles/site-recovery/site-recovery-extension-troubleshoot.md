---
title: Azure Site Recovery を使用した ディザスター リカバリーのための Azure VM 拡張機能のトラブルシューティング
description: Azure Site Recovery を使用したディザスター リカバリーのための Azure VM 拡張機能に関する問題をトラブルシューティングします。
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: c1915d108bf9465d3e5b8d6a55053b583ee4f580
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009706"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Azure VM 拡張機能に関する問題のトラブルシューティング

この記事では、VM エージェントと拡張機能に関連する Azure Site Recovery のエラーの解決に役立つ可能性のあるトラブルシューティング手順について説明します。

## <a name="low-system-resources"></a>システム リソースが少ない

この問題は、システムで使用できるメモリが少なく、モビリティ サービスのインストールでメモリを割り当てることができないときに発生します。 インストールが進行し、正常に完了できるよう、確実に十分なメモリが解放されているようにします。

## <a name="azure-site-recovery-extension-time-out"></a>Azure Site Recovery 拡張機能がタイムアウトになる  

エラー メッセージ:「開始する拡張機能の操作の追跡中にタスクの実行がタイムアウトになりました」<br>
エラー コード:"151076"

 Azure Site Recovery は、保護の有効化ジョブの一部として、仮想マシンに拡張機能をインストールしました。 以下のいずれかの状況によって、保護をトリガーできず、ジョブが失敗する場合があります。 次のトラブルシューティング手順を完了してから、必要な操作を再試行してください。

- [エージェントが VM にインストールされているが応答しない (Windows VM の場合)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery 拡張機能の更新または読み込みに失敗した](#the-site-recovery-extension-fails-to-update-or-load)

エラー メッセージ:「前の Site Recovery 機能拡張の操作に想定以上に時間がかかっています。」<br>
エラー コード:"150066"

- [エージェントが VM にインストールされているが応答しない (Windows VM の場合)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery 拡張機能の状態が正しくない](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>VM エージェントが応答しないために保護に失敗する

エラー メッセージ:「開始する拡張機能の操作の追跡中にタスクの実行がタイムアウトになりました。」<br>
エラー コード:"151099"

このエラーは、仮想マシンの Azure ゲスト エージェントが準備完了状態にない場合に発生することがあります。

[Azure portal](https://portal.azure.com/) で Azure ゲスト エージェントの状態を調べられます。 保護しようとしている仮想マシンに移動し、 **[VM]**  >  **[設定]**  >  **[プロパティ]**  >  **[エージェントの状態]** と移動して状態を調べます。 ほとんどの場合、エージェントの状態は、仮想マシンの再起動後は準備完了状態です。 ただし、再起動できない場合や、再起動後も問題に直面している場合は、次のトラブルシューティング手順を完了してください。

- [エージェントが VM にインストールされているが応答しない (Windows VM の場合)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


エラー メッセージ:「開始する拡張機能の操作の追跡中にタスクの実行がタイムアウトになりました。」<br>
エラー コード:"151095"

これは、Linux マシン上のエージェントのバージョンが最新ではない場合に発生します。 次のトラブルシューティング手順を完了してください。

- [VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>原因とソリューション

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>エージェントが VM にインストールされているが応答しない (Windows VM の場合)

#### <a name="solution"></a>解決策
VM エージェントが破損しているまたはサービスが停止している可能性があります。 VM エージェントを再インストールすることで最新バージョンを入手できます。 その際に、サービスとの通信も再開されます。

1. VM サービス (services.msc) で Windows Azure ゲスト エージェント サービスが実行されているかどうかを確認します。 Windows Azure ゲスト エージェント サービスを再起動します。    
1. [サービス] に Windows Azure ゲスト エージェント サービスが表示されない場合は、[コントロール パネル] を開きます。 **[プログラムと機能]** に移動して、Windows ゲスト エージェント サービスがインストールされているかどうかを確認します。
1. Windows Azure ゲスト エージェント サービスが **[プログラムと機能]** に表示される場合は、Windows Azure ゲスト エージェントをアンインストールします。
1. [最新バージョンのエージェント MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) をダウンロードしてインストールします。 インストールを実行するには、管理者権限が必要です。
1. [サービス] に Windows Azure ゲスト エージェント サービスが表示されることを確認します。
1. 保護ジョブを再開します。

さらに、VM に [Microsoft .NET 4.5 がインストールされていること](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)を確認します。 サービスと通信するには、VM エージェント用の .NET 4.5 が必要です。

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM にインストールされているエージェントが古くなっている (Linux VM の場合)

#### <a name="solution"></a>解決策
Linux VM の場合、エージェントに関連するエラーまたは拡張機能に関連するエラーのほとんどは、古い VM エージェントに影響する問題が原因で発生します。 この問題のトラブルシューティングを行うには、次の一般的なガイドラインに従います。

1. [Linux VM エージェントを更新](../virtual-machines/extensions/update-linux-agent.md)する手順に従います。

   > [!NOTE]
   > ディストリビューション リポジトリを通してのみエージェントを更新することを "*強くお勧め*" します。 エージェント コードを GitHub から直接ダウンロードし、それを更新することは、推奨されません。 最新のエージェントをディストリビューションで使用できない場合は、そのエージェントをインストールする方法をディストリビューション サポートにお問い合わせください。 最新のエージェントを確認するには、GitHub リポジトリの [Windows Azure Linux エージェント](https://github.com/Azure/WALinuxAgent/releases)のページをご覧ください。

1. `ps -e` コマンドを実行して、Azure エージェントが VM で実行されていることを確認します。

   このプロセスが実行されていない場合は、次のコマンドを使用してプロセスを再起動します。

   - Ubuntu の場合: `service walinuxagent start`
   - その他のディストリビューションの場合: `service waagent start`

1. [エージェントの自動再起動を構成します](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)。
1. 仮想マシンの保護を有効にします。

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Site Recovery 拡張機能の更新または読み込みに失敗した

拡張機能の状態が、"空"、"準備不完了"、または "移行中" として表示されます。

#### <a name="solution"></a>解決策

拡張機能をアンインストールし、操作をもう一度開始します。

拡張機能をアンインストールするには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) で、バックアップ エラーが発生している VM に移動します。
1. **[設定]** を選択します。
1. **[拡張機能]** を選択します。
1. **[Site Recovery 拡張機能]** を選択します。
1. **[アンインストール]** を選択します。

Linux VM で、VMSnapshot 拡張機能が Azure portal に表示されない場合は、[Azure Linux エージェントを更新](../virtual-machines/extensions/update-linux-agent.md)してください。 その後、保護を実行してください。

これらの手順が完了すると、保護中に拡張機能が再インストールされます。

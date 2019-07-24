---
title: Azure Site Recovery エージェントに関する問題のトラブルシューティング | Microsoft Docs
description: Azure Site Recovery エージェントのエラーの症状、原因、解決策に関する情報を提供します。
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 5ea701682c03370cea46f9126ecf78427a776371
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58103538"
---
# <a name="troubleshoot-issues-with-the-azure-site-recovery-agent"></a>Azure Site Recovery エージェントに関する問題のトラブルシューティング

この記事では、VM エージェントと拡張機能に関連する Azure Site Recovery のエラーの解決に役立つ可能性のあるトラブルシューティング手順について説明します。


## <a name="azure-site-recovery-extension-time-out"></a>Azure Site Recovery 拡張機能がタイムアウトになる  

エラー メッセージ:「開始する拡張機能の操作の追跡中にタスクの実行がタイムアウトになりました」<br>
エラー コード:"151076"

 Azure Site Recovery は、保護の有効化ジョブの一部として、仮想マシンに拡張機能をインストールします。 以下のいずれかの状況によって、保護をトリガーできず、ジョブが失敗する場合があります。 次のトラブルシューティング手順を完了してから、必要な操作を再試行してください。

**原因 1:[エージェントが VM にインストールされているが応答しない (Windows VM の場合)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**原因 2:[VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 3:[Site Recovery 拡張機能の更新または読み込みに失敗した](#the-site-recovery-extension-fails-to-update-or-load)**  

エラー メッセージ:「Previous site recovery extension operation is taking more time than expected. (前の Site Recovery 機能拡張の操作に想定以上に時間がかかっています。)」<br>
エラー コード:"150066"<br>

**原因 1:[エージェントが VM にインストールされているが応答しない (Windows VM の場合)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**原因 2:[VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 3:[Site Recovery 拡張機能の状態が正しくない](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>VM エージェントが応答しないために保護に失敗する

エラー メッセージ:「開始する拡張機能の操作の追跡中にタスクの実行がタイムアウトになりました。」<br>
エラー コード:"151099"<br>

このエラーは、仮想マシンの Azure ゲスト エージェントが準備完了状態にない場合に発生することがあります。
[Azure Portal](https://portal.azure.com/) で Azure ゲスト エージェントの状態を調べられます。 保護しようとしている仮想マシンに移動し、[VM]、[設定]、[プロパティ]、[エージェントの状態] と移動して状態を調べます。 ほとんどの場合、エージェントの状態は、仮想マシンの再起動後に準備完了になります。 ただし、再起動が不可能な場合や、再起動後も問題に直面している場合は、次のトラブルシューティング手順を完了してください。

**原因 1:[エージェントが VM にインストールされているが応答しない (Windows VM の場合)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**原因 2:[VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  


エラー メッセージ:「開始する拡張機能の操作の追跡中にタスクの実行がタイムアウトになりました。」<br>
エラー コード:"151095"<br>

これは、Linux マシン上のエージェントのバージョンが古くなっている場合に発生します。 次のトラブルシューティング手順を完了してください。<br>
  **原因 1:[VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
## <a name="causes-and-solutions"></a>原因とソリューション

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>エージェントが VM にインストールされているが応答しない (Windows VM の場合)

#### <a name="solution"></a>解決策
VM エージェントが破損しているまたはサービスが停止している可能性があります。 VM エージェントを再インストールすることで最新バージョンを入手できます。 その際に、サービスとの通信も再開されます。

1. VM サービス (services.msc) で "Windows Azure ゲスト エージェント サービス" が実行されているかどうかを確認します。 "Windows Azure ゲスト エージェント サービス" の再起動を試みます。    
2. コントロール パネルの [サービス] に Windows Azure ゲスト エージェント サービスが表示されない場合は、**[プログラムと機能]** に移動し、Windows ゲスト エージェント サービスがインストールされているかどうかを確認してください。
4. Windows Azure ゲスト エージェント サービスが **[プログラムと機能]** に表示される場合は、Windows ゲスト エージェントをアンインストールします。
5. [最新バージョンのエージェント MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) をダウンロードしてインストールします。 インストールを実行するには、管理者権限が必要です。
6. [サービス] に Windows Azure ゲスト エージェント サービスが表示されることを確認します。
7. 保護ジョブを再開します。

さらに、VM に [Microsoft .NET 4.5 がインストールされていること](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)を確認します。 VM エージェントがサービスと通信するためには .NET 4.5 が必要です。

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM にインストールされているエージェントが古くなっている (Linux VM の場合)

#### <a name="solution"></a>解決策
Linux VM の場合、エージェントに関連するエラーまたは拡張機能に関連するエラーのほとんどは、古い VM エージェントに影響する問題が原因で発生します。 この問題のトラブルシューティングを行うには、次の一般的なガイドラインに従います。

1. [Linux VM エージェントを更新](../virtual-machines/linux/update-agent.md)する手順に従います。

   > [!NOTE]
   > ディストリビューション リポジトリを通してのみエージェントを更新することを "*強くお勧め*" します。 エージェント コードを直接 GitHub からダウンロードして、更新することはお勧めしません。 最新のエージェントをディストリビューションで使用できない場合は、そのエージェントをインストールする方法をディストリビューション サポートにお問い合わせください。 最新のエージェントを確認するには、GitHub リポジトリの [Windows Azure Linux エージェント](https://github.com/Azure/WALinuxAgent/releases)のページをご覧ください。

2. `ps -e` コマンドを実行して、Azure エージェントが VM で実行されていることを確認します。

   このプロセスが実行されていない場合は、次のコマンドを使用してプロセスを再起動します。

   * Ubuntu の場合: `service walinuxagent start`
   * その他のディストリビューションの場合: `service waagent start`

3. [エージェントの自動再起動を構成します](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)。
4. 仮想マシンの保護を有効にします。



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Site Recovery 拡張機能の更新または読み込みに失敗した
拡張機能の状態が "空'、'準備不完了'、または移行中の場合。

#### <a name="solution"></a>解決策

拡張機能をアンインストールし、操作をもう一度開始します。

拡張機能をアンインストールするには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) で、バックアップ エラーが発生している VM に移動します。
2. **[設定]** を選択します。
3. **[拡張機能]** を選択します。
4. **[Site Recovery 拡張機能]** を選択します。
5. **[アンインストール]** を選択します。

Linux VM で、VMSnapshot 拡張機能が Azure Portal に表示されない場合は、[Azure Linux エージェントを更新](../virtual-machines/linux/update-agent.md)してから、保護を実行します。 

この手順を済ませておくと、保護中に拡張機能が再インストールされます。



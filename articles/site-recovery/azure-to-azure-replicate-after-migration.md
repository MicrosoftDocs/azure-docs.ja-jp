---
title: Azure Site Recovery を使用して Azure に移行した後に ディザスター リカバリーをセットアップする
description: この記事では、Azure Site Recovery を使用して Azure に移行した後、Azure リージョン間でのディザスター リカバリーをセットアップできるようにマシンを準備する方法について説明します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: a71e476a214c44514c7d57c54a09a38218ad6d2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135665"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Azure への移行後に Azure VM のディザスター リカバリーをセットアップする 


[Site Recovery](site-recovery-overview.md) サービスを使用して[オンプレミス マシンを Azure VM に移行](./migrate-tutorial-on-premises-azure.md)し、VM にセカンダリ Azure リージョンへのディザスター リカバリーをセットアップさせる場合、この記事の手順に従ってください。 この記事では、Azure VM エージェントが移行済みの VM に インストールされていることを確認する方法と、移行後に不要になった Site Recovery モビリティ サービスを削除する方法について説明します。



## <a name="verify-migration"></a>移行を検証する

ディザスター リカバリーをセットアップする前に、移行が予期したとおりに完了したことを確認します。 移行を正常に完了するには、フェールオーバーの後で、移行するマシンごとに **[移行の完了]** オプションを選択する必要があります。 

## <a name="verify-the-azure-vm-agent"></a>Azure VM エージェントを検証する

各 Azure VM には、[Azure VM エージェント](../virtual-machines/extensions/agent-windows.md)がインストールされている必要があります。 Azure VM をレプリケートするため、Site Recovery によってエージェントに拡張機能がインストールされます。

- マシンがバージョン 9.7.0.0 以降の Site Recovery モビリティ サービスを実行している場合、モビリティ サービスによって Azure VM エージェントが Windows VM に自動的にインストールされます。 以前のバージョンのモビリティ サービスでは、エージェントを手動でインストールします。
- Linux VM の場合は、Azure VM エージェントを手動でインストールする必要があります。 Azure VM エージェントをインストールする必要があるのは、移行済みのマシンにインストールされているモビリティ サービスが v9.6 以前の場合のみです。


### <a name="install-the-agent-on-windows-vms"></a>Windows VM にエージェントをインストールする

9.7.0.0 より前のバージョンの Site Recovery モビリティ サービスを実行しているか、またはエージェントを手動でインストールするその他の何らかの必要性がある場合、次の操作を行います。  

1. VM の管理者特権があることを確認します。
2. [VM エージェント インストーラー](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)をダウンロードします。
3. インストーラー ファイルを実行します。

#### <a name="validate-the-installation"></a>インストールを検証する
エージェントがインストールされていることを確認するには:

1. Azure VM の C:\WindowsAzure\Packages フォルダーで WaAppAgent.exe ファイルを確認します。
2. このファイルを右クリックし、**[プロパティ]** で **[詳細]** タブを選択します。
3. **[製品バージョン]** フィールドに 2.6.1198.718 以上が表示されていることを確認します。

Windows のエージェントのインストールについては、[こちら](../virtual-machines/extensions/agent-windows.md)をご覧ください。

### <a name="install-the-agent-on-linux-vms"></a>Linux VM にエージェントをインストールする

次のように手動で [Azure Linux VM](../virtual-machines/extensions/agent-linux.md) エージェントをインストールします。

1. マシンの管理者アクセス許可があることを確認します。
2. ディストリビューションのパッケージ リポジトリから RPM または DEB パッケージを使用して、Linux VM エージェントをインストールすることを強くお勧めします。 すべての[動作保証済みディストリビューション プロバイダー](../virtual-machines/linux/endorsed-distros.md)Azure Linux エージェント パッケージをイメージとリポジトリに統合します。
    - ディストリビューション リポジトリ経由でのみエージェントを更新することを強くお勧めします。
    - GitHub から直接 Linux VM エージェントをインストールしてそれを更新することはお勧めしません。
    -  最新のエージェントをディストリビューションで使用できない場合は、そのエージェントをインストールする方法をディストリビューション サポートにお問い合わせください。 

#### <a name="validate-the-installation"></a>インストールを検証する 

1. コマンド **ps -e** を実行して、Linux VM で Azure エージェントが実行されていることを確認します。
2. このプロセスが実行されていない場合は、次のコマンドを使用してプロセスを再起動します。
    - Ubuntu の場合: **service walinuxagent start**
    - その他のディストリビューションの場合: **service waagent start**


## <a name="uninstall-the-mobility-service"></a>Mobility Service をアンインストールする

1. 次のいずれかの方法を使用して、Azure VM からモビリティ サービスを手動でアンインストールします。 
    - Windows の場合は、[コントロール パネル] > **[プログラムの追加と削除]** で、**Microsoft Azure Site Recovery Mobility Service/Master Target server** をアンインストールします。 管理者特権でのコマンド プロンプトで、次を実行します。
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Linux の場合は、root ユーザーとしてサインインします。 端末で、**/user/local/ASR** に移動して、次のコマンドを実行します。
        ```
        ./uninstall.sh -Y
        ```
2. VM を再起動してからレプリケーションを構成します。

## <a name="next-steps"></a>次のステップ

Azure VM エージェント上の Site Recovery 拡張機能の[トラブルシューティングを確認](site-recovery-extension-troubleshoot.md)します。
Azure VM をセカンダリ リージョンに[迅速にレプリケート](azure-to-azure-quickstart.md)します。

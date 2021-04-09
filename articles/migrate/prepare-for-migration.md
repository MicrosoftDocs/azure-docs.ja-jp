---
title: Azure Migrate での移行に向けてマシンを準備する
description: Azure Migrate での移行に向けてオンプレミス マシンを準備する方法について説明します。
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 06/08/2020
ms.openlocfilehash: 8083b9edd49f65f29fe9c9b2cfa30edfacf89507
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102614889"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Azure への移行に向けてオンプレミスのマシンの準備を整える

この記事では、[Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) ツールを使用してオンプレミスのマシンを Azure に移行する前にマシンに対して必要になる準備作業の手順を紹介します。

この記事では、次の内容について説明します。
> [!div class="checklist"]
> * 移行に関する制約を確認する。
> * VMware VM を移行するための方法を選択する
> * 移行するマシンのハイパーバイザーとオペレーティング システムの要件を確認する。
> * 移行するマシンの URL およびポートへのアクセスを確認する。
> * 移行を開始する前に必要になることがある変更を確認する。
> * 移行されたマシンに対する Azure VM の要件を確認する
> * 移行後の Azure VM に接続できるようにマシンの準備を整える。



## <a name="verify-migration-limitations"></a>移行に関する制約を確認する

次の表は、Azure Migrate での検出、評価、移行の制限をまとめたものです。 移行前にマシンを評価することをお勧めしますが、必須ではありません。

**シナリオ** | **プロジェクト** | **検出と評価** | **移行**
--- | --- | --- | ---
**VMware VM** | 1 つの Azure Migrate プロジェクトで最大 35,000 台の VM を検出して評価します。 | 1 つの VMware 用 [Azure Migrate アプライアンス](common-questions-appliance.md)を使用して最大 10,000 台の VMware VM を検出します。 | **エージェントレス移行**: vCenter Server から最大 500 台の VM を同時にレプリケートできます。 **エージェントベース移行**: [レプリケーション アプライアンス](migrate-replication-appliance.md)を [スケールアウト](./agent-based-migration-architecture.md#performance-and-scaling)することで、多数の VM をレプリケートすることができます。<br/><br/> ポータルでは、レプリケーションのために一度に最大 10 台のマシンを選択できます。 レプリケートするマシンの台数がそれを超える場合は、10 台のバッチ単位で追加してください。
**Hyper-V VM** | 1 つの Azure Migrate プロジェクトで最大 35,000 台の VM を検出して評価します。 | 単一の Azure Migrate アプライアンスで最大 5,000 台の Hyper-V VM を検出します。 | Hyper-V の移行にアプライアンスは使用されません。 代わりに、それぞれの Hyper-V ホスト上で Hyper-V レプリケーション プロバイダーが実行されます。<br/><br/> レプリケーション容量は、パフォーマンス要因 (VM のチャーンなど) とレプリケーション データのアップロード帯域幅に左右されます。<br/><br/> ポータルでは、レプリケーションのために一度に最大 10 台のマシンを選択できます。 レプリケートするマシンの台数がそれを超える場合は、10 台のバッチ単位で追加してください。
**物理マシン** | 1 つの Azure Migrate プロジェクトで最大 35,000 台のマシンを検出して評価します。 | 物理サーバー用の 1 つの Azure Migrate アプライアンスを使用して最大 250 台の物理サーバーを検出します。 | [レプリケーション アプライアンス](migrate-replication-appliance.md)を[スケールアウト](./agent-based-migration-architecture.md#performance-and-scaling)することで、多数のサーバーをレプリケートすることができます。<br/><br/> ポータルでは、レプリケーションのために一度に最大 10 台のマシンを選択できます。 レプリケートするマシンの台数がそれを超える場合は、10 台のバッチ単位で追加してください。

## <a name="select-a-vmware-migration-method"></a>VMware の移行方法を選択する

VMware VM を Azure に移行する場合、エージェントレス移行方式とエージェントベース移行方式を[比較](server-migrate-overview.md#compare-migration-methods)して、どちらが実情に合っているかを判断してください。

## <a name="verify-hypervisor-requirements"></a>ハイパーバイザーの要件を確認する

- [VMware のエージェントレス](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)の要件または [VMware のエージェントベース](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based)の要件を確認します。
- [Hyper-V ホスト](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements)の要件を確認します。


## <a name="verify-operating-system-requirements"></a>オペレーティング システムの要件を確認する

移行がサポートされているオペレーティング システムを確認します。

- VMware VM または Hyper-V VM を移行する場合は、VMware VM の[エージェントレス](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless)移行と[エージェントベース](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based)移行に関する要件、および [Hyper-V VM](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) の要件を確認します。
- [Windows オペレーティング システム](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)が Azure でサポートされていることを確認します。
- [Linux ディストリビューション](../virtual-machines/linux/endorsed-distros.md)が Azure でサポートされていることを確認します。

## <a name="review-url-and-port-access"></a>URL およびポートに対するアクセスを確認する

移行中にアクセスされる URL とポートを確認します。

**シナリオ** | **詳細** |  **URL** | **ポート**
--- | --- | --- | ---
**VMware のエージェントレス移行** | 移行には [Azure Migrate アプライアンス](migrate-appliance-architecture.md)を使用します。 VMware VM には何もインストールされません。 | アプライアンスを使用した検出、評価、移行に必要な、パブリック クラウドと政府機関向けクラウドの [URL](migrate-appliance.md#url-access) を確認します。 | エージェントレス移行に関するポートの要件を[確認](migrate-support-matrix-vmware-migration.md#port-requirements-agentless)します。
**VMware のエージェントベース移行** | 移行には、[レプリケーション アプライアンス](migrate-replication-appliance.md)を使用します。 VM に Mobility Service エージェントがインストールされます。 | レプリケーション アプライアンスからアクセスする必要のある[パブリック クラウド](migrate-replication-appliance.md#url-access)と [Azure Government](migrate-replication-appliance.md#azure-government-url-access) の URL を確認します。 | エージェントベース移行時に使用されるポートを[確認](migrate-replication-appliance.md#port-access)します。
**Hyper-V の移行** | 移行には、Hyper-V ホストにインストールされたプロバイダーを使用します。 Hyper-V VM には何もインストールされません。 | ホスト上で実行されているレプリケーション プロバイダーからアクセスする必要のある[パブリック クラウド](migrate-support-matrix-hyper-v-migration.md#url-access-public-cloud)と [Azure Government](migrate-support-matrix-hyper-v-migration.md#url-access-azure-government) の URL を確認します。 | Hyper-V ホスト上のレプリケーション プロバイダーは、HTTPS ポート 443 のアウトバウンド接続を使用して VM のレプリケーション データを送信します。
**物理マシン** | 移行には、[レプリケーション アプライアンス](migrate-replication-appliance.md)を使用します。 物理マシンに Mobility Service エージェントがインストールされます。 | レプリケーション アプライアンスからアクセスする必要のある[パブリック クラウド](migrate-replication-appliance.md#url-access)と [Azure Government](migrate-replication-appliance.md#azure-government-url-access) の URL を確認します。 | 物理移行時に使用されるポートを[確認](migrate-replication-appliance.md#port-access)します。

## <a name="verify-required-changes-before-migrating"></a>移行前に必要な変更を確認する

Azure に VM を移行する前に、それらに対していくつかの変更を行う必要があります。

- 一部のオペレーティング システムでは、レプリケーションまたは移行プロセス中に、変更が Azure Migrate によって自動的に行われます。
- その他のオペレーティング システムでは、設定を手動で構成する必要があります。
- 設定の手動構成は、移行を開始する前に行うことが重要です。 変更を行う前に VM を移行すると、Azure で VM が起動しない可能性があります。

表をご覧のうえ、行うべき変更を確認してください。

### <a name="windows-machines"></a>Windows マシン

必要な変更を表にまとめます。

**操作** | **VMware (エージェントレス移行)** | **VMware (エージェントベース)/物理マシン** | **Hyper-V 上の Windows** 
--- | --- | --- | ---
**SAN ポリシーを Online All に構成する**<br/><br/> Azure VM 内の Windows ボリュームで、オンプレミスの VM と同じドライブ文字の割り当てが使用されます。 | Windows Server 2008 R2 以降が実行されているマシンでは、自動的に設定されます。<br/><br/> それより前のオペレーティング システムでは、手動で構成します。 | ほとんどの場合、自動的に設定されます。 | 手動で構成します。
**Hyper-V ゲスト統合をインストールする** | Windows Server 2003 が実行されているマシンに[手動でインストール](prepare-windows-server-2003-migration.md#install-on-vmware-vms)します。 | Windows Server 2003 が実行されているマシンに[手動でインストール](prepare-windows-server-2003-migration.md#install-on-vmware-vms)します。 | Windows Server 2003 が実行されているマシンに[手動でインストール](prepare-windows-server-2003-migration.md#install-on-hyper-v-vms)します。
**Azure シリアル コンソールを有効にする**<br/><br/>トラブルシューティングのために、Azure VM の[コンソールを有効](../virtual-machines/troubleshooting/serial-console-windows.md)にします。 VM を再起動する必要はありません。 Azure VM は、ディスク イメージを使用して起動します。 ディスク イメージ ブートは、新しい VM の再起動に相当します。 | 手動で有効にします。 | 手動で有効にします。 | 手動で有効にします。
**移行後に接続する**<br/><br/> 移行後に接続するためには、移行前に行うべき手順が数多くあります。 | 手動で[設定](#prepare-to-connect-to-azure-windows-vms)します。 | 手動で[設定](#prepare-to-connect-to-azure-windows-vms)します。 | 手動で[設定](#prepare-to-connect-to-azure-windows-vms)します。


#### <a name="configure-san-policy"></a>SAN のポリシーを構成する

既定では、Azure VM に一時ストレージとして使用するドライブ D が割り当てられます。

- このドライブが割り当てられると、アタッチされている他のストレージ ドライブに割り当てられている文字が 1 文字ずつ後ろにずれることになります。
- たとえば、オンプレミスのインストールでデータ ディスクを使用しており、そのディスクにアプリケーションのインストール用にドライブ D が割り当てられていた場合に、その VM を Azure に移行すると、ドライブに割り当てられている文字が 1 つ後ろにずれ、ドライブ E に変わります。 
- この自動的な割り当てを回避し、Azure により一時ボリュームに割り当てられるドライブ文字を未使用のアルファベットのうち順番が最も前のものにするには、記憶域ネットワーク (SAN) ポリシーを **OnlineAll** に設定します。

この設定は、次の手順に従って手動で構成します。

1. (ホストサーバーではなく) オンプレミスのマシンで、管理者特権でのコマンド プロンプトを開きます。
2. 「**diskpart**」と入力します。
3. 「**SAN**」と入力します。 ゲスト オペレーティング システムのドライブ文字が維持されていない場合には、**Offline All** または **Offline Shared** が返されます。
4. **DISKPART** プロンプトで、「**SAN Policy=OnlineAll**」と入力します。 この設定により、ディスクがオンラインになり、両方のディスクの読み取りと書き込みが可能になります。
5. テスト移行中に、ドライブ文字が保持されていることを確認できます。


### <a name="linux-machines"></a>Linux マシン

次のバージョンについては、各アクションが Azure Migrate によって自動的に実行されます。

- Red Hat Enterprise Linux 7.8、7.7、7.6、7.5、7.4、7.0、6.x (移行中に Azure Linux VM エージェントも自動的にインストールされます)
- Cent OS 7.7、7.6、7.5、7.4、7.0、6.x (移行中に Azure Linux VM エージェントも自動的にインストールされます)
- SUSE Linux Enterprise Server 12 SP1+
- SUSE Linux Enterprise Server 15 SP1
- Ubuntu 19.04、19.10、18.04LTS、16.04LTS、14.04LTS (移行中に Azure Linux VM エージェントも自動的にインストールされます)
- Ubuntu 18.04LTS、16.04LTS
- Debian 9、8、7
- Oracle Linux 7.7、7.7-CI

その他のバージョンでは、表に記載された情報に従ってマシンを準備してください。  


**操作** | **詳細** | **Linux バージョン**
--- | --- | ---
**Hyper-V Linux 統合サービスをインストールする** | 必要な Hyper-V ドライバーが含まれるように Linux の init イメージをリビルドします。 init イメージをリビルドすることにより、Azure で確実に VM が起動します。 | Linux ディストリビューションの新しいバージョンには、ほとんどの場合、既定でこれが含まれています。<br/><br/> 前述したバージョンを除くすべてのバージョンについては、これが含まれていない場合、手動でインストールしてください。
**Azure シリアル コンソールのログ記録を有効にする** | コンソールのログ記録を有効にすることはトラブルシューティングに役立ちます。 VM を再起動する必要はありません。 Azure VM は、ディスク イメージを使用して起動します。 ディスク イメージ ブートは、新しい VM の再起動に相当します。<br/><br/> [これらの手順](../virtual-machines/troubleshooting/serial-console-linux.md)に従って、有効化してください。
**デバイスのマップ ファイルを更新する** | 永続的なデバイス識別子を使用するよう、デバイス名とボリュームの関連付けが含まれているデバイスのマップ ファイルを更新します。 | 前述したバージョンを除くすべてのバージョンについては、手動でインストールしてください。 (エージェントベースの VMware シナリオでのみ適用可能)
**fstab エントリを更新する** |  永続的なボリューム識別子を使用するよう、エントリを更新します。    | 前述したバージョンを除くすべてのバージョンについては、手動で更新してください。
**Udev ルールを削除する** | MAC アドレスなどに基づいてインターフェイス名を予約する Udev ルールを削除します。 | 前述したバージョンを除くすべてのバージョンについては、手動で削除してください。
**ネットワーク インターフェイスを更新する** | DHCP.nst に基づいて IP アドレスを受け取るよう、ネットワーク インターフェイスを更新します。 | 前述したバージョンを除くすべてのバージョンについては、手動で更新してください。
**SSH を有効にする** | SSH を有効にして、再起動時に自動的に開始するよう sshd サービスを設定してください。<br/><br/> 受信 SSH 接続要求が、OS ファイアウォールまたはスクリプト実行可能なルールによってブロックされないようにします。| 前述したバージョンを除くすべてのバージョンについては、手動で有効にしてください。

次の表は、上記のオペレーティング システムに対して自動的に実行される手順をまとめたものです。


| アクション                                      | エージェントベースの VMware 移行 | エージェントレスの VMware 移行 | Hyper\-V   |
|---------------------------------------------|-------------------------------|----------------------------|------------|
| Hyper\-V Linux 統合サービスをインストールする | はい                           | はい                        | 不要 |
| Azure シリアル コンソールのログ記録を有効にする         | はい                           | はい                        | いいえ         |
| デバイスのマップ ファイルを更新する                      | はい                           | いいえ                         | いいえ         |
| fstab エントリを更新する                        | はい                           | はい                        | いいえ         |
| Udev ルールを削除する                            | はい                           | はい                        | いいえ         |
| ネットワーク インターフェイスを更新する                   | はい                           | はい                        | いいえ         |
| SSH を有効にする                                  | いいえ                            | いいえ                         | いいえ         |

詳細については、[Azure 上で Linux VM を稼働させる](../virtual-machines/linux/create-upload-generic.md)ための手順のページを参照してください。同ページでは、一部の人気 Linux ディストリビューションを対象とした手順も紹介しています。

Linux VM エージェントをインストールするために[必要なパッケージ](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux#requirements)の一覧を確認します。 エージェントレスの VMware 移行方法を使用する場合、RHEL6、RHEL7、CentOS7 (6 は RHEL と同様にサポート)、Ubuntu 14.04、Ubuntu 16.04、Ubuntu 18.04 の Linux VM エージェントが自動的にインストールされます。

## <a name="check-azure-vm-requirements"></a>Azure VM の要件を確認する

オンプレミスのマシンを Azure にレプリケートする場合には、オペレーティング システムとアーキテクチャ、ディスク、ネットワーク設定、および VM 名に関する Azure VM の要件を順守している必要があります。

移行前に、[VMware](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)、[Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements)、[物理サーバー](migrate-support-matrix-physical-migration.md#azure-vm-requirements)の移行に関する Azure VM の要件を確認してください。



## <a name="prepare-to-connect-after-migration"></a>移行後に接続するための準備を整える

Azure への移行中には、Azure VM が作成されます。 移行後に、この新しい Azure VM に接続できるようにする必要があります。 正常に接続するためには、いくつかの手順を踏む必要があります。

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Azure の Windows VM に対する接続を準備する

オンプレミスの Windows マシンで、次の手順を実行します。

1. Windows 設定を構成します。 設定には、すべての静的な固定ルートまたは WinHTTP プロキシを削除することが含まれます。
2. [必要なサービス](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)が実行されていることを確認します。
3. オンプレミスのマシンへのリモート接続を許可するために、リモート デスクトップ (RDP) を有効にします。 [PowerShell を使用して RDP を有効にする](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)方法に関するページを参照してください。
4. 移行後にインターネット経由で Azure VM にアクセスする場合は、オンプレミスのマシンの Windows ファイアウォールにおいて、パブリック プロファイルで TCP と UDP を許可したうえで、すべてのプロファイルで許可されたアプリとして RDP を設定します。
5. 移行後にサイト間 VPN 経由で Azure VM にアクセスする場合は、オンプレミスのマシンの Windows ファイアウォールにおいて、ドメイン プロファイルとプライベート プロファイルで RDP を許可します。 [RDP トラフィックを許可する](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)方法に関するページを参照してください。 
6. 移行する際は、オンプレミスの VM に保留中の Windows 更新プログラムがないことを確認してください。 更新プログラムがあると、移行後に Azure VM 上で更新プログラムのインストールが始まることがあり、更新が完了するまで VM にサインインできなくなります。


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Linux の Azure VM に対する接続を準備する

オンプレミスの Linux マシンで、次の手順を実行します。

1. システム起動時に Secure Shell サービスが自動的に開始される設定になっていることを確認します。
2. ファイアウォール規則で SSH 接続が許可されていることを確認します。

### <a name="configure-azure-vms-after-migration"></a>移行後に Azure VM を構成する

移行後は、作成された Azure VM で次の手順を実行します。

1. インターネット経由で VM に接続できるように、VM にパブリック IP アドレスを割り当てます。 Azure VM には、オンプレミスのマシンとは異なるパブリック IP アドレスを使用する必要があります。 [詳細については、こちらを参照してください](../virtual-network/virtual-network-public-ip-address.md)。
2. VM 上のネットワーク セキュリティ グループ (NSG) 規則で RDP または SSH ポートへの受信接続が許可されていることを確認します。
3. [[ブート診断]](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) をオンにして VM を表示します。


## <a name="next-steps"></a>次のステップ

Azure に [VMware VM を移行する](server-migrate-overview.md)のか、[Hyper-V VM ](tutorial-migrate-hyper-v.md) を移行するのか、あるいは[物理サーバー、仮想化された VM、またはクラウドの VM](tutorial-migrate-physical-virtual-machines.md) を移行するのかに応じて、使用する方法を決めます。

## <a name="see-whats-supported"></a>サポート対象

VMware VM については、Server Migration で[エージェントレスとエージェントベースの 2 種類の移行](server-migrate-overview.md)がサポートされています。

- **VMware VM**:VMware VM の [移行の要件とサポート](migrate-support-matrix-vmware-migration.md)を確認してください。
- **Hyper-V VM**:Hyper-V VM については、[移行の要件とサポート](migrate-support-matrix-hyper-v-migration.md)を確認してください。
- **物理マシン**: オンプレミスの物理マシンおよび他の仮想化されたサーバーについては、[移行の要件とサポート](migrate-support-matrix-physical-migration.md)を確認してください。 

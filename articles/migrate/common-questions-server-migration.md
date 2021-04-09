---
title: Azure Migrate Server Migration についてよく寄せられる質問
description: Azure Migrate Server Migration を使用したマシンの移行についてよく寄せられる質問の回答を示します。
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: 63c7f226dcd99ec8040f2078ce12be0fe3c594df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99548815"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate Server Migration:一般的な質問

この記事では、Azure Migrate についてよくある質問の回答を示します。サーバー移行ツール。 その他の質問については、次のリソースを確認してください。

- Azure Migrate に関する[一般的な質問](resources-faq.md)
- [Azure Migrate アプライアンス](common-questions-appliance.md)に関する質問
- [検出、評価、依存関係の視覚化](common-questions-discovery-assessment.md) に関する質問
- [Azure Migrate フォーラム](https://aka.ms/AzureMigrateForum)で質問の回答を示します。

## <a name="does-azure-migrate-convert-uefi-based-machines-to-bios-based-machines-and-migrate-them-to-azure-as-azure-generation-1-vms"></a>Azure Migrate では、UEFI ベースのマシンは BIOS ベースのマシンに変換されて Azure Generation 1 VM として Azure に移行されますか?
Azure Migrate: Server Migration ツールでは、すべての UEFI ベースのマシンは Azure Generation 2 VM として Azure に移行されます。 UEFI ベースの VM から BIOS ベースの VM への変換はサポートされなくなりました。 すべての BIOS ベースのマシンは、Azure Generation 1 VM としてのみ Azure に移行されることに注意してください。

## <a name="how-can-i-migrate-uefi-based-machines-to-azure-as-azure-generation-1-vms"></a>UEFI ベースのマシンを Azure Generation 1 VM として Azure に移行するにはどうすればよいですか?
Azure Migrate: Server Migration ツールでは、UEFI ベースのマシンは Azure Generation 2 VM として Azure に移行されます。 それらを Azure Generation 1 VM に移行する場合は、レプリケーションを開始する前にブートの種類を BIOS に変換してから Azure Migrate: Server Migration ツールを使用して Azure に移行してください。
 
## <a name="which-operating-systems-are-supported-for-migration-of-uefi-based-machines-to-azure"></a>Azure への UEFI ベースのマシンの移行がサポートされているオペレーティング システムはどれですか?

| **UEFI ベースのコンピューターがサポートされているオペレーティング システム** | **エージェントレスの VMware から Azure**                                                                                                             | **エージェントレスの Hyper-V から Azure** | **エージェントベースの VMware、物理およびその他のクラウドから Azure** |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ | ---------------------------------------------------------- |
| Windows Server 2019、2016、2012 R2、2012                | Y                                                                                                                                         | Y                              | Y                                                          |
| Windows 10 Pro、Windows 10 Enterprise                   | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 15 SP1                     | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 12 SP4                     | Y                                                                                                                                         | Y                              | Y                                                          |
| Ubuntu Server 16.04、18.04、19.04、19.10                | Y                                                                                                                                         | Y                              | Y                                                          |
| RHEL 8.1、8.0、7.8、7.7、7.6、7.5、7.4、7.0、6.x        | Y<br>                 _RHEL 8.x には [手動による準備](./prepare-for-migration.md#linux-machines)が必要_   | Y                              | Y                                                          |
| Cent OS 8.1、8.0、7.7、7.6、7.5、7.4、6.x               | Y<br>_Cent OS 8.x には [手動による準備](./prepare-for-migration.md#linux-machines)が必要_ | Y                              | Y                                                          |
| Oracle Linux 7.7、7.7-CI                                |  Y                                                                                                                                        | Y                              | Y                                                          |

## <a name="can-i-use-the-recovery-services-vault-created-by-azure-migrate-for-disaster-recovery-scenarios"></a>Azure Migrate で作成した Recovery Services コンテナーをディザスター リカバリーのシナリオに使用することはできますか?
ディザスター リカバリーのシナリオでは、Azure Migrate で作成した Recovery Services コンテナーは使用しないことをお勧めします。 これにより、Azure Migrate でレプリケーションの開始が失敗する可能性があります。 

## <a name="where-should-i-install-the-replication-appliance-for-agent-based-migrations"></a>エージェントベースの移行用のレプリケーション アプライアンスはどこにインストールすればよいですか?

レプリケーション アプライアンスは、専用のマシンにインストールする必要があります。 レプリケート対象のソース マシン、または以前にインストールした Azure Migrate 検出および評価アプライアンスに、レプリケーション アプライアンスをインストールすることはできません。 詳細については、[チュートリアル](./tutorial-migrate-physical-virtual-machines.md)をご覧ください。

## <a name="how-can-i-migrate-my-aws-ec2-instances-to-azure"></a>AWS EC2 インスタンスを Azure に移行するにはどうすればよいですか?

AWS EC2 インスタンスを検出、評価、および Azure に移行する場合は、[こちらの記事](./tutorial-migrate-aws-virtual-machines.md)をご覧ください。

## <a name="can-i-migrate-aws-vms-running-amazon-linux-operating-system"></a>Amazon Linux オペレーティング システムが動作している AWS VM を移行できますか?

Amazon Linux OS は AWS でのみサポートされているので、Amazon Linux が動作している VM をそのまま移行することはできません。
Amazon Linux で実行されているワークロードを移行するには、Azure で CentOS/RHEL VM を起動し、関連するワークロード移行方法を使用して、AWS Linux マシンで実行されているワークロードを移行します。 たとえば、ワークロードによっては、移行を支援するためのワークロード固有のツール (Web サーバーの場合のデータベースやデプロイのツールなど) が存在する場合もあります。

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Azure Migrate を使用した移行はどの地域でサポートされていますか?

[パブリック](migrate-support-matrix.md#supported-geographies-public-cloud)と [Government クラウド](migrate-support-matrix.md#supported-geographies-azure-government)でサポートされている地域を確認してください。

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-regions"></a>同じ Azure Migrate プロジェクトを使用して複数のリージョンに移行することはできますか?

Azure Migrate プロジェクト内の複数のリージョンに対して評価を作成することはできますが、1 つの Azure Migrate プロジェクトを使用してサーバーを移行できるのは 1 つの Azure リージョンに対してのみです。 移行先のリージョンごとに追加の Azure Migrate プロジェクトを作成できます。

- エージェントレスの VMware 移行では、最初のレプリケーションを有効にすると、ターゲット リージョンがロックされます。
- エージェントベースの移行 (VMware、物理サーバー、および他のクラウドからのサーバー) では、レプリケーション アプライアンスの設定中にポータルで [リソースの作成] ボタンをクリックすると、ターゲット リージョンがロックされます。
- エージェントレスの Hyper-V 移行では、Hyper-V レプリケーション プロバイダーの設定中にポータルで [リソースの作成] ボタンをクリックすると、ターゲット リージョンがロックされます。

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-subscriptions"></a>同じ Azure Migrate プロジェクトを使用して複数のサブスクリプションに移行することはできますか? 

はい。1 つの Azure Migrate プロジェクトの同じターゲット リージョン内の複数のサブスクリプションに移行できます。 1 台のマシンまたは一連のマシンのレプリケーションを有効にするときに、ターゲット サブスクリプションを選択することができます。 ターゲット リージョンは、エージェントレスの VMware 移行での最初のレプリケーション後、エージェントベースの移行でのレプリケーション アプライアンスのインストール中、およびエージェントレスの Hyper-V 移行での Hyper-V プロバイダーのインストール中にロックされます。

## <a name="what-are-the-migration-options-in-azure-migrate-server-migration"></a>Azure Migrate: Server Migration にはどのような移行オプションがありますか?

Azure Migrate: Server Migration ツールには、ソース サーバー/VM を Azure に移行するための 2 つのオプション (エージェントレスの移行とエージェントベースの移行) が用意されています。

選択した移行オプションにかかわらず、Azure Migrate: Server Migration を使用してサーバーを移行するには、最初にサーバーのレプリケーションを有効にします。 これにより、Azure への VM/サーバー データの初期レプリケーションが実行されます。 初期レプリケーションが完了すると、進行中のレプリケーション (進行中の差分同期) が確定され、増分データが Azure に移行されます。 操作が差分同期ステージに達したら、いつでも Azure に移行することを選択できます。  

移行オプションを決める際の考慮事項をいくつか次に示します。

**エージェントレスの移行** では、移行されるソース VM/サーバー上にソフトウェア (エージェント) を展開する必要はありません。 エージェントレスのオプションは、仮想化プロバイダーによって提供される機能と統合することによってレプリケーションを調整します。
エージェントレスのレプリケーション オプションは、[VMware VM](./tutorial-migrate-vmware.md) と [Hyper-V VM](./tutorial-migrate-hyper-v.md) で使用できます。

**エージェントベースの移行** では、移行されるソース VM/マシンに Azure Migrate ソフトウェア (エージェント) をインストールする必要があります。 エージェントベースのオプションは、レプリケーション機能に仮想化プラットフォームを使用しないため、x86/x64 アーキテクチャを実行しているすべてのサーバーと、エージェントベースのレプリケーション方法でサポートされているオペレーティングシステムのバージョンで使用できます。

エージェントベースの移行オプションは、[VMware VM](./tutorial-migrate-vmware-agent.md)、[Hyper-V VM](./tutorial-migrate-physical-virtual-machines.md)、[物理サーバー](./tutorial-migrate-physical-virtual-machines.md)、[AWS で実行されている VM](./tutorial-migrate-aws-virtual-machines.md)、GCP で実行されている VM、または別の仮想化プロバイダーで実行されている VM に使用できます。 エージェントベースの移行では、移行の目的でマシンを物理サーバーとして扱います。

エージェントレスの移行は、サポートされているシナリオ (VMWare と Hyper-V) でのエージェントベースのレプリケーション オプションよりも利便性とシンプルさに優れていますが、次のユース ケースではエージェントベースのシナリオの使用をご検討ください。

- IOPS の制約付き環境:エージェントレス レプリケーションでは、スナップショットを使用するため、ストレージの IOPS/帯域幅を消費します。 ご使用の環境でストレージ/IOPS に制約がある場合は、エージェントベースの移行方法が推奨されます。
- VCenter Server がない場合は、VMware VM を物理サーバーとして扱い、エージェントベースの移行ワークフローを使用することができます。

詳細については、[こちらの記事](./server-migrate-overview.md)を参照して、VMware 移行の移行オプションを比較してください。

## <a name="how-does-agentless-migration-work"></a>エージェントレスの移行はどのようなしくみになっていますか?

Azure Migrate: Server Migration には、Windows または Linux が動作している VMware 仮想マシンと Hyper-V 仮想マシンを移行するためのエージェントレスのレプリケーション オプションが用意されています。 さらに、このツールには、VMware、Hyper-V、AWS、GCP などでの x86/x64 仮想マシンだけでなく、物理サーバーの移行にも使用できる Windows および Linux サーバー用の追加のエージェントベースのレプリケーション オプションも用意されています。エージェントベースのレプリケーション オプションでは、移行されるサーバー/仮想マシンにエージェント ソフトウェアをインストールする必要があるのに対し、エージェントレスのオプションでは、仮想マシン自体にソフトウェアをインストールする必要がないため、エージェントベースのレプリケーション オプションよりも利便性とシンプルさに優れています。

エージェントレスのレプリケーション オプションは、仮想化プロバイダー (VMware、Hyper-V) によって提供されるメカニズムを使用して機能します。 VMware 仮想マシンの場合、エージェントレスのレプリケーション メカニズムでは、VMware スナップショットと VMware の変更ブロック追跡テクノロジを使用して、仮想マシンのディスクからデータをレプリケートします。 このメカニズムは、多くのバックアップ製品で使用されているものと似ています。 Hyper-V 仮想マシンの場合、エージェントレスのレプリケーション メカニズムでは、VM スナップショットと、Hyper-V レプリカの変更追跡機能を使用して、仮想マシンのディスクからデータをレプリケートします。

仮想マシンに対してレプリケーションが構成されている場合、最初に初期レプリケーション フェーズが実行されます。 初期レプリケーション中に、VM スナップショットが作成され、スナップショット ディスクからのデータの完全なコピーがサブスクリプション内のマネージド ディスクにレプリケートされます。 VM の初期レプリケーションが完了すると、レプリケーション プロセスは増分レプリケーション (差分レプリケーション) フェーズに移行します。 増分レプリケーション フェーズでは、最後に完了したレプリケーション サイクル以降に発生したデータ変更が定期的にレプリケートされて、レプリカ マネージド ディスクに適用されるため、レプリケーションは VM 上で発生する変更と同期した状態に保たれます。 VMware 仮想マシンの場合は、レプリケーション サイクル間の変更を追跡するために、VMware の変更ブロック追跡テクノロジが使用されます。 レプリケーション サイクルの開始時に、VM スナップショットが作成され、変更ブロック追跡を使用して、現在のスナップショットと最後に正常にレプリケートされたスナップショットの間の変更が取得されます。 このようにして、最後に完了したレプリケーション サイクル以降に変更されたデータのみをレプリケートして、VM のレプリケーションを同期した状態に保つ必要があります。各レプリケーション サイクルの最後に、スナップショットが解放され、仮想マシンに対してスナップショットの統合が実行されます。 同様に、Hyper-V 仮想マシンの場合は、連続するレプリケーション サイクル間の変更を追跡するために、Hyper-V レプリカの変更追跡エンジンが使用されます。
レプリケートする仮想マシンで移行操作を実行するときに、オンプレミスの仮想マシンをシャットダウンして、最後の増分レプリケーションを実行することを選択すれば、データ損失を防ぐことができます。 移行オプションを実行すると、仮想マシンに対応するレプリカ マネージド ディスクが Azure での仮想マシンの作成に使用されます。

作業を開始するには、[VMware のエージェントレスの移行](./tutorial-migrate-vmware.md)と [Hyper-V のエージェントレスの移行](./tutorial-migrate-hyper-v.md)のチュートリアルをご覧ください。

## <a name="how-does-agent-based-migration-work"></a>エージェントベースの移行はどのようなしくみになっていますか?

VMware 仮想マシンと Hyper-V 仮想マシン用のエージェントレスの移行オプションに加えて、Server Migration ツールには、物理サーバーで動作している Windows および Linux サーバー、または VMware、Hyper-V、AWS、Google Cloud Platform などで x86/x64 仮想マシンとして動作している Windows および Linux サーバーを移行するためのエージェントベースの移行オプションも用意されています。

エージェントベースの移行方法では、移行されるサーバーにインストールされているエージェント ソフトウェアを使用して、サーバー データを Azure にレプリケートします。 レプリケーション プロセスでは、エージェントがレプリケーション アプライアンスまたは構成サーバーと呼ばれる専用のレプリケーション サーバー (またはスケールアウト プロセス サーバー) にレプリケーション データをリレーするオフロード アーキテクチャを使用します。 エージェントベースの移行オプションのしくみの[詳細](./agent-based-migration-architecture.md)をご覧ください。 

注:レプリケーション アプライアンスは、Azure Migrate の検出アプライアンスとは異なり、個別/専用のマシンにインストールする必要があります。

## <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>移行のための帯域幅の要件はどのように判断したらよいでしょうか?

Azure へのデータ レプリケーションのための帯域幅は、さまざまな要因によって決まり、オンプレミスの Azure Migrate アプライアンスがデータを読み取って Azure にレプリケートできる速度と関係しています。 レプリケーションには、初期レプリケーションと差分レプリケーションの 2 つのフェーズがあります。

VM のレプリケーションが開始されると、初期レプリケーション サイクルが発生し、ディスクの完全なコピーがレプリケートされます。 初期レプリケーションが完了すると、前回のレプリケーション サイクル以降に発生したすべての変更を転送するために、増分レプリケーション サイクル (差分サイクル) が定期的にスケジュールされます。

### <a name="agentless-vmware-vm-migration"></a>エージェントレスの VMware VM の移行

初期レプリケーションが実行される波長と期間内で移動する必要のあるデータ量に基づいて帯域幅の要件を決めることができます (理想的には、実際の期間より前にテスト移行を行ったり、その期間中にダウンタイムを最小限に抑えたりするための十分な時間を確保するために、初期レプリケーションを実際の移行期間の少なくとも 3 - 4 日前に終わらせるとよいでしょう)。

次の数式を使用して、エージェントレスの VMware VM の移行に必要な帯域幅または時間を見積もることができます。

初期レプリケーションを完了するまでの時間 = {ディスクのサイズ (入手できる場合は使用サイズ) * 0.7 (控えめに見積もって圧縮率の平均を 30% とします)}/レプリケーションに使用できる帯域幅。

### <a name="agent-based-vmware-vm-migration"></a>エージェントベースの VMware VM の移行

エージェントベースのレプリケーション方法では、環境をプロファイリングしてデータ変更頻度を求めたり、必要な帯域幅の要件を予測したりするのに Deployment Planner が役立ちます。 詳細については、[こちらの記事](./agent-based-migration-architecture.md#plan-vmware-deployment)をご覧ください。 

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>エージェントレス VMware レプリケーションに対する Azure Migrate アプライアンスの使用においてレプリケーションを調整するにはどうすればよいですか?  

NetQosPolicy を使用して調整できます。 次に例を示します。

NetQosPolicy で使用する AppNamePrefix は "GatewayWindowsService.exe" です。 Azure Migrate アプライアンスで次のようなポリシーを作成することによって、アプライアンスからのレプリケーション トラフィックを調整できます:

New-NetQosPolicy -Name "ThrottleReplication" -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSecond 1 MB

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>オンプレミス環境から Azure にはどのようにしてデータが送信されますか? 送信前に暗号化されますか?

エージェントレス レプリケーションの場合の Azure Migrate アプライアンスでは、アップロード前にデータを圧縮して暗号化します。 データはセキュリティで保護された通信チャネルを介して HTTPS で送信され、TLS 1.2 以降を使用します。 さらに、Azure Storage では、データはクラウドに永続化されるときに自動的に暗号化されます (保存時暗号化)。  

## <a name="how-does-churn-rate-affect-agentless-replication"></a>チャーン レートはエージェントレス レプリケーションにどのように影響しますか?

エージェントレス レプリケーションでは日付が折りたたまれるため、*チャーン レート* より *チャーン パターン* が重要です。 ファイルが繰り返し書き込まれる場合、そのレートはあまり影響を与えません。 ただし、セクターが 1 つおきに書き込まれるパターンでは、次回のサイクルでチャーンが高くなります。 転送するデータの量を最小限に抑えるため、次回のサイクルをスケジュールする前に、できるだけ多くのデータを折りたためるようにします。

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>レプリケーション サイクルはどのくらいの頻度でスケジュールされますか?

次のレプリケーション サイクルをスケジュールする数式は、(以前のサイクル時間/2) または1時間のいずれか長い方です。

たとえば、VM で差分サイクルに 4 時間かかった場合、次回のサイクルは次の 1 時間ではなく、2 時間後にスケジュールされます。 このプロセスは、最初のレプリケーションの直後とは異なります。その場合は、最初の差分サイクルがすぐにスケジュールされます。

## <a name="how-do-i-migrate-windows-server-2003-running-on-vmwarehyper-v-to-azure"></a>VMware/Hyper-V で動作している Windows Server 2003 を Azure に移行するにはどうすればよいですか?

[Windows Server 2003 の延長サポート](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support)は、2015 年 7 月 14 日に終了しました。  Azure サポート チームでは、Azure での Windows Server 2003 の実行に関係する問題のトラブルシューティングを引き続き支援します。 ただし、このサポートは、OS レベルのトラブルシューティングや修正プログラムを必要としない問題に限定されます。
Azure クラウドの柔軟性と信頼性を効果的に活用できるようにするため、新しいバージョンの Windows Server が動作している Azure インスタンスにアプリケーションを移行することをお勧めします。

それでも、Windows Server 2003 を Azure に移行することを選択した場合は、Azure Migrate: Server Migration ツールを使用できます (Windows Server が VMware または Hyper-V で実行されている VM の場合)。Windows Server 2003 マシンを移行用に準備する場合は、[こちらの記事](./prepare-windows-server-2003-migration.md)をご覧ください。

## <a name="what-is-the-difference-between-the-test-migration-and-migrate-operations"></a>テスト移行操作と移行操作の違いは何ですか?

テスト移行は、実際の移行前に移行をテストして検証する方法を提供します。 テスト移行は、Azure のサンドボックス環境で、レプリケートする VM のテスト コピーを作成できるようにすることで機能します。 サンドボックス環境は、指定したテスト仮想ネットワークによって区切られます。 テスト移行は中断なしの操作であり、アプリケーションをソースで実行し続けながら、分離されたサンドボックス環境でクローン コピーに対してテストを実行できます。 必要に応じて複数のテストを実行して、移行を検証し、アプリのテストを実行して、実際の移行前に問題に対処することができます。

## <a name="will-windows-server-2008-and-2008-r2-be-supported-in-azure-after-migration"></a>Windows Server 2008 および 2008 R2 は、移行後に Azure でサポートされますか?

オンプレミスの Windows Server 2008 および 2008 R2 サーバーを Azure 仮想マシンに移行すると、サポート終了後 3 年間、仮想マシンの実行コストを上回る追加料金なしで、拡張セキュリティ更新プログラムを取得できます。 Azure Migrate: Server Migration ツールを使用して、Windows Server 2008 および 2008 R2 のワークロードを移行できます。

## <a name="is-there-a-rollback-option-for-azure-migrate"></a>Azure Migrate のロールバック オプションはありますか?

テスト移行オプションを使用して、Azure におけるアプリケーションの機能とパフォーマンスを検証できます。 任意の数のテスト移行を実行し、テスト移行操作を通じて信頼を確立した後で最終的な移行を実行できます。 テスト移行を行ってもオンプレミスのマシンに影響はなく、実際の移行を実行するまで、稼働状態が維持され、レプリケーションが続行されます。 テスト移行の UAT 中にエラーが発生した場合は、最終的な移行を延期し、ソース VM/サーバーが稼働して Azure にレプリケートし続けるようにすることを選択できます。 エラーを解決したら、最終的な移行を再試行できます。  
注:Azure への最終的な移行を完了し、オンプレミスのソース マシンがシャットダウンされている場合は、Azure からオンプレミス環境へのロールバックを実行することはできません。

## <a name="can-i-select-the-virtual-network-and-subnet-to-use-for-test-migrations"></a>テスト移行に使用する仮想ネットワークとサブネットを選択できますか?

テスト移行用の仮想ネットワークは選択できます。 サブネットは、次のロジックに基づいて自動的に選択されます。

- レプリケーションを有効にするときにターゲット サブネット (既定以外) が入力として指定されていた場合、Azure Migrate ではテスト移行用に選択された仮想ネットワーク内の同じ名前を持つサブネットを優先的に使用します。
- 同じ名前のサブネットが見つからない場合、Azure Migrate ではゲートウェイ/Application Gateway/ファイアウォール/Bastion サブネットではない、アルファベット順に使用可能な最初のサブネットを選択します。

## <a name="why-is-the-test-migration-button-disabled-for-my-server"></a>自分のサーバーで [テスト移行] ボタンが無効になっているのはなぜですか?

次のシナリオでは、[テスト移行] ボタンが無効状態になっている可能性があります。

- VM の初期レプリケーション (IR) が完了するまで、テスト移行を開始することはできません。 [テスト移行] ボタンは、IR プロセスが完了するまで無効になります。 VM が差分同期ステージに入ると、テスト移行を実行できます。
- テスト移行が既に完了していても、その VM に対してテスト移行のクリーンアップが実行されなかった場合は、このボタンが無効になっている可能性があります。 テスト移行のクリーンアップを実行して、操作を再試行してください。

## <a name="what-happens-if-i-dont-clean-up-my-test-migration"></a>テスト移行をクリーンアップしないとどうなりますか?

テスト移行では、レプリケートされたデータを使用してテスト用の Azure VM を作成することによって、実際の移行をシミュレートします。 サーバーは、レプリケートされたデータの特定の時点のコピーを使用してターゲット リソース グループ (レプリケーションを有効にするときに選択したもの) に "-test" というサフィックスを付けてデプロイされます。 テスト移行は、移行後の問題を最小限に抑えるためにサーバーの機能を検証することを目的としています。 テスト後にテスト移行がクリーンアップされない場合、テスト用の仮想マシンが引き続き Azure で実行され、料金が発生します。 テスト移行後にクリーンアップするには、Server Migration ツールの [マシンのレプリケート] ビューに移動し、そのマシンに対して [テスト移行をクリーンアップ] 操作を使用します。

## <a name="can-i-migrate-active-directory-domain-controllers-using-azure-migrate"></a>Azure Migrate を使用して Active Directory ドメイン コントローラーを移行できますか?

Server Migration ツールは、アプリケーションに依存しないため、ほとんどのアプリケーションで機能します。 Server Migration ツールを使用してサーバーを移行すると、そのサーバーにインストールされているすべてのアプリケーションが一緒に移行されます。 ただし、一部のアプリケーションでは、Server Migration 以外の代替移行方法が移行に適している場合があります。  Active Directory の場合、オンプレミス サイトが Azure 環境に接続されているハイブリッド環境では、別のドメイン コントローラーを Azure に追加し、Active Directory レプリケーションを設定することによって、お使いの Directory を Azure に拡張できます。 独自のドメイン コントローラーを必要とする Azure の分離環境に移行する (またはサンドボックス環境でアプリケーションをテストする) 場合は、Server Migration ツールを使用してサーバーを移行できます。

## <a name="what-happens-if-i-dont-stop-replication-after-migration"></a>移行後にレプリケーションを停止しないとどうなりますか?

レプリケーションを停止すると、Azure Migrate: Server Migration ツールによって、レプリケーション用に作成されたサブスクリプション内のマネージド ディスクがクリーンアップされます。 移行後にレプリケーションを停止しない場合は、これらのディスクに対して引き続き料金が発生します。 レプリケーションの停止は、既に移行されているマシンに接続されているディスクには影響しません。

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Vmware VM を移行するには VMware vCenter が必要ですか?

Vmware エージェントベースまたはエージェントレス移行を使用して [VMware Vm](server-migrate-overview.md) を移行するには、VM が配置されている ESXi ホストを vCenter Server で管理する必要があります。 VCenter Server がない場合は、物理サーバーとして移行することで VMware VM を移行できます。 [詳細については、こちらを参照してください](migrate-support-matrix-physical-migration.md)。

## <a name="can-i-upgrade-my-os-while-migrating"></a>移行中に OS をアップグレードできますか?

Azure Migrate: Server Migration ツールでは現在、既存のものと同じ移行のみがサポートされています。 このツールでは、移行中の OS バージョンのアップグレードはサポートされていません。 移行されたマシンの OS は、ソース マシンと同じになります。

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliances"></a>VCenter Server 内の VM を検出するために、2 つ (またはそれ以上) のアプライアンスをデプロイしました。 しかし、VM を移行しようとすると、いずれかのアプライアンスに対応する VM のみが表示されます。

複数のアプライアンスがセットアップされている場合は、指定された vCenter アカウントの VM 間に重複がないことが必要です。 このような重複が検出されるのは、サポートされていないシナリオです。

## <a name="how-do-i-know-if-my-vm-was-successfully-migrated"></a>VM が正常に移行されたかどうかを確認するにはどうすればよいですか?

VM/サーバーを正常に移行したら、[仮想マシン] ページで VM を表示して管理できます。 移行された VM に接続して検証します。
あるいは、この操作の "ジョブの状態" をレビューして、移行が正常に完了したかどうかを確認することもできます。 エラーが発生した場合は、それらを解決してから、移行操作を再試行してください。

## <a name="can-i-consolidate-multiple-source-vms-into-one-vm-while-migrating"></a>移行中に複数のソース VM を 1 つの VM に統合することはできますか?

Azure Migrate Server Migration 機能では現在、既存のものと同じ移行のみがサポートされています。 移行の一部としてサーバーの統合やオペレーティング システムのアップグレードはサポートされていません。 

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>エージェントレス レプリケーションは VMware サーバーにどのように影響しますか?

エージェントレス レプリケーションでは、VMware vCenter Server と VMware ESXi ホストのパフォーマンスに一部影響が生じます。 エージェントレス レプリケーションではスナップショットが使用されるので、ストレージに対する IOP が消費され、一部の IOPS ストレージ帯域幅が必要になります。 ご利用の環境内でストレージまたは IOP に制約がある場合は、エージェントレス レプリケーションを使用しないことをお勧めします。


## <a name="next-steps"></a>次のステップ

[Azure Migrate の概要](migrate-services-overview.md)を確認します。

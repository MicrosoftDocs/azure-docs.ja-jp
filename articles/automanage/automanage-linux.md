---
title: Azure Automanage for Linux
description: 自動的にオンボードされ、Linux マシン用に構成されているサービスに対する、Azure Automanage for virtual machines のベスト プラクティスについて学習します。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 3aab43be49cb98fbe136e1f0216590785d650392
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953251"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---linux"></a>Azure Automanage for virtual machines のベスト プラクティス - Linux

Linux VM で Automanage for virtual machines (VM) を使用する場合、これらの Azure サービスは自動的にオンボードされます。 それらはベスト プラクティスのホワイト ペーパーに不可欠なものであり、[クラウド導入フレームワーク](/azure/cloud-adoption-framework/manage/azure-server-management)を参照してください。

これらのすべてのサービスについては、Microsoft が自動オンボード、自動構成、ドリフトの監視、ドリフトが検出された場合の修復を行います。 このプロセスの詳細については、「[Azure Automanage for virtual machines](automanage-virtual-machines.md)」を参照してください。

## <a name="supported-linux-distributions-and-versions"></a>サポートされている Linux ディストリビューションとバージョン

Automanage によって、次の Linux ディストリビューションとバージョンがサポートされています。

- CentOS 7.3 以上
- RHEL 7.4 以上
- Ubuntu 16.04 および 18.04
- SLES 12 (SP3 から SP5 のみ)

## <a name="participating-services"></a>対象となるサービス

>[!NOTE]
> 現時点では、Linux VM では Microsoft Antimalware はサポートされていません。

|サービス    |説明    |サポートされている環境<sup>1</sup>    |サポートされている基本設定<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|VM の分析情報の監視    |Azure Monitor for VMs では、実行中のプロセスや他のリソースへの依存関係など、仮想マシンのパフォーマンスと正常性が監視されます。 [詳細情報](../azure-monitor/vm/vminsights-overview.md)。    |Production    |いいえ    |
|バックアップ    |Azure Backup では、VM 上のデータが誤って破壊されることを防ぐために、独立して分離されたバックアップを提供しています。 [詳細情報](../backup/backup-azure-vms-introduction.md)。 料金は、保護対象の VM の数とサイズに基づいています。 [詳細情報](https://azure.microsoft.com/pricing/details/backup/)。    |Production    |Yes    |
|Azure Security Center    |Azure Security Center は統合されたインフラストラクチャ セキュリティ管理システムであり、データ センターのセキュリティ体制を強化し、クラウド内のハイブリッド ワークロード全体にわたる高度な脅威保護が提供されます。 [詳細情報](../security-center/security-center-introduction.md)。  VM が存在するサブスクリプションは、Automanage によって、Azure Security Center の Free レベルのオファリングに構成されます。 ご利用のサブスクリプションが Azure Security Center に既にオンボードされている場合、Automanage によって再構成されることはありません。    |運用、Dev/Test    |いいえ    |
|更新管理    |Azure Automation の Update Management を使用して、仮想マシンのオペレーティング システムの更新プログラムを管理することができます。 すべてのエージェント マシンで利用可能な更新プログラムの状態をすばやく評価し、サーバーに必要な更新プログラムをインストールするプロセスを管理できます。 [詳細情報](../automation/update-management/overview.md)。    |運用、Dev/Test    |No    |
|変更履歴とインベントリ    |変更履歴とインベントリでは、変更履歴とインベントリの機能を組み合わせて、仮想マシンとサーバー インフラストラクチャの変更を追跡できます。 このサービスを利用すれば、環境内のサービス、デーモン、ソフトウェア、レジストリ、ファイル全体にわたって変更を追跡し、不要な変更を診断したりアラートを生成したりすることができます。 インベントリのサポートにより、ゲスト リソースでクエリを実行して、インストール済みのアプリケーションやその他の構成アイテムを可視化できます。  [詳細情報](../automation/change-tracking/overview.md)。    |運用、Dev/Test    |No    |
|Azure ゲスト構成    | ゲスト構成ポリシーは、マシンのコンプライアンスに関するレポートを作成し、構成を監視するために使用されます。 ゲスト構成拡張機能を使用して、Automanage サービスによって Azure Linux ベースラインがインストールされます。 Linux マシンの場合は、ゲスト構成サービスによって、ベースラインが監査専用モードでインストールされます。 ご利用の VM がベースラインに準拠していない場所を確認することはできますが、非準拠が自動的に修復されることはありません。 [詳細情報](../governance/policy/concepts/guest-configuration.md)。    |運用、Dev/Test    |No    |
|Azure Automation アカウント    |Azure Automation は、インフラストラクチャとアプリケーションのライフサイクル全体にわたる管理をサポートします。 [詳細情報](../automation/automation-intro.md)。    |運用、Dev/Test    |No    |
|Log Analytics ワークスペース    |Azure Monitor では、ログ データが Log Analytics ワークスペースに格納されます。Log Analytics ワークスペースは Azure リソースであり、データが収集、集計され、管理境界として機能するコンテナーです。 [詳細情報](../azure-monitor/logs/design-logs-deployment.md)。    |運用、Dev/Test    |いいえ    |


<sup>1</sup> Automanage を有効にすると、環境の選択を使用できます。 [詳細情報](automanage-virtual-machines.md#environment-configuration)。 環境の既定の設定を調整し、ベスト プラクティスの制約内で独自の基本設定を指定することもできます。


## <a name="next-steps"></a>次の手順

Azure portal でAzure Automanage for virtual machines を有効にしてみてください。

> [!div class="nextstepaction"]
> [Azure portal で Azure Automanage for virtual machines を有効にする](quick-create-virtual-machines-portal.md)
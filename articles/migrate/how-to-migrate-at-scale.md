---
title: Azure Migrate で移行マシンの移行を自動化する
description: Azure Migrate を使用して多数のマシンを移行するための、スクリプトの使用方法について説明します
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/01/2019
ms.openlocfilehash: 01196b23ce5bf9ce842da89d0773a77e6a1d5107
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754234"
---
# <a name="scale-migration-of-vms"></a>VM の移行をスケーリングする 

この記事では、多数の 仮想マシン (VM) を移行するための、スクリプトの使用方法について説明します。 移行をスケーリングするには、[Azure Site Recovery](../site-recovery/site-recovery-overview.md) を使用します。 

Site Recovery スクリプトは、GitHub の [Azure PowerShell サンプル](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) リポジトリからダウンロードできます。 スクリプトは、VMware、AWS、GCP の VM と物理サーバーを Azure のマネージド ディスクに移行するために使用できます。 また、VM を物理サーバーとして移行する場合は、これらのスクリプトを使用して Hyper-V VM を移行することもできます。 Azure Site Recovery PowerShell を使用したスクリプトは、[こちら](../site-recovery/vmware-azure-disaster-recovery-powershell.md)に記載されています。

## <a name="current-limitations"></a>現在の制限
- ターゲット VM のプライマリ NIC に対してのみ、静的 IP アドレスの指定がサポートされています
- スクリプトでは、Azure ハイブリッド特典に関連する入力は受け付けられません。ポータルで、レプリケートされた VM のプロパティを手動で更新する必要があります

## <a name="how-does-it-work"></a>それはどのように機能しますか?

### <a name="prerequisites"></a>前提条件
始める前に、次の手順を実行する必要があります。
- Azure サブスクリプション内に Site Recovery コンテナーが作成されていることを確認します
- 構成サーバーとプロセス サーバーがソース環境にインストールされていることと、コンテナーから環境を検出できることを確認します
- レプリケーション ポリシーが作成されていて、構成サーバーに関連付けられていることを確認します
- 構成サーバーに VM 管理者アカウントを追加したことを確認します (オンプレミスの VM をレプリケートするために使用されます)
- Azure 内にターゲット成果物が作成されていることを確認します
    - ターゲット リソース グループ
    - ターゲット ストレージ アカウント (およびそのリソース グループ) - Premium マネージド ディスクへの移行を計画している場合、Premium ストレージ アカウントを作成します
    - キャッシュ ストレージ アカウント (およびそのリソース グループ) - コンテナーと同じリージョンに Standard ストレージ アカウントを作成します
    - フェールオーバー用のターゲット仮想ネットワーク (およびそのリソース グループ)
    - ターゲット サブネット
    - テスト フェールオーバー用のターゲット仮想ネットワーク (およびそのリソース グループ)
    - 可用性セット (必要な場合)
    - ターゲット ネットワーク セキュリティ グループとそのリソース グループ
- ターゲット VM のプロパティが決定していることを確認します
    - ターゲット VM 名
    - Azure 内のターゲット VM サイズ (Azure Migrate の評価を使用して決定できます)
    - VM 内のプライマリ NIC のプライベート IP アドレス
- GitHub の [Azure PowerShell サンプル](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) リポジトリからスクリプトをダウンロードします

### <a name="csv-input-file"></a>CSV 入力ファイル
すべての前提条件を満たしたら、移行する各ソース マシンのデータを含んだ CSV ファイルを作成する必要があります。 入力 CSV には、入力の詳細を含んだヘッダー行と、移行する必要がある各コンピューターの詳細を含んだ行を含める必要があります。 すべてのスクリプトは、同じ CSV ファイルを使用するように設計されています。 scripts フォルダーには、参照用のサンプル CSV テンプレートがあります。

### <a name="script-execution"></a>スクリプトの実行
CSV の準備ができたら、次の手順を実行してオンプレミス VM の移行を実行できます。

**手順番号** | **スクリプト名** | **説明**
--- | --- | ---
1 | asr_startmigration.ps1 | csv にリストされているすべての VM のレプリケーションを有効にします。このスクリプトを実行すると、各 VM のジョブ詳細を含んだ CSV 出力が作成されます
2 | asr_replicationstatus.ps1 | レプリケーションの状態をチェックします。このスクリプトを実行すると、各 VM の状態を含んだ csv が作成されます
3 | asr_updateproperties.ps1 | VM がレプリケート/保護されたら、このスクリプトを使用して VM のターゲット プロパティ (コンピューティングとネットワークのプロパティ) を更新します
4 | asr_propertiescheck.ps1 | プロパティが適切に更新されているかどうかを確認します
5 | asr_testmigration.ps1 |  csv にリストされている VM のテスト フェールオーバーを開始します。このスクリプトを実行すると、各 VM のジョブ詳細を含んだ CSV 出力が作成されます
6 | asr_cleanuptestmigration.ps1 | テスト フェールオーバーされた VM を手動で検証したら、このスクリプトを使用してテスト フェールオーバー VM をクリーンアップします
7 | asr_migration.ps1 | csv にリストされている VM の計画外フェールオーバーを実行します。このスクリプトを実行すると、各 VM のジョブ詳細を含んだ CSV 出力が作成されます。 このスクリプトでは、アプリケーションの一貫性を保つため、フェールオーバーをトリガーする前にオンプレミス VM をシャットダウンすることはしません。スクリプトを実行する前に、VM を手動でシャットダウンすることをお勧めします。
8 | asr_completemigration.ps1 | VM 上でコミット操作を実行し、Azure Site Recovery エンティティを削除します
9 | asr_postmigration.ps1 | NIC のポスト フェールオーバーにネットワーク セキュリティ グループを割り当てる場合は、このスクリプトを使用してその操作を実行できます。 これにより、ターゲット VM 内の任意の 1 つの NIC に NSG が割り当てられます。

## <a name="how-to-migrate-to-managed-disks"></a>マネージド ディスクに移行する方法
このスクリプトは、既定で VM を Azure のマネージド ディスクに移行します。 指定されたターゲット ストレージ アカウントが Premium ストレージ アカウントの場合、移行後に Premium マネージド ディスクが作成されます。 キャッシュ ストレージ アカウントは Standard アカウントのままにすることができます。 ターゲット ストレージ アカウントが Standard ストレージ アカウントの場合、移行後に Standard ディスクが作成されます。 

## <a name="next-steps"></a>次のステップ

Azure Site Recovery を使用してサーバーを Azure に移行する方法について[詳しく知る](../site-recovery/migrate-tutorial-on-premises-azure.md)

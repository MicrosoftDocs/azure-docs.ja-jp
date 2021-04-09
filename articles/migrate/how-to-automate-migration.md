---
title: Azure Migrate でエージェントレス VMware 移行を自動化する
description: スクリプトを使用して Azure Migrate で多数の VMware VM を移行する方法について説明します
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: cdae1fe13f8e08cb6b817f8ec6431c77013020d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754268"
---
# <a name="scale-migration-of-vmware-vms"></a>VMware VM のスケール移行 

この記事は、スクリプトを使用し、エージェントレス方式を使用して多数の VMware 仮想マシン (VM) を移行する方法を理解するために役立ちます。 移行をスケーリングするには、[Azure Migrate PowerShell モジュール](./tutorial-migrate-vmware-powershell.md)を使用します。 

Azure Migrate VMware の移行自動化スクリプトは、GitHub の [Azure PowerShell サンプル](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) リポジトリからダウンロードできます。 このスクリプトを使用すると、エージェントレス移行方法を使用して VMware VM を Azure に移行できます。 これらのスクリプトで使用される Azure Migrate PowerShell コマンドについては、[こちら](./tutorial-migrate-vmware-powershell.md)を参照してください。

## <a name="current-limitations"></a>現在の制限
- これらのスクリプトは、VMware VM とすべてのディスクの移行をサポートしています。 VMware VM にアタッチされているディスクを選択的にレプリケートする場合は、スクリプトを更新できます。 
- スクリプトは、評価の推奨事項の使用をサポートしています。 評価の推奨事項が使用されていない場合、VMware VM にアタッチされているすべてのディスクが同じマネージド ディスクの種類 (Standard または Premium) に移行されます。 VM が同じで種類が複数のマネージド ディスクを使用する場合は、スクリプトを更新できます

## <a name="prerequisites"></a>前提条件

- [検出のチュートリアルを完了](tutorial-discover-vmware.md)して、移行のために Azure と VMware を準備します。
- 2 番目のチュートリアルを完了して、Azure に移行する前に [VMware VM を評価](./tutorial-assess-vmware-azure-vm.md)しておくことをお勧めします。
- Azure PowerShell `Az` モジュールがあります。 Azure PowerShell をインストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成のガイド](/powershell/azure/install-az-ps)を参照してください。

## <a name="install-azure-migrate-powershell-module"></a>Azure Migrate PowerShell モジュールをインストールする

Azure Migrate PowerShell モジュールはプレビューで利用できます。 次のコマンドを使用して、PowerShell モジュールをインストールする必要があります。 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="csv-input-file"></a>CSV 入力ファイル
すべての前提条件を満たしたら、移行する各ソース VM のデータを含んだ CSV ファイルを作成する必要があります。 すべてのスクリプトは、同じ CSV ファイルを使用するように設計されています。 scripts フォルダーには、参照用のサンプル CSV テンプレートがあります。 csv ファイルは構成可能であるため、評価の推奨事項を使用することや、特定の VM に対して特定の操作をトリガーしないかどうかを指定することもできます。 

> [!NOTE]
> 同じ csv ファイルを使用して、複数の Azure Migrate プロジェクト内の VM を移行できます。

### <a name="csv-file-schema"></a>CSV ファイル スキーマ

**列ヘッダー** | **説明**
--- | ---
AZMIGRATEPROJECT_SUBSCRIPTION_ID | Azure Migrate プロジェクトのサブスクリプション ID を指定します。
AZMIGRATEPROJECT_RESOURCE_GROUP_NAME | Azure Migrate リソース グループ名を指定します。
AZMIGRATEPROJECT_NAME | サーバーを移行する Azure Migrate プロジェクトの名前を指定します。 
SOURCE_MACHINE_NAME | Azure Migrate プロジェクト内の検出された VM のフレンドリ名 (表示名) を指定します。
AZMIGRATEASSESSMENT_NAME | 移行に利用する必要がある評価の名前を指定します。
AZMIGRATEGROUP_NAME | Azure Migrate の評価に使用されたグループの名前を指定します。
TARGET_RESOURCE_GROUP_NAME | VM の移行先にする Azure リソース グループの名前を指定します。
TARGET_VNET_NAME| 移行された VM に使用する Azure 仮想ネットワークの名前を指定します。
TARGET_SUBNET_NAME | ターゲット仮想ネットワーク内で、移行された VM に使用するサブネットの名前を指定します。 空白のままにすると、"既定" のサブネットが使用されます。
TARGET_MACHINE_NAME | Azure 内で移行された VM に使用する名前を指定します。 空白のままにすると、ソース マシン名が使用されます。  
TARGET_MACHINE_SIZE | Azure 内で VM に使用する SKU を指定します。 VM を Azure の D2_v2 VM に移行するには、このフィールドの値を "Standard_D2_v2" と指定します。 評価を使用する場合、この値は評価の推奨事項に基づいて導き出されます。
LICENSE_TYPE | Windows Server VM に Azure ハイブリッド特典を使用するかどうかを指定します。 Azure ハイブリッド特典を利用するには、値 "WindowsServer" を使用します。 それ以外の場合は、空白のままにするか、"NoLicenseType" を使用します。
OS_DISK_ID | 移行する VM の OS ディスク ID を指定します。 使用するディスク ID は、Get-AzMigrateServer コマンドレットを使用して取得したディスクの一意識別子 (UUID) プロパティです。 値が指定されていない場合、スクリプトにより、VM の最初のディスクが OS ディスクとして使用されます。
TARGET_DISKTYPE | Azure 内の VM のすべてのディスクに使用するディスクの種類を指定します。 Premium マネージド ディスクの場合は "Premium_LRS"、Standard SSD ディスクの場合は "StandardSSD_LRS"、Standard HDD ディスクの場合は "Standard_LRS" を使用します。 評価を使用することを選択した場合、スクリプトにより、VM の各ディスクに推奨されるディスクの種類を使用することが優先されます。 評価を使用しない場合、または値を指定しない場合、スクリプトにより、既定で Standard HDD ディスクが使用されます。    
AVAILABILITYZONE_NUMBER | 移行された VM に使用する可用性ゾーン番号を指定します。 可用性ゾーンを使用しない場合は、これを空白のままにすることができます。 
AVAILABILITYSET_NAME | 移行された VM に使用する可用性セットの名前を指定します。 可用性セットを使用しない場合は、これを空白のままにすることができます。
TURNOFF_SOURCESERVER | 移行時にソース VM を無効にする場合は、'Y' を指定します。 それ以外の場合は、'N' を使用します。 空白のままにすると、スクリプトにより、'N' という値が想定されます。
TESTMIGRATE_VNET_NAME | テスト移行に使用する仮想ネットワークの名前を指定します。
UPDATED_TARGET_RESOURCE_GROUP_NAME | Azure 内の移行された VM によって使用されるリソース グループを更新する場合は、Azure リソース グループの名前を指定します。それ以外の場合は空白のままにします。 
UPDATED_TARGET_VNET_NAME | Azure 内の移行された VM によって使用される仮想ネットワークを更新する場合は、Azure 仮想ネットワークの名前を指定します。それ以外の場合は空白のままにします。
UPDATED_TARGET_MACHINE_NAME | Azure 内の移行された VM によって使用される名前を更新する場合は、使用する新しい名前を指定します。それ以外の場合は空白のままにします。
UPDATED_TARGET_MACHINE_SIZE | Azure 内の移行された VM によって使用される SKU を更新する場合は、使用する新しい SKU を指定します。それ以外の場合は空白のままにします。
UPDATED_AVAILABILITYZONE_NUMBER | Azure 内の移行された VM によって使用される可用性ゾーンを更新する場合は、使用する新しい可用性ゾーンを指定します。それ以外の場合は空白のままにします。
UPDATED_AVAILABILITYSET_NAME | Azure 内の移行された VM によって使用される可用性セットを更新する場合は、使用する新しい可用性セットを指定します。それ以外の場合は空白のままにします。
UPDATE_NIC1_ID | 更新する NIC の ID を指定します。 空白のままにすると、スクリプトにより、検出された VM の最初の NIC という値が想定されます。 VM の NIC を更新しない場合は、NIC 名を含むすべてのフィールドを空白のままにします。 
UPDATED_TARGET_NIC1_SELECTIONTYPE | この NIC に使用する値を指定します。 "Primary"、"Secondary"、または "DoNot Create" を使用して、この NIC がプライマリかセカンダリか、または移行された VM 上では作成しないかを指定します。 VM のプライマリ NIC として指定できる NIC は 1 つだけです。 更新しない場合は空白のままにします。
UPDATED_TARGET_NIC1_SUBNET_NAME | 移行された VM 上の NIC に使用するサブネットの名前を指定します。 更新しない場合は空白のままにします。
UPDATED_TARGET_NIC1_IP | 静的 IP を使用する場合は、移行された VM 上の NIC に使用する IPv4 アドレスを指定します。 IP を自動的に割り当てる場合は、"auto" を使用します。 更新しない場合は空白のままにします。
UPDATE_NIC2_ID | 更新する NIC の ID を指定します。 空白のままにすると、スクリプトにより、検出された VM の 2 つ目の NIC という値が想定されます。 VM の NIC を更新しない場合は、NIC 名を含むすべてのフィールドを空白のままにします。
UPDATED_TARGET_NIC2_SELECTIONTYPE | この NIC に使用する値を指定します。 "Primary"、"Secondary"、または "DoNot Create" を使用して、この NIC がプライマリかセカンダリか、または移行された VM 上では作成しないかを指定します。 VM のプライマリ NIC として指定できる NIC は 1 つだけです。 更新しない場合は空白のままにします。
UPDATED_TARGET_NIC2_SUBNET_NAME | 移行された VM 上の NIC に使用するサブネットの名前を指定します。 更新しない場合は空白のままにします。
UPDATED_TARGET_NIC2_IP | 静的 IP を使用する場合は、移行された VM 上の NIC に使用する IPv4 アドレスを指定します。 IP を自動的に割り当てる場合は、"auto" を使用します。 更新しない場合は空白のままにします。
OK_TO_UPDATE | AzMigrate_UpdateMachineProperties スクリプトを実行するときに VM のプロパティを更新する必要があることを示すには、'Y' を使用します。 それ以外の場合は 'N' を使用するか空白のままにします。
OK_TO_MIGRATE | AzMigrate_StartMigration スクリプトを実行するときに VM を移行する必要があることを示すには、'Y' を使用します。 VM を移行しない場合は、'N' を使用するか空白のままにします。 
OK_TO_USE_ASSESSMENT | AzMigrate_StartReplication スクリプトを実行するときに、VM によって評価の推奨事項を使用してレプリケーションを開始する必要があることを示すには、'Y' を使用します。 これにより、csv ファイルの TARGET_MACHINE_SIZE 値と TARGET_DISKTYPE 値が上書きされます。 評価の推奨事項を使用しない場合は、'N' を使用するか空白のままにします。
OK_TO_TESTMIGRATE | AzMigrate_StartTestMigration スクリプトを実行するときに VM をテスト移行する必要があることを示すには、'Y' を使用します。 VM をテスト移行しない場合は、'N' を使用するか空白のままにします。 
OK_TO_RETRIEVE_REPLICATIONSTATUS | AzMigrate_ReplicationStatus スクリプトを実行するときに VM のレプリケーション状態を更新する必要があることを示すには、'Y' を使用します。 レプリケーションの状態を更新しない場合は、'N' を使用するか空白のままにします。
OK_TO_CLEANUP | AzMigrate_StopReplication スクリプトを実行するときに VM のレプリケーションをクリーンアップする必要があることを示すには、'Y' を使用します。 それ以外の場合は 'N' を使用するか空白のままにします。
OK_TO_TESTMIGRATE_CLEANUP | AzMigrate_CleanUpTestMigration スクリプトを実行するときに VM のテスト移行をクリーンアップする必要があることを示すには、'Y' を使用します。 それ以外の場合は 'N' を使用するか空白のままにします。


## <a name="script-execution"></a>スクリプトの実行

CSV の準備ができたら、次の手順を実行して、オンプレミスの VMware VM を移行できます。

**手順番号** | **スクリプト名** | **説明**
--- | --- | ---
1 | AzMigrate_StartReplication.ps1 | csv に列挙されているすべての VM のレプリケーションを有効にします。スクリプトによって、CSV 出力とトラブルシューティング用のログ ファイルが作成されます。
2 | AzMigrate_ReplicationStatus.ps1 | レプリケーションの状態を確認します。スクリプトによって、各 VM の状態を含む csv 出力とトラブルシューティング用のログ ファイルが作成されます。
3 | AzMigrate_UpdateMachineProperties.ps1 | VM によって初期レプリケーションを完了したら、このスクリプトを使用して VM のターゲット プロパティ (コンピューティングとネットワークのプロパティ) を更新します。 スクリプトによって、各 VM のジョブの詳細を含む CSV 出力が作成されます。
4 | AzMigrate_StartTestMigration.ps1 |  テスト移行用に構成された csv に列挙されているすべての VM のテスト フェールオーバーを開始します。 スクリプトによって、各 VM のジョブの詳細を含む CSV 出力が作成されます。
5 | AzMigrate_CleanUpTestMigration.ps1 | テスト フェールオーバーされた VM を手動で検証したら、このスクリプトを使用して、テスト移行のクリーンアップ用に構成された csv に列挙されているすべての VM のテスト フェールオーバー VM をクリーンアップします。 スクリプトによって、各 VM のジョブの詳細を含む CSV 出力が作成されます。
6 | AzMigrate_StartMigration.ps1 | 移行用に構成されている csv に列挙されているすべての VM の移行を開始します。 スクリプトによって、各 VM のジョブの詳細を含む CSV 出力が作成されます。
7 | AzMigrate_StopReplication.ps1 | VM が正常に移行された後、または他の理由でレプリケーションを取り消す場合は、VM のレプリケーションを停止します。 スクリプトによって、各 VM のジョブの詳細を含む CSV 出力が作成されます。


次のスクリプトは、レプリケーションの有効化、テスト移行の開始、VM プロパティの更新など、すべての Azure Migrate 操作に対して、他のスクリプトによって呼び出されます。 すべてのスクリプトが同じフォルダーおよびパスに存在することを確認します。 

**手順番号** | **スクリプト名** | **説明**
--- | --- | ---
1 | AzMigrate_Shared.ps1 | (API を介して) 評価プロパティ、検出された VM を取得し、VM をレプリケートするための関数を含む一般的なスクリプト。 
2 | AzMigrate_CSV_Processor.ps1 | ログの読み込み、読み取り、出力など、csv ファイルの操作に使用される関数を含む一般的なスクリプト。 
3 | AzMigrate_Logger.ps1 | Azure Migrate の自動化操作に関するログ ファイルを生成するために呼び出される一般的なスクリプト。 ログ ファイルの形式は log.Scriptname.Datetime.txt です。

上記に加えて、このフォルダーには、さまざまな Azure Migrate 操作用のカスタム スクリプトを構築するためのスケルトン フレームワークを含む AzMigrate_Template.ps1 も含まれています。 

### <a name="script-execution-syntax"></a>スクリプト実行構文

スクリプトをダウンロードすると、次のようにスクリプトを実行できます。

Input.csv ファイルを使用して VM のレプリケーションを開始するスクリプトを実行する場合は、次の構文を使用します。 

```azurepowershell
".\AzMigrate_StartReplication.ps1" .\Input.csv 
```

Azure Migrate を使用して VMware VM を移行する場合の Azure PowerShell の使用の詳細については、[チュートリアル](./tutorial-migrate-vmware-powershell.md)に従ってください。
---
title: Azure Backup 用の VM スナップショットの Windows 拡張機能
description: VM スナップショット拡張機能を使用して、Azure Backup から仮想マシンのアプリケーション整合性バックアップを作成します
services: backup, virtual-machines
documentationcenter: ''
author: trinadhkotturu
manager: gwallace
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.date: 10/15/2020
ms.author: trinadhk
ms.openlocfilehash: 834c886c15e50870ec008dbb5ceab38132cc4649
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561069"
---
# <a name="vm-snapshot-windows-extension-for-azure-backup"></a>Azure Backup 用の VM スナップショットの Windows 拡張機能

Azure Backup は、オンプレミスからクラウドにワークロードをバックアップし、クラウド リソースを Recovery Services コンテナーにバックアップするためのサポートを提供します。 Azure Backup では、VM スナップショット拡張機能を使用すれば、VM をシャットダウンしなくても Azure 仮想マシンのアプリケーション整合バックアップを作成できます。 VM スナップショット拡張機能は、Azure Backup サービスの一部として、Microsoft によって公開され、サポートされます。 Azure Backup は、バックアップの有効化の後にトリガーされる最初のスケジュールされたバックアップの一部として、拡張機能をインストールします。 このドキュメントでは、VM スナップショット拡張機能でサポートされているプラットフォーム、構成、デプロイのオプションについて詳しく説明します。

VMSnapshot 拡張機能は、Azure portal で、非マネージド VM に対してのみ表示されます。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム
サポートされるオペレーティング システムの一覧については、[Azure Backup でサポートされるオペレーティング システム](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)に関するページをご覧ください

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、VM スナップショット拡張機能のスキーマを示しています。 この拡張機能にはタスク ID が必要です。これは、VM でトリガーされたバックアップ ジョブ、状態 BLOB URI (スナップショット操作が書き込まれる場所)、スナップショットのスケジュールされた開始時間、ログ BLOB URI (スナップショット タスクに対応するログが書き込まれる場所)、objstr (VM ディスクとメタ データの表現) を識別します。  これらの設定は機密データとして取り扱う必要があるため、保護された設定構成に格納される必要があります。 Azure VM 拡張機能の保護された設定データは暗号化され、ターゲットの仮想マシンでのみ、暗号化が解除されます。 これらの設定は、Azure Backup サービスからバックアップ ジョブの一部としてのみ渡されることが推奨されることに注意してください。

```json
{
  "type": "extensions",
  "name": "VMSnapshot",
  "location":"<myLocation>",
  "properties": {
    "publisher": "Microsoft.Azure.RecoveryServices",
    "type": "VMSnapshot",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "locale":"<location>",
      "taskId":"<taskId used by Azure Backup service to communicate with extension>",
      "commandToExecute": "snapshot",
      "commandStartTimeUTCTicks": "<scheduled start time of the snapshot task>",
      "vmType": "microsoft.compute/virtualmachines"
    },
    "protectedSettings": {
      "objectStr": "<blob SAS uri representation of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>プロパティ値

| 名前 | 値/例 | データ型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | string |
| commandStartTimeUTCTicks | 6.36458E+17 | string |
| locale | ja-JP | string |
| objectStr | Encoding of sas uri array- "blobSASUri": ["https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmwin1404ltsc201652903941.vhd?sv=2014-02-14&sr=b&sig=TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna8461.blob.core.windows.net\/vhds\/vmwin1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&sig=5S0A6YDWvVwqPAkzWXVy%2BS%2FqMwzFMbamT5upwx05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna8461.blob.core.windows.net\/bootdiagnostics-vmwintu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmwin1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r%2BC%2BNIAork%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmwin1404ltsc-20170705-124311.vhd?sv=2014-02-14&sr=b&sig=ZUM9d28Mvvm%2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw"] | string |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |



## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 ただし、VM スナップショット拡張機能を仮想マシンに追加するための推奨される方法は、仮想マシンでバックアップを有効化することです。 これは、Resource Manager テンプレートを使って実現できます。  仮想マシンでのバックアップを有効にする Resource Manager テンプレートのサンプルは、[Azure クイック スタート ギャラリー](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/)にあります。


## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

Azure CLI を使用すると、仮想マシンでバックアップを有効にすることができます。 バックアップの有効化の後、スケジュールされた最初のバックアップ ジョブによって、VM スナップショット拡張機能が VM にインストールされます。

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal か Azure CLI を使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、Azure CLI を使用して次のコマンドを実行します。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

拡張機能の実行の出力は、次のファイルにログ記録されます。

```
C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot
```

### <a name="error-codes-and-their-meanings"></a>エラー コードとその意味

トラブルシューティングの情報については、「[Azure 仮想マシンのバックアップのトラブルシューティング](../../backup/backup-azure-vms-troubleshoot.md)」をご覧ください。

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

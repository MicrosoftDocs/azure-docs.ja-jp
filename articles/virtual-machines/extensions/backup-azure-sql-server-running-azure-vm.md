---
title: Azure VM で実行されている SQL Server 用の Azure Backup
description: Azure VM で実行されている Azure Backup の SQL Server を登録する方法
services: backup
author: swatisachdeva
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: vijayts
ms.openlocfilehash: 25f23078af67b2f80f39faab975cbec54721c560
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871901"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure VM で実行されている SQL Server 用の Azure Backup

さまざまなオファリングがある中、Azure Backup では、Azure VM で実行されている SQL Server などのワークロードをバックアップするためのサポートが提供されます。 SQL アプリケーションは Azure VM 内で実行されているため、バックアップ サービスがアプリケーションにアクセスして必要な詳細をフェッチするにはアクセス許可が必要です。
そのため、Azure Backup では、ユーザーによってトリガーされる登録プロセスの間に、SQL Server が実行されている VM に、**AzureBackupWindowsWorkload** 拡張機能がインストールされます。

## <a name="prerequisites"></a>前提条件

サポートされるシナリオの一覧については、Azure Backup でサポートされる[サポート可能性マトリックス](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support)をご覧ください。

## <a name="network-connectivity"></a>ネットワーク接続

Azure Backup では、NSG タグ、プロキシ サーバーの展開、またはリストされた IP 範囲がサポートされます。各方法について詳しくは、こちらの[記事](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity)をご覧ください。

## <a name="extension-schema"></a>拡張機能のスキーマ

拡張機能のスキーマとプロパティの値は、サービスによって CRP API に渡される構成値 (実行時の設定) です。 これらの構成値は、登録とアップグレードの間に使われます。 **AzureBackupWindowsWorkload** 拡張機能でもこのスキーマが使われます。 スキーマは事前に設定されています。objectStr フィールドで、新しいパラメーターを追加することができます

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

次の JSON では、WorkloadBackup 拡張機能のスキーマを示します。  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>プロパティ値

EnableAdfsAuthentication | 値/例 | データ型
 --- | --- | ---
locale | en-us  |  string
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | string
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ==" | string
commandStartTimeUTCTicks | "636967192566036845"  | string
vmType  | "microsoft.compute/virtualmachines"  | string
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ==" | string
logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string
statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string


## <a name="template-deployment"></a>テンプレートのデプロイ

AzureBackupWindowsWorkload 拡張機能を仮想マシンに追加する推奨される方法は、仮想マシンで SQL Server のバックアップを有効にすることです。 これは、SQL Server VM でのバックアップを自動化するために設計されている [Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup)を使って実現できます。


## <a name="powershell-deployment"></a>PowerShell でのデプロイ

SQL アプリケーションが含まれる Azure VM を、Recovery Services コンテナーに "登録" する必要があります。 登録の間に、AzureBackupWindowsWorkload 拡張機能が VM にインストールされます。 VM を登録するには、 [Register-AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) コマンドレットを使います。
 
```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```
 
そのコマンドによってこのリソースの**バックアップ コンテナー**が返され、状態は**登録済み**になります。


## <a name="next-steps"></a>次の手順

- Azure SQL Server VM のバックアップに関するトラブルシューティングのガイドラインについての[詳細](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot)
- Azure 仮想マシン (VM) 上で実行され、Azure Backup サービスを使用する SQL Server データベースのバックアップに関する[一般的な質問](https://docs.microsoft.com/azure/backup/faq-backup-sql-server)。

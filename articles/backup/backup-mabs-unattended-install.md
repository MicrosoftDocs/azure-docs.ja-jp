---
title: Azure Backup Server V2 のサイレント インストール
description: PowerShell スクリプトを使用して、Azure Backup Server V2 をサイレント モードでインストールします。 この種類のインストールは無人インストールとも呼ばれます。
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: raynew
ms.openlocfilehash: add1f4057b5b52310f53553dcd23e3357fb1ee29
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465026"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Azure Backup Server の無人インストールの実行

Azure Backup Server の無人インストールの実行について説明します。

次の手順は、Azure Backup Server V1 をインストールする場合は適用されません。

## <a name="install-backup-server"></a>Backup Server のインストール

1. Azure Backup Server V2 以降をホストしているサーバーで、テキスト ファイルを作成します。 (メモ帳などのテキスト エディターでファイルを作成できます。)MABSSetup.ini としてファイルを保存します。

2. MABSSetup.ini ファイルに次のコードを貼り付けます。 ブラケット (\< \>) 内のテキストを環境の値に置き換えます。 テキスト例を次に示します。

   ```
   [OPTIONS]
   UserName=administrator
   CompanyName=<Microsoft Corporation>
   SQLMachineName=localhost
   SQLInstanceName=<SQL instance name>
   SQLMachineUserName=administrator
   SQLMachinePassword=<admin password>
   SQLMachineDomainName=<machine domain>
   ReportingMachineName=localhost
   ReportingInstanceName=<reporting instance name>
   SqlAccountPassword=<admin password>
   ReportingMachineUserName=<username>
   ReportingMachinePassword=<reporting admin password>
   ReportingMachineDomainName=<domain>
   VaultCredentialFilePath=<vault credential full path and complete name>
   SecurityPassphrase=<passphrase>
   PassphraseSaveLocation=<passphrase save location>
   UseExistingSQL=<1/0 use or do not use existing SQL>
   ```

3. ファイルを保存します。 次に、インストール サーバーの管理者特権でのコマンド プロンプトで、次のコマンドを入力します。

   ```
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

インストールするためにこれらのフラグを使用できます。</br>
**/f**: .ini ファイル パス</br>
**/l**:ログのパス</br>
**/i**:インストール パス</br>
**/x**:アンインストール パス</br>

## <a name="next-steps"></a>次の手順
Backup Server をインストールしたら、サーバーを準備する方法、またはワークロードの保護を開始する方法について見ていきましょう。

- [Backup Server ワークロードの準備](backup-azure-microsoft-azure-backup.md)
- [Backup Server を使用した VMware サーバーのバックアップ](backup-azure-backup-server-vmware.md)
- [Backup Server を使用した SQL Server のバックアップ](backup-azure-sql-mabs.md)
- [Backup Server への Modern Backup Storage の追加](backup-mabs-add-storage.md)

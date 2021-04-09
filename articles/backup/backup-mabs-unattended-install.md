---
title: Azure Backup Server V2 のサイレント インストール
description: PowerShell スクリプトを使用して、Azure Backup Server V2 をサイレント モードでインストールします。 この種類のインストールは無人インストールとも呼ばれます。
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 1539089e713bcf8e959707c6ff4a608f062a7c00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74172242"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Azure Backup Server の無人インストールの実行

Azure Backup Server の無人インストールの実行について説明します。

次の手順は、Azure Backup Server V1 をインストールする場合は適用されません。

## <a name="install-backup-server"></a>Backup Server のインストール

1. Azure Backup Server V2 以降をホストしているサーバーで、テキスト ファイルを作成します。 (メモ帳などのテキスト エディターでファイルを作成できます。)MABSSetup.ini としてファイルを保存します。

2. MABSSetup.ini ファイルに次のコードを貼り付けます。 ブラケット (\< \>) 内のテキストを環境の値に置き換えます。 テキスト例を次に示します。

   ```text
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

   ```cmd
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

インストールするためにこれらのフラグを使用できます。</br>
**/f**: .ini ファイル パス</br>
**/l**:ログのパス</br>
**/i**:インストール パス</br>
**/x**:アンインストール パス</br>

## <a name="next-steps"></a>次のステップ

Backup Server をインストールしたら、サーバーを準備する方法、またはワークロードの保護を開始する方法について見ていきましょう。

- [Backup Server ワークロードの準備](backup-azure-microsoft-azure-backup.md)
- [Backup Server を使用した VMware サーバーのバックアップ](backup-azure-backup-server-vmware.md)
- [Backup Server を使用した SQL Server のバックアップ](backup-azure-sql-mabs.md)
- [Backup Server への Modern Backup Storage の追加](backup-mabs-add-storage.md)

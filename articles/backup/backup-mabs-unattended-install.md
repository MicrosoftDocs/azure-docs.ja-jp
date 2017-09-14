---
title: "Azure Backup Server v2 のサイレント インストール |Microsoft Docs"
description: "Azure Backup Server v2 をサイレント インストールするのに、PowerShell スクリプトを使用します。 この種類のインストールは無人インストールとも呼ばれます。"
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/30/2017
ms.author: markgal;masaran
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 91778a67f9ef523aa87b7918197e0d0ded0f5702
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017


---
# <a name="run-an-unattended-installation-of-azure-backup-server-v2"></a>Azure Backup Server v2 の無人インストールの実行

Azure Backup Server v2 の無人インストールの実行について説明します。 

次の手順は、Azure Backup Server v1 をインストールする場合は適用されません。

## <a name="install-backup-server-v2"></a>Backup Server v2 のインストール

1. Azure Backup Server v2 をホストしているサーバーで、テキスト ファイルを作成します。 (メモ帳などのテキスト エディターでファイルを作成できます。)MABSSetup.ini としてファイルを保存します。 

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
**/l**: ログのパス</br>
**/i**: インストール パス</br>
**/x**: アンインストール パス</br>

## <a name="next-steps"></a>次のステップ
Backup Server をインストールしたら、サーバーを準備する方法、またはワークロードの保護を開始する方法について見ていきましょう。

- [Backup Server ワークロードの準備](backup-azure-microsoft-azure-backup.md)
- [Backup Server を使用した VMware サーバーのバックアップ](backup-azure-backup-server-vmware.md)
- [Backup Server を使用した SQL Server のバックアップ](backup-azure-sql-mabs.md)
- [Backup Server への Modern Backup Storage の追加](backup-mabs-add-storage.md)


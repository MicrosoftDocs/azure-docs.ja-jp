---
title: "Azure Backup: Linux VM のアプリケーション整合性バックアップ | Microsoft Docs"
description: "スクリプトを使用して、Linux 仮想マシンの Azure へのアプリケーション整合性バックアップを確実にできるようにします。 スクリプトは Resource Manager デプロイ内の Linux VM にのみ適用されます。スクリプトは、Windows VM または Service Manager のデプロイには適用されません。 この記事では、トラブルシューティングを含むスクリプトを構成する手順について説明します。"
services: backup
documentationcenter: dev-center-name
author: anuragmehrotra
manager: shivamg
keywords: "アプリケーション整合性バックアップ; アプリケーション整合性 Azure VM バックアップ; Linux VM バックアップ; Azure Backup"
ms.assetid: bbb99cf2-d8c7-4b3d-8b29-eadc0fed3bef
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 4/12/2017
ms.author: anuragm;markgal
ms.openlocfilehash: 378c65bec8fd1f880ed459e76f5e4b5d85e49d2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="application-consistent-backup-of-azure-linux-vms-preview"></a>Azure Linux VM のアプリケーション整合性バックアップ (プレビュー)

この記事では、Linux の事前/事後スクリプト フレームワークと、それらを使用して Azure Linux VM のアプリケーション整合性バックアップを作成する方法について説明します。

> [!Note]
> 事前/事後スクリプト フレームワークは、Azure Resource Manager がデプロイされている Linux 仮想マシンについてのみサポートしています。 アプリケーション整合性スクリプトは、Service Manager がデプロイされた仮想マシンまたは Windows 仮想マシンについてサポートしていません。
>

## <a name="how-the-framework-works"></a>フレームワークの動作

このフレームワークは、VM スナップショットの作成中にカスタム事前/事後スクリプトを実行する機能が備わっています。 事前スクリプトは VM スナップショットの作成直前に実行され、事後スクリプトは VM スナップショットの作成直後に実行されます。 この機能を利用することで、VM スナップショットの作成中にアプリケーションと環境を柔軟に制御することができます。

このシナリオでは、アプリケーションと整合性が取れた VM バックアップを作成することが重要です。 事前スクリプトでは、アプリケーションのネイティブ API を呼び出して、入出力を休止し、メモリ内のコンテンツをディスクにフラッシュすることができます。 この処理によって、アプリケーションと整合性のあるスナップショットを作成できます (つまり、復元後に VM を起動したときに、アプリケーションも起動されます)。 事後スクリプトを使用して入出力を再開できます。 入出力を再開するには、VM のスナップショット作成後にアプリケーションが通常の処理を再開できるようにアプリケーションのネイティブ API を使用します。

## <a name="steps-to-configure-pre-script-and-post-script"></a>事前スクリプトと事後スクリプトを構成する手順

1. バックアップを作成する Linux VM にルート ユーザーとしてサインインします。

2. **VMSnapshotScriptPluginConfig.json** を [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) からダウンロードし、バックアップするすべての VM の **/etc/azure** フォルダーにコピーします。 **/etc/azure** ディレクトリを作成します (まだ存在しない場合)。

3. バックアップする予定のすべての VM にアプリケーションの事前スクリプトと事後スクリプトをコピーします。 スクリプトは、VM の任意の場所にコピーできます。 必ず **VMSnapshotScriptPluginConfig.json** に記載されているスクリプト ファイルの完全パスを更新してください。

4. ファイルのアクセス許可が次のようになっていることを確認します。

   - **VMSnapshotScriptPluginConfig.json**: アクセス許可 "600" を指定します。 たとえば、"root" ユーザーにのみ、このファイルに対する "読み取り" アクセス許可と "書き込み" アクセス許可を付与します。どのユーザーにも "実行" アクセス許可は付与しません。

   - **事前スクリプト ファイル**: アクセス許可 "700" を指定します。  たとえば、"root" ユーザーにのみ、このファイルに対する "読み取り"、"書き込み"、"実行" のすべてのアクセス許可を付与します。
  
   - **事後スクリプト ファイル**: アクセス許可 "700" を指定します。 たとえば、"root" ユーザーにのみ、このファイルに対する "読み取り"、"書き込み"、"実行" のすべてのアクセス許可を付与します。

   > [!Important]
   > このフレームワークを使用すると、ユーザーはさまざまな機能を利用できるようになります。 セキュリティで保護すること、重要な JSON ファイルとスクリプト ファイルには "ルート" ユーザーのみがアクセスできるようにすることが重要です。
   > 上記の要件を満たしていない場合、スクリプトは実行されません。 その結果、ファイル システム/クラッシュの整合性が取れたバックアップを作成できます。
   >

5. **VMSnapshotScriptPluginConfig.json** を以下のように構成します。
    - **pluginName**: このフィールドは変更しないでください。変更するとスクリプトが正しく動作しなくなることがあります。

    - **preScriptLocation**: バックアップする VM 上の事前スクリプトの完全パスを指定します。

    - **postScriptLocation**: バックアップする VM 上の事後スクリプトの完全パスを指定します。

    - **preScriptParams**: 事前スクリプトに渡す必要があるパラメーターを指定します (省略可能)。 すべてのパラメーターは引用符で囲む必要があります。また、複数のパラメーターを指定する場合はコンマ区切りにする必要があります。

    - **postScriptParams**: 事後スクリプトに渡す必要があるパラメーターを指定します (省略可能)。 すべてのパラメーターは引用符で囲む必要があります。また、複数のパラメーターを指定する場合はコンマ区切りにする必要があります。

    - **preScriptNoOfRetries**: 事前スクリプトでエラーが発生した場合の再試行回数を指定します。 0 (ゼロ) を指定すると、試行回数は 1 回のみでエラーが発生しても再試行は実行されません。

    - **postScriptNoOfRetries**: 事後スクリプトでエラーが発生した場合の再試行回数を指定します。 0 (ゼロ) を指定すると、試行回数は 1 回のみでエラーが発生しても再試行は実行されません。
    
    - **timeoutInSeconds**: 事前スクリプトと事後スクリプトのタイムアウトを指定します。

    - **continueBackupOnFailure**: 事前スクリプトまたは事後スクリプトでエラーが発生した場合、Azure Backup をファイル システム整合性/クラッシュ整合性バックアップにフォールバックする場合は、この値を **true** に設定します。 **false** に設定すると、スクリプト エラーの発生時にバックアップは失敗します (ただし、この設定に関係なく、クラッシュ整合バックアップにフォールバックする単一ディスク VM の場合を除く)。

    - **fsFreezeEnabled**: VM スナップショットの作成中、ファイル システム レベルの整合性を確保するために Linux fsfreeze を呼び出すかどうかを指定します。 アプリケーションの動作上、fsfreeze を無効にしなければならない場合を除いて、この設定は **true** にしておくことをお勧めします。

6. 以上でスクリプト フレームワークの構成は完了です。 VM のバックアップが構成済みの場合、次回のバックアップではスクリプトが呼び出され、アプリケーション整合バックアップがトリガーされます。 VM バックアップの構成が済んでいない場合は、「[Recovery Services コンテナーへの Azure 仮想マシンのバックアップ](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)」を参照して必要な構成を行ってください。

## <a name="troubleshooting"></a>トラブルシューティング

事前スクリプトまたは事後スクリプトを作成する際は、必ず適切なログ機構を追加してください。スクリプトに問題があれば、そのログを調べて修正します。 それでもスクリプトの実行中に問題が発生する場合は、さらに詳しい情報を以下の表で確認してください。

| エラー | エラー メッセージ | 推奨される操作 |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |Pre-Script returned an error so backup may not be application consistent. (事前スクリプトからエラーが返されました。バックアップのアプリケーション整合性は確保されていない可能性があります。)   | スクリプトのエラー ログを参照して問題を解決してください。|  
|   Post-ScriptExecutionFailed |    The post-script returned an error that might impact application state. (アプリケーションの状態に影響する可能性のあるエラーが事後スクリプトから返されました。) |    スクリプトのエラー ログを参照して問題を解決し、アプリケーションの状態をチェックしてください。 |
| Pre-ScriptNotFound |  The pre-script was not found at the location that's specified in the **VMSnapshotScriptPluginConfig.json** config file. (VMSnapshotScriptPluginConfig.json 構成ファイルで指定された場所に事前スクリプトが見つかりませんでした。) |   バックアップのアプリケーション整合性を確保するためには、構成ファイルで指定されているパスに事前スクリプトが存在することを確認してください。|
| Post-ScriptNotFound | The post-script wasn't found at the location that's specified in the **VMSnapshotScriptPluginConfig.json** config file. (VMSnapshotScriptPluginConfig.json 構成ファイルで指定された場所に事後スクリプトが見つかりませんでした。) |   バックアップのアプリケーション整合性を確保するためには、構成ファイルで指定されているパスに事後スクリプトが存在することを確認してください。|
| IncorrectPluginhostFile | The **Pluginhost** file, which comes with the VmSnapshotLinux extension, is corrupted, so pre-script and post-script cannot run and the backup won't be application-consistent. (VmSnapshotLinux 拡張機能に付属の Pluginhost ファイルが壊れているため、事前スクリプトと事後スクリプトを実行できません。バックアップのアプリケーション整合性は確保されません。) | **VmSnapshotLinux** 拡張機能をアンインストールしてください。次回のバックアップで自動的に再インストールされて問題が解決します。 |
| IncorrectJSONConfigFile | The **VMSnapshotScriptPluginConfig.json** file is incorrect, so pre-script and post-script cannot run and the backup won't be application-consistent. (VMSnapshotScriptPluginConfig.json ファイルが正しくないため、事前スクリプトと事後スクリプトを実行できません。バックアップのアプリケーション整合性は確保されません。) | [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) からコピーをダウンロードして再度構成してください。 |
| InsufficientPermissionforPre-Script | For running scripts, "root" user should be the owner of the file and the file should have “700” permissions (that is, only "owner" should have “read”, “write”, and “execute” permissions). (スクリプトを実行するためには、root ユーザーがファイルの所有者であること、またファイルに "700" アクセス許可が設定されていることが必要です。つまり所有者だけが、"読み取り"、"書き込み"、"実行" のアクセス許可を持っている必要があります。) | "root" ユーザーがスクリプト ファイルの "所有者" であること、また所有者にのみ "読み取り"、"書き込み"、"実行" のアクセス許可が付与されていることを確認してください。 |
| InsufficientPermissionforPost-Script | For running scripts, root user should be the owner of the file and the file should have “700” permissions (that is, only "owner" should have “read”, “write”, and “execute” permissions). (スクリプトを実行するためには、root ユーザーがファイルの所有者であること、またファイルに "700" アクセス許可が設定されていることが必要です。つまり所有者だけが、"読み取り"、"書き込み"、"実行" のアクセス許可を持っている必要があります。) | "root" ユーザーがスクリプト ファイルの "所有者" であること、また所有者にのみ "読み取り"、"書き込み"、"実行" のアクセス許可が付与されていることを確認してください。 |
| Pre-ScriptTimeout | The execution of the application-consistent backup pre-script timed-out. (アプリケーション整合性バックアップの事前スクリプトの実行がタイムアウトしました。) | スクリプトをチェックし、**/etc/azure** にある **VMSnapshotScriptPluginConfig.json** ファイルでタイムアウト値を増やしてください。 |
| Post-ScriptTimeout | The execution of the application-consistent backup post-script timed out. (アプリケーション整合性バックアップの事後スクリプトの実行がタイムアウトしました。) | スクリプトをチェックし、**/etc/azure** にある **VMSnapshotScriptPluginConfig.json** ファイルでタイムアウト値を増やしてください。 |

## <a name="next-steps"></a>次のステップ
[Recovery Services コンテナーへの VM バックアップの構成](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)

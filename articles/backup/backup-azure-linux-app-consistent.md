---
title: 'Azure Backup: Linux VM のアプリケーション整合性バックアップ'
description: Linux 仮想マシンのアプリケーション整合性バックアップを Azure に作成します。 この記事では、Azure にデプロイされた Linux VM をバックアップするためのスクリプト フレームワークの構成について説明します。 この記事にはトラブルシューティング情報も含まれています。
services: backup
author: anuragmehrotra
manager: shivamg
keywords: アプリケーション整合性バックアップ; アプリケーション整合性 Azure VM バックアップ; Linux VM バックアップ; Azure Backup
ms.service: backup
ms.topic: conceptual
ms.date: 1/12/2018
ms.author: anuragm
ms.openlocfilehash: a81c0b9c87db85771fcecab87c6b9ac88dcbd472
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53581858"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Azure Linux VM のアプリケーション整合性バックアップ

VM のバックアップ スナップショットを作成すると、アプリケーション整合性により、復元後の VM ブート時にアプリケーションが開始されます。 ご想像どおり、アプリケーション整合性はきわめて重要です。 Linux VM にアプリケーション整合性を持たせるために、Linux 事前/事後スクリプト フレームワークを使用して、アプリケーション整合性バックアップを行うことができます。 事前/事後スクリプト フレームワークは、Azure Resource Manager でデプロイされた Linux 仮想マシンをサポートしています。 アプリケーション整合性のスクリプトは、Service Manager でデプロイされた仮想マシンも Windows 仮想マシンもサポートしていません。

## <a name="how-the-framework-works"></a>フレームワークの動作

このフレームワークは、VM スナップショットの作成中にカスタム事前/事後スクリプトを実行する機能が備わっています。 事前スクリプトは VM スナップショットの作成直前に実行され、事後スクリプトは VM スナップショットの作成直後に実行されます。 事前/事後スクリプトにより、VM スナップショットの作成中にアプリケーションと環境を柔軟に制御することができます。

事前スクリプトは、ネイティブ アプリケーション API を呼び出します。これにより IO が休止し、メモリ内のコンテンツがディスクにフラッシュされます。 これらのアクションにより、スナップショットのアプリケーション整合性が確保されます。 事後スクリプトは、ネイティブ アプリケーション API を使用して IO を解放します。これにより、VM スナップショット後にアプリケーションは通常操作を再開できます。

## <a name="steps-to-configure-pre-script-and-post-script"></a>事前スクリプトと事後スクリプトを構成する手順

1. バックアップを作成する Linux VM にルート ユーザーとしてサインインします。

2. [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) から、**VMSnapshotScriptPluginConfig.json** をダウンロードし、バックアップするすべての VM の **/etc/azure** フォルダーにコピーします。 **/etc/azure** フォルダーが存在しない場合は、作成します。

3. バックアップする予定のすべての VM にアプリケーションの事前/事後スクリプトをコピーします。 スクリプトは、VM の任意の場所にコピーできます。 必ず **VMSnapshotScriptPluginConfig.json** に記載されているスクリプト ファイルの完全パスを更新してください。

4. ファイルのアクセス許可が次のようになっていることを確認します。

   - **VMSnapshotScriptPluginConfig.json**:アクセス許可 "600"。 たとえば、"root" ユーザーにのみ、このファイルに対する "読み取り" アクセス許可と "書き込み" アクセス許可を付与します。どのユーザーにも "実行" アクセス許可は付与しません。

   - **事前スクリプト ファイル**:アクセス許可 "700"。  たとえば、"root" ユーザーにのみ、このファイルに対する "読み取り"、"書き込み"、"実行" のすべてのアクセス許可を付与します。

   - **事後スクリプト ファイル**: アクセス許可 "700" を指定します。 たとえば、"root" ユーザーにのみ、このファイルに対する "読み取り"、"書き込み"、"実行" のすべてのアクセス許可を付与します。

   > [!Important]
   > このフレームワークを使用すると、ユーザーはさまざまな機能を利用できるようになります。 フレームワークを保護し、“root” ユーザーのみが重要な JSON ファイルおよびスクリプト ファイルにアクセスできるようにしてください。
   > 要件が満たされない場合、スクリプトは実行されず、その結果ファイル システムのクラッシュやバックアップの不整合が発生します。
   >

5. **VMSnapshotScriptPluginConfig.json** を以下のように構成します。
    - **pluginName**:このフィールドは変更しないでください。変更するとスクリプトが正しく動作しなくなることがあります。

    - **preScriptLocation**:バックアップする VM 上の事前スクリプトの完全なパスを指定します。

    - **postScriptLocation**:バックアップする VM 上の事後スクリプトの完全なパスを指定します。

    - **preScriptParams**:事前スクリプトに渡す必要があるパラメーターを指定します (省略可能)。 パラメーターはすべて引用符で囲んでください。 複数のパラメーターを使用する場合は、パラメーターをコンマで区切ります。

    - **postScriptParams**:事後スクリプトに渡す必要があるパラメーターを指定します (省略可能)。 パラメーターはすべて引用符で囲んでください。 複数のパラメーターを使用する場合は、パラメーターをコンマで区切ります。

    - **preScriptNoOfRetries**:事前スクリプトでエラーが発生した場合の再試行回数を指定します。 0 (ゼロ) を指定すると、試行回数は 1 回のみでエラーが発生しても再試行は実行されません。

    - **postScriptNoOfRetries**:事後スクリプトでエラーが発生した場合の再試行回数を指定します。 0 (ゼロ) を指定すると、試行回数は 1 回のみでエラーが発生しても再試行は実行されません。

    - **timeoutInSeconds**:事前スクリプトと事後スクリプトの個々のタイムアウトを指定します (最大値は 1800 です)。

    - **continueBackupOnFailure**:事前スクリプトまたは事後スクリプトでエラーが発生した場合に Azure Backup をファイル システム整合性/クラッシュ整合性バックアップにフォールバックするには、この値を **true** に設定します。 **false** に設定すると、スクリプト エラーの発生時にバックアップは失敗します (ただし、この設定に関係なく、クラッシュ整合バックアップにフォールバックする単一ディスク VM の場合を除く)。

    - **fsFreezeEnabled**:VM スナップショットの作成中、ファイル システム レベルの整合性を確保するために Linux fsfreeze を呼び出すかどうかを指定します。 アプリケーションの動作上、fsfreeze を無効にしなければならない場合を除いて、この設定は **true** にしておくことをお勧めします。

6. 以上でスクリプト フレームワークの構成は完了です。 VM のバックアップが構成済みの場合、次回のバックアップではスクリプトが呼び出され、アプリケーション整合バックアップがトリガーされます。 VM バックアップの構成が済んでいない場合は、「[Recovery Services コンテナーへの Azure 仮想マシンのバックアップ](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)」を参照して必要な構成を行ってください。

## <a name="troubleshooting"></a>トラブルシューティング

事前スクリプトまたは事後スクリプトを作成する際は、必ず適切なログ機構を追加してください。スクリプトに問題があれば、そのログを調べて修正します。 それでもスクリプトの実行中に問題が発生する場合は、さらに詳しい情報を以下の表で確認してください。

| Error | エラー メッセージ | 推奨される操作 |
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

## <a name="next-steps"></a>次の手順
[Recovery Services コンテナーへの VM バックアップの構成](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)

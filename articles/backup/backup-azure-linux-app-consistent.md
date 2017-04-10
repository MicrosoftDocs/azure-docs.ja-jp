---
title: "Azure Backup: Azure Linux VM のアプリケーション整合性バックアップ | Microsoft Docs"
description: "Azure Linux VM のアプリケーション整合性バックアップ"
services: backup
documentationcenter: dev-center-name
author: anuragm
manager: shivamg
keywords: "アプリケーション整合性バックアップ; アプリケーション整合性 Azure VM バックアップ; Linux VM バックアップ; Azure Backup"
ms.assetid: bbb99cf2-d8c7-4b3d-8b29-eadc0fed3bef
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 3/20/2017
ms.author: anuragm;markgal
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 044b5d3834518b44209485d1c1a68f2ac0f8a455
ms.lasthandoff: 03/22/2017


---
# <a name="application-consistent-backup-of-azure-linux-vms-preview"></a>Azure Linux VM のアプリケーション整合性バックアップ (プレビュー)

この記事では、Linux の事前/事後スクリプト フレームワークと、それらを使用して Azure Linux VM のアプリケーション整合性バックアップを作成する方法について説明します。

> [!Note]
> 事前/事後スクリプト フレームワークは、Resource Manager でデプロイされた Linux 仮想マシンでのみサポートされます。クラシック仮想マシンまたは Windows 仮想マシンではサポートされません。
>

## <a name="how-the-framework-works"></a>フレームワークの動作

このフレームワークは、VM スナップショットの作成中にカスタム事前/事後スクリプトを実行する機能が備わっています。 事前スクリプトは VM スナップショットの作成前に実行され、事後スクリプトは VM スナップショットの完成直後に実行されます。 この機能を利用することで、VM バックアップの作成中にアプリケーションと環境を柔軟に制御することができます。

このフレームワークの注目すべき使い方として、VM バックアップのアプリケーション整合性を確保することが挙げられます。 事前スクリプトからアプリケーションのネイティブ API を呼び出し、IO を休止してメモリの内容をディスクにフラッシュすることで、スナップショットのアプリケーション整合性を確保することができます。つまり、復元後に VM を起動するとそのアプリケーションが画面に表示されます。 VM スナップショットの作成後にアプリケーションが通常の動作を再開できるように、事後スクリプトからアプリケーションのネイティブ API を使って IO を解放することができます。

## <a name="steps-to-configure-pre-script-and-post-script"></a>事前スクリプトと事後スクリプトを構成する手順

1. バックアップする Linux VM に root ユーザーとしてログインします。

2. VMSnapshotPluginConfig.json を [github](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) からダウンロードし、バックアップするすべての VM の /etc/azure フォルダーにコピーします。 /etc/azure ディレクトリを作成します (まだ存在しない場合)。

3. バックアップするすべての VM にアプリケーションの事前スクリプトと事後スクリプトをコピーします。 スクリプトは VM 内のどこにコピーしてもかまいませんが、VMSnapshotPluginConfig.json ファイルのスクリプト ファイルの完全パスを更新する必要があります。

4. ファイルのアクセス許可が次のようになっていることを確認します。

   - VMSnapshotPluginConfig.json: "600" アクセス許可を指定します。つまり "root" ユーザーにのみ、このファイルに対する "読み取り" アクセス許可と "書き込み" アクセス許可を付与します。どのユーザーにも "実行" アクセス許可は付与しません。
   - 事前スクリプト ファイル: "700" アクセス許可を指定します。つまり "root" ユーザーにのみ、このファイルに対する "読み取り"、"書き込み"、"実行" のすべてのアクセス許可を付与します。
   - 事後スクリプト ファイル: "700" アクセス許可を指定します。つまり "root" ユーザーにのみ、このファイルに対する "読み取り"、"書き込み"、"実行" のすべてのアクセス許可を付与します。
   
   > [!Note]
   > このフレームワークがユーザーに与える影響は非常に大きいので、セキュリティを確実に確保することがきわめて重要となります。"root" 以外のユーザーには、重要な json ファイルとスクリプト ファイルに対するアクセス権を決して付与しないでください。
   > 前述の要件が満たされていない場合、スクリプトは実行されず、ファイル システム整合性/クラッシュ整合性バックアップになります。
   >
   
5. VMSnapshotPluginConfig.json を以下のように構成します。
    - **pluginName**: このフィールドは変更しないでください。変更するとスクリプトが正しく動作しなくなることがあります。
    - **preScriptLocation**: バックアップする VM 上の事前スクリプトの完全パスを指定します。
    - **postScriptLocation**: バックアップする VM 上の事後スクリプトの完全パスを指定します。
    - **preScriptParams**: 事前スクリプトに渡すオプション パラメーターを指定します。複数のパラメーターがある場合は、個々のパラメーターを引用符で囲み、コンマ区切りで指定してください。
    - **postScriptParams**: 事後スクリプトに渡すオプション パラメーターを指定します。複数のパラメーターがある場合は、個々のパラメーターを引用符で囲み、コンマ区切りで指定してください。
    - **preScriptNoOfRetries**: 事前スクリプトでエラーが発生した場合の再試行回数を指定します。 "0" は、1 回だけ試行して、エラーが発生した場合は再試行しない、という意味になります。
    - **postScriptNoOfRetries**: 事後スクリプトでエラーが発生した場合の再試行回数を指定します。 "0" は、1 回だけ試行して、エラーが発生した場合は再試行しない、という意味になります。
    - **timeoutInSeconds**: 事前スクリプトと事後スクリプトのタイムアウトを指定します。
    - **continueBackupOnFailure**: 事前スクリプトまたは事後スクリプトでエラーが発生した場合、Azure Backup をファイル システム整合性/クラッシュ整合性バックアップにフォールバックする場合は、この値を true に設定します。 これを false に設定した場合は、スクリプト エラーが発生するとバックアップが失敗します (ただしシングル ディスク VM の場合は、この設定に関係なくクラッシュ整合性バックアップにフォールバックします)。
    - **fsFreezeEnabled**: VM スナップショットの作成中、ファイル システム レベルの整合性を確保するために Linux fsfreeze を呼び出すかどうかを指定します。 アプリケーションの動作上、fsfreeze を無効にしなければならない場合を除いて、この設定は true にしておくことをお勧めします。
    
6. スクリプト フレームワークの構成はこれで完了です。VM バックアップの構成が済んでいれば、次回のバックアップ時にスクリプトが呼び出され、アプリケーション整合性バックアップがトリガーされます。 VM バックアップの構成が済んでいない場合は、「[Recovery Services コンテナーへの Azure 仮想マシンのバックアップ](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)」を参照して必要な構成を行ってください。

## <a name="troubleshooting"></a>トラブルシューティング

事前スクリプトまたは事後スクリプトを作成する際は、必ず適切なログ機構を追加してください。スクリプトに問題があれば、そのログを調べて修正します。 それでもスクリプトの実行中に問題が発生する場合は、さらに詳しい情報を以下の表で確認してください。

| エラー | エラー メッセージ | 推奨される操作 |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |Pre-Script returned an error so backup may not be application consistent. (事前スクリプトからエラーが返されました。バックアップのアプリケーション整合性は確保されていない可能性があります。)    | スクリプトのエラー ログを参照して問題を解決してください。|  
|    Post-ScriptExecutionFailed |    Post-Script returned an error which might impact application state. (アプリケーションの状態に影響する可能性のあるエラーが事後スクリプトから返されました。) |    スクリプトのエラー ログを参照して問題を解決し、アプリケーションの状態をチェックしてください。 |
| Pre-ScriptNotFound |    Pre-Script was not found at the location specified in the VMSnapshotPluginConfig.json config file. (VMSnapshotPluginConfig.json 構成ファイルで指定されている場所に事前スクリプトが見つかりませんでした。) |    バックアップのアプリケーション整合性を確保するためには、構成ファイルで指定されているパスに事前スクリプトが存在することを確認してください。|
| Post-ScriptNotFound |    Post-Script was not found at the location specified in VMSnapshotPluginConfig.json config file (VMSnapshotPluginConfig.json 構成ファイルで指定されている場所に事後スクリプトが見つかりませんでした) |    バックアップのアプリケーション整合性を確保するためには、構成ファイルで指定されているパスに事後スクリプトが存在することを確認してください。|
| IncorrectPluginhostFile |    Pluginhost file which comes with the VmSnapshotLinux extension is corrupted so pre-script and post-script cannot be executed and the backup will not be application consistent. (VmSnapshotLinux 拡張機能に付属の Pluginhost ファイルが壊れているため、事前スクリプトと事後スクリプトを実行できません。バックアップのアプリケーション整合性は確保されません。)    | VmSnapshotLinux 拡張機能をアンインストールしてください。次回のバックアップで自動的に再インストールされて問題が解決します。 |
| IncorrectJSONConfigFile | VMSnapshotPluginConfig.json file is incorrect, so pre-script and post-script cannot be executed and the backup will not be application consistent (VMSnapshotPluginConfig.json ファイルが正しくないため、事前スクリプトと事後スクリプトを実行できません。バックアップのアプリケーション整合性は確保されません) | [github](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) からコピーをダウンロードして再度構成してください。 |
| InsufficientPermissionforPre-Script | For executing scripts, root user should be the owner of the file and file should have “700” permissions i.e. only owner should have “read”, “write”, and “execute” permissions (スクリプトを実行するためには、root ユーザーがファイルの所有者であること、またファイルに "700" アクセス許可が設定されていることが必要です。つまり所有者だけが、"読み取り"、"書き込み"、"実行" のアクセス許可を持っている必要があります) | "root" ユーザーがスクリプト ファイルの "所有者" であること、また所有者にのみ "読み取り"、"書き込み"、"実行" のアクセス許可が付与されていることを確認してください。 |
| InsufficientPermissionforPost-Script | For executing scripts, root user should be the owner of the file and file should have “700” permissions i.e. only owner should have “read”, “write”, and “execute” permissions (スクリプトを実行するためには、root ユーザーがファイルの所有者であること、またファイルに "700" アクセス許可が設定されていることが必要です。つまり所有者だけが、"読み取り"、"書き込み"、"実行" のアクセス許可を持っている必要があります) | "root" ユーザーがスクリプト ファイルの "所有者" であること、また所有者にのみ "読み取り"、"書き込み"、"実行" のアクセス許可が付与されていることを確認してください。 |
| Pre-ScriptTimeout | Execution of Application Consistent Backup Pre-Script timed-out. (アプリケーション整合性バックアップの事前スクリプトの実行がタイムアウトしました。) | スクリプトをチェックし、/etc/azure にある VMSnapshotPluginConfig.json ファイルでタイムアウト値を増やしてください。 |
| Post-ScriptTimeout | Execution of Application Consistent Backup Post-Script timed-out. (アプリケーション整合性バックアップの事後スクリプトの実行がタイムアウトしました。) | スクリプトをチェックし、/etc/azure にある VMSnapshotPluginConfig.json ファイルでタイムアウト値を増やしてください。 |

## <a name="next-steps"></a>次のステップ
[Recovery Services コンテナーへの VM バックアップの構成](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)


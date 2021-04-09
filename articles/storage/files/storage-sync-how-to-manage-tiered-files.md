---
title: Azure File Sync の階層化ファイルの管理方法 | Microsoft Docs
description: 階層化ファイルを管理するために役立つヒントと PowerShell コマンドレット
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dc61792da669cd5d2c928eec0fd412d86725fc8c
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204247"
---
# <a name="how-to-manage-tiered-files"></a>階層化ファイルの管理方法

この記事では、階層化ファイルの管理に関する質問があるユーザー向けのガイダンスを提供します。 クラウドを使った階層化の概念に関する質問については、[Azure Files の FAQ](storage-files-faq.md) に関するページを参照してください。

## <a name="how-to-check-if-your-files-are-being-tiered"></a>ファイルが階層化されているかどうかを確認する方法

セット ポリシーごとにファイルを階層化する必要があるかどうかは、1 時間に 1 回評価されます。 新しいサーバー エンドポイントの作成時には、次の 2 つの状況が考えられます。

新しいサーバー エンドポイントを初めて追加するとき、多くの場合、そのサーバーの場所にはファイルが存在します。 これらは、クラウドを使った階層化を開始する前にアップロードしておく必要があります。 ボリュームの空き領域ポリシーは、すべてのファイルの初期アップロードが完了するまで動作しません。 ただし、オプションの日付ポリシーは、ファイルがアップロードされ次第、個々のファイルに対して動作し始めます。 この場合も 1 時間間隔が適用されます。 

新しいサーバー エンドポイントを追加するとき、空のサーバーの場所を、データが含まれている Azure ファイル共有に接続することができます。 サーバーへの初回ダウンロード時に名前空間をダウンロードし、コンテンツを呼び出すよう選択した場合は、名前空間がダウンロードされた後、ボリュームの空き領域ポリシーとオプションの日付ポリシーの制限に達するまで、最終更新タイムスタンプに基づいてファイルが再現されます。

ファイルが Azure ファイル共有に階層化されたかどうかを確認するには、いくつかの方法があります。
    
   *  **ファイル上でファイル属性を確認します。**
     ファイルを右クリックして **[詳細]** に移動し、 **[属性]** プロパティまで下へスクロールします。 階層化されたファイルには、次のような属性設定があります。     
        
        | 属性の文字 | 属性 | 定義 |
        |:----------------:|-----------|------------|
        | A | アーカイブ | バックアップ ソフトウェアによって、ファイルがバックアップされる必要があることを示します。 この属性は、ファイルが階層化されているか、ディスク上に完全に格納されているかに関係なく、常に設定されます。 |
        | P | スパース ファイル | ファイルがスパース ファイルであることを示します。 スパース ファイルとは、ディスク ストリーム上のファイルがほぼ空である場合に、効率的に使用するために NTFS が提供している特別な種類のファイルです。 ファイルは完全に階層化されているか、部分的に再現されているため、Azure File Sync ではスパース ファイルが使用されます。 完全に階層化されたファイルでは、ファイル ストリームがクラウドに格納されます。 部分的に再現されているファイルでは、ファイルの一部が既にディスク上に存在します。 これは、マルチメディア プレーヤーや圧縮ユーティリティなどのアプリケーションによってファイルが部分的に読み取られた場合に、発生する可能性があります。 ファイルがディスク上に完全に再現されている場合、Azure File Sync では、ファイルはスパース ファイルから通常のファイルに変換されます。 この属性は Windows Server 2016 以前でのみ設定されます。|
        | M | データ アクセス時に再現 | ファイルのデータがローカル ストレージ上に完全には存在しないことを示します。 ファイルを読み取ると、サーバー エンドポイントが接続されている Azure ファイル共有から、ファイルの内容の少なくとも一部がフェッチされます。 この属性は Windows Server 2019 でのみ設定されます。 |
        | L | 再解析ポイント | ファイルが再解析ポイントを保持していることを示します。 再解析ポイントは、ファイル システム フィルターによって使用される特殊なポインターです。 Azure File Sync では、ファイルが格納されるクラウドの場所を Azure File Sync のファイル システム フィルター (StorageSync.sys) に対して定義するために再解析ポイントを使用します。 これにより、シームレスなアクセスが可能になります。 Azure File Sync が使用されていることや、Azure ファイル共有に格納されているファイルへのアクセス方法をユーザーが知る必要はありません。 ファイルが完全に再現されている場合、Azure File Sync によって、そのファイルから再解析ポイントが削除されます。 |
        | O | オフライン | ファイルのコンテンツの一部またはすべてがディスク上に保存されていないことを示します。 ファイルが完全に再現されている場合、Azure File Sync によってこの属性は削除されます。 |

        ![[詳細] タブが選択された、ファイルの [プロパティ] ダイアログ ボックス](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > また、**属性** フィールドをエクスプローラーのテーブル表示に追加することで、フォルダー内にあるすべてのファイルの属性を確認できます。 これを行うには、既存の列 ( **[サイズ]** など) を右クリックして、 **[詳細]** を選択し、ドロップダウン リストから **[属性]** を選択します。
        
        > [!NOTE]
        > これらの属性はすべて、部分的に再現されたファイルにも表示されます。
        
   * **`fsutil` を使用して、ファイル上の再解析ポイントを確認します。**
       前記のオプションで説明したように、階層化されたファイルには必ず再解析ポイントが設定されます。 再解析ポイントを使用すると、Azure File Sync ファイル システム フィルター ドライバー (StorageSync.sys) によって、サーバーにローカルで格納されていないコンテンツを Azure ファイル共有から取得できるようになります。 

       ファイルに再解析ポイントがあるかどうかを調べるには、管理者特権でのコマンド プロンプトまたは PowerShell ウィンドウで、`fsutil` ユーティリティを実行します。

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       ファイルに再解析ポイントがある場合、"**Reparse Tag Value: 0x8000001e**" と表示されることが想定されます。 この 16 進値は、Azure File Sync が所有する再解析ポイントの値です。また、出力には、Azure ファイル共有上のファイルへのパスを表す再解析データが含まれます。
        
        > [!WARNING]  
        > `fsutil reparsepoint` ユーティリティ コマンドには、再解析ポイントを削除する機能も含まれています。 Azure File Sync のエンジニア チームによって指示されない限り、このコマンドは実行しないでください。 このコマンドを実行すると、データが失われる可能性があります。 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>クラウドを使った階層化の最終アクセス時刻の追跡からアプリケーションを除外する方法

Azure File Sync エージェント バージョン 11.1 では、最終アクセスの追跡からアプリケーションを除外できるようになりました。 アプリケーションからファイルへアクセスが行われると、ファイルの最終アクセス時刻がクラウドを使った階層化データベースで更新されます。 ファイル システムをスキャンするウイルス対策アプリケーションなどを使用すると、すべてのファイルの最終アクセス時刻が同じになるため、ファイルが階層化された時間に影響を及ぼします。

最終アクセス時刻の追跡からアプリケーションを除外するには、プロセス名を HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync の下にある HeatTrackingProcessNameExclusionList レジストリ設定に追加します。

例: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

> [!NOTE]
> データ重複除去と File Server Resource Manager (FSRM) のプロセスは既定で除外されています。 プロセスの除外リストは 5 分ごとに受け入れられます。

## <a name="how-to-access-the-heat-store"></a>ヒート ストアにアクセスする方法

クラウドを使った階層化では、ファイルへの最終アクセス時刻とアクセス頻度を使用して、どのファイルを階層化する必要があるかを判断します。 クラウド階層化フィルター ドライバー (storagesync.sys) によって、最後のアクセス時刻の追跡とクラウド階層化ヒート ストアへの情報の記録が行われます。 サーバーローカルの PowerShell コマンドレットを使用して、ヒート ストアを取得し、これを CSV ファイルに保存することができます。

同じボリューム上のすべてのファイルに対して、1 つのヒート ストアが存在します。 ヒート ストアは、場合によっては非常に大きくなります。 最もアクセス数が少ない数の項目だけを取得する必要がある場合は、-Limit と数値を使って、ボリューム ルートではなくサブ パスでフィルタリングすることも検討してください。

- PowerShell モジュールをインポートします:   `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- ボリュームの空き領域: ボリュームの空き領域ポリシーを使用して、ファイルが階層化される順序を取得する場合:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- 日付ポリシー: 日付ポリシーを使用して、ファイルが階層化される順序を取得する場合:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- 特定のファイルのヒート ストア情報を見つける:   `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- 最終アクセス時刻の降順ですべてのファイルを表示する:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- バックグラウンドでの再現または PowerShell を使用したオンデマンドでの再現によって階層化ファイルを再現する際の順序を表示する:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>ファイルまたはディレクトリを強制的に階層化する方法

> [!NOTE]
> 階層化するディレクトリを選択すると、そのディレクトリ内の現在のファイルのみが階層化されます。 この時間より後に作成されたファイルは、自動的に階層化されません。

クラウドの階層化機能が有効な場合は、クラウド エンドポイントに指定されたボリューム空き領域の割合を達成するために、最終アクセス時刻と最終変更時刻に基づいてファイルが自動的に階層化されます。 ただし、手動で強制的にファイルを階層化する必要がある場合もあります。 長期間再使用する予定がない大きなファイルを保存して、当面ボリューム上に他のファイルとフォルダーのための領域を空けておきたい場合、手動による階層化が役立つ可能性があります。 次の PowerShell コマンドを使って、強制的に階層化できます。

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>階層化ファイルをディスクに再現する方法

ディスクにファイルを再現する最も簡単な方法は、ファイルを開くことです。 Azure File Sync のファイル システム フィルター (StorageSync.sys) は、Azure ファイル共有からファイルをシームレスにダウンロードします。ユーザー側で作業を行う必要はありません。 マルチメディア ファイルや .zip ファイルなど、部分的に読み取りまたはストリーミングできるファイルの種類の場合、単にファイルを開いただけでは、ファイル全体が確実にダウンロードされたかどうかは判断できません。

ファイルがローカル ディスクに完全にダウンロードされたかどうかを確認するには、PowerShell を使用して、強制的にファイルを完全に再現する必要があります。 複数のファイル (フォルダー内にあるすべてのファイルなど) を一度に再現したい場合にも、この方法が役立つことがあります。 Azure File Sync がインストールされているサーバー ノードへの PowerShell セッションを開き、次の PowerShell コマンドを実行します。
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
省略可能なパラメーター:
* `-Order CloudTieringPolicy` では、最後に変更またはアクセスされたファイルが最初に呼び出され、現在の階層化ポリシーによって許可されます。 
    * ボリュームの空き領域ポリシーが構成されている場合、ボリュームの空き領域ポリシー設定に達するまで、ファイルが呼び出されます。 たとえば、ボリュームの空き領域ポリシー設定が 20% の場合、ボリュームの空き領域が 20% に達すると、呼び出しは停止されます。  
    * ボリュームの空き領域と日付のポリシーが構成されている場合、ボリュームの空き領域または日付のポリシー設定に達するまで、ファイルが呼び出されます。 たとえば、ボリュームの空き領域ポリシー設定が 20% で、日付ポリシーが 7 日の場合、ボリュームの空き領域が 20% に達するか、または 7 日以内にアクセスまたは変更されたすべてのファイルがローカルであれば、呼び出しは停止されます。
* `-ThreadCount` では、並行して呼び戻すことができるファイルの数を指定します。
* `-PerFileRetryCount` では、現在ブロックされているファイルの呼び戻しを試行する頻度を指定します。
* `-PerFileRetryDelaySeconds` では、再試行から呼び戻しまでの時間を秒単位で指定します。また、常に前のパラメーターと組み合わせて使用する必要があります。

例:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
>- サーバーをホストするローカル ボリュームに、階層化されたデータをすべて回収できる空き領域がない場合、`Invoke-StorageSyncFileRecall` コマンドレットは失敗します。  

> [!Note]
> 階層化されたファイルを再呼び出しするには、1 Mbps 以上のネットワーク帯域幅が必要です。 ネットワーク帯域幅が 1 Mbps 未満の場合、タイムアウト エラーでファイルの再呼び出しが失敗する可能性があります。

## <a name="next-steps"></a>次のステップ
* [Azure Files に関する FAQ](storage-files-faq.md)

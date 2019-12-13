---
title: Azure Import/Export ツールの設定 | Microsoft Docs
description: Azure Import/Export サービスのドライブの準備と修復用のツールを設定する方法について説明します。
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 01432ab68fc399f3e97eac2de5a7c356bef7078a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979038"
---
# <a name="setting-up-the-azure-importexport-tool"></a>Azure Import/Export ツールの設定

Microsoft Azure Import/Export ツールは、ドライブの準備と修復用のツールであり、Microsoft Azure Import/Export サービスで使用できます。 このツールは次の目的に使用できます。

* インポート ジョブを作成する前に、このツールを使用して、Azure データ センターに発送するハード ドライブにデータをコピーできます。
* インポート ジョブが完了したら、このツールを使用して、壊れている BLOB、不足している BLOB、または他の BLOB と競合している BLOB を修復できます。
* エクスポート ジョブが完了してドライブを受け取ったら、このツールを使用して、ドライブ上の破損しているファイルや見つからないファイルを修復できます。

## <a name="prerequisites"></a>前提条件

インポート ジョブ用の**ドライブを準備する**場合は、次の前提条件を満たす必要があります。

* アクティブな Azure サブスクリプションが必要です。
* インポートするファイルを保存するための十分な空き領域を持つストレージ アカウントがサブスクリプションに含まれている必要があります。
* ストレージ アカウント アクセス キーが少なくとも 1 つ必要です。
* Windows 7、Windows Server 2008 R2、またはそれ以降の新しい Windows オペレーティング システムがインストールされているコンピューター ("コピー用コンピューター") が必要です。
* .NET Framework 4 をコピー用コンピューターにインストールする必要があります。
* コピー用コンピューターで BitLocker を有効にする必要があります。
* コピー用マシンに接続されている 1 台以上の空の 2.5 インチまたは 3.5 インチの SATA II/III ハード ドライブまたは SSD ドライブが必要です。
* インポートするファイル (ネットワーク共有とローカル ハード ドライブのどちらにある場合でも) にコピー用コンピューターからアクセス可能であることが必要です。

部分的に失敗した**インポートを修復する**場合は、以下が必要です。

* コピー ログ ファイル
* ストレージ アカウント キー。

部分的に失敗した**エクスポートを修復する**場合は、以下が必要です。

* コピー ログ ファイル
* マニフェスト ファイル (オプション)
* ストレージ アカウント キー。

## <a name="installing-the-azure-importexport-tool"></a>Azure Import/Export ツールのインストール

まず、[Azure Import/Export ツールをダウンロード](https://www.microsoft.com/download/details.aspx?id=55280)し、コンピューター上のディレクトリ (`c:\WAImportExport` など) に展開します。

Azure Import/Export ツールは次のファイルで構成されます。

* dataset.csv
* driveset.csv
* hddid.dll
* Microsoft.Data.Services.Client.dll
* Microsoft.WindowsAzure.Storage.dll
* Microsoft.WindowsAzure.Storage.pdb
* Microsoft.WindowsAzure.Storage.xml
* WAImportExport.exe
* WAImportExport.exe.config
* WAImportExport.pdb
* WAImportExportCore.dll
* WAImportExportCore.pdb
* WAImportExportRepair.dll
* WAImportExportRepair.pdb

次に、**管理者モード**でコマンド プロンプト ウィンドウを開き、展開したファイルを含むディレクトリに移動します。

コマンドのヘルプを出力するには、パラメーターを指定せずにツール (`WAImportExport.exe`) を実行します。

```
WAImportExport, a client tool for Windows Azure Import/Export Service. Microsoft (c) 2013


Copy directories and/or files with a new copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>]
        [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]
        DataSet:<dataset.csv>

Add more drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>

Abort an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession

Resume an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession

List drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListDrives

List copy sessions:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListCopySessions

Repair a Drive:
    WAImportExport.exe RepairImport | RepairExport
        /r:<RepairFile> [/logdir:<LogDirectory>]
        [/d:<TargetDirectories>] [/bk:<BitLockerKey>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        [/CopyLogFile:<DriveCopyLogFile>] [/ManifestFile:<DriveManifestFile>]
        [/PathMapFile:<DrivePathMapFile>]

Preview an Export Job:
    WAImportExport.exe PreviewExport
        [/logdir:<LogDirectory>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        /ExportBlobListFile:<ExportBlobListFile> /DriveSize:<DriveSize>

Parameters:

    /j:<JournalFile>
        - Required. Path to the journal file. A journal file tracks a set of drives and
          records the progress in preparing these drives. The journal file must always
          be specified.
    /logdir:<LogDirectory>
        - Optional. The log directory. Verbose log files as well as some temporary
          files will be written to this directory. If not specified, current directory
          will be used as the log directory. The log directory can be specified only
          once for the same journal file.
    /id:<SessionId>
        - Optional. The session Id is used to identify a copy session. It is used to
          ensure accurate recovery of an interrupted copy session.
    /ResumeSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to resume the session.
    /AbortSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to abort the session.
    /sn:<StorageAccountName>
        - Required. Only applicable for RepairImport and RepairExport. The name of
          the storage account.
    /sk:<StorageAccountKey>
        - Required. The key of the storage account.
    /InitialDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of drives to prepare.
    /AdditionalDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of additional drives to be added.
    /r:<RepairFile>
        - Required. Only applicable for RepairImport and RepairExport.
          Path to the file for tracking repair progress. Each drive must have one
          and only one repair file.
    /d:<TargetDirectories>
        - Required. Only applicable for RepairImport and RepairExport.
          For RepairImport, one or more semicolon-separated directories to repair;
          For RepairExport, one directory to repair, e.g. root directory of the drive.
    /CopyLogFile:<DriveCopyLogFile>
        - Required. Only applicable for RepairImport and RepairExport. Path to the
          drive copy log file (verbose or error).
    /ManifestFile:<DriveManifestFile>
        - Required. Only applicable for RepairExport. Path to the drive manifest file.
    /PathMapFile:<DrivePathMapFile>
        - Optional. Only applicable for RepairImport. Path to the file containing
          mappings of file paths relative to the drive root to locations of actual files
          (tab-delimited). When first specified, it will be populated with file paths
          with empty targets, which means either they are not found in TargetDirectories,
          access denied, with invalid name, or they exist in multiple directories. The
          path map file can be manually edited to include the correct target paths and
          specified again for the tool to resolve the file paths correctly.
    /ExportBlobListFile:<ExportBlobListFile>
        - Required. Path to the XML file containing list of blob paths or blob path
          prefixes for the blobs to be exported. The file format is the same as the
          blob list blob format in the Put Job operation of the Import/Export Service
          REST API.
    /DriveSize:<DriveSize>
        - Required. Size of drives to be used for export. For example, 500GB, 1.5TB.
          Note: 1 GB = 1,000,000,000 bytes
                1 TB = 1,000,000,000,000 bytes
    /DataSet:<dataset.csv>
        - Required. A .csv file that contains a list of directories and/or a list files
          to be copied to target drives.

    /silentmode
        - Optional. If not specified, it will remind you the requirement of drives and
          need your confirmation to continue.

Examples:

    Copy a data set to a drive:
    WAImportExport.exe PrepImport
        /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GEL
        xmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /InitialDriveSet:driveset1.csv
        /DataSet:data.csv

    Copy another dataset to the same drive following the above command:
    WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#2 /DataSet:dataset2.csv

    Preview how many 1.5 TB drives are needed for an export job:
    WAImportExport.exe PreviewExport
        /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7K
        ysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\temp\myexportbloblist.xml
        /DriveSize:1.5TB

    Repair an finished import job:
    WAImportExport.exe RepairImport
        /r:9WM35C2V.rep /d:X:\ /bk:442926-020713-108086-436744-137335-435358-242242-2795
        98 /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94
        f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\temp\9WM35C2V_error.log
```

## <a name="next-steps"></a>次の手順

* [インポート ジョブ用のハード ドライブを準備する](../storage-import-export-tool-preparing-hard-drives-import.md)
* [エクスポート ジョブのドライブ使用率のプレビュー](../storage-import-export-tool-previewing-drive-usage-export-v1.md)
* [コピー ログ ファイルによるジョブの状態の確認](../storage-import-export-tool-reviewing-job-status-v1.md)
* [インポート ジョブの修復](../storage-import-export-tool-repairing-an-import-job-v1.md)
* [エクスポート ジョブの修復](../storage-import-export-tool-repairing-an-export-job-v1.md)
* [Azure Import/Export ツールのトラブルシューティング](storage-import-export-tool-troubleshooting-v1.md)

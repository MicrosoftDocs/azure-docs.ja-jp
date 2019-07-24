---
title: Azure Import/Export のインポート ジョブ用のハード ドライブを準備する | Microsoft Docs
description: WAImportExport ツールを使用して Azure Import/Export サービスのインポート ジョブを作成するためのハード ドライブを準備する方法について説明します。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 777e0aac46dbffb1e491874b5889667a888aadf5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57898688"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>インポート ジョブ用のハード ドライブを準備する

WAImportExport ツールは、ドライブの準備および修復用のツールであり、[Microsoft Azure Import/Export サービス](../storage-import-export-service.md)で使用できます。 このツールを使用して、Azure データ センターに発送するハード ドライブにデータをコピーできます。 インポート ジョブが完了したら、このツールを使用して、壊れている BLOB、不足している BLOB、または他の BLOB と競合している BLOB を修復できます。 エクスポート ジョブが完了してドライブを受け取ったら、このツールを使用して、ドライブ上の破損しているファイルや見つからないファイルを修復できます。 この記事では、このツールの使用方法について詳しく説明します。

## <a name="prerequisites"></a>前提条件

### <a name="requirements-for-waimportexportexe"></a>WAImportExport.exe の要件

- **コンピューターの構成**
  - Windows 7、Windows Server 2008 R2、またはそれ以降の新しい Windows オペレーティング システム
  - .NET Framework 4 をインストールする必要があります。 .NET Framework がマシンにインストールされているかどうかを確認する方法については、「[FAQ](#faq)」を参照してください。
- **ストレージ アカウント キー** - ストレージ アカウントのアカウント キーが少なくとも 1 つ必要です。

### <a name="preparing-disk-for-import-job"></a>インポート ジョブ用のディスクの準備

- **BitLocker** - WAImportExport ツールを実行するコンピューターで BitLocker を有効にする必要があります。 BitLocker を有効にする方法については、「[よくあるご質問 (FAQ)](#faq)」を参照してください。
- **ディスク** - WAImportExport ツールを実行するコンピューターからアクセスできるディスク。 ディスクの仕様については、「[FAQ](#faq)」を参照してください。
- **ソース ファイル** - インポートするファイル (ネットワーク共有とローカル ハード ドライブのどちらにある場合でも) にコピー用コンピューターからアクセス可能であることが必要です。

### <a name="repairing-a-partially-failed-import-job"></a>部分的に問題のあるインポート ジョブの修復

- **コピー ログ ファイル** - Azure Import/Export サービスがストレージ アカウントとディスク間でデータをコピーする場合に生成されます。 このファイルは、ターゲット ストレージ アカウントにあります。

### <a name="repairing-a-partially-failed-export-job"></a>部分的に問題のあるエクスポート ジョブの修復

- **コピー ログ ファイル** - Azure Import/Export サービスがストレージ アカウントとディスク間でデータをコピーする場合に生成されます。 このファイルは、ソース ストレージ アカウントにあります。
- **マニフェスト ファイル** - [省略可能] Microsoft から返却されたエクスポート済みのドライブにあります。

## <a name="download-and-install-waimportexport"></a>WAImportExport のダウンロードとインストール

[最新バージョンの WAImportExport.exe をダウンロード](https://www.microsoft.com/download/details.aspx?id=55280)してください。 使用するコンピューター上のディレクトリに zip の内容を展開します。

次のタスクでは、CSV ファイルを作成します。

## <a name="prepare-the-dataset-csv-file"></a>データセット CSV ファイルの準備

### <a name="what-is-dataset-csv"></a>データセット CSV とは

データセット CSV ファイルは、ターゲット ドライブにコピーするディレクトリの一覧またはファイルの一覧を含む CSV ファイルの /dataset フラグの値です。 インポート ジョブを作成するには、インポートするディレクトリとファイルを最初に決定します。 その際には、ディレクトリの一覧、一意のファイルの一覧、またはその 2 つの組み合わせを指定できます。 ディレクトリが含まれる場合は、そのディレクトリとサブディレクトリ内のすべてのファイルがインポート ジョブに追加されます。

インポートするディレクトリまたはファイルごとに、Azure Blob service でインポート先の仮想ディレクトリまたは BLOB を指定する必要があります。 これらのターゲットを WAImportExport ツールの入力値として使用します。 ディレクトリをスラッシュ (/) で区切ってください。

次の表は、BLOB ターゲットの例を示しています。

| ソースのファイルまたはディレクトリ | インポート先の BLOB または仮想ディレクトリ |
| --- | --- |
| H:\Video | https://mystorageaccount.blob.core.windows.net/video |
| H:\Photo | https://mystorageaccount.blob.core.windows.net/photo |
| K:\Temp\FavoriteVideo.ISO | https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO |
| \\myshare\john\music | https://mystorageaccount.blob.core.windows.net/music |

### <a name="sample-datasetcsv"></a>dataset.csv のサンプル

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
"F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
"F:\50M_original\","containername/",BlockBlob,rename,"None",None
```

### <a name="dataset-csv-file-fields"></a>データセット CSV ファイルのフィールド

| フィールド | 説明 |
| --- | --- |
| BasePath | **[必須]**<br/>このパラメーターの値は、インポートするデータが配置されるソースを表します。 ツールでは、このパスに配置されたすべてのデータを再帰的にコピーします。<br><br/>**使用可能な値**:ローカル コンピューター上の有効なパスまたは有効な共有パスを指定する必要があります。ユーザーがアクセスできるパスを指定してください。 ディレクトリ パスは絶対パス (相対パスではなく) にする必要があります。 パスが "\\" で終わる場合は、ディレクトリを表し、それ以外の "\\" なしで終わるパスは、ファイルを表します。<br/>このフィールドでは正規表現を使用できません。 パスにスペースが含まれる場合は、"" で囲んでください。<br><br/>**例**: "c:\Directory\c\Directory\File.txt"<br>"\\\\FBaseFilesharePath.domain.net\sharename\directory\"  |
| DstBlobPathOrPrefix | **[必須]**<br/> Microsoft Azure ストレージ アカウントのインポート先の仮想ディレクトリへのパス。 仮想ディレクトリが既に存在しているかどうかは、場合によって異なります。 仮想ディレクトリがない場合は、インポート/エクスポート サービスによって作成されます。<br/><br/>コピー先の仮想ディレクトリや BLOB を指定する場合は、有効なコンテナー名を使用してください。 コンテナー名は小文字にする必要があります。 コンテナーの名前付け規則については、「[コンテナー、BLOB、およびメタデータの名前付けおよび参照](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」をご覧ください。 ルートのみが指定されている場合、ソースのディレクトリ構造は、インポート先の BLOB コンテナーにレプリケートされます。 ソースのディレクトリ構造ではなく、別のディレクトリ構造が必要な場合は、CSV のマッピングを複数行にします。<br/><br/>コンテナーまたは BLOB プレフィックス (例: music/70s/) を指定できます。 インポート先のディレクトリの先頭はコンテナー名にして、その後にスラッシュ (/) を続けます。末尾が "/" の仮想 BLOB ディレクトリ (省略可能) を追加することもできます。<br/><br/>インポート先のコンテナーがルート コンテナーの場合は、スラッシュを含むルート コンテナーを明示的に指定する必要があります (例: $root/)。 ルート コンテナーにある BLOB の名前に "/" を含めることはできないため、インポート先のディレクトリがルート コンテナーの場合、ソース ディレクトリのサブディレクトリはコピーされません。<br/><br/>**例**<br/>コピー先 BLOB パスが https://mystorageaccount.blob.core.windows.net/video の場合、このフィールドの値は video/ となります。  |
| BlobType | **[省略可能]** block &#124; page<br/>現在、インポート/エクスポート サービスは 2 種類の BLOB をサポートしています  (ページ BLOB とブロック BLOB)。既定では、すべてのファイルがブロック BLOB としてインポートされます。 また、\*.vhd と \*.vhdx はページ BLOB としてインポートされます。ブロック BLOB と ページ BLOB に使用できるサイズには制限があります。 詳細については、[Azure Storage のスケーラビリティ ターゲット](storage-scalability-targets.md)に関するページを参照してください。  |
| Disposition | **[省略可能]** rename &#124; no-overwrite &#124; overwrite <br/> このフィールドは、インポート中 ( 例: データのディスクからストレージ アカウントへのアップロード中) のコピー動作を指定します。 利用可能なオプションは rename&#124;overwite&#124;no-overwrite です。何も指定しない場合は、既定値の "rename" になります。 <br/><br/>**rename**: 同名のオブジェクトが存在する場合に、インポート先にコピーを作成します。<br/>overwrite: ファイルを新しいファイルで上書きします。 更新日の最も新しいファイルで上書きされます。<br/>**no-overwrite**: 既存のファイルがある場合は、ファイルの書き込みをスキップします。|
| MetadataFile | **[省略可能]** <br/>このフィールドの値は、オブジェクトのメタデータを保持するか、またはカスタム メタデータを指定する必要がある場合に指定可能なメタデータ ファイルです。 インポート先の BLOB のメタデータ ファイルへのパスです。 詳細については、「[Import/Export サービスのメタデータとプロパティ ファイルの形式](../storage-import-export-file-format-metadata-and-properties.md)」を参照してください。 |
| PropertiesFile | **[省略可能]** <br/>インポート先の BLOB のプロパティ ファイルへのパスです。 詳細については、「[Import/Export サービスのメタデータとプロパティ ファイルの形式](../storage-import-export-file-format-metadata-and-properties.md)」を参照してください。 |

## <a name="prepare-initialdriveset-or-additionaldriveset-csv-file"></a>InitialDriveSet または AdditionalDriveSet CSV ファイルの準備

### <a name="what-is-driveset-csv"></a>ドライブセット CSV とは

/InitialDriveSet または /AdditionalDriveSet フラグの値は、ドライブ文字がマップされるディスクの一覧を含む CSV ファイルです。これにより、準備するディスクの一覧をツールで正しく選択できます。 データ サイズが 1 つのディスク サイズよりも大きい場合、WAImportExport ツールは最適化された方法でこの CSV ファイル内の複数のディスクにデータを分散します。

1 つのセッションでデータを書き込むことのできるディスクの数に制限はありません。 このツールはディスク サイズとフォルダー サイズに基づいてデータを分散します。 オブジェクト サイズ用に最も最適化されたディスクが選択されます。 ストレージ アカウントにアップロードされたデータは、データセット ファイルに指定されたディレクトリ構造に集約されます。 ドライブセット CSV を作成するには、次の手順に従ってください。

### <a name="create-basic-volume-and-assign-drive-letter"></a>ベーシック ボリュームの作成とドライブ文字の割り当て

ベーシック ボリュームを作成してドライブ文字を割り当てるには、「[Overview of Disk Management](https://technet.microsoft.com/library/cc754936.aspx)」(ディスク管理の概要) の「Simpler partition creation」(シンプルなパーティションの作成) の手順に従ってください。

### <a name="sample-initialdriveset-and-additionaldriveset-csv-file"></a>InitialDriveSet および AdditionalDriveSet CSV ファイルのサンプル

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
H,Format,SilentMode,Encrypt,
```

### <a name="driveset-csv-file-fields"></a>ドライブセット CSV ファイルのフィールド

| フィールド | 値 |
| --- | --- |
| DriveLetter | **[必須]**<br/> インポート先としてツールに指定する各ドライブには、シンプルな NTFS ボリュームとそのボリュームに割り当てるドライブ文字が必要です。<br/> <br/>**例**:R または r |
| FormatOption | **[必須]** Format &#124; AlreadyFormatted<br/><br/> **Format**: この値を指定すると、ディスク上のすべてのデータがフォーマットされます。 <br/>**AlreadyFormatted**: この値を指定すると、ツールはフォーマットをスキップします。 |
| SilentOrPromptOnFormat | **[必須]** SilentMode &#124; PromptOnFormat<br/><br/>**SilentMode**: この値を指定すると、ユーザーがサイレント モードでツールを実行できます。 <br/>**PromptOnFormat**:各フォーマットについて、それが本当に意図したアクションかどうかの確認を求められます。<br/><br/>値を設定しない場合は、コマンドが中止され、"Incorrect value for SilentOrPromptOnFormat: none" (SilentOrPromptOnFormat の値が正しくありません: 指定なし) というエラー メッセージが表示されます。 |
| 暗号化 | **[必須]** Encrypt &#124; AlreadyEncrypted<br/> このフィールドの値によって、暗号化するディスクとしないディスクが決定されます。 <br/><br/>**Encrypt**:ツールがドライブをフォーマットします。 "FormatOption" フィールドの値が "Format" の場合は、この値を "Encrypt" にする必要があります。 この場合に "AlreadyEncrypted" を指定すると、"When Format is specified, Encrypt must also be specified (Format を指定する場合は、Encrypt を指定する必要があります)" というエラーが表示されます。<br/>**AlreadyEncrypted**:"ExistingBitLockerKey" フィールドに指定された BitLockerKey を使用して、ツールがドライブの暗号化を解除します。 "FormatOption" フィールドの値が "AlreadyFormatted" の場合は、この値を "Encrypt" または "AlreadyEncrypted" にすることができます。 |
| ExistingBitLockerKey | **[必須]** "Encryption" フィールドの値が "AlreadyEncrypted" の場合。<br/> このフィールドの値は、特定のディスクに関連付けられている BitLocker キーです。 <br/><br/>"Encryption" フィールドの値が "Encrypt" の場合は、このフィールドを空白のままにする必要があります。  この場合に BitLocker キーを指定すると、"BitLocker Key should not be specified (BitLocker キーを指定しないでください)" というエラーが表示されます。<br/>  **例**:060456-014509-132033-080300-252615-584177-672089-411631|

##  <a name="preparing-disk-for-import-job"></a>インポート ジョブ用のディスクの準備

インポート ジョブ用のドライブを準備するには、**PrepImport** コマンドを使用して WAImportExport ツールを呼び出します。 追加するパラメーターは、最初のコピー セッションかそれ以降のコピー セッションかによって異なります。

### <a name="first-session"></a>最初のセッション

1 つまたは複数のディスクに 1 つまたは複数のディレクトリをコピーする (CSV ファイルに指定した内容によって異なります) 最初のコピー セッションでは、WAImportExport ツールの PrepImport コマンドを使用して新しいコピー セッションでディレクトリまたはファイルをコピーします。

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] /DataSet:<dataset.csv>
```

**例:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:\*\*\*\*\*\*\*\*\*\*\*\*\* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

### <a name="add-data-in-subsequent-session"></a>後続のセッションでのデータの追加

後続のコピー セッションでは、初期パラメーターを指定する必要はありません。 ツールで前回のセッションを再び使用するには、同じジャーナル ファイルを使用する必要があります。 コピー セッションの状態はジャーナル ファイルに書き込まれます。 追加のディレクトリまたはファイルをコピーする後続のコピー セッションの構文は次のとおりです。

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<DifferentSessionId>  [DataSet:<differentdataset.csv>]
```

**例:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

### <a name="add-drives-to-latest-session"></a>最新のセッションへのドライブの追加

InitialDriveset に指定したドライブにデータが収まらなかった場合は、ツールを使用して同じコピー セッションにドライブを追加できます。 

> [!NOTE]
> セッション ID は前回のセッション ID と一致する必要があります。ジャーナル ファイルは前回のセッションで指定したファイルと一致する必要があります。
> 
> ```
> WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AdditionalDriveSet:<newdriveset.csv>
> ```

**例:**

```
WAImportExport.exe PrepImport /j:SameJournalTest.jrn /id:session#2  /AdditionalDriveSet:driveset-2.csv
```

### <a name="abort-the-latest-session"></a>最新のセッションの中止:

コピー セッションが中断されて再開できない場合 (ソース ディレクトリにアクセスできない場合など) は、現在のセッションを中止し、ロールバックして新しいコピー セッションを開始できるようにする必要があります。

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AbortSession
```

**例:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /AbortSession
```

中止できるのは最後のコピー セッションだけです (異常終了した場合)。 ドライブの最初のコピー セッションは中止できません。 その代わりに、新しいジャーナル ファイルを使用してコピー セッションを再開する必要があります。

### <a name="resume-a-latest-interrupted-session"></a>最新の中断されたセッションの再開

何らかの理由でコピー セッションが中断された場合は、ジャーナル ファイルのみを指定してツールを実行することでセッションを再開できます。

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /ResumeSession
```

**例:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /ResumeSession
```

> [!IMPORTANT] 
> コピー セッションを再開する場合は、ファイルを追加または削除してソース データ ファイルとディレクトリを変更しないでください。

## <a name="waimportexport-parameters"></a>WAImportExport のパラメーター

| parameters | 説明 |
| --- | --- |
|     /j:&lt;JournalFile&gt;  | **必須**<br/> ジャーナル ファイルへのパス。 ジャーナル ファイルは、一連のドライブを追跡し、それらの準備の進行状況を記録します。 ジャーナル ファイルは必ず指定してください。  |
|     /logdir:&lt;LogDirectory&gt;  | **省略可能**。 ログ ディレクトリ。<br/> 詳細ログ ファイルと一部の一時ファイルがこのディレクトリに書き込まれます。 指定されていない場合、現在のディレクトリがログ ディレクトリとして使用されます。 ログ ディレクトリは、同じジャーナル ファイルに 1 回だけ指定できます。  |
|     /id:&lt;SessionId&gt;  | **必須**<br/> セッション ID はコピー セッションの識別に使用されます。 中断されたコピー セッションを正しく復元するために使用されます。  |
|     /ResumeSession  | 省略可能。 最後のコピー セッションが異常終了した場合は、このパラメーターを指定してセッションを再開できます。   |
|     /AbortSession  | 省略可能。 最後のコピー セッションが異常終了した場合は、このパラメーターを指定してセッションを中止できます。  |
|     /sn:&lt;StorageAccountName&gt;  | **必須**<br/> RepairImport と RepairExport にのみ適用できます。 ストレージ アカウントの名前。  |
|     /sk:&lt;StorageAccountKey&gt;  | **必須**<br/> ストレージ アカウントのキー。 |
|     /InitialDriveSet:&lt;driveset.csv&gt;  | **必須**。最初のコピー セッションを実行する場合。<br/> 準備するドライブの一覧を含む CSV ファイル。  |
|     /AdditionalDriveSet:&lt;driveset.csv&gt; | **必須**。 現在のコピー セッションにドライブを追加する場合。 <br/> 追加するドライブの一覧を含む CSV ファイル。  |
|      /r:&lt;RepairFile&gt; | **必須**。RepairImport と RepairExport にのみ適用できます。<br/> 修復の進行状況を追跡するファイルへのパス。 各ドライブには修復ファイルが 1 つのみ必要です。  |
|     /d:&lt;TargetDirectories&gt; | **必須**。 RepairImport と RepairExport にのみ適用できます。 RepairImport の場合は、修復する 1 つ以上のセミコロン区切りのディレクトリ。RepairExport の場合は、修復する 1 つのディレクトリ (ドライブのルート ディレクトリなど)。  |
|     /CopyLogFile:&lt;DriveCopyLogFile&gt; | **必須**。RepairImport と RepairExport にのみ適用できます。 ドライブ コピー ログ ファイル (詳細またはエラー) へのパス。  |
|     /ManifestFile:&lt;DriveManifestFile&gt; | **必須**。RepairExport にのみ適用できます。<br/> ドライブ マニフェスト ファイルへのパス。  |
|     /PathMapFile:&lt;DrivePathMapFile&gt; | **省略可能**。 RepairImport にのみ適用できます。<br/> ドライブのルートを基準としたファイルの相対パスの実際のファイルの場所へのマッピング (タブ区切り) を含むファイルへのパス。 最初に指定する場合は、空のターゲットを含むファイル パスが設定されます。つまり、TargetDirectories に見つからない、アクセスが拒否される、無効な名前を持つパスか、または複数のディレクトリに存在するパスです。 パス マップ ファイルは、正しいターゲット パスを含めるように手動で編集し、ツールでファイル パスを正しく解決するために再度指定できます。  |
|     /ExportBlobListFile:&lt;ExportBlobListFile&gt; | **必須**。 PreviewExport にのみ適用できます。<br/> エクスポートする BLOB の BLOB パスや BLOB パスのプレフィックスの一覧を含む XML ファイルへのパス。 ファイル形式は、インポート/エクスポート サービス REST API の Put Job 操作の BLOB 一覧の BLOB 形式と同じです。  |
|     /DriveSize:&lt;DriveSize&gt; | **必須**。 PreviewExport にのみ適用できます。<br/>  エクスポートに使用するドライブのサイズ。 例: 500 GB、1.5 TB。 注:1 GB = 1,000,000,000 バイト、1 TB = 1,000,000,000,000 バイト  |
|     /DataSet:&lt;dataset.csv&gt; | **必須**<br/> ターゲット ドライブにコピーするディレクトリの一覧またはファイルの一覧を含む CSV ファイル。  |
|     /silentmode  | **省略可能**。<br/> 指定されていない場合、ドライブの要件が通知され、操作を続行するための確認が必要になります。  |

## <a name="tool-output"></a>ツールの出力

### <a name="sample-drive-manifest-file"></a>ドライブ マニフェスト ファイルのサンプル

```xml
<?xml version="1.0" encoding="UTF-8"?>
<DriveManifest Version="2011-MM-DD">
   <Drive>
      <DriveId>drive-id</DriveId>
      <StorageAccountKey>storage-account-key</StorageAccountKey>
      <ClientCreator>client-creator</ClientCreator>
      <!-- First Blob List -->
      <BlobList Id="session#1-0">
         <!-- Global properties and metadata that applies to all blobs -->
         <MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>
         <PropertiesPath Hash="md5-hash">global-properties-file-path</PropertiesPath>
         <!-- First Blob -->
         <Blob>
            <BlobPath>blob-path-relative-to-account</BlobPath>
            <FilePath>file-path-relative-to-transfer-disk</FilePath>
            <ClientData>client-data</ClientData>
            <Length>content-length</Length>
            <ImportDisposition>import-disposition</ImportDisposition>
            <!-- page-range-list-or-block-list -->
            <!-- page-range-list -->
            <PageRangeList>
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
            </PageRangeList>
            <!-- block-list -->
            <BlockList>
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
            </BlockList>
            <MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>
            <PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>
         </Blob>
      </BlobList>
   </Drive>
</DriveManifest>
```

### <a name="sample-journal-file-xml-for-each-drive"></a>各ドライブのジャーナル ファイル (XML) のサンプル

```xml
[BeginUpdateRecord][2016/11/01 21:22:25.379][Type:ActivityRecord]
ActivityId: DriveInfo
DriveState: [BeginValue]
<?xml version="1.0" encoding="UTF-8"?>
<Drive>
   <DriveId>drive-id</DriveId>
   <BitLockerKey>*******</BitLockerKey>
   <ManifestFile>\DriveManifest.xml</ManifestFile>
   <ManifestHash>D863FE44F861AE0DA4DCEAEEFFCCCE68</ManifestHash> </Drive>
[EndValue]
SaveCommandOutput: Completed
[EndUpdateRecord]
```

### <a name="sample-journal-file-jrn-for-session-that-records-the-trail-of-sessions"></a>セッションの軌跡を記録する、セッションのジャーナル ファイル (JRN) のサンプル

```
[BeginUpdateRecord][2016/11/02 18:24:14.735][Type:NewJournalFile]
VocabularyVersion: 2013-02-01
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.749][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
LogDirectory: F:\logs
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.754][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
StorageAccountKey: *******
[EndUpdateRecord]
```

## <a name="faq"></a>FAQ

### <a name="general"></a>全般

#### <a name="what-is-waimportexport-tool"></a>WAImportExport ツールとは何ですか?

WAImportExport ツールは、ドライブの準備および修復用のツールであり、Microsoft Azure Import/Export サービスで使用できます。 このツールを使用して、Azure データ センターに発送するハード ドライブにデータをコピーできます。 インポート ジョブが完了したら、このツールを使用して、壊れている BLOB、不足している BLOB、または他の BLOB と競合している BLOB を修復できます。 エクスポート ジョブが完了してドライブを受け取ったら、このツールを使用して、ドライブ上の破損しているファイルや見つからないファイルを修復できます。

#### <a name="how-does-the-waimportexport-tool-work-on-multiple-source-dir-and-disks"></a>WAImportExport ツールは、複数のソース ディレクトリとディスクに対してはどのように機能しますか?

データ サイズがディスク サイズよりも大きい場合、WAImportExport ツールは最適化された方法で複数のディスクにデータを分散します。 複数のディスクへのデータ コピーは並列で、または同時に実行できます。 同時にデータを書き込むことのできるディスクの数に制限はありません。 このツールはディスク サイズとフォルダー サイズに基づいてデータを分散します。 オブジェクト サイズ用に最も最適化されたディスクが選択されます。 ストレージ アカウントにアップロードされたデータは、指定されたディレクトリ構造に集約されます。

#### <a name="where-can-i-find-previous-version-of-waimportexport-tool"></a>以前のバージョンの WAImportExport ツールはどこで入手できますか?

WAImportExport ツールには、WAImportExport V1 ツールのすべての機能が含まれています。 WAImportExport ツールを使用すると、ユーザーは複数のソースを指定し、複数のドライブに書き込むことができます。 また、データのコピー元となる複数の場所を 1 つの CSV ファイルで簡単に管理できます。 しかし、SAS のサポートが必要な場合や、単一のソースを単一のディスクにコピーする場合は、[WAImportExport V1 ツールをダウンロード](https://go.microsoft.com/fwlink/?LinkID=301900&amp;clcid=0x409)し、[WAImportExport V1 リファレンス](storage-import-export-tool-how-to-v1.md)で WAImportExport V1 の使用方法を参照できます。

#### <a name="what-is-a-session-id"></a>セッション ID とは何ですか?

複数のディスクにデータを分散する場合、ツールではコピー セッション (/id) パラメーターが同じであると想定します。 コピー セッションの名前を同じにすると、ユーザーは 1 つまたは複数のコピー元の場所から 1 つまたは複数のコピー先のディスク/ディレクトリにデータをコピーできます。 セッション ID を同じにすると、ツールでは前回のセッションからファイルのコピーを選択できます。

ただし、同じコピー セッションを使用して、異なるストレージ アカウントにデータをインポートすることはできません。

ツールの複数の実行にわたってコピー セッション名が同じ場合は、ログ ファイル (/logdir) とストレージ アカウント キー (/sk) も同じであると想定されます。

セッション ID には文字、数字 (0 - 9)、アンダースコア (\_)、ダッシュ (-)、またはハッシュ (#) を使用できます。ID の長さは 3 - 30 文字にする必要があります。

例: session-1、session#1、session\_1

#### <a name="what-is-a-journal-file"></a>ジャーナル ファイルとは何ですか?

WAImportExport ツールを実行してハード ドライブにファイルをコピーするたびに、コピー セッションが作成されます。 コピー セッションの状態はジャーナル ファイルに書き込まれます。 コピー セッションが中断された場合 (システムの電源が切れた場合など) は、ツールをもう一度実行し、コマンド ラインでジャーナル ファイルを指定することで、セッションを再開できます。

Azure Import/Export ツールを使用して準備する各ハード ドライブについて、ツールは "&lt;DriveID&gt;.xml" という名前の 1 つのジャーナル ファイルを作成します (ドライブ ID は、ツールがディスクから読み取るドライブに関連付けられているシリアル番号です)。 インポート ジョブを作成するには、すべてのドライブからのジャーナル ファイルが必要です。 また、ツールが中断された場合に、このジャーナル ファイルを使用してドライブの準備を再開することもできます。

#### <a name="what-is-a-log-directory"></a>ログ ディレクトリとは何ですか?

ログ ディレクトリは、詳細ログや一時的なマニフェスト ファイルの保存に使用するディレクトリを指定します。 指定されていない場合、現在のディレクトリがログ ディレクトリとして使用されます。 このログは詳細ログです。

### <a name="prerequisites"></a>前提条件

#### <a name="what-are-the-specifications-of-my-disk"></a>どのような仕様のディスクが必要ですか?

コピー用コンピューターに接続されている 1 台以上の空の 2.5 インチまたは 3.5 インチの SATA II/III ハード ドライブまたは SSD ドライブが必要です。

#### <a name="how-can-i-enable-bitlocker-on-my-machine"></a>コンピューター上で BitLocker を有効にするにはどうすればよいですか?

簡単なチェック方法としては、システム ドライブを右クリックします。 BitLocker が有効になっている場合は、そのオプションが表示されます。 無効になっている場合、オプションは表示されません。

![BitLocker の確認](./media/storage-import-export-tool-preparing-hard-drives-import/BitLocker.png)

BitLocker を有効にする方法については、[こちの記事](https://technet.microsoft.com/library/cc766295.aspx)を参照してください。

コンピューターに TPM チップが搭載されていなくてもかまいません。 tpm.msc を使用して出力を表示しない場合は、次の FAQ を参照してください。

#### <a name="how-to-disable-trusted-platform-module-tpm-in-bitlocker"></a>BitLocker でトラステッド プラットフォーム モジュール (TPM) を無効にするにはどうすればよいですか?
> [!NOTE]
> サーバーに TPM がない場合にのみ、TPM ポリシーを無効にする必要があります。 ユーザーのサーバーに信頼されている TPM がある場合、TPM を無効にする必要はありません。 
> 

BitLocker で TPM を無効にするには、次の手順に従ってください。<br/>
1. コマンド プロンプトで「gpedit.msc」と入力して**グループ ポリシー エディター**を起動します。 **グループ ポリシー エディター**を使用できない場合は、先に BitLocker を有効にします。 前述の FAQ を参照してください。
2. **[ローカル コンピューター ポリシー] &gt; [コンピューターの構成] &gt; [管理用テンプレート] &gt; [Windows コンポーネント] &gt; [BitLocker ドライブ暗号化] &gt; [オペレーティング システムのドライブ]** を開きます。
3. **[スタートアップ時に追加の認証を要求する]** ポリシーを編集します。
4. ポリシーを **[有効]** に設定し、**[互換性のある TPM が装備されていない BitLocker を許可する]** チェック ボックスがオンになっていることを確認します。

####  <a name="how-to-check-if-net-4-or-higher-version-is-installed-on-my-machine"></a>.NET 4 以降のバージョンがコンピューターにインストールされているかどうかを確認するにはどうすればよいですか?

Microsoft .NET Framework のすべてのバージョンは %windir%\Microsoft.NET\Framework\ ディレクトリにインストールされています。

ツールを実行する必要のあるコンピューターで上記の場所に移動します。 "v4" で始まるフォルダー名を探します。 このディレクトリがない場合は、.NET v4 がコンピューターにインストールされていません。 .NET 4 をご自分のマシンにダウンロードするには、[Microsoft .NET Framework 4 (Web インストーラー)](https://www.microsoft.com/download/details.aspx?id=17851) を使用します。

### <a name="limits"></a>制限

#### <a name="how-many-drives-can-i-preparesend-at-the-same-time"></a>何台のドライブを同時に準備/送信できますか?

ツールが準備できるディスクの数に制限はありません。 ただし、ツールでは入力値としてドライブ文字を想定しています。 これにより、同時に準備できるディスクの数が 25 台に制限されます。 1 つのジョブで同時に処理できる最大ディスク数は 10 台です。 同じストレージ アカウントを対象として 10 台を超えるディスクを準備する場合は、ディスクを複数のジョブに分散できます。

#### <a name="can-i-target-more-than-one-storage-account"></a>複数のストレージ アカウントを対象とすることはできますか?

1 つのコピー セッションの各ジョブで送信できるストレージ アカウントは 1 つだけです。

### <a name="capabilities"></a>機能

#### <a name="does-waimportexportexe-encrypt-my-data"></a>WAImportExport.exe ではデータが暗号化されますか?

はい。 このプロセスでは、BitLocker 暗号化を有効にしておく必要があります。

#### <a name="what-will-be-the-hierarchy-of-my-data-when-it-appears-in-the-storage-account"></a>データがストレージ アカウントに表示される場合はどのような階層構造になりますか?

データは複数のディスクに分散されますが、ストレージ アカウントにアップロードされたデータは、データセット CSV ファイルに指定されたディレクトリ構造に集約されます。

#### <a name="how-many-of-the-input-disks-will-have-active-io-in-parallel-when-copy-is-in-progress"></a>コピーの実行中には、何台の入力ディスクでアクティブ IO が並列処理されますか?

ツールでは、入力ファイルのサイズに基づいてデータを複数の入力ディスクに分散します。 しかし、並列処理されるアクティブ ディスクの数は完全に入力データの性質に依存します。 入力データセット内の個々のファイルのサイズによっては、並列処理されるアクティブ IO が 1 台以上のディスクに表示される場合があります。 詳細については、次の質問を参照してください。

#### <a name="how-does-the-tool-distribute-the-files-across-the-disks"></a>ツールではファイルがどのように複数のディスクに分散されますか?

WAImportExport ツールはバッチ単位でファイルの読み取りと書き込みを行います。1 つのバッチには最大 100,000 個のファイルが含まれています。 つまり、最大 100,000 個のファイルを並行して書き込むことができます。 この 100,000 個のファイルが複数のドライブに分散される場合は、複数のディスクに同時に書き込まれます。 ただし、ツールが複数のディスクに同時に書き込むか、または 1 台のディスクに書き込むかは、バッチの合計サイズによって異なります。 たとえば、ファイルのサイズが小さく、10,0000 個すべてのファイルが 1 台のドライブに収まる場合、このバッチの処理中にツールは 1 台のディスクに書き込みます。

### <a name="waimportexport-output"></a>WAImportExport の出力

#### <a name="there-are-two-journal-files-which-one-should-i-upload-to-azure-portal"></a>ジャーナル ファイルが 2 つあります。どちらのファイルを Azure Portal にアップロードする必要がありますか?

**.xml** - WAImportExport ツールを使用して準備する各ハード ドライブについて、ツールは `<DriveID>.xml` という名前の 1 つのジャーナル ファイルを作成します (DriveID は、ツールがディスクから読み取るドライブに関連付けられているシリアル番号です)。 Azure Portal でインポート ジョブを作成するには、すべてのドライブからのジャーナル ファイルが必要です。 また、ツールが中断された場合に、このジャーナル ファイルを使用してドライブの準備を再開することもできます。

**.jrn** - サフィックスが `.jrn` のジャーナル ファイルには、ハード ドライブのすべてのコピー セッションの状態が含まれています。 また、インポート ジョブの作成に必要な情報も含まれています。 WAImportExport ツールを実行する場合は、ジャーナル ファイルとコピー セッション ID を必ず指定してください。

## <a name="next-steps"></a>次の手順

* [Azure Import/Export ツールの設定](storage-import-export-tool-setup.md)
* [インポート処理中にプロパティとメタデータを設定する](storage-import-export-tool-setting-properties-metadata-import.md)
* [インポート ジョブ用のハード ドライブを準備するためのサンプル ワークフロー](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
* [頻繁に使用するコマンドのクイック リファレンス](storage-import-export-tool-quick-reference.md) 
* [コピー ログ ファイルによるジョブの状態の確認](storage-import-export-tool-reviewing-job-status-v1.md)
* [インポート ジョブの修復](storage-import-export-tool-repairing-an-import-job-v1.md)
* [エクスポート ジョブの修復](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Azure Import/Export ツールのトラブルシューティング](storage-import-export-tool-troubleshooting-v1.md)

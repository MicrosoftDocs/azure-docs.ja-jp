---
title: Azure Import/Export のインポート ジョブ用のハード ドライブを準備する - v1 | Microsoft Docs
description: WAImportExport v1 ツールを使用して Azure Import/Export サービスのインポート ジョブを作成するためのハード ドライブを準備する方法について説明します。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 03b504524b2f489f1ee042c6e825ccffe0a60bb3
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315064"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>インポート ジョブ用のハード ドライブを準備する
インポート ジョブ用に 1 つ以上のハード ドライブを準備するには、次の手順を実行します。

- Blob service にインポートするデータを特定します

- Blob service 内から、ターゲットとなる仮想ディレクトリと BLOB を特定します

- 必要となるドライブの数を決定します

- 各ハード ドライブにデータをコピーします

  サンプル ワークフローについては、「[インポート ジョブ用のハード ドライブを準備するためのサンプル ワークフロー](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)」をご覧ください。

## <a name="identify-the-data-to-be-imported"></a>インポートするデータを特定する
 インポート ジョブを作成するには、インポートするディレクトリとファイルを最初に決定します。 その際には、ディレクトリの一覧、一意のファイルの一覧、またはその 2 つの組み合わせを指定できます。 ディレクトリが含まれる場合は、そのディレクトリとサブディレクトリ内のすべてのファイルがインポート ジョブの対象となります。

> [!NOTE]
>  親ディレクトリが含まれる場合、サブディレクトリは再帰的に含まれるので、親ディレクトリのみを指定します。 サブディレクトリは一切指定しないでください。
>
>  現時点で、Microsoft Azure Import/Export ツールには次の制限事項があります: ハード ドライブに収容できるよりも多くのデータがディレクトリに含まれている場合は、ディレクトリをより小さなディレクトリに分割する必要があります。 たとえば、ディレクトリに 2.5 TB のデータが含まれているのに対し、ハード ドライブの容量が 2 TB しかない場合には、2.5 TB のディレクトリをより小さなディレクトリに分割する必要があります。 この制限は、今後のバージョンで解決される予定です。

## <a name="identify-the-destination-locations-in-the-blob-service"></a>BLOB サービス内の宛先の場所を特定する
 インポートするディレクトリまたはファイルごとに、Azure Blob service でインポート先の仮想ディレクトリまたは BLOB を指定する必要があります。 これらのターゲットを Azure Import/Export ツールの入力値として使用します。 ディレクトリをスラッシュ (/) で区切ってください。

 次の表は、BLOB ターゲットの例を示しています。

|ソースのファイルまたはディレクトリ|インポート先の BLOB または仮想ディレクトリ|
|------------------------------|-------------------------------------------|
|H:\Video|https:\//mystorageaccount.blob.core.windows.net/video|
|H:\Photo|https:\//mystorageaccount.blob.core.windows.net/photo|
|K:\Temp\FavoriteVideo.ISO|https:\//mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO|
|\\\myshare\john\music|https:\//mystorageaccount.blob.core.windows.net/music|

## <a name="determine-how-many-drives-are-needed"></a>必要なドライブの数を決定する
 次に、以下のこと確認する必要があります。

- データの格納に必要なハード ドライブの数。

- 各ハード ドライブにコピーするディレクトリやスタンドアロン ファイル。

  転送するデータを格納するためのハード ドライブが必要数あることを確認してください。

## <a name="copy-data-to-your-hard-drive"></a>データをハード ドライブにコピーする
 このセクションでは、Azure Import/Export ツールを呼び出して、1 つまたは複数のハード ドライブにデータをコピーする方法について説明します。 Azure Import/Export ツールを呼び出す際には、その都度新しい*コピー セッション*を作成します。 データのコピー先となる各ドライブに対し、少なくとも 1 つのコピー セッションを作成します。場合によっては、すべてのデータを 1 つのドライブにコピーするために、複数のコピー セッションが必要になることもあります。 複数のコピー セッションが必要になる理由を次に示します。

-   コピー先となるドライブごとに、個別のコピー セッションを作成する必要があります。

-   1 つのコピー セッションでドライブにコピーできるのは、1 つのディレクトリか、または 1 つの BLOB です。 複数のディレクトリ、複数の BLOB、またはそれら両方の組み合わせをコピーする場合は、複数のコピー セッションを作成する必要があります。

-   インポート ジョブを通じてインポートされる BLOB に対しては、設定されるプロパティとメタデータを指定できます。 コピー セッションに対して指定したプロパティやメタデータは、コピー セッションによって指定されたすべての BLOB に適用されます。 一部の BLOB に対して異なるプロパティやメタデータを指定する場合は、個別のコピー セッションを作成する必要があります。 詳しくは、「[インポート処理中にプロパティとメタデータを設定する](storage-import-export-tool-setting-properties-metadata-import-v1.md)」をご覧ください。

> [!NOTE]
>  「[Azure Import/Export ツールの設定](storage-import-export-tool-setup-v1.md)」で概説されている要件を満たすマシンが複数ある場合は、各マシンでこのツールのインスタンスを実行することにより、データを複数のハード ドライブに同時にコピーすることができます。

 Azure Import/Export ツールでは、準備したハード ドライブごとに 1 つのジャーナル ファイルが作成されます。 インポート ジョブを作成するには、すべてのドライブからのジャーナル ファイルが必要です。 また、ツールが中断された場合に、このジャーナル ファイルを使用してドライブの準備を再開することもできます。

### <a name="azure-importexport-tool-syntax-for-an-import-job"></a>インポート ジョブの Azure Import/Export ツール構文
 インポート ジョブ用のドライブを準備するには、**PrepImport** コマンドを使用して Azure Import/Export ツールを呼び出します。 追加するパラメーターは、最初のコピー セッションかそれ以降のコピー セッションかによって異なります。

 ドライブの最初のコピー セッションでは、いくつか追加パラメーターを指定して、ストレージ アカウント キー、対象のドライブ文字、ドライブをフォーマットする必要があるかどうか、ドライブを暗号化する必要があるかどうか、暗号化する場合の BitLocker キー、およびログ ディレクトリを指定する必要があります。 ディレクトリまたは単一ファイルをコピーするための最初のコピー セッションの構文を次に示します。

 **1 つのディレクトリをコピーするための最初のコピー セッション**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **1 つのファイルをコピーするための最初のコピー セッション**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 後続のコピー セッションでは、初期パラメーターを指定する必要はありません。 ディレクトリまたは単一ファイルをコピーする後続のコピー セッションの構文は次のとおりです。

 **1 つのディレクトリをコピーするための後続のコピー セッション**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **1 つのファイルをコピーするための後続のコピー セッション**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

### <a name="parameters-for-the-first-copy-session-for-a-hard-drive"></a>ハード ドライブの初回コピー セッションのパラメーター
 Azure Import/Export ツールを実行してハード ドライブにファイルをコピーすると、その都度コピー セッションが作成されます。 各コピー セッションでは、1 つのディレクトリか 1 つのファイルがハード ドライブにコピーされます。 コピー セッションの状態はジャーナル ファイルに書き込まれます。 コピー セッションが中断された場合 (システムの電源が切れた場合など) は、ツールをもう一度実行し、コマンド ラインでジャーナル ファイルを指定することで、セッションを再開できます。

> [!WARNING]
>  初回のコピー セッションに対して **/format** パラメーターを指定した場合、 ドライブがフォーマットされ、ドライブ上のすべてのデータが削除されます。 空のドライブは、コピー セッションにのみ使用することをお勧めします。

 各ドライブの初回コピー セッションに使用するコマンドでは、それ以降のコピー セッションのコマンドとは異なるパラメーターが必要です。 次の表は、最初のコピー セッションで使用できる追加パラメーターをまとめたものです。

|コマンド ライン パラメーター|説明|
|-----------------------------|-----------------|
|**/sk:**<StorageAccountKey\>|`Optional.` データのインポート先となるストレージ アカウントのストレージ アカウント キー。 **/sk:**<StorageAccountKey\> か **/csas:**<ContainerSas\> のいずれかをコマンドに含める必要があります。|
|**/csas:**<ContainerSas\>|`Optional` データをストレージ アカウントにインポートするために使用するコンテナー SAS。 **/sk:**<StorageAccountKey\> か **/csas:**<ContainerSas\> のいずれかをコマンドに含める必要があります。<br /><br /> このパラメーターの値は、コンテナー名で始まり、その後に疑問符 (?) と SAS トークンが続くという形式で指定する必要があります。 例: <br /><br /> `mycontainer?sv=2014-02-14&sr=c&si=abcde&sig=LiqEmV%2Fs1LF4loC%2FJs9ZM91%2FkqfqHKhnz0JM6bqIqN0%3D&se=2014-11-20T23%3A54%3A14Z&sp=rwdl`<br /><br /> アクセス許可 (URL か、または保存されているアクセス ポリシーで指定されます) には、インポート ジョブの場合は読み取り、書き込み、および削除を含め、エクスポート ジョブの場合は読み取り、書き込み、および一覧表示を含める必要があります。<br /><br /> このパラメーターを指定する場合、インポートまたはエクスポートするすべての BLOB が、共有アクセス署名で指定されたコンテナー内に存在する必要があります。|
|**/t:**<TargetDriveLetter\>|`Required.` 現在のコピー セッションのターゲットとなるハード ドライブのドライブ文字 (後ろにコロンを付けません)。|
|**/format**|`Optional.` ドライブをフォーマットする必要がある場合は、このパラメーターを指定します。それ以外の場合は省略します。 ツールがドライブをフォーマットする前に、コンソールから確認を求めるメッセージが表示されます。 確認メッセージを非表示にするには、/silentmode パラメーターを指定します。|
|**/silentmode**|`Optional.` ターゲット ドライブをフォーマットする際の確認を表示しないようにするには、このパラメーターを指定します。|
|**/encrypt**|`Optional.` ドライブがまだ BitLocker で暗号化されておらず、このツールで暗号化する必要がある場合は、このパラメーターを指定します。 ドライブが既に BitLocker で暗号化されている場合は、このパラメーターを省略して `/bk` パラメーターを指定し、既存の BitLocker キーを入力します。<br /><br /> `/format` パラメーターを指定する場合は、`/encrypt` パラメーターも指定する必要があります。|
|**/bk:**<BitLockerKey\>|`Optional.` `/encrypt` を指定した場合、このパラメーターは省略します。 `/encrypt` を省略する場合は、ドライブが既に BitLocker で暗号化されている必要があります。 このパラメーターは、BitLocker キーを指定するために使用します。 BitLocker 暗号化は、インポート ジョブのすべてのハード ドライブに対して必須です。|
|**/logdir:**<LogDirectory\>|`Optional.` ログ ディレクトリは、詳細ログや一時的なマニフェスト ファイルの保存に使用するディレクトリを指定するものです。 指定されていない場合、現在のディレクトリがログ ディレクトリとして使用されます。|

### <a name="parameters-required-for-all-copy-sessions"></a>すべてのコピー セッションに必要なパラメーター
 ジャーナル ファイルには、ハード ドライブのすべてのコピー セッションの状態が格納されます。 また、インポート ジョブの作成に必要な情報も含まれています。 Azure Import/Export ツールを実行する場合は、ジャーナル ファイルとコピー セッション ID を必ず指定してください。

|||
|-|-|
|コマンド ライン パラメーター|説明|
|**/j:**<JournalFile\>|`Required.` ジャーナル ファイルへのパス。 各ドライブには、ジャーナル ファイルが必ず 1 つあります。 ターゲット ドライブには、ジャーナル ファイルは存在しません。 ジャーナル ファイルの拡張子は `.jrn` です。|
|**/id:**<SessionId\>|`Required.` セッション ID はコピー セッションを識別するためのものです。 中断されたコピー セッションを正しく復元するために使用されます。 コピー セッションでコピーされるファイルは、ターゲット ドライブ上のセッション ID の名前が付いたディレクトリに格納されます。|

### <a name="parameters-for-copying-a-single-directory"></a>1 つのディレクトリをコピーするためのパラメーター
 1 つのディレクトリをコピーする際には、次の必須パラメーターとオプション パラメーターが適用されます。

|コマンド ライン パラメーター|説明|
|----------------------------|-----------------|
|**/srcdir:**<SourceDirectory\>|`Required.` ターゲット ドライブにコピーされるファイルが置かれているソース ディレクトリ。 ディレクトリ パスは絶対パス (相対パスではなく) にする必要があります。|
|**/dstdir:**<DestinationBlobVirtualDirectory\>|`Required.` Microsoft Azure ストレージ アカウント内の、インポート先仮想ディレクトリへのパス。 仮想ディレクトリが既に存在しているかどうかは、場合によって異なります。<br /><br /> 指定できるのは、コンテナーか BLOB プレフィックス (例: `music/70s/`) です。 インポート先ディレクトリは、コンテナー名の後にスラッシュ (/) を付けて指定します。 仮想 BLOB ディレクトリ (省略可能) を追加する場合は、その末尾にも "/" を付けます。<br /><br /> インポート先のコンテナーがルート コンテナーである場合は、ルート コンテナーを (スラッシュ付きで) 明示的に指定する必要があります (例: `$root/`)。 ルート コンテナーの下にある BLOB の名前に "/" を含めることはできないので、インポート先のディレクトリがルート コンテナーである場合、ソース ディレクトリのサブディレクトリはコピーされません。<br /><br /> コピー先の仮想ディレクトリや BLOB を指定する場合は、有効なコンテナー名を使用してください。 コンテナー名は小文字にする必要があります。 コンテナーの名前付け規則については、「[コンテナー、BLOB、およびメタデータの名前付けおよび参照](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」をご覧ください。|
|**/Disposition:**<rename&#124;no-overwrite&#124;overwrite>|`Optional.` 指定したアドレスを持つ BLOB が既に存在する場合の動作を指定します。 このパラメーターの有効値は、`rename`、`no-overwrite`、および `overwrite` です。 これらの値では、大文字と小文字が区別されます。 値を設定しなかった場合は、既定値として `rename` が使用されます。<br /><br /> このパラメーターで指定した値は、`/srcdir` パラメーターによって指定されたディレクトリ内のすべてのファイルに影響を及ぼします。|
|**/BlobType:**<BlockBlob&#124;PageBlob>|`Optional.` インポート先 BLOB の BLOB タイプを指定します。 有効値は `BlockBlob` と `PageBlob` です。 これらの値では、大文字と小文字が区別されます。 値を設定しなかった場合は、既定値として `BlockBlob` が使用されます。<br /><br /> ほとんどの場合は、`BlockBlob` を使用することをお勧めします。 `PageBlob` を指定する場合、ディレクトリ内の各ファイルの長さは 512 の倍数である必要があります (ページ BLOB のページのサイズ)。|
|**/PropertyFile:**<PropertyFile\>|`Optional.` インポート先 BLOB のプロパティ ファイルへのパス。 詳細については、「[Import/Export サービスのメタデータとプロパティ ファイルの形式](../storage-import-export-file-format-metadata-and-properties.md)」を参照してください。|
|**/MetadataFile:**<MetadataFile\>|`Optional.` インポート先 BLOB のメタデータ ファイルへのパス。 詳細については、「[Import/Export サービスのメタデータとプロパティ ファイルの形式](../storage-import-export-file-format-metadata-and-properties.md)」を参照してください。|

### <a name="parameters-for-copying-a-single-file"></a>1 つのファイルをコピーするためのパラメーター
 1 つのファイルをコピーする際には、次の必須パラメーターとオプション パラメーターが適用されます。

|コマンド ライン パラメーター|説明|
|----------------------------|-----------------|
|**/srcfile:**<SourceFile\>|`Required.` コピーするファイルの完全パス。 ディレクトリ パスは絶対パス (相対パスではなく) にする必要があります。|
|**/dstblob:**<DestinationBlobPath\>|`Required.` Microsoft Azure ストレージ アカウント内の、インポート先 BLOB へのパス。 BLOB が既に存在しているかどうかは、場合によって異なります。<br /><br /> BLOB 名は先頭をコンテナー名にして指定します。 BLOB 名の先頭を "/" やストレージ アカウント名にすることはできません。 BLOB の名前付け規則については、「[コンテナー、BLOB、およびメタデータの名前付けおよび参照](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」をご覧ください。<br /><br /> インポート先のコンテナーがルート コンテナーである場合は、`$root` をコンテナーとして明示的に指定する必要があります (例: `$root/sample.txt`)。 なお、ルート コンテナーの下にある BLOB の名前に "/" を含めることはできません。|
|**/Disposition:**<rename&#124;no-overwrite&#124;overwrite>|`Optional.` 指定したアドレスを持つ BLOB が既に存在する場合の動作を指定します。 このパラメーターの有効値は、`rename`、`no-overwrite`、および `overwrite` です。 これらの値では、大文字と小文字が区別されます。 値を設定しなかった場合は、既定値として `rename` が使用されます。|
|**/BlobType:**<BlockBlob&#124;PageBlob>|`Optional.` インポート先 BLOB の BLOB タイプを指定します。 有効値は `BlockBlob` と `PageBlob` です。 これらの値では、大文字と小文字が区別されます。 値を設定しなかった場合は、既定値として `BlockBlob` が使用されます。<br /><br /> ほとんどの場合は、`BlockBlob` を使用することをお勧めします。 `PageBlob` を指定する場合、ディレクトリ内の各ファイルの長さは 512 の倍数である必要があります (ページ BLOB のページのサイズ)。|
|**/PropertyFile:**<PropertyFile\>|`Optional.` インポート先 BLOB のプロパティ ファイルへのパス。 詳細については、「[Import/Export サービスのメタデータとプロパティ ファイルの形式](../storage-import-export-file-format-metadata-and-properties.md)」を参照してください。|
|**/MetadataFile:**<MetadataFile\>|`Optional.` インポート先 BLOB のメタデータ ファイルへのパス。 詳細については、「[Import/Export サービスのメタデータとプロパティ ファイルの形式](../storage-import-export-file-format-metadata-and-properties.md)」を参照してください。|

### <a name="resuming-an-interrupted-copy-session"></a>中断されたコピー セッションの再開
 何らかの理由でコピー セッションが中断された場合は、ジャーナル ファイルのみを指定してツールを実行することでセッションを再開できます。

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession
```

 再開できるのは、直近のコピー セッションだけです (異常終了した場合)。

> [!IMPORTANT]
>  コピー セッションを再開する場合は、ファイルを追加または削除してソース データ ファイルとディレクトリを変更しないでください。

### <a name="aborting-an-interrupted-copy-session"></a>中断されたコピー セッションの中止
 コピー セッションが中断されて再開できない場合 (ソース ディレクトリにアクセスできない場合など) は、現在のセッションを中止し、ロールバックして新しいコピー セッションを開始できるようにする必要があります。

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession
```

 中止できるのは最後のコピー セッションだけです (異常終了した場合)。 ドライブの最初のコピー セッションは中止できません。 その代わりに、新しいジャーナル ファイルを使用してコピー セッションを再開する必要があります。

## <a name="next-steps"></a>次の手順

* [Azure Import/Export ツールの設定](storage-import-export-tool-setup-v1.md)
* [インポート処理中にプロパティとメタデータを設定する](storage-import-export-tool-setting-properties-metadata-import-v1.md)
* [インポート ジョブ用のハード ドライブを準備するためのサンプル ワークフロー](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
* [頻繁に使用するコマンドのクイック リファレンス](storage-import-export-tool-quick-reference-v1.md) 
* [コピー ログ ファイルによるジョブの状態の確認](storage-import-export-tool-reviewing-job-status-v1.md)
* [インポート ジョブの修復](storage-import-export-tool-repairing-an-import-job-v1.md)
* [エクスポート ジョブの修復](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Azure Import/Export ツールのトラブルシューティング](storage-import-export-tool-troubleshooting-v1.md)

---
title: Azure Files のデプロイ方法 | Microsoft Docs
description: Azure Files をデプロイする方法を開始から終了まで説明します。
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 05/22/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: f0b6ed366f35368c70f3d2224f430f400ee9656c
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481180"
---
# <a name="how-to-deploy-azure-files"></a>Azure Files のデプロイ方法
[Azure Files](storage-files-introduction.md) はクラウドで、業界標準の SMB プロトコルを介してアクセスできる、フル マネージドのファイル共有を提供します。 この記事では、実際に組織内で Azure Files をデプロイする方法を示します。

この記事の手順を進める前に、[Azure Files のデプロイの計画](storage-files-planning.md)を読むことを強くお勧めします。

## <a name="prerequisites"></a>前提条件
この記事では、次の手順がすでに完了していることを前提としています。

- 目的のリージョンに、必要な回復性と暗号化のオプションで Azure Storage アカウントを作成していること。 ストレージ アカウントの詳しい作成手順については、[ストレージ アカウントの作成](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)に関するページを参照してください。
- ストレージ アカウントに、目的のクォータで Azure ファイル共有を作成していること。 ファイル共有の詳しい作成手順については、[ファイル共有の作成](storage-how-to-create-file-share.md)に関するページを参照してください。

## <a name="transfer-data-into-azure-files"></a>Azure Files へのデータ転送
新しい Azure ファイル共有に、オンプレミスに保存されているファイル共有などの既存のファイル共有を移行したい場合があります。 このセクションでは、[計画ガイド](storage-files-planning.md#data-transfer-method)に記載されているいくつかの一般的な方法を使用して、Azure ファイル共有にデータを移行する方法を示します

### <a name="azure-file-sync"></a>Azure File Sync
Azure ファイル同期を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を損なわずに Azure Files で組織のファイル共有を一元化できます。 これは、Windows Server を Azure ファイル共有のクイック キャッシュに変換することで行います。 Windows Server で使用可能な任意のプロトコル (SMB、NFS、FTPS など) を使用してデータにローカル アクセスすることができ、世界中に必要な数だけキャッシュを持つことができます。

Azure File Sync は、この同期メカニズムを長期的に利用する必要がない場合でも、Azure ファイル共有にデータを移行するために使用されることがあります。 Azure File Sync を使用して Azure ファイル共有にデータを転送する方法について詳しくは、[Azure ファイル同期のデプロイの計画](storage-sync-files-planning.md)と [Azure ファイル同期をデプロイする方法](storage-sync-files-deployment-guide.md)に関する記事をご覧ください。

### <a name="azure-importexport"></a>Azure Import/Export
Azure Import/Export サービスを使うと、ハード ディスク ドライブを Azure データセンターに送付することで、大量のデータを Azure ファイル共有に安全に転送できます。 サービスの概要について、詳しくは [Microsoft Azure Import/Export サービスを使用した Azure Storage へのデータの転送](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)を参照してください。

> [!Note]  
> Azure Import/Export サービスは、現時点では Azure ファイル共有からのファイルのエクスポートをサポートしていません。

以下の手順は、オンプレミスの場所から Azure ファイル共有にデータをインポートする方法を示しています。

1. Azure に送付する、必要な数のハード ディスクを調達します。 ハード ディスクのディスク サイズは任意ですが、SATA II または SATA III 標準をサポートする 2.5 インチまたは 3.5 インチの SSD または HDD のいずれかが必要です。 

2. データ転送を行うサーバー/PC 上の各ディスクを接続してマウントします。 最適なパフォーマンスを得るために、データを格納しているサーバーのローカルでオンプレミスのエクスポート ジョブを実行することをお勧めします。 データを提供するファイル サーバーが NAS デバイスの場合などは、これを行えない可能性があります。 その場合は、PC 上の各ディスクをマウントしてもまったく差し支えありません。

3. 各ドライブがオンラインで、初期化されており、ドライブ文字が割り当てられていることを確認します。 ドライブをオンラインにして、初期化し、ドライブ文字を割り当てるには、ディスクの管理 MMC スナップイン (diskmgmt.msc) を開きます。

    - (オンラインになっていない場合に) ディスクをオンラインにするには、ディスクの管理 MMC の左ペインでディスクを右クリックし、[オンライン] を選択します。
    - ディスクを初期化するには、(ディスクがオンラインになった後で) 左ペインのディスクをクリックし、[初期化] を選択します。 求められたら、[GPT] を選択します。

        ![ディスクの管理 MMC の [ディスクの初期化] メニューのスクリーン ショット](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - ディスクにドライブ文字を割り当てるには、オンラインで初期化されているディスクの「未割り当て」の空き領域を右クリックし、[新しいシンプル ボリューム] をクリックします。 これにより、ドライブ文字を割り当てることができます。 ボリュームは後でフォーマットされるため、ここでフォーマットする必要はありません。

        ![ディスクの管理 MMC の新しいシンプル ボリューム ウィザードのスクリーン ショット](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. データセット CSV ファイルを作成します。 データセット CSV ファイルは、オンプレミスのデータへのパスと、データをコピーする必要な Azure ファイル共有との間のマッピングです。 たとえば、次のデータセット CSV ファイルは、オンプレミスのファイル共有 ("F:\shares\scratch") を Azure ファイル共有 ("MyAzureFileShare") にマッピングします。
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    1 つのストレージ アカウントを使用して複数の共有を指定する場合があります。 詳細については[データセット CSV ファイルの準備](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-the-dataset-csv-file)を参照してください。

5. ドライブセット CSV ファイルを作成します。 ドライブセット CSV ファイルには、オンプレミスのエクスポート エージェントが使用可能なディスクが一覧表示されます。 たとえば、次のドライブセット CSV ファイルでは、オンプレミスのエクスポート ジョブで使用される `X:`、`Y:`、および `Z:` ドライブが一覧表示されます。

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    詳細については[ドライブセット CSV ファイルの準備](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-initialdriveset-or-additionaldriveset-csv-file)を参照してください。

6. [WAImportExport ツール](https://www.microsoft.com/download/details.aspx?id=55280)を使用して、1 台以上のハード ドライブにデータをコピーします。

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > ディスクの準備が完了した後に、ハード ディスク ドライブのデータやジャーナル ファイルを変更しないでください。

7. [インポート ジョブを作成します](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job)。
    
### <a name="robocopy"></a>Robocopy
Robocopy は、Windows および Windows Server に付属するよく知られたコピー ツールです。 Robocopy では、ファイル共有をローカルにマウントした後、マウントした場所を Robocopy コマンドのコピー先として使って、Azure Files にデータを転送できます。 Robocopy の使用は、非常にシンプルです。

1. [Azure ファイル共有をマウント](storage-how-to-use-files-windows.md)します。 最適なパフォーマンスを得るために、データを格納しているサーバーのローカルで Azure ファイル共有をマウントすることをお勧めします。 データを提供するファイル サーバーが NAS デバイスの場合などは、これを行えない可能性があります。 その場合は、PC 上の Azure ファイル共有をマウントしてもまったく差し支えありません。 この例では、コマンドラインで `net use` を使用してファイル共有をマウントします。

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. コマンドラインで `robocopy` を使用してデータを Azure ファイル共有に移動します。

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy には、必要に応じてコピー動作を変更するオプションが多数あります。 詳細については、[Robocopy](https://technet.microsoft.com/library/cc733145.aspx) のマニュアル ページを参照してください。

### <a name="azcopy"></a>AzCopy
AzCopy は、最高のパフォーマンスの単純なコマンドを使って Azure Files および Azure Blob Storage との間で双方向にデータをコピーするために設計された、コマンドライン ユーティリティです。 AzCopy の使用は簡単です。

1. [最新バージョンの AzCopy on Windows](https://aka.ms/downloadazcopy) または AzCopy on [Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#download-and-install-azcopy)をダウンロードします。
2. コマンドラインで `azcopy` を使用してデータを Azure ファイル共有に移動します。 Windows での構文は次のとおりです。 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    Linux では、コマンドの構文は少し異なります。

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy には、必要に応じてコピー動作を変更するオプションが多数あります。 詳細については、[AzCopy on Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)と[AzCopy on Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)を参照してください。

## <a name="automatically-mount-on-needed-pcsservers"></a>必要な PC/サーバーで自動的にマウントされます。
オンプレミスのファイル共有を置き換える場合は、使用するマシン上で共有をあらかじめマウントしておくことをお勧めします。 これは、マシンの一覧で自動的に実行できます。

> [!Note]  
> Azure ファイル共有をマウントするには、パスワードとしてストレージ アカウント キーを使用する必要があります。したがって、信頼できる環境でのみマウントすることをお勧めします。 

### <a name="windows"></a> Windows
複数の PC で mount コマンドを実行するために PowerShell を使用できます。 次の例では、`$computers` は手動で入力しますが、マウントするコンピューターの一覧は自動で生成できます。 たとえば、Active Directory からの結果によって、この変数を設定できます。

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
単純な bash スクリプトと SSH を組み合わせると、次の例のように同じ結果が得られます。 `$computer` 変数は、同様に左からユーザーが入力します。

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>次の手順
- [Azure ファイル同期のデプロイの計画](storage-sync-files-planning.md)
- [Azure Files のトラブルシューティング - Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Azure Files のトラブルシューティング - Linux](storage-troubleshoot-linux-file-connection-problems.md)

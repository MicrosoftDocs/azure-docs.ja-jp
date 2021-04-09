---
title: チュートリアル:AzCopy を使用してオンプレミスのデータを Azure Storage に移行する | Microsoft Docs
description: このチュートリアルでは、AzCopy を使用して、BLOB、テーブル、およびファイルのコンテンツ間でデータを移行したり、データをコピーしたりすることができます。 ローカル ストレージから Azure Storage にデータを簡単に移行できます。
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 682394329205d74859c1af0c0a68a37539da7872
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98881030"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>チュートリアル:AzCopy を使用してオンプレミスのデータをクラウド ストレージに移行する

AzCopy は、単純なコマンドを使用して Azure Blob Storage、Azure Files、および Azure Table Storage との間でデータをコピーするためのコマンドライン ツールです。 コマンドは、最適なパフォーマンスが得られるように設計されています。 AzCopy を使用すると、ファイル システムとストレージ アカウント間、またはストレージ アカウント間でデータをコピーできます。 AzCopy は、ローカル (オンプレミス) のデータをストレージ アカウントにコピーするために使用することができます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ストレージ アカウントを作成します。 
> * AzCopy を使用してすべてのデータをアップロードします。
> * テスト目的でデータを変更します。
> * アップロードする新しいファイルを特定するための、スケジュールされたタスクまたは cron ジョブを作成します。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、AzCopy の最新バージョンをダウンロードしてください。 [AzCopy の作業開始](storage-use-azcopy-v10.md)に関するページを参照してください。

Windows の場合、このチュートリアルでタスクをスケジュールするために使用する [Schtasks](/windows/win32/taskschd/schtasks) が必要になります。 Linux ユーザーは、代わりに crontab コマンドを使用します。

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>コンテナーを作成する

BLOB は常にコンテナーにアップロードする必要があるため、最初の手順はコンテナーを作成することです。 コンテナーは、BLOB のグループを整理するための方法として使用されます。コンピューターでファイルを整理するためのフォルダーと同様です。

コンテナーを作成するには、次の手順に従います。

1. メイン ページで **[ストレージ アカウント]** ボタンを選択し、作成したストレージ アカウントを選択します。
2. **[サービス]** で **[BLOB]** を選択し、 **[コンテナー]** を選択します。

   ![コンテナーの作成を示すスクリーンショット](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
コンテナー名は文字または数字で始まる必要があります。 コンテナー名には、文字、数字、およびハイフン文字 (-) のみを使用できます。 BLOB とコンテナーの名前付けの詳細については、「[Naming and referencing containers, blobs, and metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」(コンテナー、BLOB、メタデータの名前付けと参照) を参照してください。

## <a name="download-azcopy"></a>AzCopy をダウンロードする

AzCopy V10 実行可能ファイルをダウンロードします。

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

AzCopy ファイルをコンピューターの任意の場所に配置します。 ファイルの場所をシステム パス変数に追加して、コンピューター上の任意のフォルダーからこの実行可能ファイルを参照できるようにします。

## <a name="authenticate-with-azure-ad"></a>Azure AD による認証

まず、[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor)ロールを自分の ID に割り当てます。 「[Azure portal を使用して BLOB とキュー データへのアクセスのための Azure ロールを割り当てる](./storage-auth-aad-rbac-portal.md)」を参照してください。

次に、コマンド プロンプトを開き、次のコマンドを入力して、Enter キーを押します。

```azcopy
azcopy login
```

このコマンドによって、認証コードと Web サイトの URL が返されます。 Web サイトを開き、コードを指定し、 **[次へ]** ボタンを選択します。

![ログイン プロンプトを示すスクリーンショット](media/storage-use-azcopy-v10/azcopy-login.png)

サインイン ウィンドウが表示されます。 そのウィンドウで、Azure アカウント資格情報を使用して、Azure アカウントにサインインします。 正常にサインインしたら、ブラウザー ウィンドウを閉じ、AzCopy の使用を開始できます。

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>フォルダーの内容を BLOB ストレージにアップロードする

AzCopy を使用すると、フォルダー内のすべてのファイルを [Windows](./storage-use-azcopy-v10.md) または [Linux](./storage-use-azcopy-v10.md) 上の Blob Storage にアップロードできます。 フォルダー内のすべての BLOB をアップロードするには、次の AzCopy コマンドを入力します。

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* `<local-folder-path>` プレースホルダーを、ファイルがあるフォルダーへのパスに置き換えます (例: `C:\myFolder` または `/mnt/myFolder`)。

* `<storage-account-name>` プレースホルダーは、実際のストレージ アカウントの名前に置き換えます。

* `<container-name>` プレースホルダーを、作成したコンテナーの名前に置き換えます。

指定したディレクトリの内容を BLOB ストレージに再帰的にアップロードするには、`--recursive` オプションを指定します。 このオプションを使用して AzCopy を実行すると、すべてのサブフォルダーとサブフォルダー内のファイルもアップロードされます。

## <a name="upload-modified-files-to-blob-storage"></a>変更されたファイルを Blob Storage にアップロードする

AzCopy を使用して、最終更新時刻に基づいてファイルをアップロードすることができます。 

これを試すには、テストのためにソース ディレクトリ内のファイルを変更するか、または新しいファイルを作成します。 次に、AzCopy の `sync` コマンドを使用します。

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* `<local-folder-path>` プレースホルダーを、ファイルがあるフォルダーへのパスに置き換えます (例: `C:\myFolder` または `/mnt/myFolder`)。

* `<storage-account-name>` プレースホルダーは、実際のストレージ アカウントの名前に置き換えます。

* `<container-name>` プレースホルダーを、作成したコンテナーの名前に置き換えます。

`sync` コマンドの詳細については、「[Synchronize files (ファイルの同期)](./storage-use-azcopy-v10.md#transfer-data)」を参照してください。

## <a name="create-a-scheduled-task"></a>スケジュールされたタスクを作成する

AzCopy コマンド スクリプトを実行するスケジュールされたタスクまたは cron ジョブを作成できます。 このスクリプトは、特定の時間間隔で新しいオンプレミス データを識別してクラウド ストレージにアップロードします。

AzCopy コマンドをテキスト エディターにコピーします。 AzCopy コマンドのパラメーター値を適切な値に更新します。 ファイルを AzCopy 用に `script.sh` (Linux) または `script.bat` (Windows) として保存します。 

これらの例では、フォルダーの名前は `myFolder`、ストレージ アカウント名の名前は `mystorageaccount`、コンテナー名の名前は `mycontainer` であると想定しています。

> [!NOTE]
> Linux の例では SAS トークンが追加されます。 コマンドにそれを指定する必要があります。 AzCopy V10 の現在のバージョンでは、cron ジョブでの Azure AD 認証はサポートされていません。

# <a name="linux"></a>[Linux](#tab/linux)

```bash
azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-05-30T06:57:40Z&st=2019-05-29T22:57:40Z&spr=https&sig=BXHippZxxx54hQn%2F4tBY%2BE2JHGCTRv52445rtoyqgFBUo%3D" --recursive=true
```

# <a name="windows"></a>[Windows](#tab/windows)

```bash
azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true
```

---

このチュートリアルでは、Windows 上でスケジュールされたタスクを作成するために [Schtasks](/windows/win32/taskschd/schtasks) を使用します。 Linux 上で cron ジョブを作成するには、[Crontab](http://crontab.org/) コマンドを使用します。

 **Schtasks** は、管理者がローカルまたはリモート コンピューター上でスケジュールされたタスクを作成、削除、クエリ、変更、実行、および終了することを可能にします。 **Cron** は、Linux および Unix ユーザーが [cron 式](https://en.wikipedia.org/wiki/Cron#CRON_expression)を使用して、指定された日時にコマンドまたはスクリプトを実行することを可能にします。

# <a name="linux"></a>[Linux](#tab/linux)

Linux 上で cron ジョブを作成するには、端末で次のコマンドを入力します。

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

コマンドの cron 式 `*/5 * * * *` は、シェル スクリプト `script.sh` を 5 分ごとに実行することを指定します。 スクリプトは、毎日、毎月、または毎年特定の時刻に実行するようにスケジュールすることができます。 ジョブの実行日時の設定の詳細については、[cron 式](https://en.wikipedia.org/wiki/Cron#CRON_expression)に関するページを参照してください。

# <a name="windows"></a>[Windows](#tab/windows)

Windows 上でスケジュールされたタスクを作成するには、コマンド プロンプトまたは PowerShell で次のコマンドを入力します。

この例では、スクリプトはコンピューターのルート ドライブにあると想定していますが、スクリプトは任意の場所に配置できます。

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

このコマンドでは、次のパラメーターを使用しています。
- `/SC` パラメーター: 分単位でスケジュールを指定します。
- `/MO` パラメーター: 5 分の間隔を指定します。
- `/TN` パラメーター: タスク名を指定します。
- `/TR` パラメーター: `script.bat` ファイルへのパスを指定します。

Windows 上でスケジュールされたタスクを作成する方法の詳細については、「[Schtasks](/previous-versions/orphan-topics/ws.10/cc772785(v=ws.10)#BKMK_minutes)」を参照してください。

---

スケジュールされたタスクまたは cron ジョブが正常に実行されることを確認するために、`myFolder` ディレクトリ内に新しいファイルを作成します。 新しいファイルがストレージ アカウントにアップロードされたことを確認するために、5 分間待ちます。 ログ ディレクトリに移動して、スケジュールされたタスクまたは cron ジョブの出力ログを確認します。

## <a name="next-steps"></a>次のステップ

オンプレミスと Azure Storage との間でデータを移動する方法については、このリンクに従ってください。

* [Azure Storage との間のデータの移動](./storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。  

AzCopy の詳細については、以下の記事を参照してください。

* [AzCopy を使ってみる](storage-use-azcopy-v10.md)

* [AzCopy と Blob Storage でデータを転送する](./storage-use-azcopy-v10.md#transfer-data)

* [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)

* [AzCopy と Amazon S3 バケットでデータを転送する](storage-use-azcopy-s3.md)
 
* [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)

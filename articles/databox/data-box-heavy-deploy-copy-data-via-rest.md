---
title: 'チュートリアル: REST API 経由で Azure Data Box BLOB ストレージにデータをコピーする | Microsoft Docs'
description: REST API 経由でお客様の Azure Data Box BLOB ストレージにデータをコピーする方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 2c66b94cbcfa4688d9dc45d99688abe76fa55d17
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595801"
---
# <a name="tutorial-copy-data-to-azure-data-box-blob-storage-via-rest-apis"></a>チュートリアル:REST API 経由で Azure Data Box BLOB ストレージにデータをコピーする  

このチュートリアルでは、*http* または *https* を使用して REST API 経由で Azure Data Box BLOB ストレージに接続する手順について説明します。 接続後、Data Box BLOB ストレージにデータをコピーするために必要な手順を説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 前提条件
> * *http* 経由または *https* 経由で Data Box BLOB ストレージに接続する
> * データを Data Box Heavy にコピーする

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. [Azure Data Box の設定に関するAzure Data Box Heavy の設定](data-box-heavy-deploy-set-up.md)。
2. Data Box Heavy の受け取りが済んでいて、ポータル内での注文の状態が **[配送済み]** であること。
3. [Data Box BLOB ストレージのシステム要件](data-box-system-requirements-rest.md)を確認済みであり、API、SDK、ツールのサポートされているバージョンを熟知していること。
4. Data Box Heavy に対してコピーするデータがあるホスト コンピューターにアクセスできること。 このホスト コンピューターは次の条件を満たしている必要があります。
    - [サポート対象のオペレーティング システム](data-box-system-requirements.md)が実行されていること。
    - 高速ネットワークに接続していること。 最速のコピー速度を得るため、2 つの 40 GbE 接続 (ノードごとに 1 つずつ) を並列で利用できます。 40 GbE 接続を使用できない場合は、少なくとも 2 つの 10 GbE 接続 (ノードごとに 1 つずつ) を使用することをお勧めします。 
5. お客様のホスト コンピューターに [AzCopy 7.1.0 をダウンロードする](https://aka.ms/azcopyforazurestack20170417)。 お客様のホスト コンピューターから Azure Data Box BLOB ストレージにデータをコピーするために、AzCopy を使用します。


## <a name="connect-via-http-or-https"></a>http または https 経由で接続する

Data Box BLOB ストレージには *http* または *https* を使用して接続できます。

- Data Box BLOB ストレージへの接続に推奨される安全な方法は *https* です。
- *http* は、信頼されたネットワークにおける接続に使用されます。

Data Box BLOB ストレージに接続するための手順は、*http* 経由の場合と *https* 経由の場合とで異なります。

## <a name="connect-via-http"></a>http 経由で接続する

*http* 経由の Data Box BLOB ストレージ REST API への接続では、以下の手順が必要になります。

- デバイス IP と BLOB サービス エンドポイントをリモート ホストに追加する
- サードパーティ製ソフトウェアを構成し、接続を確認する

以下のセクションでは、これらの各手順について説明します。

> [!IMPORTANT]
> Data Box Heavy で 2 番目のノードに接続するには、すべての接続手順を繰り返す必要があります。

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>デバイス IP アドレスと Blob service エンドポイントを追加する

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]



### <a name="configure-partner-software-and-verify-connection"></a>パートナー ソフトウェアを構成し、接続を確認する

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>https 経由で接続する

https 経由の Azure Blob Storage REST API への接続では、以下の手順が必要になります。

- Azure portal から証明書をダウンロードする
- クライアントまたはリモート ホストで証明書をインポートする
- デバイス IP と Blob service エンドポイントをクライアントまたはリモート ホストに追加する
- サードパーティ製ソフトウェアを構成し、接続を確認する

以下のセクションでは、これらの各手順について説明します。

> [!IMPORTANT]
> Data Box Heavy で 2 番目のノードに接続するには、すべての接続手順を繰り返す必要があります。

### <a name="download-certificate"></a>[証明書のダウンロード]

Azure portal を使用して証明書をダウンロードします。

1. Azure portal にサインインします。
2. お客様の Data Box の注文に移動し、 **[全般]、[デバイスの詳細]** の順に移動します。
3. **[デバイスの資格情報]** で **[デバイスの API アクセス]** に移動します。 **[Download]** をクリックします。 この操作によって、 **\<注文名>.cer** 証明書ファイルがダウンロードされます。 このファイルを**保存**します。 デバイスに接続するために使用するクライアントまたはホスト コンピューターにこの証明書をインストールします。

    ![Azure portal での証明書のダウンロード](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)
 
### <a name="import-certificate"></a>証明書のインポート 

HTTPS 経由で Data Box BLOB ストレージにアクセスするには、デバイスの SSL 証明書が必要です。 この証明書をクライアント アプリケーションで利用できるようにする方法は、アプリケーション、オペレーティング システム、およびディストリビューションによって異なります。 システムの証明書ストアにインポートされた証明書にアクセスできるアプリケーションもあれば、そのメカニズムを利用しないアプリケーションもあります。

このセクションでは、いくつかのアプリケーションに固有の情報を説明しています。 他のアプリケーションの詳細については、使用しているアプリケーションとオペレーティング システムのドキュメントを参照してください。

次の手順に従って、`.cer` ファイルを Windows または Linux クライアントのルート ストアにインポートします。 Windows システムでは、Windows PowerShell または Windows Server UI を使用して、証明書をシステムにインポートしてインストールできます。

#### <a name="use-windows-powershell"></a>Windows PowerShell を使用する

1. Windows PowerShell セッションを管理者として開始します。
2. コマンド プロンプトに、次のコマンドを入力します。

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

#### <a name="use-windows-server-ui"></a>Windows Server UI を使用する

1.  `.cer` ファイルを右クリックし、 **[証明書のインストール]** を選択します。 このアクションにより、証明書のインポート ウィザードが開始されます。
2.  **[ストアの場所]** で **[ローカル マシン]** を選択し、 **[次へ]** をクリックします。

    ![PowerShell を使用した証明書のインポート](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3.  **[証明書をすべて次のストアに配置する]** を選択し、 **[参照]** をクリックします。 リモート ホストのルート ストアに移動し、 **[次へ]** をクリックします。

    ![PowerShell を使用した証明書のインポート](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4.  **[完了]** をクリックします。 インポートが成功したことを通知するメッセージが表示されます。

    ![PowerShell を使用した証明書のインポート](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

#### <a name="use-a-linux-system"></a>Linux システムを使用する

証明書をインポートする方法は、ディストリビューションによって異なります。

> [!IMPORTANT]
> Data Box Heavy で 2 番目のノードに接続するには、すべての接続手順を繰り返す必要があります。

Ubuntu や Debian などのいくつかのディストリビューションでは、`update-ca-certificates` コマンドを使用します。  

- Base64 エンコードの証明書ファイルの名前を変更して拡張子 `.crt` を付け、それを `/usr/local/share/ca-certificates directory` にコピーします。
- コマンド `update-ca-certificates`を実行します。

RHEL、Fedora、および CentOS の最近のバージョンでは、`update-ca-trust` コマンドを使用します。

- 証明書ファイルを `/etc/pki/ca-trust/source/anchors` ディレクトリにコピーします。
- `update-ca-trust` を実行します。

詳細については、お使いのディストリビューションに固有のドキュメントを参照してください。

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>デバイス IP アドレスと Blob service エンドポイントを追加する 

[*http* 経由での接続時のデバイスの IP アドレスと Blob service エンドポイントの追加](#add-device-ip-address-and-blob-service-endpoint)手順と同じ手順に従います。

### <a name="configure-partner-software-and-verify-connection"></a>パートナー ソフトウェアを構成し、接続を確認する

[*http* 経由での接続時に使用したパートナー ソフトウェアの構成](#configure-partner-software-and-verify-connection)手順に従います。 唯一の違いは、*http を使用するオプション*をオフにしておく必要があることです。

## <a name="copy-data-to-data-box-heavy"></a>データを Data Box Heavy にコピーする

Data Box BLOB ストレージに接続したら、次の手順はデータをコピーすることです。 データをコピーする前に、以下の考慮事項を確認してください。

-  データのコピー中は、そのサイズが [Azure Storage と Data Box Heavy の制限](data-box-limits.md)に関するページに記載されたサイズ制限に準拠していることを確実にしてください。
- Data Box Heavy によってアップロードされているデータが、Data Box Heavy の外部で別のアプリケーションによって同時にアップロードされた場合、アップロード ジョブ エラーやデータの破損が生じる可能性があります。

このチュートリアルでは、Data Box BLOB ストレージにデータをコピーするために AzCopy が使用されます。 Azure Storage Explorer (GUI ベースのツールが望ましい場合)、またはパートナー ソフトウェアを使用してデータをコピーすることもできます。

コピーの手順は次のとおりです。

- コンテナーを作成する
- フォルダーの内容を Data Box BLOB ストレージにアップロードする
- 変更されたファイルを Data Box BLOB ストレージにアップロードする


以下のセクションでは、これらの各手順について詳しく説明します。

> [!IMPORTANT]
> Data Box Heavy で 2 番目のノードにデータをコピーするには、すべてのコピーの手順を繰り返す必要があります。

### <a name="create-a-container"></a>コンテナーを作成する

BLOB は常にコンテナーにアップロードされるため、最初の手順はコンテナーを作成することです。 コンテナーを使用すると、お客様のコンピューター上のフォルダーにファイルを整理するように、BLOB のグループを整理できます。 BLOB コンテナーを作成するには、以下の手順に従います。

1. ストレージ エクスプローラーを開きます。
2. 左側のウィンドウで、BLOB コンテナーを作成するストレージ アカウントを展開します。
3. **[BLOB コンテナー]** を右クリックし、コンテキスト メニューの **[BLOB コンテナーの作成]** を選択します。

   ![Create blob containers context menu](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. **[BLOB コンテナー]** フォルダーの下にテキスト ボックスが表示されます。 BLOB コンテナーの名前を入力します。 BLOB コンテナーの名前付けに関する規則と制限については、「[コンテナーを作成し、アクセス許可を設定する](../storage/blobs/storage-quickstart-blobs-dotnet.md)」を参照してください。
5. 作業を終えたら、**Enter** キーを押して BLOB コンテナーを作成するか、**Esc** キーを押して取り消します。 BLOB コンテナーは、正常に作成されると、選択されたストレージ アカウントの **[BLOB コンテナー]** フォルダーの下に表示されます。

   ![作成された BLOB コンテナー](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>フォルダーの内容を Data Box BLOB ストレージにアップロードする

AzCopy を使用して、フォルダー内のすべてのファイルを Windows または Linux 上の BLOB ストレージにアップロードします。 フォルダー内のすべての BLOB をアップロードするには、次の AzCopy コマンドを入力します。

#### <a name="linux"></a>Linux

    azcopy \
        --source /mnt/myfolder \
        --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
        --dest-key <key> \
        --recursive

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S


`<key>` はお客様のアカウント キーで置き換えてください。 お客様のアカウント キーを取得するには、Azure portal でお客様のストレージ アカウントに移動します。 **[設定]、[アクセス キー]** の順に移動し、キーを選択して AzCopy コマンドに貼り付けます。

存在しない宛先コンテナーを指定すると、AzCopy によってコンテナーが作成され、そのコンテナーにファイルがアップロードされます。 お客様のデータ ディレクトリへのソース パスを更新して、宛先 URL の `data-box-storage-account-name` を、お客様の Data Box に関連付けられたストレージ アカウントの名前で置き換えます。

指定したディレクトリの内容を Blob Storage に再帰的にアップロードするには、`--recursive` (Linux) または `/S` (Windows) オプションを指定します。 これらのオプションのいずれかを使用して AzCopy を実行すると、すべてのサブフォルダーとサブフォルダー内のファイルもアップロードされます。

### <a name="upload-modified-files-to-data-box-blob-storage"></a>変更されたファイルを Data Box BLOB ストレージにアップロードする

AzCopy を使用して、最終更新時刻に基づいてファイルをアップロードします。 これを試すには、テストのためにソース ディレクトリ内のファイルを変更するか、または新しいファイルを作成します。 更新されたファイルまたは新しいファイルのみをアップロードするには、`--exclude-older` (Linux) または `/XO` (Windows) パラメーターを AzCopy コマンドに追加します。

宛先に存在しないソース リソースのみをコピーする場合は、AzCopy コマンドに `--exclude-older` と `--exclude-newer` (Linux) または `/XO` と `/XN` (Windows) の両方のパラメーターを指定します。 AzCopy は、タイムスタンプに基づいて、更新されたデータのみをアップロードします。

#### <a name="linux"></a>Linux
    azcopy \
    --source /mnt/myfolder \
    --destination https://data-box-heavy-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
    --dest-key <key> \
    --recursive \
    --exclude-older

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-heavy-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S /XO

接続またはコピー操作中にエラーが発生する場合は、[Data Box BLOB ストレージの問題のトラブルシューティング](data-box-troubleshoot-rest.md)に関するページを参照してください。

次に、発送するデバイスを準備します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * 前提条件
> * *http* 経由または *https* 経由で Data Box BLOB ストレージに接続する
> * データを Data Box Heavy にコピーする


次のチュートリアルに進み、お客様の Data Box を Microsoft に返送する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box Heavy を Microsoft に発送する](./data-box-heavy-deploy-picked-up.md)

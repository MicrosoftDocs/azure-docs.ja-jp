---
title: Storage Explorer の概要 | Microsoft Docs
description: Storage Explorer を使用して Azure Storage リソースの管理を始めます。 Azure Storage Explorer のダウンロードとインストール、ストレージ アカウントまたはサービスへの接続などについて説明します。
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 3a8fe3ded6608059cc6ad50901ffe6df5dcf1b08
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441590"
---
# <a name="get-started-with-storage-explorer"></a>Storage Explorer の概要

## <a name="overview"></a>概要

Microsoft Azure Storage Explorer は、Windows、macOS、Linux での Azure Storage データの操作を容易にするスタンドアロン アプリです。

この記事では、Azure Storage アカウントへの接続と管理に関するさまざまな方法を紹介します。

:::image type="content" alt-text="Microsoft Azure ストレージ エクスプローラー" source="./vs-storage-explorer-overview.png":::

## <a name="prerequisites"></a>前提条件

# <a name="windows"></a>[Windows](#tab/windows)

Storage Explorer は次のバージョンの Windows でサポートされています。

* Windows 10 (推奨)
* Windows 8
* Windows 7

Storage Explorer を使用するには、すべてのバージョンの Windows で .NET Framework 4.7.2 以降が必要です。

# <a name="macos"></a>[macOS](#tab/macos)

Storage Explorer は次のバージョンの macOS でサポートされています。

* macOS 10.12 Sierra 以降のバージョン

# <a name="linux"></a>[Linux](#tab/linux)

Storage Explorer は、Linux の一般的なディストリビューションのほとんどで、[Snap Store](https://snapcraft.io/storage-explorer) から入手できます。 このインストールには、Snap Store をお勧めします。 新しいバージョンが Snap Store に公開されると、Storage Explorer スナップによって、そのすべての依存関係と更新プログラムがインストールされます。

サポートされているディストリビューションについては、[`snapd` のインストール ページ](https://snapcraft.io/docs/installing-snapd)を参照してください。

Storage Explorer では、パスワード マネージャーを使用することが求められます。 パスワード マネージャーには、手動で接続する必要がある場合があります。 次のコマンドを実行して、Storage Explorer をシステムのパスワード マネージャーに接続できます。

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer は、 *.tar.gz* ダウンロードとして入手することもできます。 *Tar. gz* を使用する場合は、依存関係を手動でインストールする必要があります。 *.tar.gz* のインストールは次のディストリビューションの Linux でサポートされています。

* Ubuntu 20.04 x64
* Ubuntu 18.04 x64
* Ubuntu 16.04 x64

*.tar.gz* のインストールは他のディストリビューションでも機能する可能性がありますが、正式にサポートされているのは、こちらの一覧に表示されているものだけです。

Linux での Storage Explorer のインストールの詳細については、「Azure Storage Explorer トラブルシューティング ガイド」の「[Linux の依存関係](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies)」を参照してください。

---

## <a name="download-and-install"></a>ダウンロードしてインストールする

Storage Explorer をダウンロードしてインストールする方法については、「[Azure Storage Explorer](https://www.storageexplorer.com)」を参照してください。

## <a name="connect-to-a-storage-account-or-service"></a>ストレージ アカウントまたはサービスに接続する

Storage Explorer には、Azure リソースへの接続方法がいくつか用意されています。

* [Azure にサインインして自分のサブスクリプションとそのリソースにアクセスする](#sign-in-to-azure)
* [個々の Azure Storage リソースにアタッチする](#attach-to-an-individual-resource)
* [CosmosDB リソースにアタッチする](#connect-to-azure-cosmos-db)

### <a name="sign-in-to-azure"></a>Azure へのサインイン

> [!NOTE]
> サインインした後にリソースにフルにアクセスするには、Storage Explorer で管理 (Azure Resource Manager) とデータ レイヤーの両方のアクセス許可が必要です。 つまり、ストレージ アカウント、アカウント内のコンテナー、およびコンテナー内のデータにアクセスするための Azure Active Directory (Azure AD) のアクセス許可が必要です。 データ層だけにアクセス許可がある場合は、リソースにアタッチするときに、 **[Azure Active Directory (Azure AD) を使用してサインインする]** オプションを選択することを検討してします。 Storage Explorer で必要とされる具体的なアクセス許可の詳細については、「[Azure Storage Explorer トラブルシューティング ガイド](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues)」を参照してください。

1. Storage Explorer で、 **[表示]**  >  **[アカウント管理]** を選択するか、 **[アカウントの管理]** ボタンを選択します。

    :::image type="content" alt-text="アカウントの管理" source ="./vs-storage-explorer-manage-accounts.png":::

1. **[アカウント管理]** に、ユーザーがサインインしているすべての Azure アカウントが表示されます。 別のアカウントに接続するには、 **[アカウントの追加]** を選択します。

1. **[Azure Storage へ接続]** ダイアログ ボックスが開きます。 **[リソースの選択]** パネルで、 **[サブスクリプション]** を選択します。

    :::image type="content" alt-text="接続ダイアログ" source="./vs-storage-explorer-connect-dialog.png":::

1. **[Azure 環境の選択]** パネルで、サインインする Azure 環境を選択します。 グローバル Azure、各国のクラウド、または Azure Stack インスタンスにサインインできます。 **[次へ]** を選択します。

    :::image type="content" alt-text="サインインのオプション" source="./vs-storage-explorer-connect-environment.png":::

    > [!TIP]
    > Azure Stack の詳細については、[Storage Explorer を Azure Stack サブスクリプションまたはストレージ アカウントに接続する](/azure-stack/user/azure-stack-storage-connect-se)方法に関するページを参照してください。

1. Storage Explorer でサインインするための Web ページが開きます。

1. Azure アカウントでのサインインに成功すると、そのアカウントとそのアカウントに関連付けられている Azure サブスクリプションが、 **[アカウント管理]** の下に表示されます。 操作する Azure サブスクリプションを選択してから、 **[適用]** を選択します

    :::image type="content" alt-text="Azure サブスクリプションの選択" source="./vs-storage-explorer-account-panel.png":::

1. **[エクスプローラー]** に、選択した Azure サブスクリプションに関連付けられているストレージ アカウントが表示されます。

    :::image type="content" alt-text="選択された Azure サブスクリプション" source="./vs-storage-explorer-subscription-node.png":::

### <a name="attach-to-an-individual-resource"></a>個々のリソースにアタッチする

Storage Explorer を使用すると、さまざまな認証方法を使用して、Azure Data Lake Storage Gen2 コンテナーなどの個々のリソースに接続できます。 一部の認証方法は、特定のリソースの種類でのみサポートされています。

| リソースの種類    | Azure AD | アカウント名とキー | Shared Access Signature (SAS)  | パブリック (匿名) |
|------------------|----------|----------------------|--------------------------------|--------------------|
| ストレージ アカウント | はい      | はい                  | はい (接続文字列または URL) | いいえ                 |
| BLOB コンテナー  | はい      | いいえ                   | はい (URL)                      | はい                |
| Gen2 コンテナー  | はい      | いいえ                   | はい (URL)                      | はい                |
| Gen2 ディレクトリ | はい      | いいえ                   | はい (URL)                      | はい                |
| ファイル共有      | いいえ       | いいえ                   | はい (URL)                      | いいえ                 |
| キュー           | はい      | いいえ                   | はい (URL)                      | いいえ                 |
| テーブル           | いいえ       | いいえ                   | はい (URL)                      | いいえ                 |
 
Storage Explorer では、エミュレーターで構成されたポートを使用して、[ローカル ストレージ エミュレーター](#local-storage-emulator)に接続することもできます。

個々のリソースに接続するには、左側のツールバーにある **[接続]** ボタンを選択します。 次に、接続先のリソースの種類に応じた手順に従います。

:::image type="content" alt-text="Connect to Azure storage option" source="./vs-storage-explorer-connect-button.png":::

ストレージ アカウントへの接続が正常に追加されると、 **[ローカルで接続済み]**  >  **[ストレージ アカウント]** に新しいツリー ノードが表示されます。

その他のリソースの種類では、 **[ローカルで接続済み]**  >  **[ストレージアカウント]**  >  **[(アタッチされたコンテナー)]** の下に新しいノードが追加されます。 ノードは、その種類に一致するグループ ノードの下に表示されます。 たとえば、Azure Data Lake Storage Gen2 コンテナーへの新しい接続は **[Blob コンテナー]** の下に表示されます。

Storage Explorer で接続を追加できなかった場合、または接続を正常に追加した後に自分のデータにアクセスできない場合は、「[Azure Storage Explorer トラブルシューティング ガイド](./storage/common/storage-explorer-troubleshooting.md)」を参照してください。

以下のセクションでは、個々のリソースへの接続に使用できるさまざまな認証方法について説明します。

#### <a name="azure-ad"></a>Azure AD

Storage Explorer では、Azure アカウントを使用して、次のリソースの種類に接続できます。
* BLOB コンテナー
* Azure Data Lake Storage Gen2 コンテナー
* Azure Data Lake Storage Gen2 ディレクトリ
* キュー
 
リソースへのデータ層アクセス権がある一方で、管理層アクセス権がない場合は、Azure AD を使用することをお勧めします。

1. [前述の手順](#sign-in-to-azure)に従って、少なくとも 1 つの Azure アカウントにサインインします。
1. **[Azure Storage への接続]** ダイアログの **[リソースの選択]** パネルで、 **[BLOB コンテナー]** 、 **[ADLS Gen2 コンテナー]** 、または **[キュー]** を選択します。
1. **[Azure Active Directory (Azure AD) を使用してサインインする]** を選択し、 **[次へ]** を選択します。
1. Azure アカウントとテナントを選択します。 アカウントとテナントでは、アタッチ先のストレージ リソースへのアクセス権が必要です。 **[次へ]** を選択します。
1. 接続の表示名とリソースの URL を入力します。 **[次へ]** を選択します。
1. **[Summary]\(概要\)** パネルで接続情報を確認します。 接続情報が正しい場合は、 **[接続]** を選択します。

#### <a name="account-name-and-key"></a>アカウント名とキー

Storage Explorer では、ストレージ アカウントの名前とキーを使用してストレージ アカウントに接続できます。

アカウント キーは、[Azure portal](https://portal.azure.com) で確認することができます。 ストレージ アカウントの ページを開き、 **[設定]**  >  **[アクセス キー]** を選択します。

1. **[Azure Storage への接続]** ダイアログの **[リソースの選択]** パネルで、 **[ストレージ アカウント]** を選択します。
1. **[アカウント名とキーを使用]** を選択して、 **[次へ]** を選択します。
1. 接続の表示名、アカウント名、いずれかのアカウント キーなどを入力します。 適切な Azure 環境を選択します。 **[次へ]** を選択します。
1. **[Summary]\(概要\)** パネルで接続情報を確認します。 接続情報が正しい場合は、 **[接続]** を選択します。

#### <a name="shared-access-signature-sas-connection-string"></a>Shared Access Signature (SAS) の接続文字列

Storage Explorer では、接続文字列と Shared Access Signature (SAS) を使用してストレージ アカウントに接続できます。 SAS 接続文字列は次のようになります。

```text
SharedAccessSignature=sv=2020-04-08&ss=btqf&srt=sco&st=2021-03-02T00%3A22%3A19Z&se=2020-03-03T00%3A22%3A19Z&sp=rl&sig=fFFpX%2F5tzqmmFFaL0wRffHlhfFFLn6zJuylT6yhOo%2FY%3F;
BlobEndpoint=https://contoso.blob.core.windows.net/;
FileEndpoint=https://contoso.file.core.windows.net/;
QueueEndpoint=https://contoso.queue.core.windows.net/;
TableEndpoint=https://contoso.table.core.windows.net/;
```

1. **[Azure Storage への接続]** ダイアログの **[リソースの選択]** パネルで、 **[ストレージ アカウント]** を選択します。
1. **[Shared access signature (SAS)]** を選択して、 **[次へ]** を選択します。
1. 接続の表示名とストレージ アカウントの SAS 接続文字列を入力します。 **[次へ]** を選択します。
1. **[Summary]\(概要\)** パネルで接続情報を確認します。 接続情報が正しい場合は、 **[接続]** を選択します。

#### <a name="shared-access-signature-sas-url"></a>Shared Access Signature (SAS) URL

Storage Explorer では、SAS URI を使用して、次のリソースの種類に接続できます。
* BLOB コンテナー
* Azure Data Lake Storage Gen2 コンテナーまたはディレクトリ
* ファイル共有
* キュー
* テーブル

SAS URI は次のようになります。

```text
https://contoso.blob.core.windows.net/container01?sv=2020-04-08&st=2021-03-02T00%3A30%3A33Z&se=2020-03-03T00%3A30%3A33Z&sr=c&sp=rl&sig=z9VFdWffrV6FXU51T8b8HVfipZPOpYOFLXuQw6wfkFY%3F
```

1. **[Azure Storage への接続]** ダイアログの **[リソースの選択]** パネルで、接続先のリソースを選択します。
1. **[Shared access signature (SAS)]** を選択して、 **[次へ]** を選択します。
1. 接続の表示名とリソースの SAS URI を入力します。 **[次へ]** を選択します。
1. **[Summary]\(概要\)** パネルで接続情報を確認します。 接続情報が正しい場合は、 **[接続]** を選択します。

#### <a name="local-storage-emulator"></a>ローカル ストレージ エミュレーター

Storage Explorer では、Azure Storage エミュレーターに接続できます。 現在、次の 2 つの エミュレーターがサポートされています。

* [Azure Storage Emulator](storage/common/storage-use-emulator.md) (Windows のみ)
* [Azurite](https://github.com/azure/azurite) (Windows、macOS、または Linux)

エミュレーターが既定のポートでリッスンしている場合は、 **[ローカルで接続済み]**  >  **[ストレージアカウント]**  >  **[エミュレーター - 既定のポート]** のノードを使用して、エミュレーターにアクセスできます。

接続に別の名前を使用する場合、またはエミュレーターが既定のポートで実行されていない場合:

1. エミュレーターを起動します。

   > [!IMPORTANT]
   > Storage Explorer はエミュレーターを自動的に起動しません。 手動で起動する必要があります。

1. **[Azure Storage への接続]** ダイアログの **[リソースの選択]** パネルで、 **[ローカル ストレージ エミュレーター]** を選択します。
1. 接続の表示名と、使用するエミュレートされた各サービスのポート番号を入力します。 サービスを使用しない場合は、対応するポートを空白のままにします。 **[次へ]** を選択します。
1. **[Summary]\(概要\)** パネルで接続情報を確認します。 接続情報が正しい場合は、 **[接続]** を選択します。

### <a name="connect-to-azure-cosmos-db"></a>Azure Cosmos DB への接続

Storage Explorer では、Azure Cosmos DB リソースへの接続もサポートされています。

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>接続文字列を使って Azure Cosmos DB アカウントに接続する

Azure サブスクリプションを使って Azure Cosmos DB アカウントを管理する代わりに、接続文字列を使って Azure Cosmos DB に接続できます。 接続するには、次の手順に従います。

1. **[エクスプローラー]** で、 **[Local & Attached]\(ローカルで接続済み\)** を展開し、 **[Azure Cosmos DB アカウント]** を右クリックして、 **[Azure Cosmos DB に接続]** を選択します。

    ![接続文字列によって Azure Cosmos DB に接続する][21]

1. Azure Cosmos DB API を選択し、**接続文字列** のデータを入力し、 **[OK]** を選択して、Azure Cosmos DB アカウントに接続します。 接続文字列を取得する方法については、「[Azure Cosmos アカウントの管理](./cosmos-db/how-to-manage-database-account.md)」を参照してください。

    ![接続文字列][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Azure Data Lake Store に URI で接続する

サブスクリプションに含まれていないリソースにアクセスできます。 リソースの URI を提供する、そのリソースへのアクセス権を持つユーザーが必要です。 サインイン後、その URI を使って Data Lake Store に接続します。 接続するには、次の手順に従います。

1. **[エクスプローラー]** で、 **[Local & Attached]\(ローカルで接続済み\)** を展開します。

1. **[Data Lake Storage Gen1]** を右クリックし、 **[Data Lake Storage Gen1 に接続]** を選択します。

    ![[Connect to Data Lake Store]\(Data Lake Store に接続する\) コンテキスト メニュー](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. URI を入力し、 **[OK]** を選択します。 **Data Lake Storage** の下にお使いの Data Lake Store が表示されます。

    ![[Connect to Data Lake Store]\(Data Lake Store に接続する\) の結果](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

この例では、Data Lake Storage Gen1 を使用します。 Azure Data Lake Storage Gen2 が利用できるようになりました。 詳細については、「[Azure Data Lake Storage Gen1 とは](./data-lake-store/data-lake-store-overview.md)」を参照してください。

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Storage Explorer で Shared Access Signature を生成する<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>アカウント レベルの Shared Access Signature

1. 共有するストレージ アカウントを右クリックし、 **[Get Shared Access Signature]\(Shared Access Signature の取得\)** を選択します。

    ![[Shared Access Signature の取得] コンテキスト メニューのオプション][14]

1. **[Shared Access Signature]** で、アカウントに対して期間とアクセス許可を指定し、 **[作成]** を選択します。

    ![Shared Access Signature の取得][15]

1. **接続文字列** または生 **クエリ文字列** のいずれかをクリップボードにコピーします。

### <a name="service-level-shared-access-signature"></a>サービス レベルの Shared Access Signature

Shared Access Signature をサービス レベルで取得できます。 詳細については、「[BLOB コンテナーの SAS を取得する](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)」を参照してください。

## <a name="search-for-storage-accounts"></a>ストレージ アカウントを検索する

ストレージ リソースを探すには、 **[エクスプローラー]** ペインで検索します。

検索ボックスへの入力中、Storage Explorer には、その時点で入力してある検索の値に一致するすべてのリソースが表示されます。 次の例では、「**エンドポイント**」を検索しています。

![Storage account search][23]

> [!NOTE]
> 検索時間を短縮するには、 **[アカウント管理]** を使用して、検索対象の項目が含まれていないサブスクリプションを選択解除します。 また、ノードを右クリックし、 **[ここから検索]** を選択することで、特定のノードから検索を開始することもできます。

## <a name="next-steps"></a>次のステップ

* [Storage Explorer を使用して Azure Blob Storage リソースを管理する](vs-azure-tools-storage-explorer-blobs.md)
* [Azure Storage Explorer を使用してデータを操作する](./cosmos-db/storage-explorer.md)
* [Storage Explorer を使用して Azure Data Lake Store リソースを管理する](./data-lake-store/data-lake-store-in-storage-explorer.md)

[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png

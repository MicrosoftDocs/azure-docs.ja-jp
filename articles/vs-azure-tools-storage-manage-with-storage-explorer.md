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
ms.openlocfilehash: 9922601d67393d103b95da7286d8db71d913434a
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235247"
---
# <a name="get-started-with-storage-explorer"></a>Storage Explorer の概要

## <a name="overview"></a>概要

Microsoft Azure Storage Explorer は、Windows、macOS、Linux での Azure Storage データの操作を容易にするスタンドアロン アプリです。 この記事では、Azure Storage アカウントへの接続と管理に関するさまざまな方法を紹介します。

![Microsoft Azure ストレージ エクスプローラー][0]

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

サポートされているディストリビューションについては、[snapd のインストール ページ](https://snapcraft.io/docs/installing-snapd)を参照してください。

Storage Explorer では、パスワード マネージャーを使用することが求められます。 パスワード マネージャーには、手動で接続する必要がある場合があります。 次のコマンドを実行して、Storage Explorer をシステムのパスワード マネージャーに接続できます。

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer は、 *.tar.gz* ダウンロードとして入手することもできます。 依存関係は手動でインストールする必要があります。 *.tar.gz* のインストールは次のディストリビューションの Linux でサポートされています。

* Ubuntu 20.04 x64
* Ubuntu 18.04 x64
* Ubuntu 16.04 x64

*.tar.gz* のインストールは他のディストリビューションでも機能する可能性がありますが、正式にサポートされているのは、こちらの一覧に表示されているものだけです。

Linux での Storage Explorer のインストールの詳細については、「Azure Storage Explorer トラブルシューティング ガイド」の「[Linux の依存関係](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies)」を参照してください。

---

## <a name="download-and-install"></a>ダウンロードしてインストールする

Storage Explorer をダウンロードしてインストールする方法については、「[Azure Storage Explorer](https://www.storageexplorer.com)」を参照してください。

## <a name="connect-to-a-storage-account-or-service"></a>ストレージ アカウントまたはサービスに接続する

Storage Explorer には、ストレージ アカウントに対する接続方法がいくつか用意されています。 一般的に次のいずれかを実行できます。

* [Azure にサインインして自分のサブスクリプションとそのリソースにアクセスする](#sign-in-to-azure)
* [特定のストレージまたは CosmosDB リソースをアタッチする](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Azure へのサインイン

> [!NOTE]
> サインインした後にリソースにフルにアクセスするには、Storage Explorer で管理 (Azure Resource Manager) とデータ レイヤーの両方のアクセス許可が必要です。 つまり、ストレージ アカウント、アカウント内のコンテナー、およびコンテナー内のデータへのアクセスを提供する Azure Active Directory (Azure AD) のアクセス許可が必要です。 データ レイヤーのみのアクセス許可がある場合は、[Azure AD を使用してリソースを追加する](#add-a-resource-via-azure-ad)ことを検討してください。 Storage Explorer で必要とされる具体的なアクセス許可の詳細については、「[Azure Storage Explorer トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues)」を参照してください。

1. Storage Explorer で、 **[表示]**  >  **[アカウント管理]** を選択するか、 **[アカウントの管理]** ボタンを選択します。

    ![アカウントの管理][1]

1. **[アカウント管理]** に、ユーザーがサインインしているすべての Azure アカウントが表示されます。 別のアカウントに接続するには、 **[アカウントの追加]** を選択します。

1. **[Azure Storage へ接続する]** で、 **[Azure 環境]** から Azure クラウドを選択して、国内のクラウドまたは Azure Stack にサインインします。 環境を選択したら、 **[次へ]** を選択します。

    ![サインインのオプション][2]

    Storage Explorer でサインインするためのページが開きます。 詳細については、「[Storage Explorer を Azure Stack サブスクリプションまたはストレージ アカウントに接続する](/azure-stack/user/azure-stack-storage-connect-se)」を参照してください。

1. Azure アカウントでのサインインに成功すると、そのアカウントとそのアカウントに関連付けられている Azure サブスクリプションが、 **[アカウント管理]** の下に表示されます。 **[すべてのサブスクリプション]** で、一覧の Azure サブスクリプションがすべて選択された状態と、1 つも選択されていない状態を切り替えることができます。 操作する Azure サブスクリプションを選択してから、 **[適用]** を選択します

    ![Azure サブスクリプションの選択][3]

    **[エクスプローラー]** に、選択した Azure サブスクリプションに関連付けられているストレージ アカウントが表示されます。

    ![選択された Azure サブスクリプション][4]

### <a name="attach-a-specific-resource"></a>特定のリソースをアタッチする

Storage Explorer でリソースにアタッチする方法は複数あります。

* [Azure AD 経由でリソースを追加する](#add-a-resource-via-azure-ad)。 データ レイヤーのみのアクセス許可を持っている場合は、このオプションを使用して、BLOB コンテナーまたは Azure Data Lake Storage Gen2 BLOB ストレージ コンテナーを追加します。
* [接続文字列を使用する](#use-a-connection-string)。 ストレージ アカウントへの接続文字列がある場合は、このオプションを使用します。 Storage Explorer では、キーと [Shared Access Signature](storage/common/storage-dotnet-shared-access-signature-part-1.md) 接続文字列の両方がサポートされています。
* [Shared Access Signature URI を使用する](#use-a-shared-access-signature-uri)。 BLOB コンテナー、ファイル共有、キュー、またはテーブルへの [Shared Access Signature URI](storage/common/storage-dotnet-shared-access-signature-part-1.md) がある場合は、それを使用してリソースにアタッチします。 Shared Access Signature URI を取得するには、[Storage Explorer](#generate-a-sas-in-storage-explorer) または [Azure portal](https://portal.azure.com) のいずれかを使用できます。
* [名前とキーを使用する](#use-a-name-and-key)。 ストレージ アカウントへのいずれかのアカウント キーがわかっている場合は、このオプションを使用してすばやく接続することができます。 [Azure portal](https://portal.azure.com) で、 **[設定]**  >  **[アクセス キー]** を選択して、ストレージ アカウント ページでお使いのキーを探します。
* [ローカル エミュレーターにアタッチする](#attach-to-a-local-emulator)。 使用可能な Azure Storage エミュレーターのいずれかを使用している場合は、このオプションを使用して簡単にエミュレーターに接続することができます。
* [接続文字列を使って Azure Cosmos DB アカウントに接続する](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string)。 CosmosDB インスタンスへの接続文字列がある場合は、このオプションを使用します。
* [Azure Data Lake Store に URI で接続する](#connect-to-azure-data-lake-store-by-uri)。 Azure Data Lake Store への URI がある場合は、このオプションを使用します。

#### <a name="add-a-resource-via-azure-ad"></a>Azure AD 経由でリソースを追加する

1. **[コネクタ]** シンボルを選択して **[Azure Storage へ接続する]** を開きます。

    ![Connect to Azure storage option][9]

1. リソースへのアクセス権を持つ Azure アカウントにまだサインインしていない場合は、 **[Add an Azure Account]\(Azure アカウントを追加する\)** オプションを使用してサインインします。 サインイン後、 **[Azure Storage へ接続する]** に戻ります。

1. **[Add a resource via Azure Active Directory (Azure AD)]\(Azure Active Directory (Azure AD) を使用してリソースを追加する\)** を選択し、 **[次へ]** を選択します。

1. Azure アカウントとテナントを選択します。 これらの値には、アタッチ先のストレージ リソースへのアクセス権が必要です。 **[次へ]** を選択します。

1. アタッチするリソースの種類を選択します。 接続するために必要な情報を入力します。 

   このページで入力する情報は、追加するリソースの種類によって異なります。 適切なリソースの種類を選択してください。 必要な情報を入力したら、 **[次へ]** を選択します。

1. **[接続の概要]** を見直し、すべての情報が正しいことを確認します。 正しい場合は、 **[接続]** を選択します。 正しくない場合は、 **[戻る]** を選択して前のページに戻り、間違った情報を修正します。

接続が正常に追加されると、接続を表すノードにリソース ツリーが移動します。 リソースは、 **[Local & Attached]\(ローカルで接続済み\)**  >  **[ストレージ アカウント]**  >  **[(アタッチされたコンテナー)]**  >  **[BLOB コンテナー]** の下に表示されます。 Storage Explorer で接続を追加できなかった場合、または接続を正常に追加した後に自分のデータにアクセスできない場合は、「[Azure Storage Explorer トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)」を参照してください。

#### <a name="use-a-connection-string"></a>接続文字列を使用する

1. **[コネクタ]** シンボルを選択して **[Azure Storage へ接続する]** を開きます。

    ![Connect to Azure storage option][9]

1. **[Use a connection string]\(接続文字列を使用する\)** を選択し、 **[次へ]** を選択します。

1. 接続の表示名を選択し、接続文字列を入力します。 次に、 **[次へ]** を選択します。

1. **[接続の概要]** を見直し、すべての情報が正しいことを確認します。 正しい場合は、 **[接続]** を選択します。 正しくない場合は、 **[戻る]** を選択して前のページに戻り、間違った情報を修正します。

接続が正常に追加されると、接続を表すノードにリソース ツリーが移動します。 リソースは、 **[Local & Attached]\(ローカルで接続済み\)**  >  **[ストレージ アカウント]** の下に表示されます。 Storage Explorer で接続を追加できなかった場合、または接続を正常に追加した後に自分のデータにアクセスできない場合は、「[Azure Storage Explorer トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)」を参照してください。

#### <a name="use-a-shared-access-signature-uri"></a>Shared Access Signature URI を使用する

1. **[コネクタ]** シンボルを選択して **[Azure Storage へ接続する]** を開きます。

    ![Connect to Azure storage option][9]

1. **[Use a shared access signature (SAS) URI]\(Shared Access Signature (SAS) URI を使用する\)** を選択し、 **[次へ]** を選択します。

1. 接続の表示名を選択し、Shared Access Signature URI を入力します。 アタッチするリソースの種類のサービス エンドポイントは、自動入力されます。 カスタム エンドポイントを使用している場合は、そうならない可能性もあります。 **[次へ]** を選択します。

1. **[接続の概要]** を見直し、すべての情報が正しいことを確認します。 正しい場合は、 **[接続]** を選択します。 正しくない場合は、 **[戻る]** を選択して前のページに戻り、間違った情報を修正します。

接続が正常に追加されると、接続を表すノードにリソース ツリーが移動します。 リソースは、 **[Local & Attached]\(ローカルで接続済み\)**  >  **[ストレージ アカウント]**  >  **[(アタッチされたコンテナー)]**  > *アタッチしたコンテナーの種類のサービス ノード*の下に表示されます。 Storage Explorer で接続を追加できなかった場合は、「[Azure Storage Explorer トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)」を参照してください。 接続を正常に追加した後に自分のデータにアクセスできない場合は、トラブルシューティング ガイドを参照してください。

#### <a name="use-a-name-and-key"></a>名前とキーを使用する

1. **[コネクタ]** シンボルを選択して **[Azure Storage へ接続する]** を開きます。

    ![Connect to Azure storage option][9]

1. **[Use a storage account name and key]\(ストレージ アカウント名とキーを使用する\)** を選択して、 **[次へ]** を選択します。

1. 接続の表示名を選択します。

1. ストレージ アカウント名とそのいずれかのアクセス キーを入力します。

1. 使用する**ストレージ ドメイン**を選択して、 **[次へ]** を選択します。

1. **[接続の概要]** を見直し、すべての情報が正しいことを確認します。 正しい場合は、 **[接続]** を選択します。 正しくない場合は、 **[戻る]** を選択して前のページに戻り、間違った情報を修正します。

接続が正常に追加されると、接続を表すノードにリソース ツリーが移動します。 リソースは、 **[Local & Attached]\(ローカルで接続済み\)**  >  **[ストレージ アカウント]** の下に表示されます。 Storage Explorer で接続を追加できなかった場合、または接続を正常に追加した後に自分のデータにアクセスできない場合は、「[Azure Storage Explorer トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)」を参照してください。

#### <a name="attach-to-a-local-emulator"></a>ローカル エミュレーターにアタッチする

現在、Storage Explorer は次の 2 つの公式ストレージ エミュレーターをサポートしています。

* [Azure ストレージ エミュレーター](storage/common/storage-use-emulator.md) (Windows のみ)
* [Azurite](https://github.com/azure/azurite) (Windows、macOS、または Linux)

エミュレーターが既定のポートでリッスンしている場合は、**エミュレーター - 既定のポート** ノードを使用して、エミュレーターにアクセスできます。 **[Local & Attached]\(ローカルで接続済み\)**  >  **[ストレージ アカウント]** の下で、**エミュレーター - 既定のポート**を探します。

接続に別の名前を使用する場合、またはエミュレーターが既定のポートで実行されていない場合は、次の手順に従います。

1. エミュレーターを起動します。 コマンド `AzureStorageEmulator.exe status` を入力して、各サービスの種類のポートを表示します。

   > [!IMPORTANT]
   > Storage Explorer はエミュレーターを自動的に起動しません。 手動で起動する必要があります。

1. **[コネクタ]** シンボルを選択して **[Azure Storage へ接続する]** を開きます。

    ![Connect to Azure storage option][9]

1. **[Attach to a local emulator]\(ローカル エミュレーターにアタッチする\)** を選択し、 **[次へ]** を選択します。

1. 接続の表示名を選択し、エミュレーターがリッスンしているポートをサービスの種類ごとに入力します。 **[ローカル エミュレーターにアタッチする]** では、ほとんどのエミュレーターの既定のポート値が提案されます。 どちらの公式エミュレーターも Files サービスを現在サポートしていないため、 **[Files port]\(Files ポート\)** は空白です。 使用しているエミュレーターで Files がサポートされている場合は、使用するポートを入力できます。 次に、 **[次へ]** を選択します。

1. **[接続の概要]** を見直し、すべての情報が正しいことを確認します。 正しい場合は、 **[接続]** を選択します。 正しくない場合は、 **[戻る]** を選択して前のページに戻り、間違った情報を修正します。

接続が正常に追加されると、接続を表すノードにリソース ツリーが移動します。 ノードは、 **[Local & Attached]\(ローカルで接続済み\)**  >  **[ストレージ アカウント]** の下に表示されます。 Storage Explorer で接続を追加できなかった場合、または接続を正常に追加した後に自分のデータにアクセスできない場合は、「[Azure Storage Explorer トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)」を参照してください。

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>接続文字列を使って Azure Cosmos DB アカウントに接続する

Azure サブスクリプションを使って Azure Cosmos DB アカウントを管理する代わりに、接続文字列を使って Azure Cosmos DB に接続できます。 接続するには、次の手順に従います。

1. **[エクスプローラー]** で、 **[Local & Attached]\(ローカルで接続済み\)** を展開し、 **[Azure Cosmos DB アカウント]** を右クリックして、 **[Azure Cosmos DB に接続]** を選択します。

    ![接続文字列によって Azure Cosmos DB に接続する][21]

1. Azure Cosmos DB API を選択し、**接続文字列**のデータを入力し、 **[OK]** を選択して、Azure Cosmos DB アカウントに接続します。 接続文字列を取得する方法については、「[Azure Cosmos アカウントの管理](https://docs.microsoft.com/azure/cosmos-db/manage-account)」を参照してください。

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

1. **接続文字列**または生**クエリ文字列**のいずれかをクリップボードにコピーします。

### <a name="service-level-shared-access-signature"></a>サービス レベルの Shared Access Signature

Shared Access Signature をサービス レベルで取得できます。 詳細については、「[BLOB コンテナーの SAS を取得する](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)」を参照してください。

## <a name="search-for-storage-accounts"></a>ストレージ アカウントを検索する

ストレージ リソースを探すには、 **[エクスプローラー]** ペインで検索します。

検索ボックスへの入力中、Storage Explorer には、その時点で入力してある検索の値に一致するすべてのリソースが表示されます。 次の例では、「**エンドポイント**」を検索しています。

![Storage account search][23]

> [!NOTE]
> 検索時間を短縮するには、 **[アカウント管理]** を使用して、検索対象の項目が含まれていないサブスクリプションを選択解除します。 また、ノードを右クリックし、 **[ここから検索]** を選択することで、特定のノードから検索を開始することもできます。
>
>

## <a name="next-steps"></a>次のステップ

* [Storage Explorer を使用して Azure Blob Storage リソースを管理する](vs-azure-tools-storage-explorer-blobs.md)
* [Azure Storage Explorer を使用してデータを操作する](./cosmos-db/storage-explorer.md)
* [Storage Explorer を使用して Azure Data Lake Store リソースを管理する](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png

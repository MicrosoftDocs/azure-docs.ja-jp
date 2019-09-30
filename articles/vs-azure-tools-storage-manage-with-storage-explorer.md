---
title: Storage Explorer の概要 | Microsoft Docs
description: Storage Explorer を使用して Azure Storage リソースを管理する
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: 43e76b9331eb8dbe95265810b9191a10d4caee08
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272023"
---
# <a name="get-started-with-storage-explorer"></a>Storage Explorer の概要

## <a name="overview"></a>概要

Microsoft Azure Storage Explorer は、Windows、macOS、Linux での Azure Storage データの操作を容易にするスタンドアロン アプリです。 この記事では、Azure Storage アカウントへの接続と管理に関するさまざまな方法を紹介します。

![Microsoft Azure ストレージ エクスプローラー][0]

## <a name="prerequisites"></a>前提条件

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Storage Explorer は次のバージョンの Windows でサポートされています。

* Windows 10 (推奨)
* Windows 8
* Windows 7

すべてのバージョンの Windows で、.NET Framework 4.6.2 以降が必要です。

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Storage Explorer は次のバージョンの macOS でサポートされています。

* macOS 10.12 "Sierra" 以降のバージョン

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Storage Explorer は Linux の一般的なディストリビューションの [Snap Store](https://snapcraft.io/storage-explorer) から入手できます。これは推奨されるインストール方法です。 Storage Explorer スナップは、すべての依存関係を自動的にインストールし、新しいバージョンが Snap Store に発行されると更新されます。

サポートされているディストリビューションの一覧については、[snapd インストール ページ](https://snapcraft.io/docs/installing-snapd)にアクセスしてください。

Storage Explorer では、パスワード マネージャーを使用する必要があります。Storage Explorer が正しく機能するためには、これを手動で接続しなければならない場合があります。 次のコマンドを実行して、Storage Explorer をシステムのパスワード マネージャーに接続できます。

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer は、.tar. gz のダウンロードとしても入手できますが、依存関係を手動でインストールする必要があります。 .tar.gz のインストールは次のディストリビューションの Linux でサポートされています。

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

.tar.gz のインストールは他のディストリビューションでも機能する可能性がありますが、正式にサポートされているのは、こちらの一覧に表示されているものだけです。

Linux への Storage Explorer のインストールの詳細については、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies)を参照してください。

---

## <a name="download-and-install"></a>ダウンロードとインストール

[Storage Explorer をダウンロードしてインストールする](https://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>ストレージ アカウントまたはサービスに接続する

Storage Explorer には、ストレージ アカウントに対する接続方法がいくつか用意されています。 一般的に次のいずれかを実行できます。

* [Azure にサインインして自分のサブスクリプションとそのリソースにアクセスする](#sign-in-to-azure)
* [特定のストレージまたは CosmosDB リソースをアタッチする](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Azure へのサインイン

> [!NOTE]
> サインインした後にリソースにフルにアクセスするには、Storage Explorer で管理 (Azure Resource Manager) とデータ レイヤーの両方のアクセス許可が必要です。 つまり、ストレージ アカウント、アカウント内のコンテナー、およびコンテナー内のデータへのアクセスを提供する Azure Active Directory (Azure AD) のアクセス許可が必要です。 データ レイヤーのみのアクセス許可がある場合は、[Azure AD を使用してリソースを追加する](#add-a-resource-via-azure-ad)ことを検討してください。 Storage Explorer に必要な特定のアクセス許可の詳細については、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues)を参照してください。

1. Storage Explorer で、 **[アカウントの管理]** を選択して [アカウント管理] パネルに移動します。

    ![アカウントの管理][1]

2. 左側のウィンドウに、サインインしている Azure アカウントがすべて表示されます。 別のアカウントに接続するには、 **[アカウントの追加]** を選択します。

3. 国内クラウドまたは Azure Stack にサインインする場合は、 **[Azure 環境]** ドロップダウン リストを選択して、使用する Azure クラウドを選択します。 環境を選択したら、 **[サインイン]** ボタンを選択します。 詳細については、[Azure Stack サブスクリプションへの Storage Explorer の接続](/azure-stack/user/azure-stack-storage-connect-se)に関する記事を参照してください。

    ![サインイン オプション][2]

4. Azure アカウントでのサインインに成功すると、そのアカウントおよびそのアカウントに関連付けられている Azure サブスクリプションが左側のウィンドウに追加されます。 操作する Azure サブスクリプションを選択してから、 **[適用]** を選択します ( **[すべてのサブスクリプション]** チェック ボックスをオンまたはオフにすることで、一覧の Azure サブスクリプションがすべて選択された状態と、1 つも選択されていない状態を切り替えることができます)。

    ![Azure サブスクリプションの選択][3]

    左側のウィンドウに、選択した Azure サブスクリプションに関連付けられているストレージ アカウントが表示されます。

    ![選択された Azure サブスクリプション][4]

### <a name="attach-a-specific-resource"></a>特定のリソースをアタッチする

Storage Explorer でリソースにアタッチする方法は複数あります。

* [Azure AD 経由でリソースを追加する](#add-a-resource-via-azure-ad)。 データ レイヤーのみのアクセス許可を持っている場合は、このオプションを使用して、BLOB コンテナーまたは Azure Data Lake Storage Gen2 BLOB ストレージ コンテナーを追加します。
* [接続文字列を使用する](#use-a-connection-string)。 ストレージ アカウントへの接続文字列がある場合は、このオプションを使用します。 Storage Explorer では、キーと [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) 接続文字列の両方がサポートされています。
* [SAS URI を使用する](#use-a-sas-uri)。 BLOB コンテナー、ファイル共有、キュー、またはテーブルへの [SAS URI](storage/common/storage-dotnet-shared-access-signature-part-1.md) がある場合は、それを使用してリソースにアタッチします。 SAS URI を取得するには、[Storage Explorer](#generate-a-sas-in-storage-explorer) または [Azure portal](https://portal.azure.com) のいずれかを使用できます。
* [名前とキーを使用する](#use-a-name-and-key)。 ストレージ アカウントへのいずれかのアカウント キーがわかっている場合は、このオプションを使用してすばやく接続することができます。 ストレージ アカウントのキーは、[Azure portal](https://portal.azure.com) のストレージ アカウントの **[アクセス キー]** パネルにあります。
* [ローカル エミュレーターにアタッチする](#attach-to-a-local-emulator)。 使用可能な Azure Storage エミュレーターのいずれかを使用している場合は、このオプションを使用して簡単にエミュレーターに接続することができます。
* [接続文字列を使って Azure Cosmos DB アカウントに接続する](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string)。 CosmosDB インスタンスへの接続文字列がある場合は、このオプションを使用します。
* [Azure Data Lake Store に URI で接続する](#connect-to-azure-data-lake-store-by-uri)。 Azure Data Lake Store への URI がある場合は、このオプションを使用します。

#### <a name="add-a-resource-via-azure-ad"></a>Azure AD 経由でリソースを追加する

1. 左側の垂直ツールバーにある **[接続]** ボタンを選択して、 **[接続]** ダイアログ ボックスを開きます。

    ![Connect to Azure storage option][9]

2. リソースへのアクセス権を持つ Azure アカウントにまだサインインしていない場合は、 **[Add an Azure Account]\(Azure アカウントを追加する\)** オプションを使用してサインインします。 サインイン後、 **[接続]** ダイアログ ボックスに戻ります。

3. **[Add a resource via Azure Active Directory (Azure AD)]\(Azure Active Directory (Azure AD) を使用してリソースを追加する\)** を選択し、 **[次へ]** を選択します。

4. アタッチ先のストレージ リソースにアクセスできる Azure アカウントとテナントを選択します。 **[次へ]** を選択します。

5. アタッチするリソースの種類を選択し、接続に必要な情報を入力します。 このページで入力する情報は、追加するリソースの種類によって異なります。 適切なリソースの種類を選択してください。 必要な情報を入力したら、 **[次へ]** を選択します。

6. 接続の概要を確認し、すべての情報が正しいことを確認します。 正しい場合は、 **[接続]** を選択します。 正しくない場合は、 **[戻る]** ボタンを使用して前のページに戻り、間違った情報を修正します。

接続が正常に追加されると、接続を表すノードにリソース ツリーが自動的に移動します。 そのノードに移動しない場合は、**Local & Attached\(ローカルで接続済み\)**  > **ストレージ アカウント** >  **(アタッチされたコンテナー)**  > **BLOB コンテナー** を確認してください。 Storage Explorer で接続を追加できなかった場合、または接続を正常に追加した後に自分のデータにアクセスできない場合は、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)を参照してください。

#### <a name="use-a-connection-string"></a>接続文字列を使用する

1. 左側の垂直ツールバーにある **[接続]** ボタンを選択して、 **[接続]** ダイアログ ボックスを開きます。

    ![Connect to Azure storage option][9]

2. **[Use a connection string]\(接続文字列を使用する\)** を選択し、 **[次へ]** を選択します。

3. 接続の表示名を選択し、接続文字列を入力します。 次に、 **[次へ]** を選択します。

4. 接続の概要を確認し、すべての情報が正しいことを確認します。 正しい場合は、 **[接続]** を選択します。 正しくない場合は、 **[戻る]** ボタンを使用して前のページに戻り、間違った情報を修正します。

接続が正常に追加されると、接続を表すノードにリソース ツリーが自動的に移動します。 そのノードに移動しない場合は、 **[Local & Attached]\(ローカルで接続済み\)**  >  **[ストレージ アカウント]** を確認してください。 Storage Explorer で接続を追加できなかった場合、または接続を正常に追加した後に自分のデータにアクセスできない場合は、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)を参照してください。

#### <a name="use-a-sas-uri"></a>SAS URI を使用する

1. 左側の垂直ツールバーにある **[接続]** ボタンを選択して、 **[接続]** ダイアログ ボックスを開きます。

    ![Connect to Azure storage option][9]

2. **[Use a shared access signature (SAS) URI]\(Shared Access Signature (SAS) URI を使用する\)** を選択し、 **[次へ]** を選択します。

3. 接続の表示名を選択し、SAS URI を入力します。 アタッチするリソースの種類のサービス エンドポイントは、自動入力されます。 カスタム エンドポイントを使用している場合は、そうならない可能性もあります。 **[次へ]** を選択します。

4. 接続の概要を確認し、すべての情報が正しいことを確認します。 正しい場合は、 **[接続]** を選択します。 正しくない場合は、 **[戻る]** ボタンを使用して前のページに戻り、間違った情報を修正します。

接続が正常に追加されると、接続を表すノードにリソース ツリーが自動的に移動します。 そのノードに移動しない場合は、 **[Local & Attached]\(ローカルで接続済み\)**  >  **[ストレージ アカウント]**  >  **(アタッチされたコンテナー)**  > *アタッチしたコンテナーの種類のサービス ノード*を確認してください。 Storage Explorer で接続を追加できなかった場合、または接続を正常に追加した後に自分のデータにアクセスできない場合は、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)を参照してください。

#### <a name="use-a-name-and-key"></a>名前とキーを使用する

1. 左側の垂直ツールバーにある **[接続]** ボタンを選択して、 **[接続]** ダイアログ ボックスを開きます。

    ![Connect to Azure storage option][9]

2. **[Use a storage account name and key]\(ストレージ アカウント名とキーを使用する\)** を選択して、 **[次へ]** を選択します。

3. 接続の表示名を選択します。

4. ストレージ アカウント名とそのいずれかのアクセス キーを入力します。

5. 使用する**ストレージ ドメイン**を選択して、 **[次へ]** を選択します。

6. 接続の概要を確認し、すべての情報が正しいことを確認します。 正しい場合は、 **[接続]** を選択します。 正しくない場合は、 **[戻る]** ボタンを使用して前のページに戻り、間違った情報を修正します。

接続が正常に追加されると、接続を表すノードにリソース ツリーが自動的に移動します。 そのノードに移動しない場合は、 **[Local & Attached]\(ローカルで接続済み\)**  >  **[ストレージ アカウント]** を確認してください。 Storage Explorer で接続を追加できなかった場合、または接続を正常に追加した後に自分のデータにアクセスできない場合は、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)を参照してください。

#### <a name="attach-to-a-local-emulator"></a>ローカル エミュレーターにアタッチする

現在、Storage Explorer は次の 2 つの公式ストレージ エミュレーターをサポートしています。
* [Azure ストレージ エミュレーター](storage/common/storage-use-emulator.md) (Windows のみ)
* [Azurite](https://github.com/azure/azurite) (Windows、macOS、または Linux)

エミュレーターが既定のポートでリッスンしている場合は、**エミュレーター - 既定のポート** ノード ( **[Local & Attached]\(ローカルで接続済み\)**  >  **[ストレージ アカウント]** にある) を使用して、エミュレーターにすばやくアクセスできます。

接続に別の名前を使用する場合、またはエミュレーターが既定のポートで実行されていない場合は、次の手順に従います。

1. エミュレーターを起動します。 このときに、エミュレーターがリッスンしているポートをサービスの種類ごとにメモします。

   > [!IMPORTANT]
   > Storage Explorer はエミュレーターを自動的に起動しません。 手動で起動する必要があります。

2. 左側の垂直ツールバーにある **[接続]** ボタンを選択して、 **[接続]** ダイアログ ボックスを開きます。

    ![Connect to Azure storage option][9]

3. **[Attach to a local emulator]\(ローカル エミュレーターにアタッチする\)** を選択し、 **[次へ]** を選択します。

4. 接続の表示名を選択し、エミュレーターがリッスンしているポートをサービスの種類ごとに入力します。 テキスト ボックスは、ほとんどのエミュレーターの既定ポート値で開始されます。 どちらの公式エミュレーターも Files サービスを現在サポートしていないため、**Files ポート**は空白のままです。 使用しているエミュレーターで Files がサポートされている場合は、使用されているポートを入力できます。 次に、 **[次へ]** を選択します。

5. 接続の概要を確認し、すべての情報が正しいことを確認します。 正しい場合は、 **[接続]** を選択します。 正しくない場合は、 **[戻る]** ボタンを使用して前のページに戻り、間違った情報を修正します。

接続が正常に追加されると、接続を表すノードにリソース ツリーが自動的に移動します。 そのノードに移動しない場合は、 **[Local & Attached]\(ローカルで接続済み\)**  >  **[ストレージ アカウント]** を確認してください。 Storage Explorer で接続を追加できなかった場合、または接続を正常に追加した後に自分のデータにアクセスできない場合は、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)を参照してください。

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>接続文字列を使って Azure Cosmos DB アカウントに接続する

Azure サブスクリプションを使って Azure Cosmos DB アカウントを管理する代わりに、接続文字列を使って Azure Cosmos DB に接続することもできます。 そのためには、次の手順に従います。

1. リソース ツリーの左側で、 **[Local and Attached]\(ローカルで接続済み\)** を展開し、 **[Azure Cosmos DB アカウント]** を右クリックし、 **[Connect to Azure Cosmos DB]\(Azure Cosmos DB に接続\)** を選択します。

    ![接続文字列によって Azure Cosmos DB に接続する][21]

2. Azure Cosmos DB API を選択し、**接続文字列**のデータを入力し、 **[OK]** を選択して、Azure Cosmos DB アカウントに接続します。 接続文字列を取得する方法については、[接続文字列の取得](https://docs.microsoft.com/azure/cosmos-db/manage-account)に関する記事を参照してください。

    ![接続文字列][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Azure Data Lake Store に URI で接続する

サブスクリプションに含まれていないリソースにアクセスする場合は、そのリソースにアクセスできるユーザーに、リソース URI を提供してもらう必要があります。 サインイン後、その URI を使って Data Lake Store に接続できます。 そのためには、次の手順に従います。

1. ストレージ エクスプローラーを開きます。
2. 左側のウィンドウで **[Local and Attached]\(ローカルで接続済み\)** を展開します。
3. **[Data Lake Store]** を右クリックします。 ショートカット メニューから、 **[Connect to Data Lake Store]\(Data Lake Store に接続する\)** を選択します。

    ![[Connect to Data Lake Store]\(Data Lake Store に接続する\) コンテキスト メニュー](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. URI を入力します。 入力した URL の場所にツールが移動します。

    ![[Connect to Data Lake Store]\(Data Lake Store に接続する\) コンテキスト ダイアログ ボックス](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![[Connect to Data Lake Store]\(Data Lake Store に接続する\) の結果](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Storage Explorer で SAS を生成する

### <a name="account-level-sas"></a>アカウント レベル SAS

1. 共有するストレージ アカウントを右クリックし、 **[Get Shared Access Signature]\(Shared Access Signature の取得\)** を選択します。

    ![Get SAS context menu option][14]

2. **[Generate Shared Access Signature]\(Shared Access Signature の生成\)** ダイアログ ボックスで、アカウントの期間とアクセス許可を指定し、 **[作成]** を選択します。

    ![SAS の取得ダイアログ ボックス][15]

3. **接続文字列**または生**クエリ文字列**をクリップボードにコピーできるようになりました。

### <a name="service-level-sas"></a>サービス レベル SAS

[Storage Explorer で BLOB コンテナーの SAS を取得する方法](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>ストレージ アカウントを検索する

ストレージ リソースを見つける必要があるが、その場所がわからない場合は、左側のウィンドウの上部にある検索ボックスを使用してリソースを検索できます。

検索ボックスへの入力中、左側のウィンドウには、その時点で入力してある検索の値に一致するすべてのリソースが表示されます。 たとえば次のスクリーンショットは、**endpoints** を検索するようすを示しています。

![Storage account search][23]

> [!NOTE]
> 検索時間を短縮するには、[アカウント管理] パネルを使用して、検索対象の項目が含まれていないサブスクリプションを選択解除します。 また、ノードを右クリックし、 **[ここから検索]** を選択することで、特定のノードから検索を開始することもできます。
>
>

## <a name="next-steps"></a>次の手順

* [Storage Explorer を使用して Azure Blob Storage リソースを管理する](vs-azure-tools-storage-explorer-blobs.md)
* [Azure Cosmos DB を Storage Explorer で管理する (プレビュー)](./cosmos-db/storage-explorer.md)
* [Storage Explorer を使用して Azure Data Lake Store リソースを管理する](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png

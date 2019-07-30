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
ms.openlocfilehash: f7dd6d3d30f34ba2c69b40111bb28d484ce572e7
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508736"
---
# <a name="get-started-with-storage-explorer"></a>Storage Explorer の概要

## <a name="overview"></a>概要

Azure Storage Explorer は、Windows、macOS、および Linux で Azure Storage のデータを簡単に操作できるスタンドアロン アプリです。 この記事では、Azure Storage アカウントへの接続と管理に関するさまざまな方法を紹介します。

![Microsoft Azure ストレージ エクスプローラー][0]

## <a name="prerequisites"></a>前提条件

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Azure Storage Explorer は次のバージョンの Windows でサポートされています。

* Windows 10 (推奨)
* Windows 8
* Windows 7

すべてのバージョンの Windows で、.NET Framework 4.6.2 以降が必要です。

[Storage Explorer をダウンロードしてインストールする](https://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Azure Storage Explorer は次のバージョンの macOS でサポートされています。

* macOS 10.12 "Sierra" 以降のバージョン

[Storage Explorer をダウンロードしてインストールする](https://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Azure Storage Explorer は次のディストリビューションの Linux でサポートされています。

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Azure Storage Explorer は他のディストリビューションでも動作する可能性がありますが、正式にサポートされているのは上記のディストリビューションのみです。

Linux への Storage Explorer のインストールの詳細については、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies)を参照してください。

Azure Storage Explorer の[リリース ノート](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409)には、一部のディストリビューションでの具体的な手順が掲載されています。

[Storage Explorer をダウンロードしてインストールする](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>ストレージ アカウントまたはサービスに接続する

Storage Explorer には、ストレージ アカウントに対する接続方法がいくつか用意されています。 一般的に次のいずれかを実行できます。

* [Azure にサインインして自分のサブスクリプションとそのリソースにアクセスする](#sign-in-to-azure)
* [特定のストレージまたは CosmosDB リソースをアタッチする](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Azure へのサインイン

> [!NOTE]
> サインインした後にリソースにフル アクセスするには、Storage Explorer で管理 (ARM) とデータ層の両方のアクセス許可が必要です。 つまり、ストレージ アカウント、アカウント内のコンテナー、およびコンテナー内のデータへのアクセスを提供する Azure AD のアクセス許可が必要です。 データ層でのアクセス許可しかない場合は、[Azure AD によるアタッチ](#add-a-resource-via-azure-ad)の使用を検討してください。 Storage Explorer で必要な正確なアクセス許可の詳細については、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues)を参照してください。
>
>

1. Storage Explorer で、 **[アカウントの管理]** を選択して **[アカウント管理] パネル**に移動します。

    ![アカウントの管理][1]

2. 左側のウィンドウに、サインインしている Azure アカウントがすべて表示されます。 別のアカウントに接続するには、 **[アカウントの追加]** を選択します。

3. 国内クラウドまたは Azure Stack にサインインする場合は、 **[Azure 環境]** ドロップダウンをクリックして、使用する Azure クラウドを選択します。 環境を選択したら、 **[サインイン]** ボタンをクリックします。 Azure Stack にサインインする場合は、「[Azure Stack サブスクリプションに Microsoft Azure Storage Explorer を接続する](/azure-stack/user/azure-stack-storage-connect-se)」を参照してください。

    ![サインイン オプション][2]

4. Azure アカウントでのサインインに成功すると、そのアカウントおよびそのアカウントに関連付けられている Azure サブスクリプションが左側のウィンドウに追加されます。 操作する Azure サブスクリプションを選択してから、 **[適用]** を選択します ( **[すべてのサブスクリプション]** チェック ボックスをオンまたはオフにすることで、一覧の Azure サブスクリプションがすべて選択された状態と、1 つも選択されていない状態を切り替えることができます)。

    ![Azure サブスクリプションの選択][3]

    左側のウィンドウに、選択した Azure サブスクリプションに関連付けられているストレージ アカウントが表示されます。

    ![選択された Azure サブスクリプション][4]

### <a name="attach-a-specific-resource"></a>特定のリソースをアタッチする
    
リソースを Storage Explorer にアタッチするためのさまざまなオプションがあります。 次のようにすることができます。

* [Azure AD 経由でリソースを追加する](#add-a-resource-via-azure-ad):データ層でのアクセス許可しかない場合は、このオプションを使用して、BLOB コンテナーまたは ADLS Gen2 BLOB コンテナーを追加することができます。
* [接続文字列を使用する](#use-a-connection-string):ストレージ アカウントへの接続文字列がある場合。 Storage Explorer では、キーと [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) 接続文字列の両方がサポートされています。
* [SAS URI を使用する](#use-a-sas-uri):BLOB コンテナー、ファイル共有、キュー、またはテーブルへの [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) URI がある場合。 SAS URI を取得するには、[Storage Explorer](#generate-a-sas-in-storage-explorer) または [Azure portal](https://portal.azure.com) のいずれかを使用できます。
* [名前とキーを使用する](#use-a-name-and-key):ストレージ アカウントのいずれかのアカウント キーがわかっている場合は、このオプションを使用してすばやく接続することができます。 ストレージ アカウントのキーは、[Azure portal](https://portal.azure.com) のストレージ アカウントの **[アクセス キー]** ブレードにあります。
* [ローカル エミュレーターにアタッチする](#attach-to-a-local-emulator):使用可能な Azure Storage エミュレーターのいずれかを使用している場合は、このオプションを使用して簡単にエミュレーターに接続することができます。
* [接続文字列を使って Azure Cosmos DB アカウントに接続する](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string):CosmosDB インスタンスへの接続文字列がある場合。
* [Azure Data Lake Store に URI で接続する](#connect-to-azure-data-lake-store-by-uri):Azure Data Lake Store への URI がある場合。

#### <a name="add-a-resource-via-azure-ad"></a>Azure AD 経由でリソースを追加する

1. 左側の垂直ツールバーにある**接続ボタン**をクリックして、**接続ダイアログ**を開きます。

    ![Connect to Azure storage option][9]

2. リソースへのアクセス権を持つ Azure アカウントにまだサインインしていない場合は、 **[Add an Azure Account]\(Azure アカウントの追加\)** オプションを使用してサインインします。 サインイン後、**接続ダイアログ**に戻ります。

3. **[Add a resource via Azure Active Directory (Azure AD)]\(Azure Active Directory (Azure AD) 経由でリソースを追加\)** オプションを選択し、 **[次へ]** をクリックします。

4. アタッチするストレージ リソースへのアクセス権を持つ Azure アカウントとリソースがあるサブスクリプションを選択し、 **[次へ]** をクリックします。

5. アタッチするリソースの種類を選択し、接続に必要な情報を入力します。 このページでの入力は、追加するリソースの種類に応じて変わります。 適切なリソースの種類を選択してください。 必要な情報を入力したら、 **[次へ]** をクリックします。

6. 接続の概要を確認し、すべての情報が正しいかどうかを確認します。 すべての情報が正しい場合は、 **[接続]** をクリックします。正しくない場合は、 **[戻る]** ボタンを使って前のページに戻り、間違った情報を修正します。

接続が正常に追加されると、リソース ツリーが接続を表すノードに自動的に移動します。 何らかの理由でそうならない場合は、 **[Local and Attached]\(ローカルで接続済み\)** → **[ストレージ アカウント]** → **(アタッチされているコンテナー)** → **[BLOB コンテナー]** を確認してください。 Storage Explorer で接続を追加できなかった場合、または接続を正常に追加した後に、自分のデータにアクセスできない場合は、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)でヘルプを参照してください。

#### <a name="use-a-connection-string"></a>接続文字列を使用する

1. 左側の垂直ツールバーにある**接続ボタン**をクリックして、**接続ダイアログ**を開きます。

    ![Connect to Azure storage option][9]

2. **[Use a connection string]\(接続文字列を使用する\)** オプションを選択し、 **[次へ]** をクリックします。

3. 接続の表示名を選択し、接続文字列を入力します。 その後、 **[次へ]** をクリックします。

4. 接続の概要を確認し、すべての情報が正しいかどうかを確認します。 すべての情報が正しい場合は、 **[接続]** をクリックします。正しくない場合は、 **[戻る]** ボタンを使って前のページに戻り、間違った情報を修正します。

接続が正常に追加されると、リソース ツリーが接続を表すノードに自動的に移動します。 何らかの理由でそうならない場合は、 **[Local and Attached]\(ローカルで接続済み\)** → **[ストレージ アカウント]** を確認してください。 Storage Explorer で接続を追加できなかった場合、または接続を正常に追加した後に、自分のデータにアクセスできない場合は、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)でヘルプを参照してください。

#### <a name="use-a-sas-uri"></a>SAS URI を使用する

1. 左側の垂直ツールバーにある**接続ボタン**をクリックして、**接続ダイアログ**を開きます。

    ![Connect to Azure storage option][9]

2. **[Use a shared access signature (SAS) URI]\(共有アクセス署名 (SAS) URI を使用する\)** オプションを選択し、 **[次へ]** をクリックします。

3. 接続の表示名を選択し、SAS URI を入力します。 アタッチしているリソースの種類のサービス エンドポイントは、自動入力にする必要があります。 カスタム エンドポイントを使用している場合は、そうなっていない場合があります。 **[次へ]** をクリックします。

4. 接続の概要を確認し、すべての情報が正しいかどうかを確認します。 すべての情報が正しい場合は、 **[接続]** をクリックします。正しくない場合は、 **[戻る]** ボタンを使って前のページに戻り、間違った情報を修正します。

接続が正常に追加されると、リソース ツリーが接続を表すノードに自動的に移動します。 何らかの理由でそうならない場合は、 **[Local and Attached]\(ローカルで接続済み\)** → **[ストレージ アカウント]** → **(アタッチされているコンテナー)** → **アタッチしたコンテナーの種類のサービス ノード**を確認してください。 Storage Explorer で接続を追加できなかった場合、または接続を正常に追加した後に、自分のデータにアクセスできない場合は、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)でヘルプを参照してください。

#### <a name="use-a-name-and-key"></a>名前とキーを使用する

1. 左側の垂直ツールバーにある**接続ボタン**をクリックして、**接続ダイアログ**を開きます。

    ![Connect to Azure storage option][9]

2. **[Use a storage account name and key]\(ストレージ アカウント名とキーを使用\)** オプションを選択し、 **[次へ]** をクリックします。

3. 接続の表示名を選択します。

4. ストレージ アカウント名とそのいずれかのアクセス キーを入力します。

5. 使用する**ストレージ ドメイン**を選択して、 **[次へ]** をクリックします。

4. 接続の概要を確認し、すべての情報が正しいかどうかを確認します。 すべての情報が正しい場合は、 **[接続]** をクリックします。正しくない場合は、 **[戻る]** ボタンを使って前のページに戻り、間違った情報を修正します。

接続が正常に追加されると、リソース ツリーが接続を表すノードに自動的に移動します。 何らかの理由でそうならない場合は、 **[Local and Attached]\(ローカルで接続済み\)** → **[ストレージ アカウント]** を確認してください。 Storage Explorer で接続を追加できなかった場合、または接続を正常に追加した後に、自分のデータにアクセスできない場合は、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)でヘルプを参照してください。

#### <a name="attach-to-a-local-emulator"></a>ローカル エミュレーターにアタッチする

Storage Explorer では、すべてのプラットフォームでエミュレーターをサポートしています。 現在、次の 2 つの公式のエミュレーターが使用可能です。
* [Azure ストレージ エミュレーター](storage/common/storage-use-emulator.md) (Windows のみ)
* [Azurite](https://github.com/azure/azurite) (Windows、macOS、または Linux)

エミュレーターが既定のポートで実行されている場合は、**エミュレーター - 既定のポート** ノード (これは **[Local and Attached]\(ローカルで接続済み\)** → **[ストレージ アカウント]** に常にあります) を使用してエミュレーターにすばやくアクセスすることができます。 接続に別の名前を使用する場合、またはエミュレーターが既定のポートで実行されていない場合は、次の手順に従います。

1. エミュレーターを起動します。 このときに、エミュレーターがリッスンしているポートをサービスの種類ごとにメモします。 この情報は後で必要になります。

   > [!IMPORTANT]
   > Storage Explorer はエミュレーターを自動的に起動しません。 エミュレーターは自分で起動する必要があります。

2. 左側の垂直ツールバーにある**接続ボタン**をクリックして、**接続ダイアログ**を開きます。

    ![Connect to Azure storage option][9]

3. **[Attach to a local emulator]\(ローカル エミュレーターにアタッチ\)** オプションを選択し、 **[次へ]** をクリックします。

4. 接続の表示名を選択し、エミュレーターがリッスンするポートをサービスの種類ごとに入力します。 既定では、テキスト ボックスにはほとんどのエミュレーターの既定ポートの値が含まれます。 どちらの公式のエミュレーターも Files サービスを現在サポートしていないため、**Files ポート**も既定では空白のままです。 ただし、使用しているエミュレーターでサポートしている場合は、使用されているポートを入力することができます。 **[次へ]** をクリックします。

5. 接続の概要を確認し、すべての情報が正しいかどうかを確認します。 すべての情報が正しい場合は、 **[接続]** をクリックします。正しくない場合は、 **[戻る]** ボタンを使って前のページに戻り、間違った情報を修正します。

接続が正常に追加されると、リソース ツリーが接続を表すノードに自動的に移動します。 何らかの理由でそうならない場合は、 **[Local and Attached]\(ローカルで接続済み\)** → **[ストレージ アカウント]** を確認してください。 Storage Explorer で接続を追加できなかった場合、または接続を正常に追加した後に、自分のデータにアクセスできない場合は、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)でヘルプを参照してください。

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>接続文字列を使って Azure Cosmos DB アカウントに接続する

Azure サブスクリプションを使って Azure Cosmos DB アカウントを管理する以外に、接続文字列を使って Azure Cosmos DB に接続することもできます。 接続文字列を使用して接続するには、次の手順を使用します。

1. 左側のツリーで **[Local and Attached]\(ローカルで接続済み\)** を見つけ、 **[Azure Cosmos DB アカウント]** を右クリックし、 **[Azure Cosmos DB に接続...]** を選択します。

    ![接続文字列によって Azure Cosmos DB に接続する][21]

2. Azure Cosmos DB API を選び、**接続文字列**を貼り付けた後、 **[OK]** をクリックして Azure Cosmos DB アカウントに接続します。 接続文字列の取得については、「[接続文字列を取得する](https://docs.microsoft.com/azure/cosmos-db/manage-account)」を参照してください。

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Azure Data Lake Store に URI で接続する

リソースにアクセスしたいのですが、このリソースがサブスクリプションに存在しません。 ただし、他のユーザーはそのリソースの URI の取得を許可しています。 この場合、サインイン後に URI を使用して Data Lake Store に接続できます。 以下の手順を参照してください。

1. ストレージ エクスプローラーを開きます。
2. 左側のウィンドウで **[Local and Attached]\(ローカルで接続済み\)** を展開します。
3. **[Data Lake Store]** を右クリックし、コンテキスト メニューから **[Connect to Data Lake Store]\(Data Lake Store に接続する\)** を選択します。

    ![Data Lake Store への接続のコンテキスト メニュー](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. URI を入力すると、ツールが、入力した URL の場所に移動します。

    ![Data Lake Store への接続のダイアログ](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![Data Lake Store への接続の結果](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Storage Explorer で SAS を生成する

### <a name="account-level-sas"></a>アカウント レベル SAS

1. 共有するストレージ アカウントを右クリックし、 **[Get Shared Access Signature]\(Shared Access Signature の取得\)** を選択します。

    ![Get SAS context menu option][14]

2. **[Generate Shared Access Signature]\(Shared Access Signature の生成\)** ダイアログ ボックスで、アカウントに対して期間とアクセス許可を指定し、 **[作成]** をクリックします。

    ![SAS の取得ダイアログ ボックス][15]

3. **接続文字列**または生**クエリ文字列**をクリップボードにコピーできるようになりました。

### <a name="service-level-sas"></a>サービス レベル SAS

[Storage Explorer で BLOB コンテナーの SAS を取得する方法](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>ストレージ アカウントを検索する

ストレージ リソースを見つける必要があるとき、その場所がわからない場合は、左側のウィンドウの上部にある検索ボックスを使用してリソースを検索することができます。

検索ボックスへの入力中、左側のウィンドウには、その時点で入力してある検索の値に一致するすべてのリソースが表示されます。 たとえば次のスクリーンショットは、**endpoints** を検索するようすを示しています。

![Storage account search][23]

> [!NOTE]
> 検索の実行時間を短縮するため、探している項目を含んでいないサブスクリプションは、 **[Account Management Panel]\(アカウント管理パネル\)** を使用して選択を解除してください。 また、ノードを右クリックし、 **[Search From Here]\(ここから検索\)** を選択することで、特定のノードから検索を開始することもできます。
>
>

## <a name="next-steps"></a>次の手順

* [Storage Explorer を使用して Azure Blob Storage リソースを管理する](vs-azure-tools-storage-explorer-blobs.md)
* [Azure Cosmos DB を Azure Storage Explorer で管理する (プレビュー)](./cosmos-db/storage-explorer.md)
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

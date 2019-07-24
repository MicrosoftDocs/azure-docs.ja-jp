---
title: Storage Explorer の概要 | Microsoft Docs
description: Storage Explorer を使用して Azure Storage リソースを管理する
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: 38a857b1d309b92c48137a46655155e0e131908c
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002682"
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

Linux への Storage Explorer のインストールの詳細については、[トラブルシューティング ガイド](https://docs.microsoft.com/en-us/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies)を参照してください。

Azure Storage Explorer の[リリース ノート](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409)には、一部のディストリビューションでの具体的な手順が掲載されています。

[Storage Explorer をダウンロードしてインストールする](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>ストレージ アカウントまたはサービスに接続する

Storage Explorer には、ストレージ アカウントに対する接続方法がいくつか用意されています。 たとえば、次のようなことができます。

* 自分の Azure サブスクリプションに関連付けられているストレージ アカウントに接続する。
* 他の Azure サブスクリプションから共有されているストレージ アカウントとサービスに接続する。
* Azure ストレージ エミュレーターを使ってローカル ストレージに接続し、そのローカル ストレージを管理する。

さらに、国内外の Azure のストレージ アカウントを使用できます。

* [Azure サブスクリプションに接続する](#connect-to-an-azure-subscription):自分の Azure サブスクリプションに属するストレージ リソースを管理します。
* [ローカル開発ストレージを操作する](#work-with-local-development-storage):Azure ストレージ エミュレーターを使ってローカル ストレージを管理する。
* [外部ストレージにアタッチする](#attach-or-detach-an-external-storage-account):ストレージ アカウントの名前、キー、およびエンドポイントを使って、別の Azure サブスクリプションに属するストレージ リソースや国内の Azure クラウドにあるストレージ リソースを管理します。
* [SAS を使ってストレージ アカウントをアタッチする](#attach-a-storage-account-by-using-a-shared-access-signature-sas):Shared Access Signature (SAS) を使って、別の Azure サブスクリプションに属するストレージ リソースを管理します。
* [SAS を使ってサービスをアタッチする](#attach-a-service-by-using-a-shared-access-signature-sas):SAS を使って、別の Azure サブスクリプションに属する特定のストレージ サービス (BLOB コンテナー、キュー、またはテーブル) を管理します。
* [接続文字列を使って Azure Cosmos DB アカウントに接続する](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string):接続文字列を使って Cosmos DB アカウントを管理します。

## <a name="connect-to-an-azure-subscription"></a>Azure サブスクリプションに接続する

> [!NOTE]
> Azure アカウントを持っていない場合は、[無料試用版にサインアップする](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)か、[Visual Studio サブスクライバー特典を有効](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)にしてください。
>
>

1. Storage Explorer で、**[アカウントの管理]** を選択して **[アカウント管理] パネル**に移動します。

    ![アカウントの管理][1]

2. 左側のウィンドウに、サインインしている Azure アカウントがすべて表示されます。 別のアカウントに接続するには、**[アカウントの追加]** を選択します。

3. 国内クラウドまたは Azure Stack にサインインする場合は、**[Azure 環境]** ドロップダウンをクリックして、使用する Azure クラウドを選択します。 環境を選択したら、**[サインイン]** ボタンをクリックします。 Azure Stack にサインインする場合は、「[Azure Stack サブスクリプションに Microsoft Azure Storage Explorer を接続する](/azure-stack/user/azure-stack-storage-connect-se)」を参照してください。

    ![サインイン オプション][2]

4. Azure アカウントでのサインインに成功すると、そのアカウントおよびそのアカウントに関連付けられている Azure サブスクリプションが左側のウィンドウに追加されます。 操作する Azure サブスクリプションを選択してから、**[適用]** を選択します (**[すべてのサブスクリプション]** チェック ボックスをオンまたはオフにすることで、一覧の Azure サブスクリプションがすべて選択された状態と、1 つも選択されていない状態を切り替えることができます)。

    ![Azure サブスクリプションの選択][3]

    左側のウィンドウに、選択した Azure サブスクリプションに関連付けられているストレージ アカウントが表示されます。

    ![Selected Azure subscriptions][4]

## <a name="work-with-local-development-storage"></a>ローカル開発ストレージを操作する

Storage Explorer では、エミュレーターを使用してローカル ストレージを操作できます。 この方法を使えば、Azure にデプロイされたストレージ アカウントがなくても、Azure Storage の操作をシミュレートすることができます。

バージョン 1.1.0 以降、ローカル ストレージ エミュレーターは、すべてのプラットフォームでサポートされます。 Storage Explorer は、その既定のローカル ストレージ エンドポイントをリッスンしている任意のエミュレートされたサービスに接続できます。

> [!NOTE]
> ストレージ サービスと機能のサポートは、選択したエミュレーターによって大きく異なる可能性があります。 目的のサービスと機能をエミュレーターがサポートしていることを確認してください。

1. 選択したエミュレーターのサービスを、未使用のポートをリッスンするように構成します。

   エミュレートされたサービス | 既定のエンドポイント
   -----------------|-------------------------
   BLOB            | `http://127.0.0.1:10000`
   キュー           | `http://127.0.0.1:10001`
   テーブル           | `http://127.0.0.1:10002`

2. エミュレーターを起動します。
   > [!IMPORTANT]
   > Storage Explorer はエミュレーターを自動的に起動しません。 エミュレーターは自分で起動する必要があります。

3. Storage Explorer で、**[アカウントの追加]** ボタンをクリックします。 **[Attach to a local emulator]\(ローカル エミュレーターにアタッチ\)** を選択し、**[次へ]** をクリックします。

4. 前の手順で構成したサービスのポート番号を入力します (そのサービスを使用しない場合は空白のままにします)。 **[次へ]** をクリックして **[接続]** をクリックし、接続を作成します。

5. **[Local & Attached]\(ローカルおよびアタッチ済み\)** > **[ストレージ アカウント]** > ノードの順に展開し、エミュレーターの接続に対応するノードの下にあるサービス ノードを展開します。

   このノードを使用して、ローカル BLOB、キュー、およびテーブルを作成して操作できます。 それぞれの種類のストレージ アカウントを操作する方法については、次のガイドを参照してください。

   * [Azure Blob Storage リソースの管理](vs-azure-tools-storage-explorer-blobs.md)
   * [Azure File Storage リソースの管理](vs-azure-tools-storage-explorer-files.md)

## <a name="attach-or-detach-an-external-storage-account"></a>外部ストレージ アカウントをアタッチまたはデタッチする

Storage Explorer には、ストレージ アカウントを簡単に共有できるように、外部ストレージ アカウントにアタッチする機能が用意されています。 このセクションでは、外部ストレージ アカウントにアタッチ (または外部ストレージ アカウントからデタッチ) する方法について説明します。

### <a name="get-the-storage-account-credentials"></a>ストレージ アカウントの資格情報を取得する

外部ストレージ アカウントを共有するには、まずそのアカウントの所有者がアカウントの資格情報 (アカウント名とキー) を取得し、その情報を、そのアカウントへのアタッチを望んでいるユーザーと共有する必要があります。 Azure Portal からストレージ アカウントの資格情報を取得する手順は、以下のとおりです。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[参照]** を選択します。

3. **[ストレージ アカウント]** を選択します。

4. **[ストレージ アカウント]** の一覧で目的のストレージ アカウントを選択します。

5. **[設定]** で **[アクセス キー]** を選択します。

    ![Access Keys option][7]

6. **[ストレージ アカウント名]** と **[Key1]** をコピーします。

    ![[アクセス キー]][8]

### <a name="attach-to-an-external-storage-account"></a>外部ストレージ アカウントにアタッチする

外部ストレージ アカウントにアタッチするには、アカウントの名前とキーが必要になります。 「ストレージ アカウントの資格情報を取得する」セクションでは、Azure Portal からこれらの値を取得する方法を説明しました。 ただし、ポータルでは、アカウント キーが **key1** という名前になっています。 このため、Storage Explorer でアカウント キーの入力が必要な局面があれば、**key1** の値を入力してください。

1. Storage Explorer で、**接続ダイアログ**を開きます。

    ![Connect to Azure storage option][9]

2. **接続**ダイアログで **[Use a storage account name and key]\(ストレージ アカウントの名前とキーを使用する\)** を選択します。

    ![名前とキーを使用して追加するオプション][10]

3. **[アカウント名]** ボックスにアカウント名を、**[アカウント キー]** ボックスにアカウント キー (Azure Portal から得た **key1** の値) をそれぞれ貼り付けて、**[次へ]** を選択します。

    ![名前とキーのページ][11]

    > [!NOTE]
    > 国内クラウドからの名前とキーを使用するには、**[ストレージ エンドポイントのドメイン]** ボックスの一覧から適切なエンドポイント ドメインを選択してください。
    >
    >

4. **[接続の概要]** ダイアログ ボックスで、情報を確認します。 設定を変更する場合は、**[戻る]** を選択し、必要な設定をもう一度入力します。

5. **[接続]** を選択します。

6. ストレージ アカウントが正常にアタッチされると、そのストレージ アカウントが、名前の後に "**(外部)**" を伴って表示されます。

    ![Result of connecting to an external storage account][12]

### <a name="detach-from-an-external-storage-account"></a>外部ストレージ アカウントからデタッチする

1. デタッチする外部ストレージ アカウントを右クリックし、**[デタッチ]** を選択します。

    ![Detach from storage option][13]

2. 確認メッセージで **[はい]** を選択して、外部ストレージ アカウントからのデタッチを確定します。

## <a name="attach-a-storage-account-by-using-a-shared-access-signature-sas"></a>Shared Access Signature (SAS) を使用してストレージ アカウントをアタッチする

Shared Access Signature ([SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md)) を使うと、Azure サブスクリプションの管理者が Azure サブスクリプションの資格情報を提供しなくても、ストレージ アカウントへのアクセスを一時的に許可できるようになります。

このシナリオをわかりやすく説明するために、Azure サブスクリプションの管理者である UserA が UserB に特定のアクセス許可を付与し、一時的にストレージ アカウントへのアクセスを許可する場面を考えてみます。

1. UserA は、目的のアクセス許可と一定の期間が設定された SAS 接続文字列を生成します。

2. UserA は、ストレージ アカウントへのアクセスを希望しているユーザー (この例では UserB) と SAS を共有します。

3. UserB は Storage Explorer からその SAS を使用し、UserA に属するアカウントにアタッチします。

### <a name="generate-a-sas-query-string-for-the-account-you-want-to-share"></a>共有するアカウントの SAS クエリ文字列を生成する

1. Storage Explorer で、共有するストレージ アカウントを右クリックし、**[Get Shared Access Signature]\(Shared Access Signature の取得\)** を選択します。

    ![Get SAS context menu option][14]

2. **[Generate Shared Access Signature]\(Shared Access Signature の生成\)** ダイアログ ボックスで、アカウントに対して期間とアクセス許可を指定し、**[作成]** をクリックします。

    ![SAS の取得ダイアログ ボックス][15]

3. **[クエリ文字列]** ボックスの横にある **[コピー]** を選択してクリップボードにコピーし、**[閉じる]** をクリックします。

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>SAS 接続文字列を使用してストレージ アカウントにアタッチする

1. Storage Explorer で、**接続ダイアログ**を開きます。

    ![Connect to Azure storage option][9]

2. **接続**ダイアログで **[Use a connection string or shared access signature URI]\(接続文字列または Shared Access Signature URI を使用する\)** を選択し、**[次へ]** をクリックします。

    ![[Azure Storage へ接続] ダイアログ][16]

3. **[Use a connection string]\(接続文字列を使用する\)** を選択し、実際の接続文字列を **[接続文字列]** フィールドに貼り付けます。 **[次へ]** をクリックします。

    ![[Azure Storage へ接続] ダイアログ][17]

4. **[接続の概要]** ダイアログ ボックスで、情報を確認します。 変更する場合は、**[戻る]** を選択して、必要な設定を入力します。

5. **[接続]** を選択します。

6. ストレージ アカウントが正常にアタッチされると、そのストレージ アカウントが、名前の後に "**(SAS)**" を伴って表示されます。

    ![SAS を使ってアカウントをアタッチした結果][18]

## <a name="attach-a-service-by-using-a-shared-access-signature-sas"></a>Shared Access Signature (SAS) を使用してサービスをアタッチする

「SAS を使ってストレージ アカウントをアタッチする」セクションでは、Azure サブスクリプションの管理者がストレージ アカウントの SAS を生成および共有し、ストレージ アカウントへの一時的なアクセスを許可する方法を説明しました。 同様に、SAS は、ストレージ アカウント内の特定のサービス (BLOB コンテナー、キュー、テーブル、またはファイル共有) に対して生成できます。

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>共有するサービスの SAS を生成する

この場合のサービスは、BLOB、コンテナー、キュー、テーブル、またはファイル共有です。 表示されているサービスの SAS を生成するには、以下のいずれかを参照してください。

* [BLOB コンテナーの SAS を取得する](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>SAS URI を使って共有アカウント サービスにアタッチする

1. Storage Explorer で、**接続ダイアログ**を開きます。

    ![Connect to Azure storage option][9]

2. **接続**ダイアログ ボックスで **[Use a connection string or shared access signature URI]\(接続文字列または Shared Access Signature URI を使用する\)** を選択し、**[次へ]** をクリックします。

    ![[Azure Storage へ接続] ダイアログ][16]

3. **[Use a SAS URI]\(SAS URI を使用する\)** を選択し、実際の URI を **[URI]** フィールドに貼り付けます。 **[次へ]** をクリックします。

    ![[Azure Storage へ接続] ダイアログ][19]

4. **[接続の概要]** ダイアログ ボックスで、情報を確認します。 変更する場合は、**[戻る]** を選択して、必要な設定を入力します。

5. **[接続]** を選択します。

6. サービスが正常にアタッチされると、そのサービスが **[(SAS-Attached Services)]\((SAS アタッチ サービス)\)** ノードに表示されます。

    ![SAS を使って共有サービスをアタッチした結果][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>接続文字列を使って Azure Cosmos DB アカウントに接続する

Azure サブスクリプションを使って Azure Cosmos DB アカウントを管理する以外に、接続文字列を使って Azure Cosmos DB に接続することもできます。 接続文字列を使用して接続するには、次の手順を使用します。

1. 左側のツリーで **[Local and Attached]\(ローカルで接続済み\)** を見つけ、**[Azure Cosmos DB アカウント]** を右クリックし、**[Azure Cosmos DB に接続...]** を選択します。

    ![接続文字列によって Azure Cosmos DB に接続する][21]

2. Azure Cosmos DB API を選び、**接続文字列**を貼り付けた後、**[OK]** をクリックして Azure Cosmos DB アカウントに接続します。 接続文字列の取得については、「[接続文字列を取得する](https://docs.microsoft.com/azure/cosmos-db/manage-account)」を参照してください。

    ![connection-string][22]

## <a name="connect-to-azure-data-lake-store-by-uri"></a>Azure Data Lake Store に URI で接続する

リソースにアクセスしたいのですが、このリソースがサブスクリプションに存在しません。 ただし、他のユーザーはそのリソースの URI の取得を許可しています。 この場合、サインイン後に URI を使用して Data Lake Store に接続できます。 以下の手順を参照してください。

1. ストレージ エクスプローラーを開きます。
2. 左側のウィンドウで **[Local and Attached]\(ローカルで接続済み\)** を展開します。
3. **[Data Lake Store]** を右クリックし、コンテキスト メニューから **[Connect to Data Lake Store]\(Data Lake Store に接続する\)** を選択します。

    ![Data Lake Store への接続のコンテキスト メニュー](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. URI を入力すると、ツールが、入力した URL の場所に移動します。

    ![Data Lake Store への接続のダイアログ](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![Data Lake Store への接続の結果](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="search-for-storage-accounts"></a>ストレージ アカウントを検索する

ストレージ リソースを見つける必要があるとき、その場所がわからない場合は、左側のウィンドウの上部にある検索ボックスを使用してリソースを検索することができます。

検索ボックスへの入力中、左側のウィンドウには、その時点で入力してある検索の値に一致するすべてのリソースが表示されます。 たとえば次のスクリーンショットは、**endpoints** を検索するようすを示しています。

![Storage account search][23]

> [!NOTE]
> 検索の実行時間を短縮するため、探している項目を含んでいないサブスクリプションは、**[Account Management Panel]\(アカウント管理パネル\)** を使用して選択を解除してください。 また、ノードを右クリックし、**[Search From Here]\(ここから検索\)** を選択することで、特定のノードから検索を開始することもできます。
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

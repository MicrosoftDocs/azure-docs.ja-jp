---
title: "ストレージ エクスプローラー (プレビュー) の概要 | Microsoft Docs"
description: "ストレージ エクスプローラー (プレビュー) を使用した Azure ストレージ リソースの管理"
services: storage
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: kraigb
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 0abc33bb5b0ffe46397baa5a2d53637bc3f2984c
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="get-started-with-storage-explorer-preview"></a>ストレージ エクスプローラー (プレビュー) の概要
## <a name="overview"></a>概要
Azure ストレージ エクスプローラー (プレビュー) は、Windows、macOS、Linux で Azure Storage のデータを簡単に操作できるスタンドアロン アプリです。 この記事では、Azure ストレージ アカウントへの接続と管理に関するさまざまな方法を紹介します。

![Microsoft Azure ストレージ エクスプローラー (プレビュー)][15]

## <a name="prerequisites"></a>前提条件
* [ストレージ エクスプローラー (プレビュー) をダウンロードしてインストールする](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>ストレージ アカウントまたはサービスに接続する
ストレージ エクスプローラー (プレビュー) には、ストレージ アカウントに対する接続方法がいくつか用意されています。 たとえば、次のようなことができます。
* 自分の Azure サブスクリプションに関連付けられているストレージ アカウントに接続する。
* 他の Azure サブスクリプションから共有されているストレージ アカウントとサービスに接続する。
* Azure ストレージ エミュレーターを使ってローカル ストレージに接続し、そのローカル ストレージを管理する。 

さらに、国内外の Azure のストレージ アカウントを使用できます。

* [Azure サブスクリプションに接続する](#connect-to-an-azure-subscription): 自分の Azure サブスクリプションに属するストレージ リソースを管理します。
* [ローカル開発ストレージを操作する](#work-with-local-development-storage): Azure ストレージ エミュレーターを使ってローカル ストレージを管理します。
* [外部ストレージにアタッチする](#attach-or-detach-an-external-storage-account): ストレージ アカウントの名前、キー、およびエンドポイントを使って、別の Azure サブスクリプションに属するストレージ リソースや国内の Azure クラウドにあるストレージ リソースを管理します。
* [SAS を使ってストレージ アカウントをアタッチする](#attach-storage-account-using-sas): Shared Access Signature (SAS) を使って、別の Azure サブスクリプションに属するストレージ リソースを管理します。
* [SAS を使ってサービスをアタッチする](#attach-service-using-sas): SAS を使って、別の Azure サブスクリプションに属する特定のストレージ サービス (BLOB コンテナー、キュー、またはテーブル) を管理します。

## <a name="connect-to-an-azure-subscription"></a>Azure サブスクリプションに接続する
> [!NOTE]
> Azure アカウントを持っていない場合は、[無料試用版にサインアップする](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)か、[Visual Studio サブスクライバー特典を有効](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)にしてください。
>
>

1. ストレージ エクスプローラー (プレビュー) で、 **[Azure アカウントの設定]**を選択します。

    ![[Azure アカウントの設定]][0]

2. 左側のウィンドウに、サインインしている Microsoft アカウントがすべて表示されます。 別のアカウントに接続するには、**[アカウントの追加]** を選択し、表示される指示に従って、少なくとも 1 つのアクティブな Azure サブスクリプションと関連付けられている Microsoft アカウントでサインインします。

    >[!NOTE]
    >特定の国のみを対象に提供している Azure への接続 (Azure ドイツ、Azure Government、Azure 中国などにサインインして接続すること) は現在、サポートされていません。 特定の国の Azure ストレージ アカウントに接続する方法については、「外部ストレージ アカウントをアタッチまたはデタッチする」を参照してください。

3. Microsoft アカウントでのサインインに成功すると、左側のウィンドウに、そのアカウントに関連付けられている Azure サブスクリプションが表示されます。 操作する Azure サブスクリプションを選択してから、**[適用]** を選択します  (**[すべてのサブスクリプション]** チェック ボックスをオンまたはオフにすることで、一覧の Azure サブスクリプションがすべて選択された状態と、1 つも選択されていない状態を切り替えることができます)。

    ![Select Azure subscriptions][3]  
    左側のウィンドウに、選択した Azure サブスクリプションに関連付けられているストレージ アカウントが表示されます。

    ![選択された Azure サブスクリプション][4]

## <a name="connect-to-an-azure-stack-subscription"></a>Azure Stack サブスクリプションに接続する

Azure Stack サブスクリプションに接続する方法については、「[Connect Storage Explorer to an Azure Stack subscription (ストレージ エクスプローラーを Azure Stack サブスクリプションに接続する)](azure-stack/azure-stack-storage-connect-se.md)」を参照してください。

## <a name="work-with-local-development-storage"></a>ローカル開発ストレージを操作する
ストレージ エクスプローラー (プレビュー) では、Azure ストレージ エミュレーターを使ってローカル ストレージを操作することができます。 この方法を使えば、Azure ストレージ エミュレーターによってストレージ アカウントがエミュレートされるため、Azure にデプロイされたストレージ アカウントがなくても、ストレージ関連のコードを記述したり、ストレージをテストしたりできます。

> [!NOTE]
> 現時点では、Azure ストレージ エミュレーターは Windows に対してのみサポートされています。
>
>

1. ストレージ エクスプローラー (プレビュー) の左側のウィンドウで、**[(Local and Attached) ((ローカルおよびアタッチ済み))]** > **[ストレージ アカウント]** > **[(開発)]** ノードの順に展開します。

    ![Local development node][21]

2. Azure ストレージ エミュレーターをインストールしていない場合は、情報バーを使用してインストールするよう求められます。 情報バーが表示されている場合は、**[最新バージョンのダウンロード]** を選択し、エミュレーターをインストールします。

    ![Download Azure Storage Emulator prompt][22]

3. エミュレーターがインストールされたら、ローカルの BLOB、キュー、テーブルを作成および操作できるようになります。 それぞれの種類のストレージ アカウントを操作する方法については、以下のいずれかを参照してください。

    * [Azure Blob Storage リソースの管理](vs-azure-tools-storage-explorer-blobs.md)
    * Azure ファイル共有ストレージ リソースの管理: "*近日公開予定*"
    * Azure Queue Storage リソースの管理: "*近日公開予定*"
    * Azure Table Storage リソースの管理: "*近日公開予定*"

## <a name="attach-or-detach-an-external-storage-account"></a>外部ストレージ アカウントをアタッチまたはデタッチする
ストレージ エクスプローラー (プレビュー) には、ストレージ アカウントを簡単に共有できるように、外部ストレージ アカウントにアタッチする機能が用意されています。 このセクションでは、外部ストレージ アカウントにアタッチ (または外部ストレージ アカウントからデタッチ) する方法について説明します。

### <a name="get-the-storage-account-credentials"></a>ストレージ アカウントの資格情報を取得する
外部ストレージ アカウントを共有するには、まずそのアカウントの所有者がアカウントの資格情報 (アカウント名とキー) を取得し、その情報を、その (外部) アカウントへのアタッチを望んでいるユーザーと共有する必要があります。 Azure Portal からストレージ アカウントの資格情報を取得する手順は、以下のとおりです。

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. **[参照]**を選択します。

3. **[ストレージ アカウント]**を選択します。

4. **[ストレージ アカウント]** ブレードで目的のストレージ アカウントを選択します。

5. 選択したストレージ アカウントの **[設定]** ブレードで、**[アクセス キー]** を選択します。

    ![Access Keys option][5]

6. **[アクセス キー]** ブレードで、ストレージ アカウントにアタッチするときに使用する **[ストレージ アカウント名]** と **[key1]** の値をコピーします。

    ![アクセス キー][6]

### <a name="attach-to-an-external-storage-account"></a>外部ストレージ アカウントにアタッチする
外部ストレージ アカウントにアタッチするには、アカウントの名前とキーが必要になります。 「ストレージ アカウントの資格情報を取得する」セクションでは、Azure Portal からこれらの値を取得する方法を説明しました。 ただし、ポータルでは、アカウント キーが **key1** という名前になっています。 このため、ストレージ エクスプローラーでアカウント キーの入力が必要な局面があれば、**key1** の値を入力してください。

1. ストレージ エクスプローラー (プレビュー) で、 **[Connect to Azure storage (Azure ストレージに接続)]**を選択します。

    ![Connect to Azure storage option][23]

2. **[Azure Storage へ接続]** ダイアログ ボックスで、アカウント キー (Azure Portal から取得した **key1** の値) を指定し、**[次へ]** を選択します。

    > [!NOTE]
    > 特定の国の Azure のストレージ アカウントのストレージ接続文字列を入力できます。 たとえば、Azure ドイツのストレージ アカウントに接続するには、次のような接続文字列を入力します。 
    >
    >* DefaultEndpointsProtocol=https
    >* AccountName=cawatest03
    >* AccountKey=<storage_account_key>
    >* EndpointSuffix=core.cloudapi.de
    
    >接続文字列は、「ストレージ アカウントの資格情報を取得する」セクションで説明した方法で Azure Portal から取得できます。

    ![[Azure Storage へ接続] ダイアログ][24]

3. **[Attach External Storage (外部ストレージのアタッチ)]** ダイアログ ボックスで、**[アカウント名]** ボックスにストレージ アカウント名を入力し、必要な他の設定を指定してから、**[次へ]** を選択します。

    ![[Attach External Storage (外部ストレージのアタッチ)] ダイアログ][8]

4. **[接続の概要]** ダイアログ ボックスで、情報を確認します。 設定を変更する場合は、**[戻る]** を選択し、必要な設定をもう一度入力します。 

5. **[接続]**を選択します。

6. 接続が正常に完了すると、外部ストレージ アカウントが表示されます。外部ストレージ アカウントの場合には、ストレージ アカウント名に "**(外部)**" というテキストが付きます。

    ![Result of connecting to an external storage account][9]

### <a name="detach-from-an-external-storage-account"></a>外部ストレージ アカウントからデタッチする
1. デタッチする外部ストレージ アカウントを右クリックし、**[デタッチ]** を選択します。

    ![Detach from storage option][10]

2. 確認メッセージで **[はい]** を選択して、外部ストレージ アカウントからのデタッチを確定します。

## <a name="attach-a-storage-account-by-using-an-sas"></a>SAS を使ってストレージ アカウントをアタッチする
[SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) を使うと、Azure サブスクリプションの管理者が Azure サブスクリプションの資格情報を提供しなくても、ストレージ アカウントへのアクセスを一時的に許可できるようになります。

このシナリオをわかりやすく説明するために、Azure サブスクリプションの管理者である UserA が UserB に特定のアクセス許可を付与し、一時的にストレージ アカウントへのアクセスを許可する場面を考えてみます。

1. UserA は、目的のアクセス許可と一定の期間が設定された SAS (ストレージ アカウントへの接続文字列で構成) を生成します。

2. UserA は、ストレージ アカウントへのアクセスを希望しているユーザー (この例では UserB) と SAS を共有します。  

3. UserB はストレージ エクスプローラー (プレビュー) からその SAS を使用し、UserA に属するアカウントにアタッチします。

### <a name="get-an-sas-for-the-account-you-want-to-share"></a>共有するアカウントの SAS を取得する
1. ストレージ エクスプローラー (プレビュー) で、共有するストレージ アカウントを右クリックし、**[Get Shared Access Signature (Shared Access Signature の取得)]**を選択します。

    ![Get SAS context menu option][13]

2. **[Shared Access Signature]** ダイアログ ボックスで、アカウントに対して期間とアクセス許可を指定し、**[作成]** を選択します。

    ![SAS の取得ダイアログ ボックス][14]  
    **[Shared Access Signature]** ダイアログ ボックスが開き、SAS が表示されます。

3. **[接続文字列]** の横にある **[コピー]** を選択してクリップボードにコピーし、**[閉じる]** を選択します。

### <a name="attach-to-the-shared-account-by-using-the-sas"></a>SAS を使って共有アカウントにアタッチする
1. ストレージ エクスプローラー (プレビュー) で、 **[Connect to Azure storage (Azure ストレージに接続)]**を選択します。

    ![Connect to Azure storage option][23]

2. **[Azure Storage へ接続]** ダイアログ ボックスで、接続文字列を指定し、**[次へ]** を選択します。

    ![[Azure Storage へ接続] ダイアログ][24]

3. **[接続の概要]** ダイアログ ボックスで、情報を確認します。 変更する場合は、**[戻る]** を選択して、必要な設定を入力します。 

4. **[接続]**を選択します。

5. アタッチされると、指定したアカウント名に **(SAS)** というテキストが追加された状態でストレージ アカウントが表示されます。

    ![SAS を使ってアカウントをアタッチした結果][17]

## <a name="attach-a-service-by-using-an-sas"></a>SAS を使ってサービスをアタッチする
「SAS を使ってストレージ アカウントをアタッチする」セクションでは、Azure サブスクリプションの管理者がストレージ アカウントの SAS を生成および共有し、ストレージ アカウントへの一時的なアクセスを許可する方法を説明しました。 これと同じように、ストレージ アカウント内の特定のサービス (BLOB コンテナー、キュー、またはテーブル) の SAS を生成できます。  

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>共有するサービスの SAS を生成する
この場合のサービスは、BLOB、コンテナー、キュー、またはテーブルです。 表示されているサービスの SAS を生成するには、以下のいずれかを参照してください。

* [BLOB コンテナーの SAS を取得する](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* ファイル共有の SAS を取得する: "*近日公開予定*"
* キューの SAS を取得する: "*近日公開予定*"
* テーブルの SAS を取得する: "*近日公開予定*"

### <a name="attach-to-the-shared-account-service-by-using-the-sas"></a>SAS を使って共有アカウント サービスにアタッチする
1. ストレージ エクスプローラー (プレビュー) で、 **[Connect to Azure storage (Azure ストレージに接続)]**を選択します。

    ![Connect to Azure storage option][23]

2. **[Azure Storage へ接続]** ダイアログ ボックスで SAS の URI を指定し、**[次へ]** を選択します。

    ![[Azure Storage へ接続] ダイアログ][24]

3. **[接続の概要]** ダイアログ ボックスで、情報を確認します。 変更する場合は、**[戻る]** を選択して、必要な設定を入力します。 

4. **[接続]**を選択します。

5. アタッチが終わると、新たにアタッチされたサービスが **[(Service SAS) ((サービス SAS))]** ノードに表示されます。

    ![SAS を使って共有サービスをアタッチした結果][20]

## <a name="search-for-storage-accounts"></a>ストレージ アカウントを検索する
多数のストレージ アカウントがある場合は、左側のウィンドウの上部にある検索ボックスを使用すると、特定のストレージ アカウントをすばやく見つけることができます。

検索ボックスへの入力中、左側のウィンドウには、その時点で入力してある検索の値に一致するストレージ アカウントが表示されます。 たとえば、**tarcher** という文字が含まれるストレージ アカウントをすべて検索した場合の表示は、以下のスクリーンショットのようになります。

![Storage account search][11]

## <a name="next-steps"></a>次のステップ
* [ストレージ エクスプローラー (プレビュー) を使用した Azure Blob Storage リソースの管理](vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
[25]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-certificate-azure-stack.png
[26]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/export-root-cert-azure-stack.png
[27]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/import-azure-stack-cert-storage-explorer.png
[28]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-target-azure-stack.png
[29]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-azure-stack-account.png
[30]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-accounts-azure-stack.png
[31]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/azure-stack-storage-account-list.png


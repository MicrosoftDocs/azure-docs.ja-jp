---
title: "Azure Portal で Data Lake Store の使用を開始する | Microsoft Docs"
description: "Azure Portal を使用して、Data Lake Store アカウントを作成し、Data Lake Store で基本的な操作を実行します"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: fea324d0-ad1a-4150-81f0-8682ddb4591c
ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: db53f991aab10bace2d253fa96f739f99640bd77
ms.lasthandoff: 01/24/2017


---
# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Azure ポータルで Azure Data Lake Store の使用を開始する
> [!div class="op_single_selector"]
> * [ポータル](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Azure ポータルを使用して、Azure Data Lake Store アカウントを作成し、フォルダーの作成、データ ファイルのアップロードとダウンロード、アカウントの削除などの基本操作を行う方法について説明します。Data Lake Store の詳細については、「[Azure Data Lake Store の概要](data-lake-store-overview.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

## <a name="do-you-learn-faster-with-videos"></a>ビデオで速習する
Data Lake Store の基本操作については以下のビデオをご覧ください。

* [Data Lake Store アカウントを作成する](https://mix.office.com/watch/1k1cycy4l4gen)
* [データ エクスプローラーで Data Lake Store のデータを管理する](https://mix.office.com/watch/icletrxrh6pc)

## <a name="create-an-azure-data-lake-store-account"></a>Azure Data Lake Store アカウントを作成する
1. 新しい [Azure ポータル](https://portal.azure.com)にサインオンします。
2. **[新規]**、**[データ + ストレージ]**、**[Azure Data Lake Store]** の順にクリックします。 **[Azure Data Lake Store]** ブレードに表示された情報を確認し、ブレードの左下隅にある **[作成]** をクリックします。
3. **[新しい Data Lake Store]** ブレードで、次の画面キャプチャに示すように値を指定します。
   
    ![新しい Azure Data Lake Store アカウントの作成](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "新しい Azure Data Lake アカウントの作成")
   
   * **名前**。 Data Lake Store アカウントの一意の名前を入力します。
   * **サブスクリプション**。 新しい Data Lake Store アカウントを作成するサブスクリプションを選択します。
   * **リソース グループ**。 既存のリソース グループを選択するか、**[新規作成]** をクリックしてリソース グループを作成します。 リソース グループは、1 つのアプリケーションの関連リソースを保持するコンテナーです。 詳細については、[Azure のリソース グループ](../azure-resource-manager/resource-group-overview.md#resource-groups)に関する記事をご覧ください。
   * **[場所]**: Data Lake Store アカウントを作成する場所を選択します。
   * **暗号化設定**。 Data Lake Store アカウントを暗号化するかどうかを選択できます。 暗号化する場合は、アカウントのデータの暗号化に使用するマスター暗号化キーを管理する方法も指定できます。
     
     * (オプション) 暗号化しない場合は、ドロップダウンから **[暗号化を有効にしない]** を選択します。
     * (既定) Azure Data Lake Store で暗号化キーを管理する場合は、**[Azure Data Lake で管理されるキーを使用します]** を選択します。
       
         ![Data Lake Store の暗号化](./media/data-lake-store-get-started-portal/adls-encryption-1.png "Data Lake Store の暗号化")
     * (オプション) Azure Key Vault にある独自のキーを使用する場合は、**[Azure Key Vault からキーを選択します]** を選択します。 このオプションでは、Key Vault アカウントとキーがまだない場合にこれらを作成することもできます。
       
         ![Data Lake Store の暗号化](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Data Lake Store の暗号化")
       
       **[暗号化設定]** ブレードで **[OK]** をクリックします。
       
       > [!NOTE]
       > Azure Key Vault のキーを使用して Data Lake Store アカウントの暗号化を構成する場合は、Azure Key Vault にアクセスするために、Azure Data Lake Store アカウントのアクセス許可を割り当てる必要があります。 この手順については、「[Azure Key Vault にアクセス許可を割り当てる](#assign-permissions-to-the-azure-key-vault)」をご覧ください。
       > 
       > 
4. **[作成]**をクリックします。 アカウントをダッシュボードにピン留めすることを選択した場合、ダッシュボードに戻ると、Data Lake Store アカウントのプロビジョニングの進行状況を確認できます。 Data Lake Store アカウントのプロビジョニングが完了すると、アカウントのブレードが表示されます。

## <a name="assign-permissions-to-the-azure-key-vault"></a>Azure Key Vault にアクセス許可を割り当てる
Azure Key Vault のキーを使用して Data Lake Store アカウントの暗号化を構成した場合は、Data Lake Store アカウントと Azure Key Vault アカウントの間でアクセスを構成する必要があります。 そのためには、次の手順を実行します。

1. Azure Key Vault のキーを使用している場合、Data Lake Store アカウントのブレードの上部に警告が表示されます。 この警告をクリックして、**[Key Vault アクセス許可の構成]** ブレードを開きます。
   
    ![Data Lake Store の暗号化](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Data Lake Store の暗号化")
2. ブレードには、アクセスを構成する&2; つのオプションが表示されます。
   
   * 最初のオプションでは、**[アクセス許可の付与]** をクリックしてアクセスを構成します。 最初のオプションは、Data Lake Store アカウントを作成したユーザーが Azure Key Vault の管理者でもある場合にのみ有効になります。
   * もう&1; つのオプションでは、ブレードに表示されている PowerShell コマンドレットを実行します。 この場合、Azure Key Vault の所有者であるか、Azure Key Vault に対するアクセス許可を付与できる必要があります。 コマンドレットを実行したら、ブレードに戻り、**[有効にする]** をクリックしてアクセスを構成します。

## <a name="a-namecreatefolderacreate-folders-in-azure-data-lake-store-account"></a><a name="createfolder"></a>Azure Data Lake Store アカウントにフォルダーを作成する
Data Lake Store アカウントにフォルダーを作成し、データの管理と保存を行うことができます。

1. 先ほど作成した Data Lake Store アカウントを開きます。 左側のウィンドウで、**[参照]**、**[Data Lake Store]** の順にクリックし、[Data Lake Store] ブレードで、フォルダーを作成するアカウント名をクリックします。 スタート画面にアカウントをピン留めした場合は、そのアカウントのタイルをクリックします。
2. Data Lake Store アカウントのブレードで、 **[データ エクスプローラー]**をクリックします。
   
    ![Data Lake Store アカウントでのフォルダーの作成](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Data Lake Store アカウントでのフォルダーの作成")
3. Data Lake Store アカウントのブレードで、**[新しいフォルダー]** をクリックし、新しいフォルダーの名前を入力して、**[OK]** をクリックします。
   
    ![Data Lake Store アカウントでのフォルダーの作成](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Data Lake Store アカウントでのフォルダーの作成")
   
    新しく作成されたフォルダーが **[データ エクスプローラー]** ブレードに表示されます。 レベルに上限なく入れ子になったフォルダーを作成できます。
   
    ![Data Lake アカウントでのフォルダーの作成](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Data Lake アカウントでのフォルダーの作成")

## <a name="a-nameuploaddataaupload-data-to-azure-data-lake-store-account"></a><a name="uploaddata"></a>Azure Data Lake Store アカウントにデータをアップロードする
データは、Azure Data Lake Store アカウントのルート レベルに直接をアップロードすることも、アカウント内に作成したフォルダーにアップロードすることもできます。 次の画面キャプチャでは、 **[データ エクスプローラー]** ブレードからサブフォルダーにファイルをアップロードする手順を示しています。 この画面キャプチャの場合、ファイルは、階層リンクで示されているサブフォルダー (赤色の四角形で示されています) にアップロードされます。

アップロードするいくつかのサンプル データを探している場合は、 **Azure Data Lake Git リポジトリ** から [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)フォルダーを取得できます。

![データのアップロード](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "データのアップロード")

## <a name="a-namepropertiesaproperties-and-actions-available-on-the-stored-data"></a><a name="properties"></a>保存データに対して使用できるプロパティと操作
新しく追加したファイルをクリックし、 **[プロパティ]** ブレードを開きます。 このブレードでは、そのファイルに関連付けられたプロパティと、そのファイルに対して実行できる操作を利用できます。 また、次の画面キャプチャで赤い四角形で強調表示されている、Azure Data Lake Store アカウント内のファイルへの完全なパスをコピーすることもできます。

![データのプロパティ](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "データのプロパティ")

* **[プレビュー]** をクリックすると、ブラウザーから直接、ファイルのプレビューが表示されます。 プレビューの形式を指定することもできます。 **[プレビュー]** をクリックし、**[ファイルのプレビュー]** ブレードの **[形式]** をクリックします。**[ファイルのプレビュー形式]** ブレードで、表示する行数、使用するエンコード、使用する区切り記号などのオプションを指定します。
  
  ![ファイルのプレビュー形式](./media/data-lake-store-get-started-portal/ADL.File.Preview.png "ファイルのプレビュー形式")
* ファイルをコンピューターにダウンロードするには、 **[ダウンロード]** をクリックします。
* ファイルの名前を変更するには、 **[ファイル名の変更]** をクリックします。
* ファイルを削除するには、 **[ファイルの削除]** をクリックします。

## <a name="secure-your-data"></a>データのセキュリティ保護
Azure Data Lake Store アカウントに保存したデータは、Azure Active Directory とアクセス制御 (ACL) を使用してセキュリティで保護することができます。 その方法の詳細については、「 [Azure Data Lake Store でのデータのセキュリティ保護](data-lake-store-secure-data.md)」を参照してください。

## <a name="delete-azure-data-lake-store-account"></a>Azure Data Lake Store アカウントの削除
Azure Data Lake Store アカウントを削除するには、Data Lake Store ブレードで **[削除]**をクリックします。 この操作を確認するために、削除するアカウントの名前を入力するよう求められます。 アカウントの名前を入力し、 **[削除]**をクリックします。

![Data Lake アカウントの削除](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Data Lake アカウントの削除")

## <a name="next-steps"></a>次のステップ
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
* [Data Lake Store で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Data Lake Store の診断ログへのアクセス](data-lake-store-diagnostic-logs.md)



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
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: fa13266993017374ba49709f8e22fbe6b03a28c7
ms.contentlocale: ja-jp
ms.lasthandoff: 08/07/2017

---
# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Azure Portal で Azure Data Lake Store の使用を開始する
> [!div class="op_single_selector"]
> * [ポータル](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Azure Portal を使用して、Azure Data Lake Store アカウントを作成し、フォルダーの作成、データ ファイルのアップロードとダウンロード、アカウントの削除などの基本操作を行う方法について説明します。詳細については、「[Azure Data Lake Store の概要](data-lake-store-overview.md)」を参照してください。

次の 2 つのビデオには、この記事の説明と同じ情報が含まれています。

* [Data Lake Store アカウントを作成する](https://mix.office.com/watch/1k1cycy4l4gen)
* [データ エクスプローラーで Data Lake Store のデータを管理する](https://mix.office.com/watch/icletrxrh6pc)

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

## <a name="create-an-azure-data-lake-store-account"></a>Azure Data Lake Store アカウントを作成する

1. 新しい [Azure Portal](https://portal.azure.com) にサインオンします。
2. **[新規]**、**[データ + ストレージ]**、**[Azure Data Lake Store]** の順にクリックします。 **[Azure Data Lake Store]** ブレードに表示された情報を確認し、ブレードの左下隅にある **[作成]** をクリックします。
3. **[新しい Data Lake Store]** ブレードで、次のスクリーンショットに示されているように値を指定します。
   
    ![新しい Azure Data Lake Store アカウントの作成](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "新しい Azure Data Lake アカウントの作成")
   
   * **名前**。 Data Lake Store アカウントの一意の名前を入力します。
   * **サブスクリプション**。 新しい Data Lake Store アカウントを作成するサブスクリプションを選択します。
   * **リソース グループ**。 既存のリソース グループを選択するか、**[新規作成]** をクリックしてリソース グループを作成します。 リソース グループは、1 つのアプリケーションの関連リソースを保持するコンテナーです。 詳細については、[Azure のリソース グループ](../azure-resource-manager/resource-group-overview.md#resource-groups)に関する記事をご覧ください。
   * **[場所]**: Data Lake Store アカウントを作成する場所を選択します。
   * **暗号化設定**。 次の 3 つのオプションがあります。
     
     * **暗号化を有効にしない**。
     * **Azure Data Lake で管理されるキーを使用する**。  Azure Data Lake Store に暗号化キーを管理させる場合。
     * **Azure Key Vault からキーを選択します**。 既存の Azure Key Vault を選択するか、新しい Key Vault を作成することができます。 Key Vault のキーを使用するには、Azure Key Vault にアクセスするために、Azure Data Lake Store アカウントのアクセス許可を割り当てる必要があります。 手順については、「[Azure Key Vault にアクセス許可を割り当てる](#assign-permissions-to-azure-key-vault)」を参照してください。
       
        ![Data Lake Store の暗号化](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Data Lake Store の暗号化")
       
        **[暗号化設定]** ブレードで **[OK]** をクリックします。

        詳細については、「[Azure Data Lake Store でのデータの暗号化](./data-lake-store-encryption.md)」を参照してください。

4. **[作成]**をクリックします。 アカウントをダッシュボードにピン留めすることを選択した場合、ダッシュボードに戻ると、Data Lake Store アカウントのプロビジョニングの進行状況を確認できます。 Data Lake Store アカウントのプロビジョニングが完了すると、アカウントのブレードが表示されます。

Azure Resource Manager テンプレートを使用して、Data Lake Store アカウントを作成することもできます。 以下のテンプレートは、[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?term=data+lake+store)からアクセスすることができます。

- データ暗号化なし: [Deploy Azure Data Lake Store account with no data encryption (Azure Data Lake Store アカウントをデータ暗号化なしでデプロイする)](https://azure.microsoft.com/en-us/resources/templates/101-data-lake-store-no-encryption/)。
- Data Lake Store を使用したデータ暗号化付きで: [Deploy Data Lake Store account with encryption(Data Lake) (暗号化 (Data Lake) 付きで Data Lake Store アカウントをデプロイする)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)。
- Azure Key Vault を使用したデータ暗号化付きで: [Deploy Data Lake Store account with encryption(Key Vault) (暗号化 (Key Vault) 付きで Data Lake Store アカウントをデプロイする)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)。

### <a name="assign-permissions-to-azure-key-vault"></a>Azure Key Vault にアクセス許可を割り当てる
Azure Key Vault のキーを使用して Data Lake Store アカウントの暗号化を構成した場合は、Data Lake Store アカウントと Azure Key Vault アカウントの間でアクセスを構成する必要があります。 そのためには、次の手順を実行します。

1. Azure Key Vault のキーを使用している場合、Data Lake Store アカウントのブレードの上部に警告が表示されます。 この警告をクリックして、**[Key Vault アクセス許可の構成]** ブレードを開きます。
   
    ![Data Lake Store の暗号化](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Data Lake Store の暗号化")
2. ブレードには、アクセスを構成する 2 つのオプションが表示されます。
   
   * 最初のオプションでは、**[アクセス許可の付与]** をクリックしてアクセスを構成します。 最初のオプションは、Data Lake Store アカウントを作成したユーザーが Azure Key Vault の管理者でもある場合にのみ有効になります。
   * もう 1 つのオプションでは、ブレードに表示されている PowerShell コマンドレットを実行します。 この場合、Azure Key Vault の所有者であるか、Azure Key Vault に対するアクセス許可を付与できる必要があります。 コマンドレットを実行したら、ブレードに戻り、**[有効にする]** をクリックしてアクセスを構成します。

## <a name="createfolder"></a>Azure Data Lake Store アカウントにフォルダーを作成する
Data Lake Store アカウントにフォルダーを作成し、データの管理と保存を行うことができます。

1. 作成した Data Lake Store アカウントを開きます。 左側のウィンドウで、**[参照]**、**[Data Lake Store]** の順にクリックし、[Data Lake Store] ブレードで、フォルダーを作成するアカウント名をクリックします。 スタート画面にアカウントをピン留めした場合は、そのアカウントのタイルをクリックします。
2. Data Lake Store アカウントのブレードで、 **[データ エクスプローラー]**をクリックします。
   
    ![Data Lake Store アカウントでのフォルダーの作成](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Data Lake Store アカウントでのフォルダーの作成")
3. Data Lake Store アカウントのブレードで、**[新しいフォルダー]** をクリックし、新しいフォルダーの名前を入力して、**[OK]** をクリックします。
   
    ![Data Lake Store アカウントでのフォルダーの作成](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Data Lake Store アカウントでのフォルダーの作成")
   
    新しく作成されたフォルダーが **[データ エクスプローラー]** ブレードに表示されます。 レベルに上限なく入れ子になったフォルダーを作成できます。
   
    ![Data Lake アカウントでのフォルダーの作成](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Data Lake アカウントでのフォルダーの作成")

## <a name="uploaddata"></a>Azure Data Lake Store アカウントにデータをアップロードする
データは、Azure Data Lake Store アカウントのルート レベルに直接をアップロードすることも、アカウント内に作成したフォルダーにアップロードすることもできます。 次のスクリーンショットでは、**[データ エクスプローラー]** ブレードからサブフォルダーにファイルをアップロードする手順を示しています。 このスクリーンショットの場合、ファイルは、階層リンクで示されているサブフォルダー (赤色の四角形で示されています) にアップロードされます。

アップロードするいくつかのサンプル データを探している場合は、 **Azure Data Lake Git リポジトリ** から [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)フォルダーを取得できます。

![データのアップロード](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "データのアップロード")

## <a name="properties"></a>保存データに対して使用できるプロパティと操作
新しく追加したファイルをクリックし、 **[プロパティ]** ブレードを開きます。 このブレードでは、そのファイルに関連付けられたプロパティと、そのファイルに対して実行できる操作を利用できます。 また、次のスクリーンショットで赤い四角形で強調表示されている、Azure Data Lake Store アカウント内のファイルへの完全なパスをコピーすることもできます。

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



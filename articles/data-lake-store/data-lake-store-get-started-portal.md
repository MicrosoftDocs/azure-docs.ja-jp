---
title: Azure Data Lake Storage Gen1 の使用を開始する - ポータル
description: Azure portal を使用して Data Lake Storage Gen1 アカウントを作成し、アカウントで基本的な操作を実行します。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 428ed96c3223e644b0c78712723231a5fabbdc77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91578582"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Azure Portal で Azure Data Lake Storage Gen1 の使用を開始する

> [!div class="op_single_selector"]
> * [ポータル](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure portal を使用して、Data Lake Storage Gen1 アカウントを作成し、フォルダーの作成、データ ファイルのアップロードとダウンロード、ご自分のアカウントの削除などの基本操作を行う方法について説明します。詳しくは、「[Azure Data Lake Storage Gen1 の概要](data-lake-store-overview.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 アカウントを作成する

1. 新しい [Azure Portal](https://portal.azure.com) にサインオンします。
2. **[リソースの作成]、[ストレージ]、[Data Lake Storage Gen1]** の順にクリックします。
3. **[新しい Data Lake Storage Gen1]** ブレードで、次のスクリーンショットに示されているように値を指定します。

    ![新しい Data Lake Storage Gen1 アカウントの作成](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "新しい Data Lake Storage Gen1 アカウントを作成する")

   * **名前**。 Data Lake Storage Gen1 アカウント用に一意の名前を入力します。
   * **サブスクリプション**。 新しい Data Lake Storage Gen1 アカウントを作成するサブスクリプションを選択します。
   * **リソース グループ**。 既存のリソース グループを選択するか、 **[新規作成]** をクリックしてリソース グループを作成します。 リソース グループは、1 つのアプリケーションの関連リソースを保持するコンテナーです。 詳細については、[Azure のリソース グループ](../azure-resource-manager/management/overview.md#resource-groups)に関する記事をご覧ください。
   * **[場所]** :Data Lake Storage Gen1 アカウントを作成する場所を選択します。
   * **暗号化設定**。 次の 3 つのオプションがあります。

     * **暗号化を有効にしない**。
     * Data Lake Storage Gen1 で暗号化キーを管理する場合は、**Data Lake Storage Gen1 で管理されるキーを使用します**。
     * **独自のキー コンテナーのキーを使用する**。 既存の Azure Key Vault を選択するか、新しい Key Vault を作成することができます。 Key Vault のキーを使用するには、Azure Key Vault にアクセスするために、Data Lake Storage Gen1 アカウントのアクセス許可を割り当てる必要があります。 手順については、「[Azure Key Vault にアクセス許可を割り当てる](#assign-permissions-to-azure-key-vault)」を参照してください。

        ![新しい Data Lake Storage Gen1 ブレード と [暗号化設定] ブレードのスクリーンショット。](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Data Lake Storage Gen1 の暗号化")

        **[暗号化設定]** ブレードで **[OK]** をクリックします。

        詳細については、「[Azure Data Lake Store でのデータの暗号化](./data-lake-store-encryption.md)」を参照してください。

4. **Create** をクリックしてください。 アカウントをダッシュボードにピン留めすることを選択した場合、ダッシュボードに戻ると、ご自分の Data Lake Storage Gen1 アカウントのプロビジョニングの進行状況を確認できます。 Data Lake Storage Gen1 アカウントのプロビジョニングが完了すると、アカウントのブレードが表示されます。

## <a name="assign-permissions-to-azure-key-vault"></a><a name="assign-permissions-to-azure-key-vault"></a>Azure Key Vault にアクセス許可を割り当てる

Azure Key Vault のキーを使用して Data Lake Storage Gen1 アカウントの暗号化を構成した場合は、Data Lake Storage Gen1 アカウントと Azure Key Vault アカウントの間でアクセスを構成する必要があります。 そのためには、次の手順を実行します。

1. Azure Key Vault のキーを使用している場合、Data Lake Storage Gen1 アカウントのブレードの上部に警告が表示されます。 警告をクリックして **[暗号化]** を開きます。

    ![Data Lake Storage Gen1 アカウントのブレードのスクリーンショット。警告「Key Vault アクセス許可の構成が必要です。設定するには、ここをクリックしてください」が表示されています。](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Data Lake Storage Gen1 の暗号化")
2. ブレードには、アクセスを構成する 2 つのオプションが表示されます。

    ![[暗号化] ブレードのスクリーンショット。](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Data Lake Storage Gen1 の暗号化")

   * 最初のオプションでは、 **[アクセス許可の付与]** をクリックしてアクセスを構成します。 最初のオプションは、Data Lake Storage Gen1 アカウントを作成したユーザーが Azure Key Vault の管理者でもある場合にのみ有効になります。
   * もう 1 つのオプションでは、ブレードに表示されている PowerShell コマンドレットを実行します。 この場合、Azure Key Vault の所有者であるか、Azure Key Vault に対するアクセス許可を付与できる必要があります。 コマンドレットを実行したら、ブレードに戻り、 **[有効にする]** をクリックしてアクセスを構成します。

> [!NOTE]
> Azure Resource Manager テンプレートを使用して、Data Lake Storage Gen1 アカウントを作成することもできます。 以下のテンプレートは、[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?term=data+lake+store)からアクセスすることができます。
> * データ暗号化なし: [Deploy Azure Data Lake Storage Gen1 account with no data encryption](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/) (Azure Data Lake Storage Gen1 アカウントをデータ暗号化なしでデプロイする)。
> * Data Lake Storage Gen1 によるデータ暗号化あり: [Deploy Data Lake Storage Gen1 account with encryption(Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/) (Data Lake Storage Gen1 アカウントを暗号化 (Data Lake) ありでデプロイする)。
> * Azure Key Vault によるデータ暗号化あり:[Deploy Data Lake Storage Gen1 account with encryption(Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/) (Data Lake Storage Gen1 アカウントを暗号化 (Key Vault) ありでデプロイする)。
>
>

## <a name="create-folders"></a><a name="createfolder"></a>フォルダーの作成

ご自分の Data Lake Storage Gen1 アカウントにフォルダーを作成し、データの管理と保存を行うことができます。

1. 作成した Data Lake Storage Gen1 アカウントを開きます。 左側のウィンドウで **[すべてのリソース]** をクリックし、 **[すべてのリソース]** ブレードで、フォルダーの作成先となるアカウント名をクリックします。 スタート画面にアカウントをピン留めした場合は、そのアカウントのタイルをクリックします。
2. Data Lake Storage Gen1 アカウントのブレードで、 **[データ エクスプローラー]** をクリックします。

    ![[データ エクスプローラー] オプションが強調表示されている Data Lake Storage Gen 1 アカウントのブレードのスクリーンショット。](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Data Lake Storage Gen1 アカウントでフォルダーを作成する")
3. [データ エクスプローラー] ブレードで、 **[新しいフォルダー]** をクリックし、新しいフォルダーの名前を入力して、 **[OK]** をクリックします。

    ![[新しいフォルダー] オプションと [新しいフォルダーの作成] テキスト ボックスが強調表示された [データ エクスプローラー] ブレードのスクリーンショット。](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Data Lake Storage Gen1 アカウントでフォルダーを作成する")

    新しく作成されたフォルダーが **[データ エクスプローラー]** ブレードに表示されます。 レベルに上限なく入れ子になったフォルダーを作成できます。

    ![Data Lake アカウントにフォルダーを作成する](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Data Lake アカウントにフォルダーを作成する")

## <a name="upload-data"></a><a name="uploaddata"></a>データのアップロード

データは、Data Lake Storage Gen1 アカウントの root レベルに直接アップロードすることも、アカウント内に作成したフォルダーにアップロードすることもできます。

1. **[データ エクスプローラー]** ブレードで、 **[アップロード]** をクリックします。
2. **[ファイルのアップロード]** ブレードで、アップロードするファイルに移動し、 **[選択されたファイルを追加します]** をクリックします。 複数のファイルを選択してアップロードすることもできます。

    ![データのアップロード](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "データのアップロード")

アップロードするいくつかのサンプル データを探している場合は、 **Azure Data Lake Git リポジトリ** から [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)フォルダーを取得できます。

## <a name="actions-available-on-the-stored-data"></a><a name="properties"></a>保存データに対して実行できるアクション

ファイルに対応する省略記号アイコンをクリックします。ポップアップ メニューで、データに対して実行するアクションをクリックします。

![データのプロパティ](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "データのプロパティ")

## <a name="secure-your-data"></a>データのセキュリティ保護

Data Lake Storage Gen1 アカウントの格納データは、Azure Active Directory とアクセスの制御 (ACL) を使用してセキュリティで保護することができます。 その方法の詳細については、[Azure Data Lake Storage Gen1 でのデータのセキュリティ保護](data-lake-store-secure-data.md)に関するページを参照してください。

## <a name="delete-your-account"></a>アカウントの削除

Data Lake Storage Gen1 アカウントを削除するには、Data Lake Storage Gen1 ブレードで **[削除]** をクリックします。 この操作を確認するために、削除するアカウントの名前を入力するよう求められます。 アカウントの名前を入力し、 **[削除]** をクリックします。

![Data Lake Storage Gen1 アカウントの削除](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Data Lake アカウントの削除")

## <a name="next-steps"></a>次のステップ

* [Data Lake Storage Gen1 を使用してビッグ データの要件に対応する](data-lake-store-data-scenarios.md)
* [Data Lake Storage Gen1 でのデータのセキュリティ保護](data-lake-store-secure-data.md)
* [Data Lake Storage Gen1 で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Storage Gen1 で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)

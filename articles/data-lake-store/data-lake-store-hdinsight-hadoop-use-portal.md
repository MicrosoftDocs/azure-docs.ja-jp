---
title: Azure portal を使用して、Azure Data Lake Storage Gen1 を使用する Azure HDInsight クラスターを作成する | Microsoft Docs
description: Azure portal を使用して、Azure Data Lake Storage Gen1 を使用する HDInsight クラスターを作成および使用します
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 6f9064c6027499fff3a8551ee60722cd66c54dc2
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883430"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Azure portal を使用して、Azure Data Lake Storage Gen1 を使用する HDInsight クラスターを作成する
> [!div class="op_single_selector"]
> * [Azure ポータルの使用](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell の使用 (既定のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell の使用 (追加のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager の使用](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Azure portal を使用して、既定のストレージまたは追加のストレージとして Azure Data Lake Storage Gen1 アカウントを使用して HDInsight クラスターを作成する方法を説明します。 追加のストレージは HDInsight クラスターでは省略可能ですが、業務データは追加のストレージのアカウントに格納することをお勧めします。

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に、次の要件を満たしていることを確認します。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。
* **Data Lake Storage Gen1 アカウント**。 「[Azure portal で Azure Data Lake Storage Gen1 の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。 アカウントのルート フォルダーも作成する必要があります。  このチュートリアルでは、__/clusters__ という名前のルート フォルダーを使用します。
* **Azure Active Directory サービス プリンシパル**。 このチュートリアルでは、Azure Active Directory (Azure AD) でサービス プリンシパルを作成する方法について説明します。 ただし、サービス プリンシパルを作成するには、Azure AD 管理者である必要があります。 管理者である場合は、この前提条件をスキップしてチュートリアルを進めることができます。

    >[!NOTE]
    >Azure AD 管理者である場合にのみ、サービス プリンシパルを作成することができます。 Data Lake Storage Gen1 で HDInsight クラスターを作成する前に、まず Azure AD 管理者がサービス プリンシパルを作成する必要があります。 また、「[証明書を使用したサービス プリンシパルの作成](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)」で説明しているように、サービス プリンシパルは証明書を使って作成する必要があります。
    >

## <a name="create-an-hdinsight-cluster"></a>HDInsight クラスターの作成

このセクションでは、既定のまたは追加のストレージとして Data Lake Storage Gen1 アカウントを使用して HDInsight クラスターを作成します。 この記事では、Data Lake Storage Gen1 アカウントの構成の一部のみを取り上げます。  一般的なクラスターの作成に関する情報および手順については、[HDInsight での Hadoop クラスターの作成](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Data Lake Storage Gen1 を既定のストレージとして使用してクラスターを作成する

**Data Lake Storage Gen1 アカウントを既定のストレージ アカウントとして使用する HDInsight クラスターを作成するには**

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. HDInsight クラスターの作成に関する一般的な情報については、「[クラスターの作成](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters)」を参照してください。
3. **[ストレージ]** ブレードの **[プライマリ ストレージの種類]** で、**[Azure Data Lake Storage Gen1]** を選択して、次の情報を入力します。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "HDInsight クラスターにサービス プリンシパルを追加する")

    - **Data Lake Store アカウントを選択する**:既存の Data Lake Storage Gen1 アカウントを選択します。 既存の Data Lake Storage Gen1 アカウントが必要です。  「[前提条件](#prerequisites)」を参照してください。
    - **ルート パス**:クラスターに固有のファイルが格納されるパスを入力します。 このスクリーン ショットでは、__/clusters/myhdiadlcluster/__ です。この場合、__/clusters__ フォルダーが存在する必要があり、Portal では *myhdicluster* フォルダーが作成されます。  *myhdicluster* がクラスター名です。
    - **[Data Lake Store アクセス]**:Data Lake Storage Gen1 アカウントと HDInsight クラスターの間のアクセスを構成します。 手順については、「Data Lake Storage Gen1 のアクセスの構成」を参照してください。
    - **追加のストレージ アカウント**:クラスターの追加のストレージ アカウントとして Azure ストレージ アカウントを追加します。 Data Lake Storage Gen1 アカウントの追加は、プライマリ ストレージ タイプとして Data Lake Storage Gen1 アカウントを構成する際に、他の Data Lake Storage Gen1 アカウントのデータに対するクラスターのアクセス許可を与えることで完了します。 「Data Lake Storage Gen1 のアクセスの構成」を参照してください。

4. **[Data Lake Store アクセス]** で、**[選択]** をクリックし、[HDInsight での Hadoop クラスターの作成](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)に関するページの説明に従ってクラスターの作成に進みます。


### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Data Lake Storage Gen1 を追加のストレージとして使用してクラスターを作成する

次に、既定のストレージとして Azure ストレージ アカウントを使用し、追加のストレージとして Data Lake Storage Gen1 アカウントを使用して、HDInsight クラスターを作成する方法を説明します。

**Data Lake Storage Gen1 アカウントを追加のストレージ アカウントとして使用する HDInsight クラスターを作成するには**

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. HDInsight クラスターの作成に関する一般的な情報については、「[クラスターの作成](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters)」を参照してください。
3. **[ストレージ]** ブレードの **[プライマリ ストレージの種類]** で、**[Azure Storage]** を選択して、次の情報を入力します。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "HDInsight クラスターにサービス プリンシパルを追加する")

    - **[メソッドの選択]**: 次のいずれかのオプションを使うことができます。

        * Azure サブスクリプションの一部であるストレージ アカウントを指定するには、**[個人用サブスクリプション]** を選択し、ストレージ アカウントを選択します。
        * Azure サブスクリプションの外部にあるストレージ アカウントを指定するには、**[アクセス キー]** を選択し、外部のストレージ アカウントの情報を入力します。

    - **既定のコンテナー**: 既定値を使用するか、独自の名前を指定します。

    - [追加のストレージ アカウント]: 追加のストレージとして Azure ストレージ アカウントを追加します。
    - [Data Lake Store アクセス]: Data Lake Storage Gen1 アカウントと HDInsight クラスターの間のアクセスを構成します。 手順については、「[Data Lake Storage Gen1 のアクセスの構成](#configure-data-lake-storage-gen1-access)」を参照してください。

## <a name="configure-data-lake-storage-gen1-access"></a>Data Lake Storage Gen1 のアクセスの構成 

このセクションでは、Azure Active Directory サービス プリンシパルを使用した、HDInsight クラスターからの Data Lake Storage Gen1 へのアクセスを構成します。 

### <a name="specify-a-service-principal"></a>サービス プリンシパルの指定

Azure Portal から、既存のサービス プリンシパルを使用するか、新しいものを作成することができます。

**Azure portal からサービス プリンシパルを作成するには**

1. [ストレージ] ブレードから **[Data Lake Store アクセス]** をクリックします。
2. **[Data Lake Storage Gen1 アクセス]** ブレードで、**[新規作成]** をクリックします。
3. **[サービス プリンシパル]** をクリックし、サービス プリンシパルを作成する手順に従います。
4. 証明書を今後も使用する場合は、ダウンロードします。 証明書のダウンロードは、追加の HDInsight クラスターを作成するときに同じサービス プリンシパルを使用したい場合に役立ちます。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "HDInsight クラスターにサービス プリンシパルを追加する")

4. **[アクセス]** をクリックして、フォルダーへのアクセスを構成します。  「[ファイルのアクセス許可を構成する](#configure-file-permissions)」を参照してください。


**Azure portal から既存のサービス プリンシパルを使用するには**

1. **[Data Lake Store アクセス]** をクリックします。
1. **[Data Lake Storage Gen1 アクセス]** ブレードで、**[既存のものを使用]** をクリックします。
2. **[サービス プリンシパル]** をクリックし、サービス プリンシパルを選択します。 
3. 選択したサービス プリンシパルに関連付けられている証明書 (.pfx ファイル) をアップロードし、証明書のパスワードを入力します。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "HDInsight クラスターにサービス プリンシパルを追加する")

4. **[アクセス]** をクリックして、フォルダーへのアクセスを構成します。  「[ファイルのアクセス許可を構成する](#configure-file-permissions)」を参照してください。


### <a name="configure-file-permissions"></a>ファイルのアクセス許可を構成する

構成は、アカウントを既定のストレージとして使用するか、追加のストレージとして使用するかによって異なります。

- 既定のストレージとして使用する

    - Data Lake Storage Gen1 アカウントのルート レベルでのアクセス許可
    - HDInsight クラスター記憶域のルート レベルでのアクセス許可。 たとえば、このチュートリアルで使用した __/clusters__ フォルダー。
- 追加のストレージとして使用する

    - ファイル アクセスが必要なフォルダーのアクセス許可。

**Data Lake Storage Gen1 アカウントのルート レベルでアクセス許可を割り当てるには**

1. **[Data Lake Storage Gen1 アクセス]** ブレードで、**[アクセス]** をクリックします。 **[ファイル アクセス許可の選択]** ブレードが開きます。 このブレードには、サブスクリプション内のすべての Data Lake Storage Gen1 アカウントが一覧表示されます。
2. Data Lake Storage Gen1 アカウント名の上にマウス ポインターを置いて (クリックしないでください) チェック ボックスを表示し、そのチェック ボックスを選択します。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "HDInsight クラスターにサービス プリンシパルを追加する")

   既定では、__[読み取り]__、__[書き込み]__、__[実行]__ がすべて選択されています。

3. ページの下部にある **[選択]** をクリックします。
4. **[実行]** をクリックして、アクセス許可を割り当てます。
5. **[Done]** をクリックします。

**HDInsight クラスターのルート レベルでアクセス許可を割り当てるには**

1. **[Data Lake Storage Gen1 アクセス]** ブレードで、**[アクセス]** をクリックします。 **[ファイル アクセス許可の選択]** ブレードが開きます。 このブレードには、サブスクリプション内のすべての Data Lake Storage Gen1 アカウントが一覧表示されます。
1. **[ファイル アクセス許可の選択]** ブレードで、そのコンテンツを表示する Data Lake Storage Gen1 アカウントの名前をクリックします。
2. フォルダーの左側のチェック ボックスを選択して HDInsight クラスター記憶域のルートを選択します。 前のスクリーンショットでは、クラスター記憶域のルートは、Data Lake Storage Gen1 を既定のストレージとして選択したときに指定した __/clusters__ フォルダーです。
3. フォルダーのアクセス許可を設定します。  既定では、[読み取り]、[書き込み]、[実行] がすべて選択されています。
4. ページの下部にある **[選択]** をクリックします。
5. **[実行]** をクリックします。
6. **[Done]** をクリックします。

Data Lake Storage Gen1 を追加のストレージとして使用している場合は、HDInsight クラスターからアクセスするフォルダーに対してのみアクセス許可を割り当てる必要があります。 たとえば、次のスクリーンショットでは、Data Lake Storage Gen1 アカウントの **mynewfolder** フォルダーへのアクセスのみを提供します。

![HDInsight クラスターにサービス プリンシパルのアクセス許可を割り当てる](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "HDInsight クラスターにサービス プリンシパルのアクセス許可を割り当てる")


## <a name="verify-cluster-set-up"></a>クラスターのセットアップを確認する

クラスターのセットアップが完了したら、クラスター ブレードで、次の手順のいずれかまたは両方を実行して結果を確認します。

* クラスターに関連付けられているストレージが、指定した Data Lake Storage Gen1 アカウントであることを確認するには、左側のウィンドウで **[ストレージ アカウント]** をクリックします。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "HDInsight クラスターにサービス プリンシパルを追加する")

* サービス プリンシパルが HDInsight クラスターに正しく関連付けられていることを確認するには、左側のウィンドウで **[Data Lake Storage Gen1 アクセス]** をクリックします。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "HDInsight クラスターにサービス プリンシパルを追加する")


## <a name="examples"></a>例

Data Lake Storage Gen1 をストレージとして使用するクラスターの設定が完了したら、HDInsight クラスターを使用して Data Lake Storage Gen1 に格納されているデータを分析する方法について、以下に示すいくつかの例をご覧ください。

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>(プライマリ ストレージとしての) Data Lake Storage Gen1 アカウントに格納されているデータに対して Hive クエリを実行する

Hive クエリを実行する場合は、Ambari ポータルで提供されている Hive ビュー インターフェイスを使用します。 Ambari Hive ビューの使用方法については、「[HDInsight での Hive View と Hadoop の使用](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md)」をご覧ください。

Data Lake Storage Gen1 アカウント内のデータを操作するときは、いくつかの文字列を変更する必要があります。

たとえば、プライマリ ストレージとして Data Lake Storage Gen1 を使用するクラスターを作成した場合は、データのパスは *adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.net/path/to/file* になります。 Data Lake Storage Gen1 アカウントに格納されているサンプル データからテーブルを作成する Hive クエリは、次のようなステートメントになります。

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

上記のクエリで、
* `adl://hdiadlsg1storage.azuredatalakestore.net/`  は Data Lake Storage Gen1 アカウントのルートです。
* `/clusters/myhdiadlcluster`  はクラスターの作成時に指定したクラスター データのルートです。
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/`  はクエリで使用したサンプル ファイルの場所です。

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>(追加ストレージとしての) Data Lake Storage Gen1 アカウントに格納されているデータに対して Hive クエリを実行する

作成したクラスターで既定のストレージとして Blob Storage を使用している場合、追加ストレージとして使用されている Data Lake Storage Gen1 アカウントにサンプル データは含まれません。 このような場合、Blob Storage から Data Lake Storage Gen1 アカウントにデータを転送してから、上の例に示したようにクエリを実行します。

Blob Storage から Data Lake Storage Gen1 アカウントにデータをコピーする方法については、次の記事をご覧ください。

* [Distcp を使用して Azure Storage Blob と Data Lake Storage Gen1 の間でデータをコピーする](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy を使用して Azure Storage Blob のデータを Data Lake Storage Gen1 にコピーする](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Spark クラスターで Data Lake Storage Gen1 を使用する
Spark クラスターを使用すると、Data Lake Storage Gen1 アカウントに格納されているデータに対して Spark ジョブを実行できます。 詳細については、[HDInsight Spark クラスターを使用した Data Lake Storage Gen1 のデータの分析](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)に関するページをご覧ください。


### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Storm トポロジで Data Lake Storage Gen1 を使用する
Data Lake Storage Gen1 アカウントを使用して、Storm トポロジからデータを書き込むことができます。 このシナリオを実現する方法については、[HDInsight で Apache Storm によって Azure Data Lake Storage Gen1 を使用する方法](../hdinsight/storm/apache-storm-write-data-lake-store.md)に関するページをご覧ください。

## <a name="see-also"></a>関連項目
* [Azure HDInsight クラスターで Data Lake Storage Gen1 を使用する](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell:Data Lake Storage Gen1 を使用する HDInsight クラスターを作成する](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

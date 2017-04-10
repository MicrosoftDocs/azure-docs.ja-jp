---
title: "Azure Portal を使用して、Data Lake Store を使用する Azure HDInsight クラスターを作成する | Microsoft Docs"
description: "Azure Portal を使用して、Azure Data Lake Store を使用する HDInsight クラスターを作成および使用する"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: f7c977dc2e385819dada976afa9497e9a20fd90c
ms.lasthandoff: 03/22/2017


---
# <a name="create-hdinsight-clusters-with-data-lake-store-by-using-the-azure-portal"></a>Azure Portal を使用して、Data Lake Store を使用する HDInsight クラスターを作成する
> [!div class="op_single_selector"]
> * [Azure Portal の使用](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell の使用 (既定のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell の使用 (追加のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager の使用](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

この記事では、Azure Portal を使用して、Azure Data Lake Store にアクセスできる HDInsight クラスターを作成する方法について説明します。 サポートされている種類のクラスターでは、Data Lake Store を既定のストレージまたは追加のストレージ アカウントとして使用できます。 

Data Lake Store を追加のストレージとして使用した場合、クラスターの既定のストレージ アカウントは Azure Blob Storage のままであり、クラスター関連のファイル (ログなど) は引き続きその既定のストレージに書き込まれます。 ただし、処理するデータは Data Lake Store アカウントに格納できます。 Data Lake Store を追加のストレージ アカウントとして使用しても、クラスターからストレージに対する読み取り/書き込みのパフォーマンスや機能には影響しません。

HDInsight で Data Lake Store を使用するための重要な考慮事項を次に示します。

* 既定のストレージとしての Data Lake Store にアクセスできる HDInsight クラスターを作成するオプションは、HDInsight バージョン 3.5 で使用できます。

* 既定のストレージとしての Data Lake Store にアクセスできる HDInsight クラスターを作成するオプションは、HDInsight Premium クラスターでは*使用できません*。

* 追加のストレージとしてのData Lake Store にアクセスできる HDInsight クラスターを作成するオプションは、HDInsight バージョン 3.2、3.4、3.5 で使用できます。

* HBase クラスター (Windows および Linux) の場合、Data Lake Store は既定のストレージとしても追加のストレージとしても*サポートされません*。

* Storm クラスター (Windows および Linux) の場合、Storm トポロジからデータを書き込むために Data Lake Store を使用できます。 また、Data Lake Store を、Storm トポロジから読み取ることができる参照データとして使用することもできます。 詳細については、「 [Storm トポロジで Data Lake Store を使用する](#use-data-lake-store-in-a-storm-topology)」を参照してください。


## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に、次の要件を満たしていることを確認します。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。

* **Azure Data Lake Store アカウント**。 「[Azure Portal で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。

* **Azure Active Directory サービス プリンシパル**。 このチュートリアルでは、Azure Active Directory (Azure AD) でサービス プリンシパルを作成する方法について説明します。 ただし、サービス プリンシパルを作成するには、Azure AD 管理者である必要があります。 管理者である場合は、この前提条件をスキップしてチュートリアルを進めることができます。

    >[!NOTE]
    >Azure AD 管理者である場合にのみ、サービス プリンシパルを作成することができます。 Data Lake Store で HDInsight クラスターを作成する前に、まず Azure AD 管理者がサービス プリンシパルを作成する必要があります。 また、「[証明書を使用したサービス プリンシパルの作成](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)」で説明しているように、サービス プリンシパルは証明書を使って作成する必要があります。
    >

## <a name="create-an-hdinsight-cluster-with-access-to-a-data-lake-store"></a>Data Lake Store にアクセスできる HDInsight クラスターを作成する
このセクションでは、Data Lake Store を既定または追加のストレージとして使用する HDInsight Hadoop クラスターを作成します。

### <a name="create-a-cluster-with-data-lake-store-as-default-storage"></a>Data Lake Store を既定のストレージとして使用してクラスターを作成する

>[!NOTE]
>このオプションは、HDInsight 3.5 クラスターでのみ使用できます (Standard Edition)。 HDInsight 3.5 クラスター内では、このオプションを HBase の種類のクラスターに使用することはできません。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. [HDInsight での Hadoop クラスターの作成](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)に関する記事の手順に従って、HDInsight クラスターのプロビジョニングを開始します。

3. **[ストレージ]** ブレードの **[プライマリ ストレージの種類]** で、**[Data Lake Store]** をクリックします。

4. 既存の Data Lake Store アカウントを選択し、クラスターに固有のファイルが格納されるルート フォルダーのパスを入力します。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "HDInsight クラスターにサービス プリンシパルを追加する")

    
    上のスクリーンショットでは、ルート フォルダーのパスが /clusters/myhdiadlcluster ですが、ここで *myhdiadlcluster* は作成するクラスターの名前です。 このような場合は、*/clusters* フォルダーがData Lake Store アカウント内に存在することを確認してください。 *myhdiadlcluster* フォルダーは、クラスターの作成時に作成されます。 同様に、ルートのパスを */hdinsight/clusters/data/myhdiadlcluter* に設定した場合は、*/hdinsight/clusters/data/* が Data Lake Store アカウント内に存在することを確認する必要があります。

5. **[Data Lake Store アクセス]** をクリックして、Data Lake Store アカウントと HDInsight クラスターの間のアクセスを構成します。 手順については、「[HDInsight クラスターと Data Lake Store の間のアクセスを構成する](#configure-access-between-hdinsight-cluster-and-data-lake-store)」を参照してください。 


### <a name="create-a-cluster-with-data-lake-store-as-additional-storage"></a>Data Lake Store を追加のストレージとして使用してクラスターを作成する 

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. [HDInsight での Hadoop クラスターの作成](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)に関する記事の手順に従って、HDInsight クラスターのプロビジョニングを開始します。

3. **[ストレージ]** ブレードの **[プライマリ ストレージの種類]** で、**[Azure Storage]** を選択します。
 
4. **[メソッドの選択]** で、次のいずれかを実行します。

    * Azure サブスクリプションの一部であるストレージ アカウントを指定するには、**[個人用サブスクリプション]** を選択し、ストレージ アカウントを選択します。

    * Azure サブスクリプションの外部にあるストレージ アカウントを指定するには、**[アクセス キー]** を選択し、外部のストレージ アカウントの情報を入力します。

5. **[既定のコンテナー]** で、ポータルによって示された既定のコンテナー名をそのまま使用するか、独自の名前を指定します。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "HDInsight クラスターにサービス プリンシパルを追加する")

6. Blob Storage を既定のストレージとして使用していても、Data Lake Store をクラスターの追加のストレージとして使用できます。 それには、**[Data Lake Store アクセス]** をクリックして、Data Lake Store アカウントと HDInsight クラスターの間のアクセスを構成します。 手順については、「[HDInsight クラスターと Data Lake Store の間のアクセスを構成する](#configure-access-between-hdinsight-cluster-and-data-lake-store)」を参照してください。

## <a name="configure-access-between-hdinsight-cluster-and-data-lake-store"></a>HDInsight クラスターと Data Lake Store の間のアクセスを構成する

このセクションでは、Azure Active Directory サービス プリンシパルを使用した、HDInsight クラスターと Data Lake Store の間のアクセスを構成します

1. **[Data Lake Store アクセス]** ブレードで、新しいサービス プリンシパルを使用するか、既存のサービス プリンシパルを使用するかを指定します。 この手順では、新しいサービス プリンシパルを作成します。 既存のサービス プリンシパルを使用するには、次の手順までスキップしてください。

    a. **[Data Lake Store アクセス]** ブレードで、**[新規作成]** をクリックし、**[サービス プリンシパル]** をクリックします。

    b. **[サービス プリンシパルの作成]** ブレードで、必要な値を入力します。  

    c. **[作成]**をクリックします。 証明書と Azure AD アプリケーションが自動的に作成されます。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "HDInsight クラスターにサービス プリンシパルを追加する")

    **[証明書のダウンロード]** をクリックして、作成したサービス プリンシパルに関連付けられている証明書をダウンロードすることもできます。 証明書のダウンロードは、追加の HDInsight クラスターを作成するときに同じサービス プリンシパルを使用したい場合に役立ちます。 **[選択]**をクリックします。

2. 既存のサービス プリンシパルを使用するには、次の手順を実行します。

    a. **[Data Lake Store アクセス]** ブレードで、**[既存のものを使用]** をクリックし、**[サービス プリンシパル]** をクリックします。

    b. **[サービス プリンシパルの選択]** ブレードで、既存のサービス プリンシパルを探します。 使用するサービス プリンシパルをクリックし、**[選択]** をクリックします。

    c. **[Data Lake Store アクセス]** ブレードで、選択したサービス プリンシパルに関連付けられている証明書 (.pfx ファイル) をアップロードし、証明書のパスワードを入力します。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "HDInsight クラスターにサービス プリンシパルを追加する")

7. **[Data Lake Store アクセス]** ブレードで、**[アクセス]** をクリックします。 **[ファイル アクセス許可の選択]** ブレードが既定で開きます。 このブレードには、サブスクリプション内のすべての Data Lake Store アカウントが一覧表示されます。

8. クラスターに関連付ける Data Lake Store アカウントをクリックします。 

    **Data Lake Store を既定のストレージとして使用する場合**は、2 つのレベルでアクセス許可を割り当てる必要があります。

    a. 最初に、Data Lake Store アカウントのルート レベルでアクセス許可を割り当てます。 これを行うには、Data Lake Store アカウント名の上にマウス ポインターを置いて (クリックしないでください)、チェック ボックスを表示します。 チェック ボックスをオンにします。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "HDInsight クラスターにサービス プリンシパルを追加する")

    b. 次に、アクセス許可を、HDInsight クラスター ストレージのルートに割り当てる必要があります。 前のスクリーンショットでは、クラスター ストレージのルートは、Data Lake Store を既定のストレージとして選択したときに指定した **/clusters** フォルダーです。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.add.png "HDInsight クラスターにサービス プリンシパルを追加する")

    **Data Lake Store を追加のストレージとして使用している場合**は、HDInsight クラスターからアクセスするフォルダーに対してのみアクセス許可を割り当てる必要があります。 たとえば、次のスクリーンショットでは、Data Lake Store アカウントの **hdiaddonstorage** フォルダーへのアクセスのみを提供します。

    ![HDInsight クラスターにサービス プリンシパルのアクセス許可を割り当てる](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "HDInsight クラスターにサービス プリンシパルのアクセス許可を割り当てる")

9. 選択したすべてのアカウントおよびパスについて、アクセス許可 (読み取り、書き込み、または実行) を選択し、そのアクセス許可が子項目にも再帰的に適用されるかどうかを指定して、**[選択]** をクリックします。

11. **[選択したアクセス許可の割り当て]**画面で、**[実行]** をクリックし、選択したアカウント、ファイル、フォルダーでの Azure Active Directory サービス プリンシパルのアクセス許可を割り当てます。

    ![HDInsight クラスターにサービス プリンシパルのアクセス許可を割り当てる](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-2.png "HDInsight クラスターにサービス プリンシパルのアクセス許可を割り当てる")

12. アクセス許可が正常に割り当てられたら、開いている各ブレードで **[完了]** をクリックして、**[Data Lake Store アクセス]** ブレードに戻ります。

13. **[Data Lake Store アクセス]** で、**[選択]** をクリックし、[HDInsight での Hadoop クラスターの作成](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)に関するページの説明に従ってクラスターの作成に進みます。

## <a name="verify-cluster-set-up"></a>クラスターのセットアップを確認する

クラスターのセットアップが完了したら、クラスター ブレードで、次のいずれかまたは両方を実行して結果を確認します。

* クラスターに関連付けられているストレージが、指定した Data Lake Store アカウントであることを確認するには、左側のウィンドウで **[ストレージ アカウント]** をクリックします。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "HDInsight クラスターにサービス プリンシパルを追加する")

* サービス プリンシパルが HDInsight クラスターに正しく関連付けられていることを確認するには、左側のウィンドウで **[Data Lake Store アクセス]** をクリックします。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "HDInsight クラスターにサービス プリンシパルを追加する")


## <a name="examples"></a>例

Data Lake Store をストレージとして使用するクラスターのセットアップが完了したら、HDInsight クラスターを使用して Data Lake Store に格納されているデータを分析する方法について、以下に示すいくつかの例をご覧ください。

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-primary-storage"></a>(プライマリ ストレージとしての) Data Lake Store に格納されているデータに対して Hive クエリを実行する

Hive クエリを実行する場合は、Ambari ポータルで提供されている Hive ビュー インターフェイスを使用します。 Ambari Hive ビューの使用方法については、「[HDInsight での Hive View と Hadoop の使用](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md)」をご覧ください。

Data Lake Store 内のデータを操作するときは、いくつかの文字列を変更する必要があります。

たとえば、プライマリ ストレージとして Data Lake Store を使用するクラスターを作成した場合は、データのパスは *adl://<data_lake_store_account_name>/azuredatalakestore.net/path/to/file* になります。 Data Lake Store アカウントに格納されているサンプル データからテーブルを作成する Hive クエリは、次のようになります。

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

上記のクエリで、
* `adl://hdiadlstorage.azuredatalakestore.net/` は Data Lake Store アカウントのルートです。
* `/clusters/myhdiadlcluster` はクラスターの作成時に指定したクラスター データのルートです。
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` はクエリで使用したサンプル ファイルの場所です。

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-additional-storage"></a>(追加ストレージとしての) Data Lake Store に格納されているデータに対して Hive クエリを実行する

作成したクラスターで既定のストレージとして Blob Storage を使用している場合、追加ストレージとして使用されている Azure Data Lake Store アカウントにサンプル データは含まれません。 このような場合、Blob Storage から Data Lake Store にデータを転送してから、上の例に示したようにクエリを実行します。

Blob Storage から Data Lake Store にデータをコピーする方法については、次の記事をご覧ください。

* [Distcp を使用して Azure Storage BLOB と Data Lake Store の間でデータをコピーする](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy を使用して Azure Storage BLOB のデータを Data Lake Store にコピーする](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-store-with-a-spark-cluster"></a>Spark クラスターで Data Lake Store を使用する
Spark クラスターを使用すると、Data Lake Store に格納されているデータで Spark ジョブを実行できます。 詳細については、「[HDInsight Spark クラスターを使用して Data Lake Store のデータを分析する](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md)」をご覧ください。


### <a name="use-data-lake-store-in-a-storm-topology"></a>Storm トポロジで Data Lake Store を使用する
Data Lake Store を使用して、Storm トポロジからデータを書き込むことができます。 このシナリオを実現する方法については、「 [HDInsight で Apache Storm によって Azure Data Lake Store を使用する](../hdinsight/hdinsight-storm-write-data-lake-store.md)」をご覧ください。

## <a name="see-also"></a>関連項目
* [Azure PowerShell を使用して、Data Lake Store を使用する HDInsight クラスターをプロビジョニングする](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx


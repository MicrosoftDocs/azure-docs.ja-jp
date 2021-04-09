---
title: Data Lake Storage Gen1 で Azure HDInsight クラスターを作成する - ポータル
description: Azure portal を使用して、Azure Data Lake Storage Gen1 を使用する HDInsight クラスターを作成および使用します
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 666b39e2a600fe6ca004798ed4f8371cdd1dfe5f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96340256"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Azure portal を使用して、Azure Data Lake Storage Gen1 を使用する HDInsight クラスターを作成する

> [!div class="op_single_selector"]
> * [Azure Portal の使用](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell の使用 (既定のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell の使用 (追加のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager の使用](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Azure portal を使用して、既定のストレージまたは追加のストレージとして Azure Data Lake Storage Gen1 を使用する HDInsight クラスターを作成する方法を説明します。 追加のストレージは HDInsight クラスターでは省略可能ですが、業務データは追加のストレージ アカウントに格納することをお勧めします。

## <a name="prerequisites"></a>前提条件

開始する前に、次の要件を満たしていることを確認します。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。
* **Azure Data Lake Storage Gen1 アカウント**。 「[Azure portal で Azure Data Lake Storage Gen1 の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。 アカウントのルート フォルダーも作成する必要があります。  この記事では、 __/clusters__ という名前のルート フォルダーを使用します。
* **Azure Active Directory サービス プリンシパル**。 この攻略ガイドでは、Azure Active Directory (Azure AD) でサービス プリンシパルを作成する方法について説明します。 ただし、サービス プリンシパルを作成するには、Azure AD 管理者である必要があります。 管理者である場合は、この前提条件をスキップして続行することができます。

>[!NOTE]
>Azure AD 管理者である場合にのみ、サービス プリンシパルを作成することができます。 Data Lake Storage Gen1 で HDInsight クラスターを作成する前に、まず Azure AD 管理者がサービス プリンシパルを作成する必要があります。 また、「[証明書を使用したサービス プリンシパルの作成](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)」で説明しているように、サービス プリンシパルは証明書を使って作成する必要があります。
>

## <a name="create-an-hdinsight-cluster"></a>HDInsight クラスターの作成

このセクションでは、既定のまたは追加のストレージとして Data Lake Storage Gen1 を使用する HDInsight クラスターを作成します。 この記事では、Data Lake Storage Gen1 の構成の一部のみを取り上げます。 一般的なクラスターの作成に関する情報および手順については、[HDInsight での Hadoop クラスターの作成](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Data Lake Storage Gen1 を既定のストレージとして使用してクラスターを作成する

既定のストレージ アカウントとして Data Lake Storage Gen1 を使用する HDInsight クラスターを作成するには:

1. [Azure portal](https://portal.azure.com) にサインインする
2. HDInsight クラスターの作成に関する一般的な情報については、「[クラスターの作成](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters)」を参照してください。
3. **[ストレージ]** ブレードの **[プライマリ ストレージの種類]** で、 **[Azure Data Lake Storage Gen1]** を選択して、次の情報を入力します。

    ![HDInsight のストレージ アカウントの設定](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png)

    * **[Data Lake Store アカウントを選択する]** : 既存の Data Lake Storage Gen1 アカウントを選択します。 既存の Data Lake Storage Gen1 アカウントが必要です。  「[前提条件](#prerequisites)」を参照してください。
    * **[ルート パス]** : クラスターに固有のファイルが格納されるパスを入力します。 このスクリーン ショットでは、 __/clusters/myhdiadlcluster/__ です。この場合、 __/clusters__ フォルダーが存在する必要があり、Portal では *myhdicluster* フォルダーが作成されます。  *myhdicluster* がクラスター名です。
    * **[Data Lake Store アクセス]** : Data Lake Storage Gen1 アカウントと HDInsight クラスターの間のアクセスを構成します。 手順については、「[Data Lake Storage Gen1 のアクセスの構成](#configure-data-lake-storage-gen1-access)」を参照してください。
    * **[追加のストレージ アカウント]** : クラスターの追加のストレージ アカウントとして Azure ストレージ アカウントを追加します。 Data Lake Storage Gen1 アカウントの追加は、プライマリ ストレージ タイプとして Data Lake Storage Gen1 アカウントを構成する際に、他の Data Lake Storage Gen1 アカウントのデータに対するクラスターのアクセス許可を与えることで完了します。 「[Data Lake Storage Gen1 のアクセスの構成](#configure-data-lake-storage-gen1-access)」を参照してください。

4. **[Data Lake Store アクセス]** で、 **[選択]** をクリックし、[HDInsight での Hadoop クラスターの作成](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)に関するページの説明に従ってクラスターの作成に進みます。

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Data Lake Storage Gen1 を追加のストレージとして使用してクラスターを作成する

次の手順で、既定のストレージとして Azure Blob ストレージ アカウントを使い、追加のストレージとして Data Lake Storage Gen1 を使用するストレージ アカウントを使って HDInsight クラスターを作成します。

Data Lake Storage Gen1 を追加のストレージ アカウントとして使用する HDInsight クラスターを作成するには:

1. [Azure portal](https://portal.azure.com) にサインインする
2. HDInsight クラスターの作成に関する一般的な情報については、「[クラスターの作成](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters)」を参照してください。
3. **[ストレージ]** ブレードの **[プライマリ ストレージの種類]** で、 **[Azure Storage]** を選択して、次の情報を入力します。

    ![HDInsight のストレージ アカウントの設定 - 追加のストレージ](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png)

    * **選択方法** - Azure サブスクリプションの一部であるストレージ アカウントを指定するには、 **[個人用サブスクリプション]** を選択し、ストレージ アカウントを選択します。 Azure サブスクリプションの外部にあるストレージ アカウントを指定するには、**アクセス キー** を選択し、外部のストレージ アカウントの情報を入力します。

    * **[既定のコンテナー]** - 既定値を使用するか、独自の名前を指定します。
    * **[追加のストレージ アカウント]** - 追加のストレージとして Azure ストレージ アカウントを追加します。
    * **[Data Lake Store アクセス]** - Data Lake Storage Gen1 アカウントと HDInsight クラスターの間のアクセスを構成します。 手順については、「[Data Lake Storage Gen1 のアクセスの構成](#configure-data-lake-storage-gen1-access)」を参照してください。

## <a name="configure-data-lake-storage-gen1-access"></a>Data Lake Storage Gen1 のアクセスの構成

このセクションでは、Azure Active Directory サービス プリンシパルを使用した、HDInsight クラスターからの Data Lake Storage Gen1 へのアクセスを構成します。

### <a name="specify-a-service-principal"></a>サービス プリンシパルの指定

Azure Portal から、既存のサービス プリンシパルを使用するか、新しいものを作成することができます。

Azure portal からサービス プリンシパルを作成するには:
1. Azure Active Directory 使用した[サービス プリンシパルと証明書の作成](../active-directory/develop/howto-create-service-principal-portal.md)に関する記事を参照してください。

Azure portal から既存のサービス プリンシパルを使用するには:

1. サービス プリンシパルには、ストレージ アカウントの所有者権限を与える必要があります。 [サービス プリンシパルをストレージ アカウントの所有者にする権限を設定する](#configure-serviceprincipal-permissions)方法に関するページを参照してください。
1. **[Data Lake Store アクセス]** を選択します。
1. **[Data Lake Storage Gen1 アクセス]** ブレードで、**[既存のものを使用]** を選択します。
1. **[サービス プリンシパル]** を選択し、サービス プリンシパルを選択します。
1. 選択したサービス プリンシパルに関連付けられている証明書 (.pfx ファイル) をアップロードし、証明書のパスワードを入力します。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png)

1. **[アクセス]** を選択して、フォルダーへのアクセスを構成します。  「[ファイルのアクセス許可を構成する](#configure-file-permissions)」を参照してください。

### <a name="set-up-permissions-for-the-service-principal-to-be-owner-on-the-storage-account"></a><a name="configure-serviceprincipal-permissions"></a>サービス プリンシパルをストレージ アカウントの所有者にする権限を設定する
1. ストレージ アカウントの [アクセス制御 (IAM)] ブレードで [ロールの割り当てを追加する] をクリックします。 
2. [ロールの割り当てを追加する] ブレードで、ロールとして "所有者" を選択し、SPN を選択し、[保存] をクリックします。

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>ファイルのアクセス許可を構成する

構成は、アカウントを既定のストレージとして使用するか、追加のストレージ アカウントとして使用するかによって異なります。

* 既定のストレージとして使用する

  * Data Lake Storage Gen1 アカウントのルート レベルでのアクセス許可
  * HDInsight クラスター記憶域のルート レベルでのアクセス許可。 たとえば、このチュートリアルで使用した __/clusters__ フォルダー。

* 追加のストレージとして使用する

  * ファイル アクセスが必要なフォルダーのアクセス許可。

Data Lake Storage Gen1 を使用するストレージ アカウントのルート レベルでアクセス許可を割り当てるには:

1. **[Data Lake Storage Gen1 アクセス]** ブレードで、**[アクセス]** を選択します。 **[ファイル アクセス許可の選択]** ブレードが開きます。 サブスクリプション内のすべてのストレージ アカウントが一覧表示されます。
1. Data Lake Storage Gen1 を使用するアカウント名の上にマウス ポインターを置いて (クリックしないでください) チェック ボックスを表示し、そのチェック ボックスを選択します。

    ![ファイル アクセス許可の選択](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png)

   既定では、 __[読み取り]__ 、 __[書き込み]__ 、 __[実行]__ がすべて選択されています。

1. ページの下部にある **[選択]** をクリックします。
1. **[実行]** を選択して、アクセス許可を割り当てます。
1. **[Done]** を選択します。

HDInsight クラスターのルート レベルでアクセス許可を割り当てるには:

1. **[Data Lake Storage Gen1 アクセス]** ブレードで、**[アクセス]** を選択します。 **[ファイル アクセス許可の選択]** ブレードが開きます。 サブスクリプション内のすべての Data Lake Storage Gen1 を使用するストレージ アカウントが一覧表示されます。
1. **[ファイル アクセス許可の選択]** ブレードで、Data Lake Storage Gen1 を使用するストレージ アカウントの名前を選択して、その内容を表示します。
1. フォルダーの左側のチェック ボックスを選択して HDInsight クラスター記憶域のルートを選択します。 前のスクリーンショットでは、クラスター記憶域のルートは、Data Lake Storage Gen1 を既定のストレージとして選択したときに指定した __/clusters__ フォルダーです。
1. フォルダーのアクセス許可を設定します。  既定では、[読み取り]、[書き込み]、[実行] がすべて選択されています。
1. ページの下部にある **[選択]** をクリックします。
1. **[実行]** を選択します。
1. **[Done]** を選択します。

Data Lake Storage Gen1 を追加のストレージとして使用している場合は、HDInsight クラスターからアクセスするフォルダーに対してのみアクセス許可を割り当てる必要があります。 たとえば、次のスクリーンショットでは、Data Lake Storage Gen1 を使用するストレージ アカウントの **mynewfolder** フォルダーへのアクセスのみを提供します。

![HDInsight クラスターにサービス プリンシパルのアクセス許可を割り当てる](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png)

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>クラスター設定の確認

クラスターのセットアップが完了したら、クラスター ブレードで、次の手順のいずれかまたは両方を実行して結果を確認します。

* クラスターに関連付けられているストレージが、指定した Data Lake Storage Gen1 を使用するストレージ アカウントであることを確認するには、左側のペインで **[ストレージ アカウント]** を選択します。

    ![関連付けられているストレージの確認](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png)

* サービス プリンシパルが HDInsight クラスターに正しく関連付けられていることを確認するには、左側のペインで **[Data Lake Storage Gen1 アクセス]** を選択します。

    ![サービス プリンシパルの確認](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png)

## <a name="examples"></a>例

Data Lake Storage Gen1 をストレージとして使用するクラスターを設定したら、HDInsight クラスターを使用して Data Lake Storage Gen1 に格納されているデータを分析する方法について、以下に示すいくつかの例をご覧ください。

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-primary-storage"></a>(プライマリ ストレージとしての) Data Lake Storage Gen1 に格納されているデータに対して Hive クエリを実行する

Hive クエリを実行する場合は、Ambari ポータルで提供されている Hive ビュー インターフェイスを使用します。 Ambari Hive ビューの使用方法については、「[HDInsight での Hive View と Hadoop の使用](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md)」をご覧ください。

Data Lake Storage Gen1 内のデータを操作するときは、いくつかの文字列を変更する必要があります。

たとえば、プライマリ ストレージとして Data Lake Storage Gen1 を使用するクラスターを作成した場合は、データのパスは *adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.net/path/to/file* になります。 Data Lake Storage Gen1 に格納されているサンプル データからテーブルを作成する Hive クエリは、次のようなステートメントになります。

```console
CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'
```

上記のクエリで、

* `adl://hdiadlsg1storage.azuredatalakestore.net/` は Data Lake Storage Gen1 を使用するアカウントのルートです。
* `/clusters/myhdiadlcluster` はクラスターの作成時に指定したクラスター データのルートです。
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` はクエリで使用したサンプル ファイルの場所です。

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-additional-storage"></a>(追加ストレージとしての) Data Lake Storage Gen1 に格納されているデータに対して Hive クエリを実行する

作成したクラスターで既定のストレージとして Blob Storage を使用している場合、追加ストレージとして使用されている Data Lake Storage Gen1 を使用するストレージ アカウントにサンプル データは含まれません。 このような場合、Blob Storage から Data Lake Storage Gen1 を使用するストレージ アカウントにデータを転送してから、上の例に示したようにクエリを実行します。

Blob Storage から Data Lake Storage Gen1 を使用するストレージ アカウントにデータをコピーする方法については、次の記事をご覧ください。

* [Distcp を使用して Azure Blob Storage と Data Lake Storage Gen1 の間でデータをコピーする](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy を使用して Azure Blob Storage のデータを Data Lake Storage Gen1 にコピーする](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Spark クラスターで Data Lake Storage Gen1 を使用する

Spark クラスターを使用すると、Data Lake Storage Gen1 に格納されているデータに対して Spark ジョブを実行できます。 詳細については、[HDInsight Spark クラスターを使用した Data Lake Storage Gen1 のデータの分析](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)に関するページをご覧ください。

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Storm トポロジで Data Lake Storage Gen1 を使用する

Data Lake Storage Gen1 を使用するストレージ アカウントを使用して、Storm トポロジからデータを書き込むことができます。 このシナリオを実現する方法については、[HDInsight で Apache Storm によって Azure Data Lake Storage Gen1 を使用する方法](../hdinsight/storm/apache-storm-write-data-lake-store.md)に関するページをご覧ください。

## <a name="see-also"></a>関連項目

* [Azure HDInsight クラスターで Data Lake Storage Gen1 を使用する](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [PowerShell: Data Lake Storage Gen1 を使用する HDInsight クラスターを作成する](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: /windows-hardware/drivers/devtest/pvk2pfx
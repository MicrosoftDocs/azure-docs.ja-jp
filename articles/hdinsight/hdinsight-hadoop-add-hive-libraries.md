---
title: クラスター作成時の Apache Hive ライブラリ - Azure HDInsight
description: クラスターの作成中に Apache Hive ライブラリ (jar ファイル) を HDInsight クラスターに追加する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: b6695e5e985a30d6f912095225c4899e1c910e34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945948"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>HDInsight クラスターを作成するときにカスタム Apache Hive ライブラリを追加する

HDInsight で [Apache Hive](https://hive.apache.org/) ライブラリを事前に読み込む方法を説明します。 このドキュメントには、クラスターの作成時にスクリプト操作を使ってライブラリを事前に読み込む方法についての情報が含まれます。 このドキュメントの手順を使用して追加されたライブラリは、Hive でグローバルに使用できます。[ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) を使用して読み込む必要はありません。

## <a name="how-it-works"></a>しくみ

クラスターを作成するときは、スクリプト アクションを使用して、作成されたクラスター ノードを変更できます。 このドキュメントのスクリプトは、唯一のパラメーターでライブラリの場所を受け取ります。 この場所は Azure Storage アカウントである必要があり、ライブラリは jar ファイルとして格納されている必要があります。

クラスターの作成時に、そのスクリプトによってファイルが列挙され、ヘッド ノードとワーカー ノードの `/usr/lib/customhivelibs/` ディレクトリにコピーされて、`core-site.xml` ファイルの `hive.aux.jars.path` プロパティに追加されます。 Linux ベースのクラスターでは、それらのファイルの場所に合わせて `hive-env.sh` ファイルも更新されます。

この記事のスクリプト アクションを使用すると、**WebHCat** および **HiveServer2** 用に Hive クライアントを使用するときにライブラリが使用可能になります。

## <a name="the-script"></a>スクリプトは、以下のことを行います

**スクリプトの場所**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>必要条件

* このスクリプトは、**ヘッド ノード** と **ワーカー ノード** の両方に適用する必要があります。

* インストールする jar は、Azure Blob Storage の **単一のコンテナー** に格納する必要があります。

* jar ファイルのライブラリを含むストレージ アカウントを、作成時に HDInsight クラスターにリンクする **必要があります**。 これは既定のストレージ アカウントか、または __ストレージ アカウントの設定__ によって追加されたアカウントのどちらかである必要があります。

* コンテナーへの WASB パスは、スクリプト アクションのパラメーターとして指定する必要があります。 たとえば、jar が **mystorage** という名前のストレージ アカウント上の **libs** という名前のコンテナーに格納されている場合、パラメーターは `wasbs://libs@mystorage.blob.core.windows.net/` になります。

  > [!NOTE]  
  > このドキュメントでは、既にストレージ アカウントと BLOB コンテナーを作成し、そこにファイルをアップロードしていることを前提としています。
  >
  > ストレージ アカウントを作成していない場合は、[Azure Portal](https://portal.azure.com) で作成できます。 その後、[Azure Storage エクスプローラー](https://storageexplorer.com/) などのユーティリティを使用して、アカウントにコンテナーを作成し、そこにファイルをアップロードできます。

## <a name="create-a-cluster-using-the-script"></a>スクリプトを使用してクラスターを作成する

1. [Linux での HDInsight クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)に関するページの手順を使用してクラスターのプロビジョニングを開始しますが、プロビジョニングを完了しないでください。 Azure PowerShell または HDInsight .NET SDK を使用し、このスクリプトを使用してクラスターを作成することもできます。 これらの方法の詳細については、 [スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)に関するページを参照してください。 Azure portal の場合は、 **[構成と価格]** タブで、 **[+ スクリプト アクションの追加]** を選択します。

1. **[ストレージ]** で、jar ファイルのライブラリが含まれているストレージ アカウントがクラスターに使用されるアカウントと異なる場合は、**[Additional storage accounts] (追加のストレージ アカウント)** に入力します。

1. **[スクリプト アクション]** では、次の情報を指定します。

    |プロパティ |値 |
    |---|---|
    |スクリプトの種類|- Custom|
    |名前|ライブラリ |
    |Bash スクリプト URI|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |ノードの種類|ヘッド、ワーカー|
    |パラメーター|jar が格納されているコンテナーとストレージ アカウントの WASB アドレスを入力します。 たとえば、「 `wasbs://libs@mystorage.blob.core.windows.net/` 」のように入力します。|

    > [!NOTE]
    > Apache Spark 2.1 の場合は、次の bash スクリプト URI を使用します。`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh`

1. 「[Linux の HDInsight クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)」の説明に従って、クラスターのプロビジョニングを続行します。

クラスターの作成が完了したら、`ADD JAR` ステートメントを使用しなくても、このスクリプトによって追加された jar を Hive から使用できます。

## <a name="next-steps"></a>次のステップ

Hive の操作の詳細については、[HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)に関するページを参照してください

---
title: HDInsight クラスター作成時の Apache Hive ライブラリの追加 - Azure
description: クラスターの作成時に Apache Hive ライブラリ (jar ファイル) を HDInsight クラスターに追加する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 6001d291581dc317da89cadbf3891e334362062b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57897590"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>HDInsight クラスターを作成するときにカスタム Apache Hive ライブラリを追加する

HDInsight で [Apache Hive](https://hive.apache.org/) ライブラリを事前に読み込む方法を説明します。 このドキュメントには、クラスターの作成時にスクリプト操作を使ってライブラリを事前に読み込む方法についての情報が含まれます。 このドキュメントの手順を使用して追加されたライブラリは Hive でグローバルに使用できます ([ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) を使用して読み込む必要がありません)。

## <a name="how-it-works"></a>動作のしくみ

クラスターを作成するときに、スクリプト操作を使って、作成されるクラスター ノードを変更できます。 このドキュメントのスクリプトは、唯一のパラメーターでライブラリの場所を受け取ります。 この場所は Azure Storage アカウントである必要があり、ライブラリは jar ファイルとして格納されている必要があります。

クラスターの作成時に、そのスクリプトによってファイルが列挙され、ヘッド ノードとワーカー ノードの `/usr/lib/customhivelibs/` ディレクトリにコピーされて、`core-site.xml` ファイルの `hive.aux.jars.path` プロパティに追加されます。 Linux ベースのクラスターでは、それらのファイルの場所に合わせて `hive-env.sh` ファイルも更新されます。

> [!NOTE]  
> この記事のスクリプト アクションを使用すると、次のシナリオでライブラリを使用できます。
>
> * **Linux ベースの HDInsight** - Hive クライアント、**WebHCat**、および **HiveServer2** を使用する場合。
> * **Windows ベースの HDInsight** - Hive クライアントおよび **WebHCat** を使用する場合。

## <a name="the-script"></a>スクリプト

**スクリプトの場所**

**Linux ベースのクラスター**の場合: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

**Windows ベースのクラスター**の場合: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

> [!IMPORTANT]  
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

**要件**

* このスクリプトは、**ヘッド ノード**と**ワーカー ノード**の両方に適用する必要があります。

* インストールする jar は、Azure Blob Storage の **単一のコンテナー**に格納する必要があります。

* jar ファイルのライブラリを含むストレージ アカウントを、作成時に HDInsight クラスターにリンクする**必要があります**。 既定のストレージ アカウントまたは __オプションの構成__ を通じて追加されたアカウントである必要があります。

* コンテナーへの WASB パスは、スクリプト アクションのパラメーターとして指定する必要があります。 たとえば、jar が **mystorage** という名前のストレージ アカウントの **libs** というコンテナーに格納されている場合、パラメーターは **wasb://libs\@mystorage.blob.core.windows.net/** となります。

  > [!NOTE]  
  > このドキュメントでは、既にストレージ アカウントと BLOB コンテナーを作成し、そこにファイルをアップロードしていることを前提としています。
  >
  > ストレージ アカウントを作成していない場合は、[Azure Portal](https://portal.azure.com) で作成できます。 その後、[Azure Storage エクスプローラー](https://storageexplorer.com/) などのユーティリティを使用して、アカウントにコンテナーを作成し、そこにファイルをアップロードできます。

## <a name="create-a-cluster-using-the-script"></a>スクリプトを使用してクラスターを作成する

> [!NOTE]  
> 次の手順では、Linux ベースの HDInsight クラスターを作成します。 Windows ベースのクラスターを作成するには、クラスターの作成時にクラスターの OS として **Windows** を選択し、Bash スクリプトの代わりに Windows (PowerShell) スクリプトを使用します。
>
> Azure PowerShell または HDInsight .NET SDK を使用し、このスクリプトを使用してクラスターを作成することもできます。 これらの方法の詳細については、 [スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)に関するページを参照してください。

1. 「[Linux の HDInsight クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)」に記載されている手順を使用してクラスターのプロビジョニングを開始します。ただし、プロビジョニングを完了しないでください。

2. **[オプションの構成]** セクションで **[スクリプト アクション]** を選択し、以下の情報を指定します。

   * **[名前]**: スクリプト アクションのフレンドリ名を入力します。

   * **[スクリプト URI]**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh。

   * **[ヘッド]**: このオプションをオンにします。

   * **[ワーカー]**:このオプションをオンにします。

   * **[ZooKeeper]**: 空白のままにします。

   * **[パラメーター]**: jar が格納されているコンテナーとストレージ アカウントの WASB アドレスを入力します。 たとえば、**wasb://libs\@mystorage.blob.core.windows.net/** です。

3. **[スクリプト アクション]** の下部で、**[選択]** を使用して構成を保存します。

4. **[オプションの構成]** セクションで、**[リンクされたストレージ アカウント]** を選択し、**[ストレージ キーの追加]** リンクをクリックします。 jar が格納されているストレージ アカウントを選択します。 **選択** ボタンを使って設定を保存し、**オプションの構成**を戻します。

5. オプションの構成を保存するには、**[オプションの構成]** セクションの下部にある **[選択]** ボタンを使います。

6. 「[Linux の HDInsight クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)」の説明に従って、クラスターのプロビジョニングを続行します。

クラスターの作成が完了したら、このスクリプトで追加した jar を、`ADD JAR` ステートメントを使用することなく、Hive から使用できます。

## <a name="next-steps"></a>次の手順

Hive の操作の詳細については、[HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)に関するページを参照してください

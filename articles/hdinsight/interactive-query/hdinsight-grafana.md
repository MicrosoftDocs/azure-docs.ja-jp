---
title: Azure HDInsight で Grafana を使用する
description: Azure HDInsight で Grafana にアクセスする方法について説明します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: be804ac1aea76daf5f17e62dd97b8b57b8fdf1fb
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67458819"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Azure HDInsight で Grafana にアクセスする


[Grafana](https://grafana.com/) は、人気のあるオープン ソースのグラフとダッシュボードのビルダーです。 Grafana は豊富な機能を備えています。カスタマイズ可能で共有可能なダッシュボードを作成できるだけでなく、テンプレート化/スクリプト化されたダッシュボード、LDAP の統合、複数のデータ ソースなどの機能があります。

現在、Azure HDInsight では、Grafana は Hbase と Interactive Query のクラスターの種類でサポートされています。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="create-an-apache-hadoop-cluster"></a>Apache Hadoop クラスターを作成する

このセクションでは、Azure Resource Manager テンプレートを利用して、HDInsight で対話型クエリ クラスターを作成します。 この記事に従うために、Resource Manager テンプレートの使用経験は必要ありません。 

1. 以下の **[Deploy to Azure] (Azure へのデプロイ)** ボタンをクリックして Azure にサインインし、Azure Portal で Resource Manager テンプレートを開きます。 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-grafana/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 次のスクリーンショットで提案されているように値を入力するか、選択します。

    > [!NOTE]  
    > 指定する値は一意である必要があり、名前付けガイドラインに従う必要があります。 テンプレートでは、検証チェックは実行されません。 指定した値が既に使用されている場合、またはガイドラインに従ってない場合、テンプレートを送信した後にエラーが発生します。       
    > 
    >
    
    ![HDInsight Linux の使用。ポータルの Resource Manager テンプレート](./media/hdinsight-grafana/hdinsight-linux-get-started-arm-template-on-portal.png "Azure Portal とリソース グループ マネージャー テンプレートを使用して HDInsight で Hadoop クラスターをデプロイする")

    次の値を入力または選択します。
    
    |プロパティ  |説明  |
    |---------|---------|
    |**サブスクリプション**     |  Azure サブスクリプションを選択します。 |
    |**リソース グループ**     | リソース グループを作成するか、既存のリソース グループを選択します。  リソース グループとは、Azure コンポーネントのコンテナーです。  この場合、リソース グループには、HDInsight クラスターおよび依存する Azure ストレージ アカウントが含まれています。 |
    |**Location**     | クラスターを作成する Azure の場所を選択します。  パフォーマンスを向上させるため、お近くの場所を選択してください。 |
    |**クラスターの種類**     | **[hadoop]** を選択します。 |
    |**クラスター名**     | Apache Hadoop クラスターの名前を入力します。 HDInsight のすべてのクラスターでは同じ DNS 名前空間が共有されるため、この名前は一意である必要があります。 この名前は、文字、数字、ハイフンを含む最大 59 文字で構成できます。 名前の先頭と末尾の文字をハイフンにすることはできません。 |
    |**クラスター ログイン名とパスワード**     | 既定のログイン名は **admin** です。パスワードは 10 文字以上で、数字、大文字、小文字、英数字以外の文字 (' " ` を除く\) が少なくとも 1 つずつ含まれる必要があります。 "Pass@word1" などのよく使われるパスワードを**指定していない**ことを確認してください。|
    |**SSH ユーザー名とパスワード**     | 既定のユーザー名は **sshuser** です。  SSH ユーザー名は、変更が可能です。  SSH ユーザーのパスワードには、クラスターのログイン パスワードと同じ要件が適用されます。|
       
    一部のプロパティは、テンプレートにハードコーディングされています。  これらの値はテンプレートから構成することができます。 これらのプロパティについて詳しくは、[HDInsight での Apache Hadoop クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)に関するページをご覧ください。

3. **[上記の使用条件に同意する]** 、 **[ダッシュボードにピン留めする]** の順に選択し、 **[購入]** を選択します。 ポータルのダッシュボードに、 **[デプロイを送信しています]** という新しいタイルが表示されます。 クラスターの作成には約 20 分かかります。

    ![テンプレートのデプロイの進行状況](./media/hdinsight-grafana/deployment-progress-tile.png "Azure テンプレートのデプロイの進行状況")

4. クラスターが作成されると、タイルのキャプションが、指定したリソース グループ名に変更されます。 タイルには、リソース グループ内で作成された HDInsight クラスターも一覧表示されます。 
   
    ![HDInsight Linux の使用。リソース グループ](./media/hdinsight-grafana/hdinsight-linux-get-started-resource-group.png "Azure HDInsight クラスター リソース グループ")
    
5. タイルには、クラスターに関連付けられている既定のストレージも一覧表示されます。 各クラスターには、[Azure ストレージ アカウント](../hdinsight-hadoop-use-blob-storage.md)または [Azure Data Lake アカウント](../hdinsight-hadoop-use-data-lake-store.md)との依存関係があります。 このアカウントを、既定のストレージ アカウントと呼びます。 HDInsight クラスターとその既定のストレージ アカウントは、同じ Azure リージョンに配置されている必要があります。 クラスターを削除しても、ストレージ アカウントは削除されません。
    

> [!NOTE]  
> その他のクラスター作成方法と、この記事で使うプロパティの詳細については、[HDInsight クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。 

## <a name="access-the-grafana-dashboard"></a>Grafana ダッシュボードにアクセスする

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[HDInsight クラスター]** を選択し、前のセクションで作成したクラスター名を選択します。

3. **[クイック リンク]** で、 **[クラスター ダッシュボード]** をクリックします。

    ![HDInsight クラスター ダッシュボード ポータル](./media/hdinsight-grafana/hdinsight-portal-cluster-dashboard.png "ポータルの HDInsight クラスター ダッシュボード")

4. ダッシュボードから **[Grafana]** タイルをクリックします。 または、クラスター URL の `/grafana/` パスを参照します。 たとえば、「 `https://<clustername>.azurehdinsight.net/grafana/` 」のように入力します。

5. Hadoop クラスター ユーザーの資格情報を入力します。

6. Grafana ダッシュボードが表示され、次の例のようになります:

    ![HDInsight Grafana ダッシュボード](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HDInsight Grafana ダッシュボード")

   

## <a name="clean-up-resources"></a>リソースのクリーンアップ
記事を完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。 

> [!NOTE]  
> *すぐに*次のチュートリアルに進み、HDInsight で Hadoop を使用して ETL 操作を実行する方法を学習する場合は、クラスターを実行したままにしておいてかまいません。 そのチュートリアルでは Hadoop クラスターを再度作成する必要があるからです。 ただし、すぐに次のチュートリアルに進まない場合は、クラスターを今すぐ削除する必要があります。

**クラスターと既定のストレージ アカウントを削除するには、次の手順に従います。**

1. ブラウザーの Azure Portal を開いているタブに戻ります。 [クラスターの概要] ページが表示されているはずです。 クラスターのみを削除し、既定のストレージ アカウントを保持する場合は、 **[削除]** を選択します。

    ![HDInsight におけるクラスターの削除](./media/hdinsight-grafana/hdinsight-delete-cluster.png "HDInsight クラスターの削除")

2. クラスターと既定のストレージ アカウントを削除する場合は、(上のスクリーンショットで強調表示されている) リソース グループ名を選択して、リソース グループのページを開きます。

3. **[リソース グループの削除]** を選択して、クラスターと既定のストレージ アカウントが含まれたリソース グループを削除します。 リソース グループを削除するとストレージ アカウントも削除されます。 ストレージ アカウントを残しておく場合は、クラスターのみを削除してください。

## <a name="next-steps"></a>次の手順
この記事では、Resource Manager テンプレートを利用して Linux ベースの HDInsight クラスターを作成する方法と基本的な Apache Hive クエリを実行する方法について説明しました。 次の記事では、HDInsight で Hadoop を使用して抽出、変換、読み込み (ETL) 操作を実行する方法を学習します。

> [!div class="nextstepaction"]
>[HDInsight で対話型クエリを使用してデータの抽出、変換、読み込みを行う](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)

実際のデータを使用する準備が整っていて、HDInsight のデータの格納方法や HDInsight にデータを取り込む方法を確認する場合は、以下の記事を参照してください。

* HDInsight で Azure Storage を使用する方法の詳細については、[HDInsight での Azure Storage の使用](../hdinsight-hadoop-use-blob-storage.md)に関するページを参照してください。
* データを HDInsight にアップロードする方法については、[データを HDInsight にアップロードする方法](../hdinsight-upload-data.md)に関する記事を参照してください。

HDInsight でデータを分析する方法の詳細については、次の記事を参照してください。

* Visual Studio から Hive クエリを実行する方法など、HDInsight で Hive を使用する方法の詳細については、[HDInsight での Apache Hive の使用](../hdinsight-use-hive.md)に関する記事を参照してください。
* データの変換に使用される言語 Pig の詳細については、[HDInsight での Apache Pig の使用](../hdinsight-use-pig.md)に関する記事を参照してください。
* Hadoop 上のデータを処理するプログラムを作成する方法の 1 つである MapReduce の詳細については、[HDInsight での MapReduce の使用](../hdinsight-use-mapreduce.md)に関する記事を参照してください。
* HDInsight Tools for Visual Studio を使用して HDInsight 上のデータを分析する方法については、 [HDInsight Hadoop Tools for Visual Studio の使用開始](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)に関するページを参照してください。



HDInsight クラスターの作成または管理の詳細については、以下の記事を参照してください。

* Linux ベースの HDInsight クラスターを管理する方法については、「 [Ambari を使用した HDInsight クラスターの管理](../hdinsight-hadoop-manage-ambari.md)」を参照してください。
* HDInsight クラスターの作成時に選択できるオプションの詳細については、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

---
title: "Azure Data Lake Store の概要 | Microsoft Docs"
description: "Azure Data Lake Store とは何か、Azure Data Lake Store が他のデータ ストアで提供する値はどのようなものか、を理解する"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b3475057-9427-4492-a3af-25a802a23a79
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: a1d30c77dd29a134017be7deec6bb50991e6a698


---
# <a name="overview-of-azure-data-lake-store"></a>Azure Data Lake Store の概要
Azure Data Lake Store は、ビッグ データの分析ワークロードに対応するエンタープライズ規模のハイパースケール リポジトリです。 Azure Data Lake を使用すると、運用分析や調査分析を目的として任意のサイズ、種類、および取り込み速度のデータを 1 か所でキャプチャすることができます。

> [!TIP]
> Azure Data Lake Store サービスの調査を開始するには、 [Data Lake Store ラーニング パス](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) を使用してください。
> 
> 

Azure Data Lake Store には、Hadoop (HDInsight クラスターで使用可能) から、WebHDFS 互換の REST API を使用してアクセスできます。 Azure Data Lake Store は、格納されたデータを分析できるように特別に設計されており、データ分析シナリオに合わせてパフォーマンスの調整が行われます。 これには、企業における実際のユース ケースで不可欠なエンタープライズ レベルのすべての機能 (セキュリティ、管理の容易性、スケーラビリティ、信頼性、および可用性) が既定で組み込まれています。

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Azure Data Lake の主要な機能のいくつかを以下に示します。

### <a name="built-for-hadoop"></a>Hadoop 用に構築
Azure Data Lake Store は、Hadoop 分散ファイル システム (HDFS) と互換性のある Apache Hadoop ファイル システムであり、Hadoop エコシステムと連携して動作します。  WebHDFS API を使用する既存の HDInsight アプリケーションまたはサービス は、Data Lake Store と容易に統合することができます。 Data Lake Store では、アプリケーション向けの WebHDFS と互換性のある REST インターフェイスを公開しています。

Data Lake Store に格納されたデータは、MapReduce または Hive などの Hadoop 分析フレームワークを使用して簡単に分析することができます。 Data Lake Store に格納されたデータに直接アクセスするように Microsoft Azure HDInsight クラスターをプロビジョニングおよび構成することができます。

### <a name="unlimited-storage-petabyte-files"></a>無制限のストレージ、ペタバイト ファイル
Azure Data Lake Store では無制限のストレージを実現しています。このため、分析を目的としてさまざまなデータを格納するのに適しています。 Data Lake に格納できるアカウント サイズ、ファイル サイズ、またはデータ量に関する制限は設定されていません。 対応可能な個々のファイルのサイズはキロバイトからペタバイトの範囲にわたり、任意の種類のデータを自由に格納することができます。 データは複数のコピーを作成して格納されるため障害が発生しても保護されます。Data Lake でのデータの格納期間に制限はありません。

### <a name="performance-tuned-for-big-data-analytics"></a>ビッグ データを分析するためのパフォーマンス チューニング
Azure Data Lake Store は、大量のデータのクエリと分析のために非常に高いスループットを必要とする、大規模な分析システムを実行するために構築されています。 Data Lake では、ファイル内のデータを複数の異なる記憶域サーバーに分散します。 これにより、ファイルを並列に読み取ってデータ分析を実行する場合の読み取りスループットが向上します。

### <a name="enterprise-ready-highly-available-and-secure"></a>エンタープライズ対応: 高い可用性とセキュリティ保護
Azure Data Lake Store では、業界標準の可用性と信頼性を提供します。 データ資産は、冗長なコピーを作成して格納されるので、予期せぬ障害が発生しても保護されます。 企業では、実際のソリューションの中で既存のデータ プラットフォームの重要な部分として Azure Data Lake を使用できます。

Data Lake Store では、格納されたデータに対してエンタープライズ レベルのセキュリティも提供します。 詳細については、「 [Azure Data Lake Store 内のデータをセキュリティで保護する](#DataLakeStoreSecurity)」を参照してください。

### <a name="all-data"></a>すべてのデータ
Azure Data Lake Store では、任意のデータをネイティブ形式でそのまま格納することができ、事前の変換は必要ありません。 Data Lake Store では、データを読み込む前にスキーマを定義する必要はなく、分析時にデータを解釈しスキーマを定義するかどうかは個々の分析フレームワークに任されます。 任意のサイズおよび形式のファイルを格納できるようにすると、Data Lake Store は構造化データ、半構造化データ、および非構造化データを処理できるようになります。

Azure Data Lake Store のデータ コンテナーは本質的にはフォルダーとファイルです。 格納されたデータを、SDK、Azure ポータル、Azure Powershell を使用して操作します。 これらのインターフェイスと適切なコンテナーを使用してストアにデータを配置する限り、あらゆる種類のデータを格納できます。 Data Lake Store では、格納されているデータの種類に基づくデータの特別な処理は実行されません。

## <a name="a-namedatalakestoresecurityasecuring-data-in-azure-data-lake-store"></a><a name="DataLakeStoreSecurity"></a>Azure Data Lake Store でのデータのセキュリティ保護
Azure Data Lake Store では、Azure Active Directory を使用し、認証およびアクセス制御リスト (ACL) によってデータへのアクセスを管理します。

| 機能 | Description |
| --- | --- |
| 認証 |Azure Data Lake Store は、Azure Active Directory (AAD) と統合することで、Azure Data Lake Store に格納されたすべてのデータの ID 管理とアクセス管理を行います。 この統合によって、Azure Data Lake Store は、AAD のあらゆる機能 (たとえば、多要素認証、条件付きアクセス、ロール ベースのアクセス制御、アプリケーション使用状況の監視、セキュリティの監視とアラート通知など) から恩恵を受けます。Azure Data Lake Store では、REST インターフェイスでの認証に対応する OAuth 2.0 プロトコルをサポートしています。 |
| Access control |Azure Data Lake Store では、WebHDFS プロトコルで公開された POSIX 形式のアクセス許可をサポートすることにより、アクセス制御を実現しています。 Data Lake Store パブリック プレビュー (現在のリリース) では、ルート フォルダー、サブフォルダー、個々のファイルで ACL を有効にすることができます。 Data Lake Store のコンテキストにおける ACL のしくみの詳細については、[Data Lake Store のアクセス制御](data-lake-store-access-control.md)に関する記事をご覧ください。 |
| Encryption |Data Lake Store では、アカウントに格納されているデータを暗号化することもできます。 暗号化設定は、Data Lake Store アカウントの作成時に指定します。 データを暗号化するかどうかを選択できます。 暗号化関連の構成を提供する方法の詳細については、「[Azure Portal で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)」をご覧ください。 |

Data Lake Store に格納されているデータのセキュリティ保護の詳細については、 以下のページを参照してください。

* Data Lake Store 内のデータをセキュリティで保護する方法については、「 [Azure Data Lake Store 内のデータをセキュリティで保護する](data-lake-store-secure-data.md)」を参照してください。
* ビデオの方がよいですか? [こちらのビデオ](https://mix.office.com/watch/1q2mgzh9nn5lx) をご覧ください。

## <a name="applications-compatible-with-azure-data-lake-store"></a>Azure Data Lake Store と互換性のあるアプリケーション
Azure Data Lake Store は、Hadoop のエコシステムを構成するほとんどのオープン ソース コンポーネントと共存することができます。 他の Azure サービスとの連携性にも優れています。 その点において、Data Lake Store はあらゆるデータ ストレージのニーズを満たす最適な選択肢といえます。 オープン ソース コンポーネントや各種 Azure サービスと Data Lake Store との連携について詳しくは、以下のページをご覧ください。

* Data Lake Store と相互運用可能な一連のオープン ソース アプリケーションについては、「 [Azure Data Lake Store で機能するオープン ソースのビッグ データ アプリケーション](data-lake-store-compatible-oss-other-applications.md) 」を参照してください。
* Data Lake Store と他の Azure サービスを組み合わせて広範なシナリオを有効にする方法を理解するには、「 [他の Azure サービスとの統合](data-lake-store-integrate-with-other-services.md) 」を参照してください。
* データの取り込み、データの処理、データのダウンロード、データの視覚化などの各種シナリオにおける Data Lake Store の使い方については、「 [Azure Data Lake Store に関するデータ シナリオ](data-lake-store-data-scenarios.md) 」を参照してください。

## <a name="what-is-azure-data-lake-store-file-system-adl"></a>Azure Data Lake Store ファイル システム (adl://) とは
Hadoop 環境 (HDInsight クラスターで使用可能) では、新しいファイル システムである AzureDataLakeFilesystem (adl://) 経由で Data Lake Store にアクセスできます。 adl:// を使用するアプリケーションとサービスでは、WebHDFS で現在まだサポートされていない、より有効なパフォーマンスの最適化を利用できるようになります。 結果として、Data Lake Store では、adl:// を使用する推奨オプションで最適なパフォーマンスを利用することも、引き続き WebHDFS API を直接使用することにより既存のコードを維持することもできるという柔軟性が得られます。 Azure HDInsight は、AzureDataLakeFilesystem をフルに活用して Data Lake Store で最適なパフォーマンスを実現します。

Data Lake Store 内のデータには、 `adl://<data_lake_store_name>.azuredatalakestore.net`を使用してアクセスすることができます。 Data Lake Store 内のデータへのアクセス方法の詳細については、「 [格納されたデータのプロパティを表示する](data-lake-store-get-started-portal.md#properties)

## <a name="how-do-i-start-using-azure-data-lake-store"></a>Azure Data Lake Store の使用を開始するにはどうすればよいですか?
Azure ポータルを使用して Data Lake Store をプロビジョニングする方法については、「 [Azure ポータルを使用してData Lake Store の使用を開始する](data-lake-store-get-started-portal.md)」を参照してください。 Azure Data Lake をプロビジョニングしたら、ビッグ データ ソリューション (Azure Data Lake Analytics、または Azure HDInsight と Data Lake Store の組み合わせ) を使用する方法を確認できます。 Azure Data Lake Store のアカウントを作成し操作 (データのアップロードやデータのダウンロードなど) を実行するための .NET アプリケーションを作成することもできます。

* [Azure Data Lake Analytics の使用を開始する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)
* [.NET SDK で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-net-sdk.md)

## <a name="data-lake-store-videos"></a>Data Lake Store のビデオ
Data Lake Store の一連の機能について紹介するビデオをご覧ください。

* [Azure Data Lake Store アカウントを作成する](https://mix.office.com/watch/1k1cycy4l4gen)
* [データ エクスプローラーを使用して Azure Data Lake Store のデータを管理する](https://mix.office.com/watch/icletrxrh6pc)
* [Azure Data Lake Analytics を Azure Data Lake Store に接続する](https://mix.office.com/watch/qwji0dc9rx9k)
* [Azure Data Lake Store に Data Lake Analytics 経由でアクセスする](https://mix.office.com/watch/1n0s45up381a8)
* [Azure HDInsight を Azure Data Lake Store に接続する](https://mix.office.com/watch/l93xri2yhtp2)
* [Azure Data Lake Store に Hive や Pig 経由でアクセスする](https://mix.office.com/watch/1n9g5w0fiqv1q)
* [DistCp (Hadoop Distributed Copy) を使用して Azure Data Lake Store にデータをコピーしたり Azure Data Lake Store からデータをコピーしたりする](https://mix.office.com/watch/1liuojvdx6sie)
* [リレーショナル ソースと Azure Data Lake Store との間で Apache Sqoop を使用してデータを移動する](https://mix.office.com/watch/1butcdjxmu114)
* [Azure Data Lake Store 用 Azure Data Factory を使用したデータのオーケストレーション](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Azure Data Lake Store でのデータのセキュリティ保護](https://mix.office.com/watch/1q2mgzh9nn5lx)




<!--HONumber=Dec16_HO2-->



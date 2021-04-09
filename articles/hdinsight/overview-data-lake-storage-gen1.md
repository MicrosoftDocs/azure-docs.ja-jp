---
title: HDInsight での Azure Data Lake Storage Gen1 の概要
description: HDInsight での Data Lake Storage Gen1 の概要について説明します。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 4ac1a48733d104e7674acbc13bfbb1e7a4cf02b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938872"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>HDInsight での Azure Data Lake Storage Gen1 の概要

Azure Data Lake Storage Gen1 は、ビッグ データの分析ワークロードに対応するエンタープライズ レベルのハイパースケール リポジトリです。 Azure Data Lake を使用すると、任意のサイズ、型、および取り込み速度のデータをキャプチャできます。 これは、運用分析や調査分析のために 1 か所で行われます。

Data Lake Storage Gen1 には、WebHDFS と互換性のある REST API を使用して Hadoop (HDInsight クラスターで使用可能) からアクセスします。 Data Lake Storage Gen1 は、格納されたデータに対する分析を可能にするように設計されており、データ分析シナリオ用にパフォーマンスがチューニングされます。 Gen1 には、企業における実際のユース ケースで不可欠な機能が組み込まれています。 これらの機能には、セキュリティ、管理の容易性、適応性、信頼性、および可能性が含まれます。

Azure Data Lake Storage Gen1 の詳細については、「[Azure Data Lake Storage Gen1 の概要](../data-lake-store/data-lake-store-overview.md)」の詳しい説明を参照してください。

Data Lake Storage Gen1 の主要な機能を以下に示します。

## <a name="compatibility-with-hadoop"></a>Hadoop との互換性

Data Lake Storage Gen1 は、HDFS および Hadoop 環境と互換性のある Apache Hadoop ファイル システムです。  WebHDFS API を使用する HDInsight アプリケーションまたはサービスは、Data Lake Storage Gen1 と簡単に統合することができます。 Data Lake Storage Gen1 では、アプリケーション向けの WebHDFS と互換性のある REST インターフェイスも公開されています。

Data Lake Storage Gen1 に格納されたデータは、Hadoop 分析フレームワークを使用して簡単に分析することができます。 MapReduce や Hive などのフレームワーク。 Data Lake Storage Gen1 に格納されたデータに直接アクセスするように Azure HDInsight クラスターをプロビジョニングおよび構成することができます。

## <a name="unlimited-storage-petabyte-files"></a>無制限のストレージ、ペタバイト ファイル

Data Lake Storage Gen1 では無制限のストレージが提供されます。そのため、分析を目的としてさまざまな種類のデータを格納するのに適しています。 アカウント サイズまたはファイル サイズに制限はありません。 データ レイクに格納できるデータの量も同様です。 対応可能な個々のファイルのサイズはキロバイトからペタバイトの範囲にわたり、任意の種類のデータを Data Lake Storage Gen1 に自由に格納することができます。 データは複数のコピーを作成して格納されるため、障害が発生しても保護されます。 また、Data Lake でのデータの格納期間に制限はありません。

## <a name="performance-tuning-for-big-data-analytics"></a>ビッグ データを分析するためのパフォーマンス チューニング

Data Lake Storage Gen1 は、分析システム向けに構築されています。 大量のデータのクエリと分析に大量のスループットを必要とするシステム。 Data Lake では、ファイル内のデータを複数の異なる記憶域サーバーに分散します。 このセットアップにより、データ分析時にファイルを並列に読み取る場合の読み取りスループットが向上します。

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>エンタープライズへの対応:高可用性およびセキュリティ

Data Lake Storage Gen1 では、業界標準の可用性と信頼性が提供されます。 データ資産は、冗長なコピーを作成して格納されるため、予期せぬ障害が発生しても保護されます。 企業では、実際のソリューションで既存のデータ プラットフォームの重要な部分として Data Lake Storage Gen1 を使用できます。

Data Lake Storage Gen1 では、格納されたデータに対してエンタープライズ レベルのセキュリティも提供されます。 詳細については、[Azure Data Lake Storage Gen1 内のデータのセキュリティ保護](#data-security-in-data-lake-storage-gen1)に関するページを参照してください。

## <a name="flexible-data-structures"></a>柔軟なデータ構造

Data Lake Storage Gen1 では、任意のデータをネイティブ形式でそのまま格納することができ、事前の変換は不要です。 Data Lake Storage Gen1 では、データが読み込まれる前にスキーマを定義する必要はありません。 分析時にデータを解釈してスキーマを定義するかどうかは個々の分析フレームワークに任されます。 Data Lake Storage Gen1 は構造化データを扱うことができます。 半構造化データ、および非構造化データも同様です。

Data Lake Storage Gen1 のデータ コンテナーは、本質的にはフォルダーとファイルです。 格納されたデータを SDK、Azure Portal、および Azure PowerShell を使用して操作します。 これらのインターフェイスとコンテナーを使用してストアに格納されるデータは、任意のデータ型を格納できます。 Data Lake Storage Gen1 では、データの種類に基づくデータの特別な処理は行われません。

## <a name="data-security-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1 のデータのセキュリティ

Data Lake Storage Gen1 では、Azure Active Directory を使用して認証を行い、アクセス制御リスト (ACL) を使用してデータへのアクセスを管理します。

| **機能** | **説明** |
| --- | --- |
| 認証 |Data Lake Storage Gen1 では、Azure Active Directory (Azure AD) と統合することで、Data Lake Storage Gen1 に格納されたすべてのデータの ID およびアクセスの管理を行います。 この統合により、Data Lake Storage Gen1 では、Azure AD のすべての機能の利点が得られます。 これらの機能には、多要素認証、条件付きアクセス、Azure ロールベースのアクセス制御などがあります。 また、アプリケーションの使用状況の監視、セキュリティの監視とアラートなどを行うこともできます。 Data Lake Storage Gen1 では、REST インターフェイスでの認証に対応する OAuth 2.0 プロトコルがサポートされます。 「[Azure Data Lake Storage Gen1 での Azure Active Directory を使用した認証](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)」を参照してください。|
| アクセス制御 |Data Lake Storage Gen1 では、WebHDFS プロトコルで公開された POSIX 形式のアクセス許可をサポートすることにより、アクセス制御が提供されます。 ルート フォルダー、サブフォルダー、個々のファイルで ACL を有効にすることができます。 Data Lake Storage Gen1 のコンテキストにおける ACL のしくみの詳細については、「[Data Lake Storage Gen1 でのアクセス制御](../data-lake-store/data-lake-store-access-control.md)」を参照してください。 |
| 暗号化 |Data Lake Storage Gen1 では、アカウントに格納されているデータを暗号化することもできます。 暗号化設定は、Data Lake Storage Gen1 アカウントの作成時に指定します。 データを暗号化するかどうかを選択できます。 詳細については、[Data Lake Storage Gen1 での暗号化](../data-lake-store/data-lake-store-encryption.md)に関するページを参照してください。 暗号化関連の構成を提供する方法については、「[Azure Portal で Azure Data Lake Storage Gen1 の使用を開始する](../data-lake-store/data-lake-store-get-started-portal.md)」を参照してください。 |

Data Lake Storage Gen1 内のデータのセキュリティ保護の詳細については、「[Azure Data Lake Storage Gen1 に格納されているデータのセキュリティ保護](../data-lake-store/data-lake-store-secure-data.md)」を参照してください。

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Data Lake Storage Gen1 と互換性のあるアプリケーション

Data Lake Storage Gen1 は、Hadoop 環境内のほとんどのオープンソース コンポーネントと互換性があります。 他の Azure サービスとの連携性にも優れています。  Data Lake Storage Gen1 をオープンソース コンポーネントとその他の Azure サービスの両方と共に使用する方法の詳細については、下のリンクに従ってください。

* 「[Azure Data Lake Storage Gen1 と互換性のあるオープン ソースのビッグ データ アプリケーション](../data-lake-store/data-lake-store-compatible-oss-other-applications.md)」を参照してください。
* Data Lake Storage Gen1 を他の Azure サービスと共に使用して広範なシナリオを有効にする方法については、「[Azure Data Lake Storage Gen1 と他の Azure サービスを統合する](../data-lake-store/data-lake-store-integrate-with-other-services.md)」を参照してください。
* 「[Data Lake Storage Gen1 を使用してビッグ データの要件に対応する](../data-lake-store/data-lake-store-data-scenarios.md)」を参照してください。

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 ファイル システム (adl://)

Hadoop 環境では、新しいファイル システムである AzureDataLakeFilesystem (adl://) 経由で Data Lake Storage Gen1 にアクセスできます。 `adl://` を使用するアプリケーションとサービスのパフォーマンスは、WebHDFS で現在まだサポートされていない方法で最適化できます。 その結果、推奨される adl:// を使用して最適なパフォーマンスを実現できる柔軟性が得られます。 または、引き続き WebHDFS API を直接使用して、既存のコードを維持します。 Azure HDInsight は、AzureDataLakeFilesystem をフルに活用して Data Lake Storage Gen1 で最適なパフォーマンスを実現します。

Data Lake Storage Gen1 内のデータには、以下の URI を使用してアクセスすることができます。

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Data Lake Storage Gen1 内のデータへのアクセス方法の詳細については、「[保存データに対して実行できるアクション](../data-lake-store/data-lake-store-get-started-portal.md#properties)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Data Lake Storage Gen2 の概要](../storage/blobs/data-lake-storage-introduction.md)
* [Azure ストレージの概要](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage Gen2 の概要](./overview-data-lake-storage-gen2.md)
---
title: HDI で .NET for Apache Spark のバージョンを v1.0 に更新する
description: HDI で .NET for Apache Spark のバージョンを 1.0 に更新する方法と、それが既存のコードおよびクラスターに与える影響について説明します。
author: Niharikadutta
ms.author: nidutta
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/05/2021
ms.openlocfilehash: a1602f29a6d0066ec3c99e990532411621652c47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788131"
---
# <a name="updating-net-for-apache-spark-to-version-v10--in-hdinsight"></a>HDInsight で .NET for Apache Spark のバージョンを v1.0 に更新する

このドキュメントでは、[.NET for Apache Spark](https://github.com/dotnet/spark) の最初のメジャー バージョンと、それが HDInsight クラスター内の現在の運用パイプラインに与える可能性のある影響について説明します。

## <a name="about-net-for-apache-spark-version-100"></a>.NET for Apache Spark バージョン 1.0.0 について

これは、.NET for Apache Spark の最初の[公式メジャー リリース](https://github.com/dotnet/spark/releases/tag/v1.0.0)であり、Spark 2.4.x および Spark 3.0.x 用の DataFrame API の完全性とその他の機能を提供します。 すべての機能、機能強化、バグ修正の完全な一覧については、公式の [v1.0.0 リリース ノート](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md)を参照してください。
注意すべきもう 1 つの重要な点は、このバージョンは、以前のバージョンの `Microsoft.Spark` および `Microsoft.Spark.Worker` と互換性が **ない** ことです。 .NET for Apache Spark アプリケーションを v1.0.0 と互換性を維持するようにアップグレードする予定である場合は、[移行ガイド](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10)を参照してください。

## <a name="using-net-for-apache-spark-v10-in-hdinsight"></a>HDInsight での .NET for Apache Spark v1.0 の使用

現在の HDI クラスターは影響を受けませんが (つまり、以前と同じバージョンのままです)、新しく作成される HDI クラスターは、.NET for Apache Spark のこの最新の v1.0.0 バージョンを実行します。 これは、次のことを意味します。

- **以前の HDI クラスターを使用する場合**: Spark .NET アプリケーションを v1.0.0 にアップグレードする (推奨) 場合、HDI クラスター上の `Microsoft.Spark.Worker` のバージョンを更新する必要があります。 詳細については、「[HDI クラスター上の .NET for Apache Spark のバージョン変更](#changing-net-for-apache-spark-version-on-hdinsight)」セクションを参照してください。
アプリケーションで .NET for Apache Spark の現行バージョンを更新しない場合は、それ以上の手順は必要ありません。  

- **新しい HDI クラスターを使用する場合**: Spark .NET アプリケーションを v1.0.0 にアップグレードする (推奨) 場合、HDI 上の worker を変更するための手順は必要ありませんが、[移行ガイド](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10)を参照して、コードおよびパイプラインの更新に必要な手順を理解する必要があります。
アプリケーションで .NET for Apache Spark の現行バージョンを変更しない場合は、HDI クラスター上のバージョンを v1.0 (新しいクラスター上の既定) から、使用しているバージョンに変更する必要があります。 詳細については、「[HDI クラスター上の .NET for Apache Spark のバージョン変更](spark-dotnet-version-update.md#changing-net-for-apache-spark-version-on-hdinsight)」セクションを参照してください。  

## <a name="changing-net-for-apache-spark-version-on-hdinsight"></a>HDInsight 上の .NET for Apache Spark のバージョン変更

### <a name="deploy-microsoftsparkworker"></a>Microsoft.Spark.Worker をデプロイする

`Microsoft.Spark.Worker` は、Spark クラスターの個々のワーカー ノードに存在するバックエンド コンポーネントです。 C# UDF (ユーザー定義関数) を実行する必要がある場合、Spark では、この UDF を実行するために .NET CLR の起動方法を理解する必要があります。 `Microsoft.Spark.Worker` により、この機能を有効にするクラスのコレクションが Spark に提供されます。 HDI クラスターにデプロイする .NET for Apache Spark のバージョンに応じて、worker のバージョンを選択します。

1. 特定のバージョンの Microsoft.Spark.Worker Linux リリースをダウンロードします。 たとえば、`.NET for Apache Spark v1.0.0` が必要な場合、[Microsoft.Spark.Worker.netcoreapp3.1.linux-x64-1.0.0.tar.gz](https://github.com/dotnet/spark/releases/tag/v1.0.0) をダウンロードします。  

2. [install-worker.sh](https://github.com/dotnet/spark/blob/master/deployment/install-worker.sh) スクリプトをダウンロードして、手順 1 でダウンロードしたワーカー バイナリを、HDI クラスターのすべてのワーカー ノードにインストールします。  

3. 前述のファイルを、ご使用のクラスターがアクセスできる Azure Storage アカウントにアップロードします。 詳細については、[.NET for Apache Spark HDI のデプロイに関する記事](/dotnet/spark/tutorials/hdinsight-deployment#upload-files-to-azure)を参照してください。

4. スクリプト アクションを使用して、クラスターのすべてのワーカー ノードで `install-worker.sh` スクリプトを実行します。 詳細については、[.NET for Apache Spark HDI のデプロイに関する記事](/dotnet/spark/tutorials/hdinsight-deployment#run-the-hdinsight-script-action)を参照してください。

### <a name="update-your-application-to-use-specific-version"></a>特定のバージョンを使用するようにアプリケーションを更新する

特定のバージョンを使用するように .NET for Apache Spark アプリケーションを更新するには、プロジェクトで [Microsoft.Spark NuGet package](https://www.nuget.org/packages/Microsoft.Spark/) の必要なバージョンを選択します。 その特定のバージョンのリリース ノートと前述の[移行ガイド](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) (アプリケーションを v1.0.0 に更新することを選択した場合) を必ず確認してください。

## <a name="faqs"></a>FAQ

### <a name="will-my-existing-hdi-cluster-with-version--100-start-failing-with-the-new-release"></a>バージョンが 1.0.0 未満の既存の HDI クラスターは、新しいリリースでは失敗しますか?

既存の HDI クラスターでは、.NET for Apache Spark は以前と同じバージョンであるため、(以前のバージョンの Spark .NET を含む) 既存のアプリケーションは影響を受けません。

## <a name="next-steps"></a>次のステップ

[.NET for Apache Spark アプリケーションを HDInsight にデプロイする](/dotnet/spark/tutorials/hdinsight-deployment)
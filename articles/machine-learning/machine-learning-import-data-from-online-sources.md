---
title: "オンライン データ ソースから Machine Learning Studio にデータをインポートする | Microsoft Docs"
description: "さまざまなオンライン ソースから Azure Machine Learning Studio にトレーニング データをインポートする方法"
keywords: "データのインポート、データ形式、データ型、データ ソース、トレーニング データ"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 701b93fe-765b-4d15-a1cf-9b607f17add6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev;garye
translationtype: Human Translation
ms.sourcegitcommit: a6bc79b2cb5b73109cddd6cf57caeba754b52e2e
ms.openlocfilehash: afecdde0cbc0bcbe0932b23dc1a8e067d02ded12
ms.lasthandoff: 12/20/2016


---
# <a name="import-data-into-azure-machine-learning-studio-from-various-online-data-sources-with-the-import-data-module"></a>データのインポート モジュールで各種オンライン データ ソースから Azure Machine Learning Studio にデータをインポートする
この記事では、各種ソースからのオンライン データのインポートのサポートについて説明し、これらのソースから Azure Machine Learning の実験にデータを移動するために必要な情報を提供します。

> [!NOTE]
> この記事では、[データのインポート][import-data] モジュールに関する一般的な情報を提供します。 アクセスできるデータの種類、形式、パラメーターの詳細や、よく寄せられる質問への回答については、[データのインポート][import-data] モジュールのリファレンス トピックをご覧ください。
> 
> 

<!-- -->

[!INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

## <a name="introduction"></a>はじめに
[データのインポート][import-data] モジュールを使用すると、[Azure Machine Learning Studio](https://studio.azureml.net/Home) で実験を実行する際に、さまざまなオンライン データ ソースのデータにアクセスできます。

* HTTP を使用した Web URL
* HiveQL を使用した Hadoop
* Azure BLOB ストレージ
* Azure テーブル
* Azure SQL Database または Azure VM の SQL Server
* オンプレミス SQL Server データベース
* データ フィード プロバイダー (現在は OData)

Studio の実験でオンライン データ ソースにアクセスするには、[データのインポート][import-data] モジュールを追加し、**[データ ソース]** を選択して、データ アクセスに必要なパラメーターを指定します。 サポートされているオンライン データ ソースは、以下の表にまとめました。 サポートされているファイル形式と、データにアクセスするためのパラメーターも記載されています。

このトレーニング データは実験中にアクセスされるため、利用できるのは、その実験が行われている間だけです。 一方、データセット モジュールに保存されているデータには、ワークスペース内の任意の実験からアクセスできます。

> [!IMPORTANT]
> 現在、[データのインポート][import-data] モジュールと [データのエクスポート][export-data] モジュールでは、クラシック デプロイメント モデルを使用して作成された Azure Storage からのみ、データの読み取りと書き込みを行うことができます。 つまり、ホット ストレージ アクセス層またはクール ストレージアクセス層を利用できる新しい Azure BLOB ストレージ アカウントの種類は、まだサポートされていません。 
> 
> 一般的に、このサービス オプションが利用可能になる前に作成された可能性のある Azure ストレージ アカウントに、影響が及ぶことはありません。 
> 新しいアカウントを作成する必要がある場合は、デプロイメント モデルとして **[クラシック]** を選択するか、Resource Manager を使用して、**[アカウントの種類]** に **[Blob ストレージ]** ではなく **[General (汎用)]** を選択します。 
> 
> 詳細については、「 [Azure Blob Storage: ホット層とクール層](../storage/storage-blob-storage-tiers.md)」を参照してください。
> 
> 

## <a name="supported-online-data-sources"></a>サポートされるオンライン データ ソース
Azure Machine Learning の **データのインポート** モジュールは、以下のデータ ソースをサポートしています。

| [データ ソース] | Description | パラメーター |
| --- | --- | --- |
| HTTP を使用する Web URL |コンマ区切り (CSV)、タブ区切り (TSV)、ARFF (Attribute-Relation File Format)、サポート ベクター マシン (SVM-Light) の各形式のデータを HTTP を使用する任意の Web URL から読み取ります。 |<b>URL</b>: サイトの URL とファイル名、拡張子を含むファイルの完全名を指定します。 <br/><br/><b>データ形式</b>: サポートされているいずれかのデータ形式 (CSV、TSV、ARFF、SVM-light) を指定します。 データにヘッダー行がある場合、その行が列名の割り当てに使用されます。 |
| Hadoop/HDFS |Hadoop の分散ストレージからデータを読み取ります。 必要なデータは、HiveQL という SQL に似たクエリ言語で指定します。 Machine Learning Studio にデータを追加する前に、HiveQL を使用してデータを集計したり、データのフィルタリングを実行したりすることもできます。 |<b>Hive データベース クエリ</b>: データを生成するために使用する Hive クエリを指定します。<br/><br/><b>HCatalog サーバー URI</b>: *&lt;your cluster name&gt;.azurehdinsight.net* 形式でクラスターの名前を指定します。<br/><br/><b>Hadoop ユーザー アカウント名</b>: クラスターをプロビジョニングするために使用する Hadoop ユーザー アカウント名を指定します。<br/><br/><b>Hadoop ユーザー アカウントのパスワード</b>: クラスターをプロビジョニングするために使用する資格情報を指定します。 詳細については、[HDInsight での Hadoop クラスターの作成](../hdinsight/hdinsight-provision-clusters.md)に関する記事をご覧ください。<br/><br/><b>出力データの場所</b>: データを Hadoop 分散ファイル システム (HDFS) に保存するか、Azure に保存するかを指定します。 <br/><ul>出力データを HDFS に保存する場合、HDFS サーバーの URI を指定します (必ず、HTTPS:// プレフィックスなしの HDInsight クラスター名を使用してください)。 <br/><br/>出力データを Azure に保存する場合は、Azure ストレージ アカウント名、ストレージ アクセス キー、ストレージ コンテナー名を指定する必要があります。</ul> |
| SQL データベース |Azure 仮想マシン上で動作する SQL Server データベースに保存されているデータまたは Azure SQL Database に保存されているデータを読み取ります。 |<b>データベース サーバー名</b>: データベースが実行されているサーバーの名前を指定します。<br/><ul>Azure SQL Database の場合は、生成されたサーバー名を入力してください。 通常、*&lt;generated_identifier&gt;.database.windows.net.* 形式となります。 <br/><br/>Azure 仮想マシンでホストされる SQL Server の場合は、*tcp:&lt;Virtual Machine DNS Name&gt;, 1433* 形式で入力します。</ul><br/><b>データベース名</b>: サーバー上のデータベースの名前を指定します。 <br/><br/><b>サーバー ユーザー アカウント名</b>: データベースへのアクセス権限を持つアカウントのユーザー名を指定します。 <br/><br/><b>サーバー ユーザー アカウントのパスワード</b>: ユーザー アカウントのパスワードを指定します。<br/><br/><b>すべてのサーバー証明書を受け入れる</b>: データを読み取る前に、サイト証明書の確認をスキップする場合は、このオプションを使用します (セキュリティが低下します)。<br/><br/><b>データベース クエリ</b>: 読み取るデータを記述した SQL ステートメントを入力します。 |
| オンプレミス SQL データベース |オンプレミス SQL データベースに格納されているデータを読み取ります。 |<b>データ ゲートウェイ</b>: SQL Server データベースにアクセスできるコンピューターにインストールされている Data Management Gateway の名前を指定します。 ゲートウェイの設定については、[オンプレミス SQL Server のデータを使用した Azure Machine Learning での高度な分析の実行](machine-learning-use-data-from-an-on-premises-sql-server.md)に関する記事をご覧ください。<br/><br/><b>データベース サーバー名</b>: データベースが実行されているサーバーの名前を指定します。<br/><br/><b>データベース名</b>: サーバー上のデータベースの名前を指定します。 <br/><br/><b>サーバー ユーザー アカウント名</b>: データベースへのアクセス権限を持つアカウントのユーザー名を指定します。 <br/><br/><b>ユーザー名とパスワード</b>: <b>[Enter values (値の入力)]</b> をクリックして、データベース資格情報を入力します。 オンプレミス SQL Server の構成方法に応じて、Windows 統合認証または SQL Server 認証を使用することができます。<br/><br/><b>データベース クエリ</b>: 読み取るデータを記述した SQL ステートメントを入力します。 |
| Azure テーブル |Azure Storage の Table service からデータを読み取ります。<br/><br/>大量のデータを低頻度で読み取る場合、Azure Table service を使用してください。 高い柔軟性、非リレーショナル (NoSQL)、圧倒的なスケーラビリティ、低コスト、高い可用性を特徴とするストレージ ソリューションです。 |アクセスの対象がパブリックな情報であるか、ログイン資格情報を必要とするプライベートなストレージ アカウントであるかによって**データのインポート**のオプションは変化します。 その点を決めるのが、<b>認証の種類</b>です。"PublicOrSAS" と "Account" のいずれかの値になります。その値によって、一連のパラメーターが異なります。 <br/><br/><b>パブリックな URI または Shared Access Signature (SAS) の URI</b>: パラメーターは次のとおりです。<br/><br/><ul><b>テーブルの URI</b>: テーブルのパブリック URL または SAS URL を指定します。<br/><br/><b>プロパティ名のスキャン対象となる行を指定します</b>: 指定した行数をスキャンする場合は <i>TopN</i> を、テーブル内のすべての行を取得する場合は <i>ScanAll</i> を値として指定します。 <br/><br/>データが均一で予測可能である場合は、*TopN* を選択し、N の値を入力することをお勧めします。大きなテーブルでは、その方が読み取り時間が短くなります。<br/><br/>データを構成する一連のプロパティが、テーブルの深さと位置によって異なる場合は、*ScanAll* オプションを選択してすべての行をスキャンしてください。 これによって、プロパティとメタデータの変換結果に整合性を確保することができます。<br/><br/></ul><b>プライベート ストレージ アカウント</b>: パラメーターは次のとおりです。 <br/><br/><ul><b>アカウント名</b>: 読み取るテーブルを含んだアカウントの名前を指定します。<br/><br/><b>アカウント キー</b>: アカウントに関連付けられているストレージ キーを指定します。<br/><br/><b>テーブル名</b>: 読み取るデータを含んだテーブルの名前を指定します。<br/><br/><b>プロパティ名のスキャン対象となる行</b>: 指定した行数をスキャンする場合は <i>TopN</i> を、テーブル内のすべての行を取得する場合は <i>ScanAll</i> を値として指定します。<br/><br/>データが均一で予測可能である場合は、*TopN* を選択し、N の値を入力することをお勧めします。大きなテーブルでは、その方が読み取り時間が短くなります。<br/><br/>データを構成する一連のプロパティが、テーブルの深さと位置によって異なる場合は、*ScanAll* オプションを選択してすべての行をスキャンしてください。 これによって、プロパティとメタデータの変換結果に整合性を確保することができます。<br/><br/> |
| Azure BLOB ストレージ |画像、非構造化テキスト、バイナリ データなど、Azure Storage の Blob service に保存されているデータを読み取ります。<br/><br/>Blob service を使用して、データをパブリックに公開したり、アプリケーション データをプライベートに保存したりすることができます。 HTTP または HTTPS 接続を使用してどこからでもデータにアクセスできます。 |アクセスの対象がパブリックな情報であるか、ログイン資格情報を必要とするプライベートなストレージ アカウントであるかによって**データのインポート** モジュールのオプションは変化します。 その点を決めるのが<b>認証の種類</b>です。"PublicOrSAS" と "Account" のいずれかの値になります。<br/><br/><b>パブリックな URI または Shared Access Signature (SAS) の URI</b>: パラメーターは次のとおりです。<br/><br/><ul><b>URI</b>: Storage BLOB のパブリック URL または SAS URL を指定します。<br/><br/><b>ファイル形式</b>: Blob service のデータの形式を指定します。 サポートされている形式は、CSV、TSV、ARFF です。<br/><br/></ul><b>プライベート ストレージ アカウント</b>: パラメーターは次のとおりです。 <br/><br/><ul><b>アカウント名</b>: 読み取る BLOB を含んだアカウントの名前を指定します。<br/><br/><b>アカウント キー</b>: アカウントに関連付けられているストレージ キーを指定します。<br/><br/><b>コンテナー、ディレクトリ、BLOB のパス</b>: 読み取るデータを含んだ BLOB の名前を指定します。<br/><br/><b>BLOB ファイル形式</b>: Blob service のデータの形式を指定します。 サポートされているデータ形式は CSV、TSV、ARFF、CSV (エンコーディング指定付き)、Excel です。 <br/><br/><ul>形式が CSV または TSV である場合は、ファイルにヘッダー行が含まれているかどうかを必ず指定してください。<br/><br/>Excel を選択すると、Excel ブックからデータを読み取ることができます。 <i>[Excel データ形式]</i> オプションで、データの保存先が Excel ワークシートの範囲か Excel テーブルかを指定してください。 <i>[Excel sheet or embedded table (Excel シートまたは埋め込みテーブル)]</i> オプションで、読み取りの対象となるシートまたはテーブルの名前を指定します。</ul><br/> |
| データ フィード プロバイダー |サポートされているフィード プロバイダーからデータを読み取ります。 現在サポートされているのは Open Data Protocol (OData) 形式だけです。 |<b>データ コンテンツの種類</b>: OData 形式を指定します。<br/><br/><b>ソース URL</b>: データ フィードに使用する完全 URL を指定します。 <br/>次の URL は、Northwind サンプル データベースから読み取る例です: http://services.odata.org/northwind/northwind.svc/ |

## <a name="next-steps"></a>次のステップ

[データのインポート モジュールとエクスポート モジュールを使用する Azure ML Web サービスのデプロイ](machine-learning-web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


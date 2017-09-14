---
title: "分析プラットフォーム: Apache Storm と Stream Analytics の比較 | Microsoft Docs"
description: "Apache Storm と Stream Analytics を比較して、クラウドの分析プラットフォームを選択する際の指針を示します。 機能と違いについて説明します。"
keywords: "分析プラットフォーム、クラウド分析プラットフォーム、storm との比較"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: b9aac017-9866-4d0a-b98f-6f03881e9339
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/27/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 97044cb5d7b0b3fcb3b85328df618a265bc59b61
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---
# <a name="choosing-a-streaming-analytics-platform-comparing-apache-storm-and-azure-stream-analytics"></a>Stream Analytics プラットフォームの選択: Apache Storm と Azure Stream Analytics との比較
Azure にはストリーミング データを分析するソリューションとして [Azure Streaming Analytics](https://docs.microsoft.com/azure/stream-analytics/) や [Azure HDInsight 上の Apache Storm](https://azure.microsoft.com/services/hdinsight/apache-storm/) といった複数のソリューションがあります。 どちらの分析プラットフォームにも PaaS ソリューションのメリットがありますが、 2 つのプラットフォームには機能と構成および管理の方法に大きな違いがあります。 

この記事では、Apache Storm と Azure Stream Analytics の機能を比較して、クラウドの分析プラットフォームを選択する際の指針を示します。 

## <a name="general-features"></a>全般的な機能

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>HDInsight での Apache Storm</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>オープン ソース</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
いいえ。 Azure Stream Analytics は、Microsoft 独自のサービスです。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
はい。 Apache Storm は、Apache がライセンス供与するテクノロジです。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Microsoft によるサポート</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
あり </p>
            </td>
            <td width="246" valign="top">
                <p>
はい </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>ハードウェア要件</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
なし。 Azure Stream Analytics はAzure のサービスです。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
なし。 Apache Storm は、Azure のサービスです。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>上位レベルのユニット</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
ユーザーはストリーミング ジョブをデプロイし、監視します。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
ユーザーは複数のストーム ジョブやその他のワークロード (バッチを含む) をホストできる、クラスター全体をデプロイし、監視します。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>価格</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
処理するデータのボリュームと必要な (ジョブが実行されている 1 時間あたりの) ストリーミング ユニットの数によって請求されます。 
                </p>
                    <p>詳細については、「<a href="http://azure.microsoft.com/pricing/details/stream-analytics/">Stream Analytics の価格</a>」をご覧ください。</p>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
購入単位はクラスター ベースであり、デプロイされるジョブとは無関係に、実行されるクラスターの時間に基づいて請求されます。
                </p>
                <p>
詳細については、「<a href="http://azure.microsoft.com/pricing/details/hdinsight/">HDInsight の価格</a>」をご覧ください。
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="authoring"></a>作成

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>HDInsight での Apache Storm</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>機能: SQL DSL</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
はい。 Stream Analytics には SQL に類似した変換用の言語があります。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
いいえ。 コードを Java または C#、あるいは Trident API で記述する必要があります。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>機能: 一時的な演算子</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
ウィンドウ表示の集計と、一時的な結合は標準でサポートされています。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
一時的な演算子は、ユーザーが実装する必要があります。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>開発環境</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
ユーザーはライブ ストリームから派生したサンプル データを利用して Azure ポータルからジョブを作成、デバッグ、監視できます。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
開発、デバッグ、監視の操作性については、.NET のユーザーの場合は Visual Studio を使用しますが、 Java とその他の言語を使用する場合は、IDE を使用することもできます。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>デバッグのサポート</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
デバッグの方法として基本的なジョブの状態と操作ログを提供しますが、 現在、Stream Analytics には、ログに何をどれだけ含めるかを指定するといった柔軟性 (たとえば、詳細モード) はありません。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
詳細なログを利用できます。 ユーザーがログを表示するには、Visual Studio による方法と、クラスターにログインして直接ログにアクセスする方法があります。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>カスタム コードで拡張可能</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
JavaScript UDF では部分的にサポートします。 詳細については、<a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-javascript-user-defined-functions">JavaScript UDF の統合</a>に関する記事をご覧ください。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
はい。 C#、Java や、Storm でサポートされているその他の言語でカスタム コードを記述することができます。
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="data-sources-inputs-and-outputs"></a>データ ソースと出力 ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>HDInsight での Apache Storm</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>入力データ ソース</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Azure Event Hubs と Azure Blob ストレージ。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Azure Event Hubs、Azure Service Bus、Kafka などに使用可能なコネクタがあります。 カスタム コードを使用して作成できるコネクタもあります。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>入力データ形式</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Avro、JSON、CSV </p>
            </td>
            <td width="246" valign="top">
                <p>
カスタム コードを使用して任意の形式を実装できます。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>出力</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
ストリーミング ジョブには、複数の出力がある場合があります。 サポートされている出力: Azure Event Hubs、Azure Blob Storage、Azure Table Storage、Azure SQL DB、PowerBI。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Storm はトポロジの多くの出力をサポートしており、各出力にはダウンストリーム処理のためのカスタム ロジックがある場合があります。 Storm には、PowerBI、Azure Event Hubs、Azure Blob Storage、Azure Cosmos DB、SQL、HBase のコネクタが含まれています。 カスタム コードを使用して作成できるコネクタもあります。    
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>データ エンコード形式</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
データは UTF-8 形式にする必要があります。
                </p>
            </td>   
            <td width="246" valign="top">
                <p>
カスタム コードを使用して任意のデータ エンコード形式を実装できます。
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="management-and-operations"></a>管理と操作 ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>HDInsight での Apache Storm</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>ジョブのデプロイメント モデル</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Azure ポータル、PowerShell、REST API
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Azure ポータル、PowerShell、 Visual Studio、REST API
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>監視 (統計、カウンターなど)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
監視は、Azure ポータルと REST API を使用して実装されます。 ユーザーは Azure のアラートを構成することもできます。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
監視は、Storm UI と REST API を使用して実装されます。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>拡張性</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
スケーラビリティは、各ジョブのストリーミング ユニット (SU) の数によって決定されます。 各ストリーミング ユニットは、最大 1 MB/秒、最大 50 個のユニットを処理します。 詳細については、<a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-jobs">ストリーム データ処理スループット向上のための Azure Stream Analytics ジョブのスケーリング</a>に関する記事をご覧ください。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
スケーラビリティは、HDInsight Storm クラスター内のノードの数によって決定されます。 最上位ノードの数の制限は、ユーザーの Azure クォータによって定義されます。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>データ処理の制限</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
ユーザーは、データ処理を増やすか、1 GB/秒の数の上限にストリーミング ユニットの数を増減することによってコストを最適化することができます。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
ユーザーは、クラスターのサイズを拡大または縮小できます。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>停止/再開</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
停止し、最後に停止した場所から再開できます。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
停止し、ウォーターマークに基づいて最後に停止した場所から再開できます。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>サービスとフレームワークの更新</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
ダウンタイムのない自動修正プログラム。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
ダウンタイムのない自動修正プログラム。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>保証された SLA を備えた高可用性サービスによるビジネス継続性</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <ul>
                <li>アップタイム 99.9% の SLA</li>
                <li>障害からの自動復旧</li>
                <li>組み込みのステートフルな一時的な演算子の復旧</li>
                </ul>
            </td>
            <td width="246" valign="top">
                <p>
Storm クラスターの アップタイム 99.9% の SLA。 
                </p>
                <p>
Apache Storm は、フォールト トレラント ストリーミング プラットフォームですが、 ストリーミング ジョブを中断することなく実行するのはユーザーの責任です。
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="advanced-features"></a>高度な機能 ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>HDInsight での Apache Storm</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>遅延着信と順不同イベントの処理</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
イベントの順序変更、ドロップ、イベント時間の調整のための構成可能なポリシーが組み込まれています。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
ユーザーは、ロジックを実装してこのシナリオを処理する必要があります。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>参照データ</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
参照データは、最大サイズ 100 MB のメモリ内キャッシュを備えた Azure Blob ストレージから使用できます。 参照データの更新は、サービスによって管理されます。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
データ サイズに制限はありません。 HBase、Azure Cosmos DB、SQL Server、Azure のコネクタが利用可能です。 カスタム コードを使用して作成できるコネクタもあります。 参照データは、カスタム コードを使用して更新する必要があります。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Machine Learning との統合</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
ジョブの作成中に、公開済みの Azure Machine Learning モデルを関数として構成します。 詳細については、「<a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-with-machine-learning-functions">Azure Machine Learning 関数を使用した Stream Analytics ジョブのスケーリング</a>」をご覧ください。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Storm Bolt で使用できます。
                </p>
            </td>
        </tr>
    </tbody>
</table>


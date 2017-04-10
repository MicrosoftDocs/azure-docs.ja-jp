---
title: "分析プラットフォーム: Apache Storm と Stream Analytics の比較 | Microsoft Docs"
description: "Apache Storm と Stream Analytics を比較して、クラウドの分析プラットフォームを選択する際の指針を示します。 機能と違いについて説明します。"
keywords: "分析プラットフォーム、クラウド分析プラットフォーム、storm との比較"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: b9aac017-9866-4d0a-b98f-6f03881e9339
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 9dcac84fdc33515ef12ab5b8154f700bb8618290
ms.lasthandoff: 03/18/2017


---
# <a name="help-choosing-a-streaming-analytics-platform-apache-storm-comparison-to-azure-stream-analytics"></a>Stream Analytics プラットフォームの選択: Apache Storm と Azure Stream Analytics との比較
以下では、Apache Storm と Azure Stream Analytics を比較して、クラウドの分析プラットフォームを選択する際の指針を示します。 また、実際のビジネス事例に適したソリューションを選択できるように、Azure HDInsight の管理されたサービスとして Stream Analytics と Apache Storm が持つ価値提案について説明します。

どちらの分析プラットフォームにも PaaS ソリューションのメリットがありますが、2 つのプラットフォームを区別する主な機能がいくつかあります。 2 つのサービスの機能と制限を一覧にまとめました。この一覧を参照して、目標を達成するために必要なソリューションを選択してください。

## <a name="storm-comparison-to-stream-analytics-general-features"></a>Storm と Stream Analytics の比較: 全般的な機能

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
いいえ、Azure Stream Analytics は、Microsoft 独自のサービスです。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
はい、Apache Storm は、Apache がライセンス供与するテクノロジです。
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
はい </p>
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
ハードウェア要件はありません。 Azure Stream Analytics はAzure のサービスです。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
ハードウェア要件はありません。 Apache Storm は、Azure のサービスです。
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
Azure Stream Analytics を使用して、ユーザーはストリーミング ジョブをデプロイし、監視します。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
HDInsight の Apache Storm を使用して、ユーザーは複数のストーム ジョブやその他のワークロード (バッチを含む) をホストできる、クラスター全体をデプロイし、監視します。
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
Stream Analytics では、処理するデータのボリュームと必要な (ジョブが実行されている&1; 時間あたりの) ストリーミング ユニットの数によって請求されます。
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/stream-analytics/">価格についての詳細は、こちらをご覧ください。</a>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
HDInsight の Apache Storm では、購入単位はクラスター ベースであり、デプロイされるジョブとは無関係に、実行されるクラスターの時間に基づいて請求されます。
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/hdinsight/">価格についての詳細は、こちらをご覧ください。</a>
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="authoring-on-each-analytics-platform"></a>各分析プラットフォームでの作成 ##

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
はい、使いやすい SQL 言語のサポートを利用できます。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
いいえ、コードを Java C# かTrident API で記述する必要があります。
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
一時的な演算子は、ユーザーによって実装される必要があります。
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
サンプル データでのAzure ポータルによるインタラクティブな作成とデバッグの操作性。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
開発、デバッグ、監視の操作性については、.NET のユーザーの場合は Visual Studio の操作性から提供されますが、Java とその他の言語の開発者は、IDE を選択して使用することもできます。
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
Stream Analytics はデバッグの方法として基本的なジョブの状態と操作ログを提供しますが、現在は、ログに何をどれだけ含めるかを指定するといった柔軟性 (たとえば、詳細モード) はありません。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
詳細なログは、デバッグ目的で利用できます。 ユーザーにログを表示する には、Visual Studio による方法と、クラスターに RDP 接続してログにアクセスする方法の&2; つの方法があります。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>UDF のサポート (ユーザー定義関数)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
現在、UDF のサポートはありません。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
UDF は、C#、Java や任意の言語で記述できます。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>拡張可能 - カスタム コード </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Stream Analytics では、拡張可能コードのサポートはありません。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
はい、 C#、Java や、Storm でサポートされているその他の言語でカスタム コードを記述する可用性があります。
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="data-sources-and-outputs"></a>データ ソースと出力 ##

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
                <p>サポートされている入力ソースは、Azure Event Hubs と Azure Blob です。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Event Hubs、Service Bus、Kafka などの使用可能なコネクタがあります。サポートされていないコネクタは、カスタム コードを使用して実装できます。
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
サポートされている入力形式は、Avro、JSON、CSV です。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
いずれの形式も、カスタム コードを使用して実装できます。
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
ストリーミング ジョブには、複数の出力がある場合があります。 サポートされている出力: Azure Event Hubs、Azure Blob Storage、Azure Tables、Azure SQL DB、PowerBI。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
トポロジの多くの出力をサポートしており、各出力にはダウンストリーム処理のためのカスタム ロジックがあることがあります。 既定の Storm には、 PowerBI、Azure Event Hubs、Azure Blob Store、Azure DocumentDB、SQL、HBase のコネクタが含まれています。 サポートされていないコネクタは、カスタム コードを使用して実装できます。
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
Stream Analytics に使用するための UTF-8 データ形式が必要です。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
いずれのデータ エンコード形式も、カスタム コードを使用して実装できます。
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
                <p>
                    - <strong>Azure Portal</strong>
                </p>
                <p>
                    - <strong>Visual Studio</strong>
                </p>
                <p>
                    - <strong>PowerShell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
デプロイは、Azure ポータル、PowerShell、REST API を使用して実装されます。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
デプロイは、Azure ポータル、PowerShell、 Visual Studio、REST API を使用して実装されます。
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
監視は、Azure ポータルと REST API を使用して実装されます。
                </p>
                <p>
ユーザーは Azure のアラートを構成することもできます。
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
各ジョブのストリーミング ユニットの数。 各ストリーミング ユニットは、1 MB/秒まで処理することができます。 既定では最大 50 ユニット。 制限値の増加の呼び出し。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
HDI Storm クラスター内のノードの数。 ノードの数には制限はありません (Azure のクォータで定義されている上位の制限) 。 制限値の増加の呼び出し。
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
ユーザーは、ストリーミング ユニットの数を拡大するか、縮小して、データ処理を増やすか、コストを最適化することができます。
                </p>
                <p>
最大 1 GB/秒に拡大 </p>
            </td>
            <td width="246" valign="top">
                <p>
ユーザーは、ニーズに合わせてクラスターのサイズを拡大するか、縮小できます。
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
                <p>
アップタイム 99.9% の SLA </p>
                <p>
障害からの自動復旧 </p>
                <p>
ステートフルな一時的な演算子の復旧が、組み込まれています。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Storm クラスターの アップタイム 99.9% の SLA。 Apache Storm は、フォールト トレラントなストリーミング プラットフォームですが、ストリーミング ジョブを中断することなく実行するのはユーザーの責任です。
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
順序変更、イベントのドロップ、イベント時間の調整のための構成可能なポリシーが組み込まれています。
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
参照データは、Azure Blobs から最大サイズ 100 MB のメモリ内の参照キャッシュまで使用できます。 参照データの更新は、サービスによって管理されます。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
データ サイズに制限はありません。 HBase、DocumentDB、SQL Server、Azure に利用可能なコネクタ。 サポートされていないコネクタは、カスタム コードを使用して実装できます。
                </p>
                <p>
参照データの更新は、カスタム コードで処理される必要があります。
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
ASA ジョブの作成中に、公開済みの Azure Machine Learning モデルを関数として構成します <a href="http://blogs.msdn.com/b/streamanalytics/archive/2015/05/24/real-time-scoring-of-streaming-data-using-machine-learning-models.aspx">(プライベート プレビュー)</a>。
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


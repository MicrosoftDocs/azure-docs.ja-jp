---
title: クイック スタート:Metrics Advisor Web ポータル
titleSuffix: Azure Cognitive Services
description: Metrics Advisor Web ポータルを使い始める方法について説明します。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: f853dca12b6a5855dc0603259ab534fa9933cd5b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432300"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>クイック スタート:Web ポータルを使用して初めてのメトリックを監視する

Metrics Advisor インスタンスをプロビジョニングするとき、サービスの操作には、API と Web ベースのワークスペースを使用できます。 Web ベースのワークスペースは使いやすく、サービスをすぐに利用することができます。 また、設定の構成、モデルのカスタマイズ、根本原因分析の実行のための視覚的な手段も用意されています。 

* メトリック データをオンボードする
* メトリックと視覚エフェクトを表示する
* 検出構成を微調整する
* 診断の分析情報を調べる
* 異常アラートを作成してサブスクライブする

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* Azure サブスクリプションを作成したら、Azure portal で <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Metrics Advisor リソースを作成"  target="_blank">Metrics Advisor リソースを作成</a>して、Metrics Advisor インスタンスをデプロイします。  

    
> [!TIP]
> * Metrics Advisor リソースのデプロイには 10 分から 30 分ほどかかります。 デプロイに成功したら、 **[リソースに移動]** をクリックします。
> * REST API を使用してサービスを対話的に操作したい場合は、作成したリソースのキーとエンドポイントが必要となります。 それらは、作成したリソースの **[キーとエンドポイント]** タブで確認できます。

このドキュメントでは、初めてのモニターを作成する例として SQL Database を使用します。

## <a name="sign-in-to-your-workspace"></a>ワークスペースにサインインする

リソースが作成されたら、[Metrics Advisor ポータル](https://go.microsoft.com/fwlink/?linkid=2143774)にサインインします。 ワークスペースを選択して、メトリックの監視を開始します。 
 
現在、使用可能なリージョンごとに 1 つの Metrics Advisor リソースを作成できます。 Metrics Advisor ポータルでは、いつでもワークスペースを切り替えることができます。


## <a name="onboard-time-series-data"></a>時系列データをオンボードする

Metrics Advisor には、SQL Database、Azure Data Explorer、Azure Table Storage など、さまざまなデータ ソース用のコネクタが用意されています。 データを接続する手順は、コネクタが異なっていても似ています。ただし、いくつかの構成パラメーターが異なる可能性があります。 特定のデータ ソースに必要なパラメーターについては、[さまざまなソースのデータを接続](../data-feeds-from-different-sources.md)する方法に関するページを参照してください。

このクイックスタートは、例として SQL Database を使用します。 同じ手順に従って独自のデータを取り込むこともできます。

まず、ご使用の Active Directory アカウントで Metrics Advisor ワークスペースにサインインします。 ランディング ページから、**ディレクトリ**、**サブスクリプション**、作成した **ワークスペース** を選択して、 **[Get started]\(開始する\)** をクリックします。 ワークロードのメイン ページが読み込まれたら、左側のメニューから **[Add data feed]\(データ フィードの追加\)** を選択します。

### <a name="data-schema-requirements-and-configuration"></a>データ スキーマの要件と構成

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings"></a>接続設定を構成する

> [!TIP]
> 使用できるパラメーターの詳細については、[データ フィードを追加する方法](../how-tos/onboard-your-data.md)に関するページを参照してください。

目的の時系列データ ソースに接続してデータ フィードを追加します。 最初に次のパラメーターを選択します。

* **[Source Type]\(ソースの種類\)** : 時系列データを格納するデータ ソースの種類。
* **[細分性]** : 時系列データ内の連続するデータ ポイントの間隔 (年単位、月単位、日単位など)。 カスタマイズでサポートされている最小間隔は 60 秒です。
* **[Ingest data since (UTC)]\(データの取り込みの開始 (UTC)\)** : 取り込む対象の最初のタイムスタンプの開始時刻。 


次に、データ ソースの資格情報を含んだ **接続文字列** とカスタム **クエリ** を指定します。 取り込み後、必要なスキーマに変換されるデータは、このクエリを使用して指定されます。

[!INCLUDE [query requirements](../includes/query-requirements.md)]

:::image type="content" source="../media/connection-settings.png" alt-text="接続の設定" lightbox="../media/connection-settings.png":::


### <a name="verify-the-connection-and-load-the-data-schema"></a>接続を確認してデータ スキーマを読み込む

接続文字列とクエリ文字列を作成した後、 **[Verify and get schema]\(確認してスキーマを取得\)** を選択して接続を確認し、クエリを実行してデータ ソースからデータ スキーマを取得します。 データ ソース接続によりますが、通常は数秒かかります。 この手順でエラーが発生した場合は、次のことを確認してください。

1. 接続文字列とクエリが正しいこと。
2. ファイアウォール設定がある場合、Metrics Advisor インスタンスがデータ ソースに接続できる。

### <a name="schema-configuration"></a>スキーマの構成

データ スキーマが読み込まれて、以下のように表示されたら、適切なフィールドを選択します。


|選択  |説明  |メモ  |
|---------|---------|---------|
|**Timestamp**     | データ ポイントのタイムスタンプ。 省略されている場合、代わりに、Metrics Advisor により、データ ポイントが取り込まれた時点のタイムスタンプが使用されます。 データ フィードごとに、最大 1 列をタイムスタンプとして指定できます。        | 省略可能。 指定できるのは最大 1 列です。       |
|**測定値**     |  データ フィード内の数値。 データ フィードごとに複数のメジャーを指定できますが、少なくとも 1 列をメジャーとして選択する必要があります。        | 少なくとも 1 列を指定する必要があります。        |
|**ディメンション**     | カテゴリ値。 異なる値を組み合わせることで、国、言語、テナントなどの特定の 1 次元の時系列が識別されます。 0 個以上の任意の数の列をディメンションとして選択できます。 注: 文字列以外の列をディメンションとして選択する場合は、ディメンションのエクスプロージョンに注意が必要です。 | 省略可能。        |
|**無視**     | 選択した列を無視します。        |         |


:::image type="content" source="../media/schema-configuration.png" alt-text="スキーマの構成" lightbox="../media/schema-configuration.png":::

### <a name="automatic-roll-up-settings"></a>自動ロール アップ設定

> [!IMPORTANT]
> **根本原因分析** およびその他の診断機能を有効にする場合は、"自動ロール アップ設定" を構成する必要があります。 有効にすると、自動ロールアップ設定を変更できなくなります。

Metrics Advisor は、インジェスト中に各ディメンションで集計 (SUM、MAX、MIN など) を自動的に実行でき、ルート ケース分析やその他の診断機能で使用される階層を構築します。 詳細については、「[自動ロール アップ設定](../how-tos/onboard-your-data.md#automatic-roll-up-settings)」を参照してください。

データ フィードのカスタム名を指定します。これは、ワークスペースに表示されます。 **[Submit]\(送信\)** をクリックします。 

## <a name="tune-detection-configuration"></a>検出構成を調整する

データ フィードが追加されると、Metrics Advisor は、指定された開始日からメトリック データの取り込みを試みます。 データが完全に取り込まれるまでには、しばらく時間がかかります。インジェストの状況は、データ フィード ページの上部にある **[Ingestion progress]\(インジェスト進行状況\)** をクリックして確認できます。 データが取り込まれた場合、Metrics Advisor は検出を適用し、引き続きソースの新しいデータを監視します。

検出が適用されたら、データ フィードに一覧表示されるいずれかのメトリックをクリックして **メトリックの詳細ページ** を探し、次のことを行います。 
- このメトリック下にあるすべての時系列スライスの視覚エフェクトを表示する
- 必要な結果が得られるように検出構成を更新する
- 異常が検出された場合の通知を設定する

:::image type="content" source="../media/metric-details.png" alt-text="メトリックの詳細" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>診断の分析情報を表示する

検出構成の調整後、見つかった異常は、データ内の実際の異常を反映していると考えられます。 Metrics Advisor は、多次元メトリックに対して、異常クラスタリング、インシデントの相関関係、根本原因分析などの分析を実行します。 これらの機能を使用して、データ内のインシデントを分析、診断します。

診断の分析情報を表示するには、時系列の視覚エフェクトに表示される赤色のドットをクリックします。これらは、検出された異常を表しています。 ウィンドウが開いて、インシデント分析ページへのリンクが表示されます。 

:::image type="content" source="../media/incident-link.png" alt-text="インシデント リンク" lightbox="../media/incident-link.png":::

リンクをクリックすると、対応する異常に関するインシデント分析ページが、さまざまな診断の分析情報と共に表示されます。 一番上には、**重大度**、**関係する異常**、影響の **開始時刻** と **終業時刻** など、そのインシデントについての統計情報が表示されます。 

次に、そのインシデントの先祖にあたる異常や、根本原因に関する自動化されたアドバイスが表示されます。 根本原因に関する自動化されたアドバイスは、関連するすべての異常 (偏差、分散、上位の異常への寄与度など) のインシデント ツリーを分析することによって生成されます。 

:::image type="content" source="../media/incident-diagnostic.png" alt-text="インシデントの診断" lightbox="../media/incident-diagnostic.png":::

これらの情報から、何が起こっていて、そのインシデントによってどのような影響があるか、最も考えられる根本原因は何かを簡単に、いち早く把握することができます。 早急な対応を行うことで、可能な限り速やかにインシデントを解決することができます。 

ただし、さらに別の機能を活用して、ディメンション別に異常をドリルダウンしたり、同様の異常を確認したり、メトリック間で比較したりすれば、他のさまざまな診断分析情報の観点から分析を行うこともできます。 詳細については、[インシデントを診断する方法](../how-tos/diagnose-incident.md)に関するページを参照してください。 

## <a name="get-notified-when-new-anomalies-are-found"></a>新しい異常が見つかったときに通知を受け取る

データに異常が検出されたときに通知を受け取りたい場合、1 つまたは複数のメトリックに関するサブスクリプションを作成することができます。 Metrics Advisor は、フックを使用してアラートを送信します。 電子メール、Web、Azure DevOps の 3 種類のフックがサポートされています。 ここでは例として Web hook を使用します。 

### <a name="create-a-web-hook"></a>Web hook を作成する

Web hook は、Metrics Advisor サービスからプログラム的な手段によって通知された異常を取得するためのエントリ ポイントです。アラートがトリガーされると、そこから、ユーザーによって指定された API が呼び出されます。フックの作成方法について詳しくは、[アラートを構成する方法とフックを使用して通知を受け取る方法](../how-tos/alerts.md#create-a-hook)に関するページの **フックの作成** に関するセクションを参照してください。 

### <a name="configure-alert-settings"></a>アラート設定の構成

フックの作成後、どのアラート通知がどのように送信されるかは、アラートの設定によって決まります。 アラートの設定は、メトリックごとに複数用意することができます。 重要な設定は 2 つあります。1 つは、対象となる異常を指定する **[Alert for]\(アラート対象\)** で、もう 1 つは、アラートに含める異常を定義する **[Filter anomaly options]\(異常のフィルター処理オプション\)** です。 詳細については、[アラートを構成する方法とフックを使用して通知を受け取る方法](../how-tos/alerts.md#add-or-edit-alert-settings)に関するページの **アラート設定を追加または編集する** 方法に関するセクションを参照してください。


## <a name="next-steps"></a>次のステップ

- [データ フィードをオンボードする](../how-tos/onboard-your-data.md)
    - [データ フィードを管理する](../how-tos/manage-data-feeds.md)
    - [さまざまなデータ ソースの構成](../data-feeds-from-different-sources.md)
- [REST API またはクライアント ライブラリを使用する](./rest-api-and-client-library.md)
- [メトリックを構成して検出構成を微調整する](../how-tos/configure-metrics.md)
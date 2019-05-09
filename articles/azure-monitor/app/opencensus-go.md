---
title: Azure Application Insights による OpenCensus Go のトレース | Microsoft Docs
description: OpenCensus Go のトレースをローカル フォワーダーおよび Application Insights と統合する手順について説明します。
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cdf01fbbcc8ef1f90b2e0f8973f59c46c5bf70f8
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55694114"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Go から分散トレースを収集する (プレビュー)

Application Insights は、[OpenCensus](https://opencensus.io) と Microsoft の新しい[ローカル フォワーダー](./opencensus-local-forwarder.md)との統合により、Go アプリケーションの分散トレースをサポートするようになりました。 この記事では、OpenCensus for Go を設定し、トレース データを Application Insights に取得するプロセスについて順を追って説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションが必要です。
- Go をインストールする必要があります、この記事では、バージョン 1.11 の [Go ダウンロード](https://golang.org/dl/)を使用します。
- 手順に従って[ローカル フォワーダーを Windows サービスとして](./opencensus-local-forwarder.md)インストールします。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="create-application-insights-resource"></a>Application Insights リソースを作成する

まず、インストルメンテーション キー (ikey) を生成する Application Insights リソースを作成する必要があります。 ikey を使用して、OpenCensus でインストルメント化されたアプリケーションから Application Insights に分散トレースを送信するようにローカル フォワーダーを構成します。   

1. **[リソースの作成]** > **[開発者ツール]** > **[Application Insights]** の順に選択します。

   ![Application Insights リソースの追加](./media/opencensus-Go/0001-create-resource.png)

   構成ボックスが表示されたら、次の表を使用して入力フィールドに入力します。

    | 設定        | 値           | 説明  |
   | ------------- |:-------------|:-----|
   | **Name**      | グローバルに一意の値 | 監視しているアプリを識別する名前 |
   | **アプリケーションの種類** | 全般 | 監視しているアプリの種類 |
   | **リソース グループ**     | myResourceGroup      | App Insights データをホストする新しいリソース グループの名前 |
   | **場所** | 米国東部 | 近くにある場所か、アプリがホストされている場所の近くを選択します。 |

2. **Create** をクリックしてください。

## <a name="configure-local-forwarder"></a>ローカル フォワーダーを構成する

1. **[概要]** > **[Essentials]** を選択し、アプリケーションの**インストルメンテーション キー**をコピーします。

   ![インストルメンテーション キーのスクリーンショット](./media/opencensus-Go/0003-instrumentation-key.png)

2. `LocalForwarder.config` ファイルを編集し、インストルメンテーション キーを追加します。 [前提条件](./opencensus-local-forwarder.md)の手順に従った場合、このファイルは `C:\LF-WindowsServiceHost` にあります。

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. アプリケーションの**ローカル フォワーダー** サービスを再開します。

## <a name="opencensus-go-packages"></a>OpenCensus Go パッケージ

1. コマンド ラインから Go 用の OpenCensus パッケージをインストールします。

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. 次のコードを .go ファイルに追加し、ビルドして、実行します。 (この例は、OpenCensus の公式ガイダンスに、ローカル フォワーダーとの統合を容易にするコードを追加したものです)

     ```go
        // Copyright 2018, OpenCensus Authors
        //
        // Licensed under the Apache License, Version 2.0 (the "License");
        // you may not use this file except in compliance with the License.
        // You may obtain a copy of the License at
        //
        //     https://www.apache.org/licenses/LICENSE-2.0
        //
        // Unless required by applicable law or agreed to in writing, software
        // distributed under the License is distributed on an "AS IS" BASIS,
        // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
        // See the License for the specific language governing permissions and
        // limitations under the License.
        package main
        
        import (
        
            "bytes"
            "fmt"
            "log"
            "net/http"
            os "os"
            
            ocagent "contrib.go.opencensus.io/exporter/ocagent"
            "go.opencensus.io/plugin/ochttp"
            "go.opencensus.io/plugin/ochttp/propagation/tracecontext"
            "go.opencensus.io/trace"
        
        )
        
        func main() {
            // Register stats and trace exporters to export the collected data.
            serviceName := os.Getenv("SERVICE_NAME")
            if len(serviceName) == 0 {
                serviceName = "go-app"
            }
            fmt.Printf(serviceName)
            agentEndpoint := os.Getenv("OCAGENT_TRACE_EXPORTER_ENDPOINT")

            if len(agentEndpoint) == 0 {
                agentEndpoint = fmt.Sprintf("%s:%d", ocagent.DefaultAgentHost, ocagent.DefaultAgentPort)
            }
        
            fmt.Printf(agentEndpoint)
            exporter, err := ocagent.NewExporter(ocagent.WithInsecure(), ocagent.WithServiceName(serviceName), ocagent.WithAddress(agentEndpoint))
        
            if err != nil {
                log.Printf("Failed to create the agent exporter: %v", err)
            }
        
            trace.RegisterExporter(exporter)
        
            trace.ApplyConfig(trace.Config{DefaultSampler: trace.AlwaysSample()})
        
            client := &http.Client{Transport: &ochttp.Transport{Propagation: &tracecontext.HTTPFormat{}}}
        
            http.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                var jsonStr = []byte(`[ { "url": "http://blank.org", "arguments": [] } ]`)
                r, _ := http.NewRequest("POST", "http://blank.org", bytes.NewBuffer(jsonStr))
                r.Header.Set("Content-Type", "application/json")
        
                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }
            })
        
            http.HandleFunc("/call_blank", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                r, _ := http.NewRequest("GET", "http://blank.org", nil)

                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
        
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }        
            })
        
            log.Fatal(http.ListenAndServe(":50030", &ochttp.Handler{Propagation: &tracecontext.HTTPFormat{}}))
        
        }
     ```

3. シンプルな Go アプリが実行したら、`http://localhost:50030` に移動します。 ブラウザーの更新のたび、テキスト "hello world" と、ローカル フォワーダーで取得される対応するスパン データが生成されます。

4. **ローカル フォワーダー**がトレースを取得していることを確認するには、`LocalForwarder.config` ファイルを確認します。 [前提条件](https://docs.microsoft.com/azure/application-insights/local-forwarder)の手順に従った場合は、`C:\LF-WindowsServiceHost` にあります。

    ログ ファイルの以下の画像では、追加した 2 つ目のスクリプトを実行する前に、エクスポーター `OpenCensus input BatchesReceived` は 0 だったことがわかります。 更新されたスクリプトの実行を開始すると、増分された `BatchesReceived` が入力した値の数に等しくなります。
    
    ![新しい App Insights リソースのフォーム](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Azure Portal で監視を開始する

1. Azure portal で Application Insights の **[概要]** ページを再度開き、現在実行中のアプリケーションに関する詳細情報を表示できます。 **[Live Metric Stream]\(ライブ メトリック ストリーム\)** を選択します。

   ![ライブ メトリック ストリームが赤い枠線で書き込まれた概要ウィンドウのスクリーンショット](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. 2 つ目の Go アプリをもう一度実行し、`http://localhost:50030` でブラウザーの更新を開始すると、ローカル フォワーダー サービスから Application Insights に送信されたライブ トレース データが表示されます。

   ![パフォーマンス データが表示されたライブ メトリック ストリームのスクリーンショット](./media/opencensus-go/0006-stream.png)

3. **[概要]** ページに戻り、**[アプリケーション マップ]** を選択して、アプリケーション コンポーネント間の依存関係と呼び出しのタイミングを視覚的に表したレイアウトを表示します。

    ![基本的なアプリケーション マップのスクリーンショット](./media/opencensus-go/0007-application-map.png)

    1 つのメソッド呼び出しのみをトレースしていたため、このアプリケーション マップはあまり興味深い内容ではありません。 ただし、アプリケーション マップをスケーリングして、分散性がはるかに高いアプリケーションを視覚化することができます。

   ![アプリケーション マップ](media/opencensus-go/application-map.png)

4. **[パフォーマンスの調査]** を選択して詳細なパフォーマンス分析を実行し、パフォーマンス低下の根本原因を判断します。

    ![パフォーマンス ウィンドウのスクリーンショット](./media/opencensus-go/0008-performance.png)

5. **[サンプル]** を選択し、右側のウィンドウに表示されるいずれかのサンプルをクリックすると、エンドツーエンド トランザクションの詳細エクスペリエンスが起動します。 このサンプル アプリには単一のイベントのみが表示されますが、より複雑なアプリケーションでは、個々のイベントのコール スタックのレベルまでエンドツーエンドのトランザクションを探索できます。

     ![エンドツーエンドのトランザクション インターフェイスのスクリーンショット](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>OpenCensus for Go のトレース

OpenCensus for Go をローカル フォワーダーおよび Application Insights と統合する手順の基本についてのみ説明しました。 [公式の OpenCensus Go 使用ガイダンス](https://godoc.org/go.opencensus.io)では、より高度なトピックを扱っています。

## <a name="next-steps"></a>次の手順

* [アプリケーション マップ](./../../azure-monitor/app/app-map.md)
* [エンドツーエンドのパフォーマンスの監視](./../../azure-monitor/learn/tutorial-performance.md)

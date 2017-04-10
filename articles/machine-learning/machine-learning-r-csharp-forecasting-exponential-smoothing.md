---
title: "(非推奨) 予測指数平滑法 - Azure | Microsoft Docs"
description: "(非推奨) Web サービス: 予測指数平滑法"
services: machine-learning
documentationcenter: 
author: yijichen
manager: jhubbard
editor: cgronlun
ms.assetid: a4150681-6eac-4145-9eca-0cdf60781dde
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/06/2017
ms.author: yijichen
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: f6ad106e769c807d1c281c8d19127eabc2048f30
ms.openlocfilehash: c3fc7b8d7ba9faf79d80b55514ef09570e0d1655
ms.lasthandoff: 01/11/2017


---
# <a name="deprecated-forecasting---exponential-smoothing"></a>(非推奨) 予測指数平滑法

> [!NOTE]
> Microsoft DataMarket は間もなく提供終了となる予定です。この API は推奨されていません。 
> 
> [Cortana Intelligence ギャラリー](http://gallery.cortanaintelligence.com)では、便利なサンプル実験や API が提供されています。 ギャラリーについて詳しくは、「 [Cortana Intelligence ギャラリーでリソースを共有および発見する](machine-learning-gallery-how-to-use-contribute-publish.md)」をご覧ください。

この [Web サービス](https://datamarket.azure.com/dataset/aml_labs/ets) は、指数平滑法モデル (ETS) を実装して、ユーザーが指定した履歴データに基づいて予測を生成します。 今年、特定の製品の需要が増加するでしょうか。 クリスマス シーズンの製品販売を予測して、効果的な在庫計画を策定できるでしょうか。 予測モデルは、このような質問に答えることができます。 これらのモデルは、過去のデータを指定すると、隠れた傾向や季節性を検証し、将来の傾向を予測します。  

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

> この Web サービスは、モバイル アプリ、Web サイト、ローカル コンピューターなどからユーザーが使用できます。 この Web サービスのもう&1; つの目的は、Azure Machine Learning を使用して R コード上に Web サービスを作成する方法の例を示すことです。 数行の R コードを記述し、Azure Machine Learning Studio 内でボタンを何回かクリックするだけで、R コードで実験を作成し、Web サービスとして発行できます。 この Web サービスは Azure Marketplace に発行され、Web サービスの作成者がインフラストラクチャを設定することなく、世界中のユーザーやデバイスで使用されます。
> 
> 

## <a name="consumption-of-web-service"></a>Web サービスの使用
このサービスは、4 つの引数を使用し、ETS 予測を計算します。
入力引数は、次のとおりです。

* Frequency - 生データの頻度 (毎日/毎週/毎月/毎四半期/毎年) を示します。
* Horizon - 将来の予測時間枠
* Date - 時間の新しい時系列データを追加します。
* Value - 新しい時系列データの値を追加します。

このサービスの出力は、計算された予測値です。

入力例は以下のとおりです。 

* Frequency - 12
* Horizon - 12
* Date - 1/15/2012;2/15/2012;3/15/2012;4/15/2012;5/15/2012;6/15/2012;7/15/2012;8/15/2012;9/15/2012;10/15/2012;11/15/2012;12/15/2012; 1/15/2013;2/15/2013;3/15/2013;4/15/2013;5/15/2013;6/15/2013;7/15/2013;8/15/2013;9/15/2013;10/15/2013;11/15/2013;12/15/2013; 1/15/2014;2/15/2014;3/15/2014;4/15/2014;5/15/2014;6/15/2014;7/15/2014;8/15/2014;9/15/2014
* Value - 3.479;3.68;3.832;3.941;3.797;3.586;3.508;3.731;3.915;3.844;3.634;3.549;3.557;3.785;3.782;3.601;3.544;3.556;3.65;3.709;3.682;3.511; 3.429;3.51;3.523;3.525;3.626;3.695;3.711;3.711;3.693;3.571;3.509

> Azure Marketplace でホストされているこのサービスは、OData サービスです。これらは、POST や GET メソッドによって呼び出すことができます。 
> 
> 

自動でサービスを使用するための複数の方法があります ([ここ](http://microsoftazuremachinelearning.azurewebsites.net/etsForecasting.aspx)にアプリケーション例があります)。

### <a name="starting-c-code-for-web-service-consumption"></a>Web サービスを使用する C# コードを開始します。
    public class Input
    {
            public string frequency;
            public string horizon;
            public string date;
            public string value;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }



## <a name="creation-of-web-service"></a>Web サービスの作成
> この Web サービスは、Azure Machine Learning を使用して作成されました。 無料試用版の場合は、実験の作成と [Web サービスの発行](machine-learning-publish-a-machine-learning-web-service.md)に関する入門ビデオのほか、[azure.com/ml](http://azure.com/ml) もご覧ください。 Web サービスを作成した実験のスクリーン ショット、および実験内の各モジュールに対するコード例を以下に示します。
> 
> 

Azure Machine Learning 内で、新しい空白の実験が作成されました。 入力データのサンプルは、事前定義済みのデータ スキーマにアップロードされました。 データ スキーマには [R スクリプトの実行][execute-r-script]モジュールがリンクされ、R の 'ets' と 'forecast' 関数を使用して、ETS の予測モデルを生成します。 

![実験フロー][2]

#### <a name="module-1"></a>モジュール 1:
    # Add in the CSV file with the data in the format shown below 
![データ サンプル][3]    

#### <a name="module-2"></a>モジュール 2:
    # Data input
    data <- maml.mapInputPort(1) # class: data.frame
    library(forecast)

    # Preprocessing
    colnames(data) <- c("frequency", "horizon", "dates", "values")
    dates <- strsplit(data$dates, ";")[[1]]
    values <- strsplit(data$values, ";")[[1]]

    dates <- as.Date(dates, format = '%m/%d/%Y')
    values <- as.numeric(values)

    # Fit a time-series model
    train_ts<- ts(values, frequency=data$frequency)
    fit1 <- ets(train_ts)
    train_model <- forecast(fit1, h = data$horizon)
    plot(train_model)

    # Produce forcasting
    train_pred <- round(train_model$mean,2)
    data.forecast <- as.data.frame(t(train_pred))
    colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")

    # Data output
    maml.mapOutputPort("data.forecast");


## <a name="limitations"></a>制限事項
これは、ETS 予測のきわめて簡単な例です。 上のコード例からわかるように、エラーのキャッチは実装されません。このサービスは、すべての変数が連続した正の値で、頻度を 1 より大きい整数とする必要があります。 日付と値のベクターの長さは同じにします。 日付変数の形式は、'/mm/dd/yyyy' とします。

## <a name="faq"></a>FAQ
Web サービスの使用や、Azure Marketplace への発行に関するよく寄せられる質問については、 [ここ](machine-learning-marketplace-faq.md)をご覧ください。

[1]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img1.png
[2]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img2.png
[3]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/



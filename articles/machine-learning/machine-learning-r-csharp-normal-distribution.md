---
title: "(非推奨) 正規分布の Web サービス スイート - Azure | Microsoft Docs"
description: "(非推奨) 正規分布の Web サービス スイート"
services: machine-learning
documentationcenter: 
author: ireiter
manager: jhubbard
editor: cgronlun
ms.assetid: aab7b92e-953b-43d8-b0af-031394406bfe
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/06/2017
ms.author: ireiter
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: f6ad106e769c807d1c281c8d19127eabc2048f30
ms.openlocfilehash: da7b70171d13c82e9269064069e69a35b9b2daaa
ms.lasthandoff: 01/11/2017


---
# <a name="deprecated-normal-distribution-suite"></a>(非推奨) 正規分布スイート

> [!NOTE]
> Microsoft DataMarket は間もなく提供終了となる予定です。この API は推奨されていません。 
> 
> [Cortana Intelligence ギャラリー](http://gallery.cortanaintelligence.com)では、便利なサンプル実験や API が提供されています。 ギャラリーについて詳しくは、「 [Cortana Intelligence ギャラリーでリソースを共有および発見する](machine-learning-gallery-how-to-use-contribute-publish.md)」をご覧ください。

正規分布のスイートは、一連のサンプル Web サービス ([正規分布ジェネレーター](https://datamarket.azure.com/dataset/aml_labs/ndg7)、[変位値計算](https://datamarket.azure.com/dataset/aml_labs/ndq5)、[確率計算](https://datamarket.azure.com/dataset/aml_labs/ndp5)) で、正規分布を生成し処理します。 これらのサービスにより、任意の長さの正規分布シーケンスの生成、与えられた確率からの変位値の計算、および与えられた変位値からの確率の計算ができます。 それぞれのサービスは、選択したサービスに基づいて異なる結果を出力します (以下の説明を参照してください)。 正規分布スイートは、R の統計パッケージに含まれている R 関数 qnorm、rnorm、および pnorm に基づいています。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

> この Web サービスは、モバイル アプリ、Web サイト、ローカル コンピューターなどからユーザーが使用できます。 この Web サービスのもう&1; つの目的は、Azure Machine Learning を使用して R コード上に Web サービスを作成する方法の例を示すことです。 数行の R コードを記述し、Azure Machine Learning Studio 内でボタンを何回かクリックするだけで、R コードで実験を作成し、Web サービスとして発行できます。 この Web サービスは Azure Marketplace に発行され、Web サービスの作成者がインフラストラクチャを設定することなく、世界中のユーザーやデバイスで使用されます。  
> 
> 

## <a name="consumption-of-web-service"></a>Web サービスの使用
正規分布スイートには、次の 3 つのサービスが含まれています。

### <a name="normal-distribution-quantile-calculator"></a>正規分布の変位値計算:
このサービスでは、正規分布の 4 つの引数を使用し、関連付けられている変位値を計算します。

入力引数は、次のとおりです。

* p – 正規分布のイベントの&1; つの確率 
* mean – 正規分布の平均
* SD – 正規分布の標準偏差 
* Side - L は分布の下部、U は分布の上部

サービスの出力は計算された変位値で、指定された確率に関連付けられています。

### <a name="normal-distribution-probability-calculator"></a>正規分布の確率計算:
このサービスでは、正規分布の 4 つの引数を使用し、関連する確率を計算します。

入力引数は、次のとおりです。

* q – 正規分布でのイベントの&1; つの変位値 
* mean – 正規分布の平均
* SD – 正規分布の標準偏差 
* Side - L は分布の下部、U は分布の上部

サービスの出力は計算された確率で、指定された変位値に関連付けられています。

### <a name="normal-distribution-generator"></a>正規分布ジェネレーター
このサービスは、正規分布の 3 つの引数を使用し、正規分布の数値のランダム シーケンスを生成します。 要求には、次の引数を指定する必要があります。

* n – 観察数 
* mean – 正規分布の平均
* SD – 正規分布の標準偏差 

サービスの出力は、mean と sd の引数に基づいた正規分布での長さ n のシーケンスです。

> Azure Marketplace でホストされているこのサービスは、OData サービスです。これらは、POST や GET メソッドによって呼び出すことができます。 
> 
> 

自動でサービスを使用するための複数の方法があります (アプリケーション例: [ジェネレーター](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx)、[確率計算](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx)、[変位値計算](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx))。

### <a name="starting-c-code-for-web-service-consumption"></a>Web サービスを使用する C# コードを開始します。
### <a name="normal-distribution-quantile-calculator"></a>正規分布の変位値計算:
    public class Input
    {
            public string p;
            public string mean;
            public string sd;
            public string side;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { p = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


### <a name="normal-distribution-probability-calculator"></a>正規分布の確率計算:
    public class Input
    {
            public string q;
            public string mean;
            public string sd;
            public string side;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { q = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }

### <a name="normal-distribution-generator"></a>正規分布ジェネレーター
    public class Input
    {
            public string n;
            public string mean;
            public string sd;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { n = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text };
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
> この Web サービスは、Azure Machine Learning を使用して作成されました。 無料試用版の場合は、実験の作成と [Web サービスの発行](machine-learning-publish-a-machine-learning-web-service.md)に関する入門ビデオのほか、[azure.com/ml](http://azure.com/ml) もご覧ください。 
> 
> 

Web サービスを作成した実験のスクリーン ショット、および実験内の各モジュールに対するコード例を以下に示します。

### <a name="normal-distribution-quantile-calculator"></a>正規分布の変位値計算:
実験フロー:

![実験フロー][2]

    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port

    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    if (param$p < 0 ) {
    print('Bad input: p must be between 0 and 1')
    param$p = 0
    } else if (param$p > 1) {
    print('Bad input: p must be between 0 and 1')
    param$p = 1
    }
    q = qnorm(param$p,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    q = 2* param$mean - q
    } else if (param$side =='L') {
    q = q
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(q)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

### <a name="normal-distribution-probability-calculator"></a>正規分布の確率計算:
実験フロー:

![実験フロー][3]

     #Data schema with example data (replaced with data from web service)
    data.set=data.frame(q=-1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port

    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    prob = pnorm(param$q,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    prob = 1 - prob
    } else if (param$side =='B') {
    prob = ifelse(prob<=0.5,prob * 2, 1)
    } else if (param$side =='L') {
    prob = prob
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(prob)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

### <a name="normal-distribution-generator"></a>正規分布ジェネレーター
実験フロー:

![実験フロー][4]

    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,mean=0,sd=1);
    maml.mapOutputPort("data.set"); #send data to output port

    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    dist = rnorm(param$n,mean=param$mean,sd=param$sd)

    output = as.data.frame(t(dist))

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

## <a name="limitations"></a>制限事項
これらは、正規分布周辺のきわめて単純な例です。 上のコード例からわかるように、エラーのキャッチはほとんど実装されていません。

## <a name="faq"></a>FAQ
Web サービスの使用や、Azure Marketplace への発行に関するよく寄せられる質問については、 [ここ](machine-learning-marketplace-faq.md)をご覧ください。

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png



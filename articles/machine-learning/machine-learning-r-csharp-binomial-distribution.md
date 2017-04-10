---
title: "(非推奨) 二項分布スイート - Azure | Microsoft Docs"
description: "(非推奨) 二項分布スイート"
services: machine-learning
documentationcenter: 
author: ireiter
manager: jhubbard
editor: cgronlun
ms.assetid: 6d102d57-8f20-4ab3-be31-02fcfe4d15ed
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
ms.openlocfilehash: 4d4a343be86909acf054eaaf9cc4a1b0df5a4209
ms.lasthandoff: 01/11/2017


---
# <a name="deprecated-binomial-distribution-suite"></a>(非推奨) 二項分布スイート

> [!NOTE]
> Microsoft DataMarket は間もなく提供終了となる予定です。この API は推奨されていません。 
> 
> [Cortana Intelligence ギャラリー](http://gallery.cortanaintelligence.com)では、便利なサンプル実験や API が提供されています。 ギャラリーについて詳しくは、「 [Cortana Intelligence ギャラリーでリソースを共有および発見する](machine-learning-gallery-how-to-use-contribute-publish.md)」をご覧ください。

二項分布スイートは、一連のサンプル Web サービス ([二項ジェネレーター](https://datamarket.azure.com/dataset/aml_labs/bdg5)、[確率計算](https://datamarket.azure.com/dataset/aml_labs/bdp4)、[変位値計算](https://datamarket.azure.com/dataset/aml_labs/bdq5)) で、具体的には二項分布を生成し処理します。 これらのサービスにより、任意の長さの二項分布シーケンスの生成、与えられた確率からの変位値の計算、および与えられた変位値からの確率の計算ができます。 それぞれのサービスは、選択したサービスに基づいて異なる結果を出力します (以下の説明を参照してください)。 二項分布スイートは、R の統計パッケージに含まれている R 関数 qbinom、rbinom、pbinom に基づいています。 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

> この Web サービスは、 モバイル アプリ、Web サイト、ローカル コンピューターなどから、Marketplace 上で直接ユーザーが使用できます。 この Web サービスのもう&1; つの目的は、Azure Machine Learning を使用して R コード上に Web サービスを作成する方法の例を示すことです。 数行の R コードを記述し、Azure Machine Learning Studio 内でボタンを何回かクリックするだけで、R コードで実験を作成し、Web サービスとして発行できます。 この Web サービスは Azure Marketplace に発行され、Web サービスの作成者がインフラストラクチャを設定することなく、世界中のユーザーやデバイスで使用されます。
> 
> 

## <a name="consumption-of-web-service"></a>Web サービスの使用
二項分布スイートには、次の 3 つのサービスが含まれています。

### <a name="binomial-distribution-quantile-calculator"></a>二項分布の変位値計算
このサービスでは、正規分布の 4 つの引数を使用し、関連付けられている変位値を計算します。
入力引数は、次のとおりです。

* p – 複数の試行回数の&1; つの集計された確率  
* size – 試行の回数
* prob – 試行で成功する確率
* Side - L は分布の下部、U は分布の上部 

サービスの出力は計算された変位値で、指定された確率に関連付けられています。

### <a name="binomial-distribution-probability-calculator"></a>二項分布の確率計算
このサービスでは、二項分布の 4 つの引数を使用し、関連する変位値を計算します。
入力引数は、次のとおりです。

* q – 二項分布でのイベントの&1; つの変位値 
* size – 試行の回数
* prob – 試行で成功する確率
* side– L は分布の下部、U は分布の上部、E は&1; つの成功数に相当します。

サービスの出力は計算された確率で、指定された変位値に関連付けられています。

### <a name="binomial-distribution-generator"></a>二項分布ジェネレーター
このサービスは、二項分布の 3 つの引数を使用し、二項分布の数値のランダム シーケンスを生成します。 要求には、次の引数を指定する必要があります。

* n – 観察数 
* size – 試行の回数
* prob – 成功する確率

サービスの出力は、size と prob の引数に基づいた二項分布の長さ n のシーケンスです。

> Azure Marketplace でホストされているこのサービスは、OData サービスです。これらは、POST や GET メソッドによって呼び出すことができます。 
> 
> 

自動でサービスを使用するための複数の方法があります (アプリケーション例: [ジェネレーター](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionGenerator.aspx)、[確率計算](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionProbabilityCalculator.aspx)、[変位値計算](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionQuantileCalculator))。 

### <a name="starting-c-code-for-web-service-consumption"></a>Web サービスを使用する C# コードを開始します。
### <a name="binomial-distribution-quantile-calculator"></a>二項分布の変位値計算
    public class Input
    {
            public string p;
            public string size;
            public string prob;
            public string side;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void main()
    {
            var input = new Input() { p = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }

### <a name="binomial-distribution-probability-calculator"></a>二項分布の確率計算
    public class Input
    {
            public string q;
            public string size;
            public string prob;
            public string side;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { q = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = " PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


### <a name="binomial-distribution-generator"></a>二項分布ジェネレーター
    public class Input
    {
            public string n;
            public string size;
            public string p;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { n = TextBox1.Text, size = TextBox2.Text, p = TextBox3.Text };
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

### <a name="binomial-distribution-quantile-calculator"></a>二項分布の変位値計算
![Create workspace][4]

#### <a name="module-1"></a>モジュール 1:
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
#### <a name="module-2"></a>モジュール 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    if (param$p < 0 ) {
    print('Bad input: p must be between 0 and 1')
    param$p = 0
    } else if (param$p > 1) {
    print('Bad input: p must be between 0 and 1')
    param$p = 1
    }

    if (param$prob < 0 ) {
    print('Bad input: prob must be between 0 and 1')
    param$prob = 0
    } else if (param$prob > 1) {
    print('Bad input: prob must be between 0 and 1')
    param$prob = 1
    }

    quantile = qbinom(param$p,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    quantile

    if (param$side == 'U'){
    quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = F)
    band=subset(df,x>quantile)
    } else if (param$side =='L') {
    quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = T)
    band=subset(df,x<=quantile)
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(quantile)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");


### <a name="binomial-distribution-probability-calculator"></a>二項分布の確率計算
![Create workspace][5]

#### <a name="module-1"></a>モジュール 1:
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(q=5,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port


#### <a name="module-2"></a>モジュール 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    prob = pbinom(param$q,size=param$size,prob=param$prob)
    prob.eq = dbinom(param$q,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    prob

    if (param$side == 'U'){
    prob = 1 - prob
    band=subset(df,x>param$q)
    } else if (param$side =='E') {
    prob = prob.eq
    band=subset(df,x==param$q)
    } else if (param$side =='L') {
    prob = prob
    band=subset(df,x<=param$q)
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(prob)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

### <a name="binomial-distribution-generator"></a>二項分布ジェネレーター
![Create workspace][6]

#### <a name="module-1"></a>モジュール 1:
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,size=10,p=.5);
    maml.mapOutputPort("data.set"); #send data to output port

#### <a name="module-2"></a>モジュール 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    dist = rbinom(param$n,param$size,param$p)

    output = as.data.frame(t(dist))

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

## <a name="limitations"></a>制限事項
これらは、二項分布周辺のきわめて単純な例です。 上のコード例からわかるように、エラーのキャッチはほとんど実装されていません。

## <a name="faq"></a>FAQ
Web サービスの使用や、Azure Marketplace への発行に関するよく寄せられる質問については、 [ここ](machine-learning-marketplace-faq.md)をご覧ください。

[1]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_1.png

[2]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_2.png

[3]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_3.png

[4]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_4.png

[5]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_5.png

[6]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_6.png



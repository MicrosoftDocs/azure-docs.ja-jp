---
title: "(非推奨) 比率検定での相違 - Azure | Microsoft Docs"
description: "(非推奨) 比率検定での相違"
services: machine-learning
documentationcenter: 
author: aniedea
manager: jhubbard
editor: cgronlun
ms.assetid: 9356b821-5345-44f6-8e26-719f2dea5e6d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/06/2017
ms.author: aniedea
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: f6ad106e769c807d1c281c8d19127eabc2048f30
ms.openlocfilehash: 18023b5057d1eeca88a341d62cbeb543102b3abf
ms.lasthandoff: 01/11/2017


---
# <a name="deprecated-difference-in-proportions-test"></a>(非推奨) 比率検定での相違

> [!NOTE]
> Microsoft DataMarket は間もなく提供終了となる予定です。この API は推奨されていません。 
> 
> [Cortana Intelligence ギャラリー](http://gallery.cortanaintelligence.com)では、便利なサンプル実験や API が提供されています。 ギャラリーについて詳しくは、「 [Cortana Intelligence ギャラリーでリソースを共有および発見する](machine-learning-gallery-how-to-use-contribute-publish.md)」をご覧ください。

2 つの比率は統計的に異なりますか。 ユーザーが&2; つのムービーを比較して、一方のムービーが他方のムービーと比べて "いいね" の比率が大幅に高いかどうかを判断したいとします。 大規模なサンプルでは、0.50 と 0.51 の比率の間に統計上重大は相違が発生する可能性があります。 小規模なサンプルでは、これらの比率が実際に相違を生じているかを判別するにはデータ量が十分ではない可能性があります。 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

この [Web サービス](https://datamarket.azure.com/dataset/aml_labs/prop_test) は、ユーザーが入力した 2 つの比較グループに対する成功の数と試行回数の合計に基づいて、2 つの比率の仮説検定を実行します。 この Web サービスはムービー比較アプリから呼び出すことができ、ムービー評価に基づいて、あるムービーが他のムービーと比較して実際に「いいね」と評価されている場合が多いかどうかをユーザーに示すというシナリオが考えられます。

> この Web サービスは、モバイル アプリ、Web サイト、ローカル コンピューターなどからユーザーが使用できます。 この Web サービスのもう&1; つの目的は、Azure Machine Learning を使用して R コード上に Web サービスを作成する方法の例を示すことです。 数行の R コードを記述し、Azure Machine Learning Studio 内でボタンを何回かクリックするだけで、R コードで実験を作成し、Web サービスとして発行できます。 この Web サービスは Azure Marketplace に発行され、Web サービスの作成者がインフラストラクチャを設定することなく、世界中のユーザーやデバイスで使用されます。
> 
> 

## <a name="consumption-of-web-service"></a>Web サービスの使用
このサービスは 4 つの引数を使用し、比率の仮説検定を行います。

入力引数は、次のとおりです。

* Successes1: サンプル 1 での成功イベントの数
* Successes2: サンプル 2 での成功イベントの数
* Total1: サンプル 1 のサイズ
* Total2: サンプル 2 のサイズ

サービスの出力は、カイ二乗統計量、df、p 値、サンプル 1 と 2 の比率、および信頼区間限界を含む仮説検定の結果です。

> Azure Marketplace でホストされているこのサービスは、OData サービスです。これらは、POST や GET メソッドによって呼び出すことができます。 
> 
> 

自動でサービスを使用するための複数の方法があります ([ここ](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx)にアプリケーション例があります)。

### <a name="starting-c-code-for-web-service-consumption"></a>Web サービスを使用する C# コードを開始します。
    public class Input
    {
            public string successes1;
            public string successes2;
            public string total1;
            public string total2;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { successes1 = TextBox1.Text, successes2 = TextBox2.Text, total1 = TextBox3.Text, total2 = TextBox4.Text };
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

Azure Machine Learning 内で、新しい空白の実験が作成され、2 つの [R スクリプトの実行][execute-r-script]が取得されました。 1 番目のモジュールではデータ スキーマを定義し、2 番目のモジュールでは R 内で prop.test コマンドを使用して、2 つの比率に対して仮説検定を実行します。 

### <a name="experiment-flow"></a>実験フロー:
![実験フロー][2]

#### <a name="module-1"></a>モジュール 1:
    ####Schema definition  
    data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
    maml.mapOutputPort("data.set"); #send data to output port
    dataset1 = maml.mapInputPort(1) #read data from input port


#### <a name="module-2"></a>モジュール 2:
    test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

    result=data.frame(
    result=ifelse(test$p.value<0.05,"The proportions are different!",
    "The proportions aren't different statistically."),
    ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
    pvalue=round(test$p.value,4),
    proportion1=round(test$estimate[1],4),
    proportion2=round(test$estimate[2],4),
    confintlow=round(test$conf.int[1],4),
    confinthigh=round(test$conf.int[2],4)) 

    maml.mapOutputPort("result"); #output port


## <a name="limitations"></a>制限事項
これは、2 つの比率の相違をテストするきわめて簡単な例です。 上のコード例からわかるように、エラーのキャッチは実装されません。サービスは、すべての変数が連続していると想定しています。

## <a name="faq"></a>FAQ
Web サービスの使用や、Azure Marketplace への発行に関するよく寄せられる質問については、 [ここ](machine-learning-marketplace-faq.md)をご覧ください。

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/



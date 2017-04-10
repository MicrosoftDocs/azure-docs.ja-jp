---
title: "(非推奨) クラスター モデル - Azure | Microsoft Docs"
description: "(非推奨) クラスター モデル"
services: machine-learning
documentationcenter: 
author: FrancescaLazzeri
manager: jhubbard
editor: cgronlun
ms.assetid: 51b8d012-ed44-4312-920c-9c808dfd4ff6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/06/2017
ms.author: lazzeri
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: f6ad106e769c807d1c281c8d19127eabc2048f30
ms.openlocfilehash: 84c94b83878f851c2ac281fe33afe53e02f8456c
ms.lasthandoff: 01/11/2017


---
# <a name="deprecated-cluster-model"></a>(非推奨) クラスター モデル

> [!NOTE]
> Microsoft DataMarket は間もなく提供終了となる予定です。この API は推奨されていません。 
> 
> [Cortana Intelligence ギャラリー](http://gallery.cortanaintelligence.com)では、便利なサンプル実験や API が提供されています。 ギャラリーについて詳しくは、「 [Cortana Intelligence ギャラリーでリソースを共有および発見する](machine-learning-gallery-how-to-use-contribute-publish.md)」をご覧ください。

クレジット カード発行者の償却リスクを軽減するために、クレジット カード所有者グループの行動を予測することはできますか。 職場でのパフォーマンスを向上させるために、従業員の性格特性グループを定義することができますか。 医師が、病気の特性に基づいて患者をグループに分類できますか。 原則として、こうした質問はすべて、クラスター分析によって回答できます。   

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

クラスター分析は、変数の組み合わせに基づいて、一連の観察を&2; つ以上の相互に排他的な未知のグループに分類します。 クラスター分析の目的は、観察 (通常は人々やその特性に対する観察) をグループに整理する方法を発見することです。グループのメンバーは、通常、プロパティを共有しています。 この[サービス](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model)は、一般的に使用されるクラスタリング手法である K 平均法を使用して、任意のデータをグループに分類します。 この Web サービスは、入力としてデータとクラスターの数 k を取得し、各観察がどの k グループに属するかの予測を生成します。 

> この Web サービスは、モバイル アプリ、Web サイト、ローカル コンピューターなどからユーザーが使用できます。 この Web サービスのもう&1; つの目的は、Azure Machine Learning を使用して R コード上に Web サービスを作成する方法の例を示すことです。 数行の R コードを記述し、Azure Machine Learning Studio 内でボタンを何回かクリックするだけで、R コードで実験を作成し、Web サービスとして発行できます。 この Web サービスは Azure Marketplace に発行され、Web サービスの作成者がインフラストラクチャを設定することなく、世界中のユーザーやデバイスで使用されます。  
> 
> 

## <a name="consumption-of-web-service"></a>Web サービスの使用
この Web サービスは、一連の k 個のグループにデータをグループ化し、行ごとにグループの割り当てを出力します。 Web サービスでは、エンド ユーザーが、行をコンマ (,) で区切り、列をセミコロン (;) で区切った文字列として、データを入力する必要があります。 Web サービスでは、一度に 1 行を入力します。 データセットの例は、次のようになります。

![サンプル データ][1]

ユーザーがこのデータを相互に排他的な 3 つのグループに分割するとします。 上のデータセットに次のデータを入力します。 出力は、それぞれの行に対して予測されたグループ メンバーシップです。

> Azure Marketplace でホストされているこのサービスは、OData サービスです。これらは、POST や GET メソッドによって呼び出すことができます。 
> 
> 

自動でサービスを使用するための複数の方法があります ([ここ](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx)にアプリケーション例があります)。

### <a name="starting-c-code-for-web-service-consumption"></a>Web サービスを使用する C# コードを開始します。
    public class Input
    {
            public string value;
            public string k;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { value = TextBox1.Text, k = TextBox2.Text };
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

Azure Machine Learning 内で、新しい空白の実験が作成され、ワークスペース上に&2; つの [R スクリプトの実行][execute-r-script] を取得しました。 データ スキーマは、単純な [R スクリプトの実行][execute-r-script] で作成されました。 次にそのデータ スキーマがクラスター モデル セクションにリンクされ、[R スクリプト実行][execute-r-script] をやり直して作成されました。 クラスター モデルに使用された [R スクリプトの実行][execute-r-script] では、Web サービスは "k-means" 関数を使用します。この関数は、Azure Machine Learning の [R スクリプト実行][execute-r-script] に事前に組み込まれています。    

![実験フロー][3]

#### <a name="module-1"></a>モジュール 1:
    #Enter the input data as a string 
    mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)

    maml.mapOutputPort("mydata");     


#### <a name="module-2"></a>モジュール 2:
    # Map 1-based optional input ports to variables
    mydata <- maml.mapInputPort(1) # class: data.frame

    data.split <- strsplit(mydata[1,1], ",")[[1]]
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- as.data.frame(t(data.split))

    data.split <- data.matrix(data.split)
    data.split <- data.frame(data.split)

    # K-Means cluster analysis
    fit <- kmeans(data.split, mydata$k) # k-cluster solution

    # Get cluster means 
    aggregate(data.split,by=list(fit$cluster),FUN=mean)
    # Append cluster assignment
    mydatafinal <- data.frame(t(fit$cluster))
    n_col=ncol(mydatafinal)
    colnames(mydatafinal) <- paste("V",1:n_col,sep="")

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("mydatafinal");


## <a name="limitations"></a>制限事項
これは、Web サービスのクラスタリングのきわめて単純な例です。 上のコード例からわかるように、エラーのキャッチは実装されていません。このサービスでは、Web サービスの作成時に数値のみを入力するため、すべてが連続した変数 (カテゴリ別機能は使用できません) である必要があります。 また、サービスが処理できるデータ サイズは制限されています。これは、Web サービス呼び出しの要求/応答の性質と、Web サービスを呼び出すたびにモデルが適合されることによります。 

## <a name="faq"></a>FAQ
Web サービスの使用や、Azure Marketplace への発行に関するよく寄せられる質問については、 [ここ](machine-learning-marketplace-faq.md)をご覧ください。

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/



---
title: "(非推奨) 辞書ベースの感情分析 - Azure | Microsoft Docs"
description: "(非推奨) 辞書ベースのセンチメント分析"
services: machine-learning
documentationcenter: 
author: pengxia
manager: jhubbard
editor: cgronlun
ms.assetid: 912f41af-966c-4d79-a413-6f9fc02823df
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/06/2017
ms.author: pengxia
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: f6ad106e769c807d1c281c8d19127eabc2048f30
ms.openlocfilehash: 02a6005c47734419903f324f6fb60440f9d62664
ms.lasthandoff: 01/11/2017


---
# <a name="deprecated-lexicon-based-sentiment-analysis"></a>(非推奨) 辞書ベースのセンチメント分析

> [!NOTE]
> Microsoft DataMarket は間もなく提供終了となる予定です。この API は推奨されていません。 
> 
> [Cortana Intelligence ギャラリー](http://gallery.cortanaintelligence.com)では、便利なサンプル実験や API が提供されています。 ギャラリーについて詳しくは、「 [Cortana Intelligence ギャラリーでリソースを共有および発見する](machine-learning-gallery-how-to-use-contribute-publish.md)」をご覧ください。

Facebook の投稿記事、ツイート、レビューなど、オンライン ソーシャル ネットワーク上のブランドやトピックに対するユーザーの意見や態度はどのように測定できるでしょうか。 センチメント分析は、このような質問を分析するための方法を提供します。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

センチメント分析には、一般的な&2; つの方法があります。 1 つは、教師あり学習アルゴリズムを使用します。もう&1; つは教師なし学習として扱うことができます。 教師あり学習アルゴリズムは、一般に大規模な注釈付きコーパスの分類モデルを構築します。 その精度は主として注釈の品質に依存し、通常、トレーニング プロセスには長い時間がかかります。 その上、アルゴリズムを別のドメインに適用すると、通常よい結果は得られません。 教師あり学習に対して、辞書ベースの教師なし学習はセンチメント辞書を使用します。これには、大規模なデータ コーパスやトレーニングを格納する必要がないため、全体の処理がはるかに高速になります。 

この[サービス](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis)は、最もよく使用される主観性辞書の&1; つである MPQA 主観性用語集 (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/) 上に構築されています。 MPQA には 5,097 個の負の単語と 2,533 個の正の単語があります。 これらの単語はすべて、強い極性または弱い極性の注釈が付けられています。 コーパス全体は、GNU General Public License (GNU 一般公衆ライセンス) で許諾されています。 この Web サービスは、ツイート、Facebook の投稿などの短い文章に適用できます。 

> この Web サービスは、モバイル アプリ、Web サイト、ローカル コンピューターなどからユーザーが使用できます。 この Web サービスのもう&1; つの目的は、Azure Machine Learning を使用して R コード上に Web サービスを作成する方法の例を示すことです。 数行の R コードを記述し、Azure Machine Learning Studio 内でボタンを何回かクリックするだけで、R コードで実験を作成し、Web サービスとして発行できます。 この Web サービスは Azure Marketplace に発行され、Web サービスの作成者がインフラストラクチャを設定することなく、世界中のユーザーやデバイスで使用されます。
> 
> 

## <a name="consumption-of-web-service"></a>Web サービスの使用
入力データは任意のテキストを指定できますが、この Web サービスは短い文章に適しています。 出力は、-1 から 1 までの数値です。 0 未満の値は、テキストのセンチメントが負であることを示します。0 より大きい場合は正であることを表します。 結果の絶対値は、関連付けられているセンチメントの強度を表します。 

> Azure Marketplace でホストされているこのサービスは、OData サービスです。これらは、POST や GET メソッドによって呼び出すことができます。 
> 
> 

自動でサービスを使用するための複数の方法があります ([ここ](http://microsoftazuremachinelearning.azurewebsites.net/)にアプリケーション例があります)。

### <a name="starting-c-code-for-web-service-consumption"></a>Web サービスを使用する C# コードを開始します。
    public class ScoreResult
    {
            [DataMember]
            public double result
            {
                get;
                set;
            }
    }

    void main()
    {
            using (var wb = new WebClient())
            {
                var acitionUri = new Uri("PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score");
                DataServiceContext ctx = new DataServiceContext(acitionUri);
                var cred = new NetworkCredential("PutEmailAddressHere", "ChangeToAPIKey");
                var cache = new CredentialCache();

                cache.Add(acitionUri, "Basic", cred);
                ctx.Credentials = cache;
                var query = ctx.Execute<ScoreResult>(acitionUri, "POST", true, new BodyOperationParameter("Text", TextBox1.Text));
                ScoreResult scoreResult = query.ElementAt(0);
                double result = scoreResult.result;
            }
    }



入力が "Today is a good day." (今日はいい日ですね) で、 出力が "1" です。 これは入力された文書に対する正のセンチメントを示します。 

## <a name="creation-of-web-service"></a>Web サービスの作成
> この Web サービスは、Azure Machine Learning を使用して作成されました。 無料試用版の場合は、実験の作成と [Web サービスの発行](machine-learning-publish-a-machine-learning-web-service.md)に関する入門ビデオのほか、[azure.com/ml](http://azure.com/ml) もご覧ください。 Web サービスを作成した実験のスクリーン ショット、および実験内の各モジュールに対するコード例を以下に示します。
> 
> 

Azure Machine Learning 内で、新しい空白の実験が作成されました。 次の図は、辞書ベースのセンチメント分析の実験フローを示します。 "Sent_dict.csv" は MPQA 主観性辞書であり、[R スクリプトの実行][execute-r-script]の入力の&1; つとして設定されています。 他の入力は、Amazon レビューのデータセットからテスト用にサンプリングされたレビューです。選択、列名の変更、分割操作を実行しました。 ハッシュ パッケージを使用して主観性辞書をメモリに格納し、スコアの計算プロセスを高速化します。 テキスト全体は、"tm" パッケージによってトークン化され、センチメント辞書の単語と比較されます。 最後に、テキストの主観的な各単語の重みを追加して、スコアが計算されます。 

### <a name="experiment-flow"></a>実験フロー:
![実験フロー][2]

#### <a name="module-1"></a>モジュール 1:
    # Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame

# <a name="install-hash-package"></a>ハッシュ パッケージのインストール
    install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE)
    library(tm)
    library(stringr)

    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)

    #  Compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
    polarity_ratio <- 0
    polarity_total <- 0
    not <- 0
    sentence <- tolower(dataset2[m,1])
    if (nchar(sentence) > 0){
        token_array <- scan_tokenizer(sentence)
        for (j in 1:length(token_array)){
            word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
            for (k in 1:length(negation_word)){
              if (word == negation_word[k]){
                not <- (not+1) %% 2

              }
            }
            if (word != ""){
                if (!is.null(sent_dict[[word]])){
                  polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
                  polarity_total <- polarity_total + abs(sent_dict[[word]])
                }
            }

        }
    }
    if (polarity_total > 0){
        result <- c(result, polarity_ratio/polarity_total)
    }else{
        result<- c(result,0)
    }
    }

    # Sample operation
    data.set <- data.frame(result)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")



## <a name="limitations"></a>制限事項
アルゴリズムの観点からは、辞書ベースのセンチメント分析は一般的なセンチメント分析ツールであり、特定のフィールドに対する分類方法より優れた分析を行うことはできません。 否定の問題にはうまく対応できません。 プログラムには、いくつかの否定単語をハードコーディングしていますが、否定の辞書を使用して、ルールをいくつか構築する方をお勧めします。 この Web サービスは、Amazon レビューなどの長くて複雑な文章より、ツイート、Facebook の投稿など短くて簡潔な文書でパフォーマンスが向上します。 

## <a name="faq"></a>FAQ
Web サービスの使用や、Azure Marketplace への発行に関するよく寄せられる質問については、 [ここ](machine-learning-marketplace-faq.md)をご覧ください。

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/




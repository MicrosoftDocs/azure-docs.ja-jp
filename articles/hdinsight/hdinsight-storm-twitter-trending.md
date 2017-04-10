---
title: "HDInsight での Apache Storm を使用した Twitter のトレンディング トピック | Microsoft Docs"
description: "Trident を使用して、Twitter で (ハッシュタグに基づいて) トレンディング トピックを見つける Apache Storm トポロジを作成する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 63b280ea-5c07-4dc8-a35f-dccf5a96ba93
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 53c18f6bb294c42456a0a4cd3c2a83812e9b13d0
ms.lasthandoff: 11/17/2016


---
# <a name="determine-twitter-trending-topics-with-apache-storm-on-hdinsight"></a>HDInsight での Apache Storm を使用した Twitter のトレンディング トピックの確認

Trident を使用して、Twitter のトレンディング トピック (ハッシュタグ) を見つける Storm トポロジを作成する方法について説明します。

Trident は、結合、集計、グループ化、関数、フィルタリングなどのツールを提供する大枠の抽象概念です。 さらに、Trident はステートフルな増分処理を行うためのプリミティブを追加します。 この例では、カスタムのスパウト、関数、Trident で提供される複数のビルトイン関数を用いてトポロジを構築する方法を示しています。

> [!NOTE]
> この例は、Juan Alonso による [Trident Storm](https://github.com/jalonsoramos/trident-storm) の例に基づいています。


## <a name="requirements"></a>必要条件
* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java と JDK 1.7</a>
* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>
* <a href="http://git-scm.com/" target="_blank">Git</a>
* Twitter の開発者アカウント

## <a name="download-the-project"></a>プロジェクトのダウンロード
次のコードを使用してプロジェクトの複製をローカルに作成します。

    git clone https://github.com/Blackmist/TwitterTrending

## <a name="topology"></a>トポロジ
この例のトポロジは次のとおりです。

![トポロジ](./media/hdinsight-storm-twitter-trending/trident.png)

> [!NOTE]
> これは、トポロジの簡略化されたビューです。 コンポーネントの複数のインスタンスが、クラスター内のノード全体に配布されます。


トポロジを実装する Trident コードは次のとおりです。

    topology.newStream("spout", spout)
        .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
        .groupBy(new Fields("hashtag"))
        .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
        .newValuesStream()
        .applyAssembly(new FirstN(10, "count"))
        .each(new Fields("hashtag", "count"), new Debug());

このコードは、次の処理を実行します。

1. スパウトから新しいストリームを作成します。 スパウトが Twitter からツイートを取得し、特定のキーワード (この例では、love、music、coffee) でフィルターします。
2. カスタム関数の HashtagExtractor を使用して各ツイートからハッシュタグを抽出します。 これらは、ストリームに出力されます。
3. ストリームはハッシュタグごとにグループ化され、アグリゲーターに渡されます。 このアグリゲーターがハッシュタグが発生した回数のカウントを作成します。 このデータは、メモリに保存されます。 最後に、ハッシュタグとカウントを含む新しいストリームが出力されます。
4. 必要なのは、特定のツイート群の最も人気のあるハッシュタグのみであるため、カウント フィールドに基づいて上位 10 個の値のみを返す **FirstN** アセンブリが適用されます。

> [!NOTE]
> スパウトと HashtagExtractor 以外については、ビルトインの Trident 関数を使用します。
> 
> ビルトイン操作については、「<a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">Package storm.trident.operation.builtin</a>」をご覧ください。
> 
> MemoryMapState 以外の Trident ステートの実装については、以下をご覧ください。
> 
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">Storm Trident の柔軟な検索</a>
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">trident-redis</a>
> 
> 

### <a name="the-spout"></a>スパウト
スパウトである **TwitterSpout** は、<a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a> を使用して Twitter からのツイートを取得します。 フィルター (love、music、coffee) が作成され、フィルターに一致する受信ツイート (ステータス) がリンクされたブロック キューに格納されます  (詳細については、「<a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">Class LinkedBlockingQueue</a>」をご覧ください)。最後に、アイテムがキューから取り出されてトポロジに出力されます。

### <a name="the-hashtagextractor"></a>HashtagExtractor
ハッシュタグを抽出するには、<a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a> を使用してツイートに含まれるすべてのハッシュタグを取得します。 その後、これらはストリームに出力されます。

## <a name="enable-twitter"></a>Twitter を有効にする
次の手順を使用して、新しい Twitter アプリケーションを登録し、Twitter からの読み取りに必要なコンシューマーとアクセス トークンを取得します。

1. <a href="https://apps.twitter.com" target="_blank">Twitter アプリ</a>に移動して、**[Create new app (新しいアプリの作成)]** ボタンをクリックします。 フォームの入力時、 **[Callback URL]** フィールドは空白のままにします。
2. アプリが作成されたら、 **[Keys and Access Tokens]** タブをクリックします。
3. **[Consumer Key (コンシューマー キー)]** と **[Consumer Secret (コンシューマーシークレット)]** の情報をコピーします。
4. トークンがない場合、ページ下部で **[Create my access token]** を選択します。 トークンを作成したら、**[Access Token]** と **[Access Token Secret]** の情報をコピーします。
5. 前に複製した **TwitterSpoutTopology** プロジェクトで、**resources/twitter4j.properties** ファイルを開いて、前の手順で収集した情報を追加し、ファイルを保存します。

## <a name="build-the-topology"></a>トポロジの作成
次のコードを使用してプロジェクトを構築します。

        cd [directoryname]
        mvn compile

## <a name="test-the-topology"></a>トポロジのテスト
次のコマンドを使用して、ローカルでトポロジをテストします。

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

トポロジの起動後、トポロジから出力されたハッシュタグとカウントを含むデバッグ情報を確認します。 出力は次のようになります。

    DEBUG: [Quicktellervalentine, 7]
    DEBUG: [GRAMMYs, 7]
    DEBUG: [AskSam, 7]
    DEBUG: [poppunk, 1]
    DEBUG: [rock, 1]
    DEBUG: [punkrock, 1]
    DEBUG: [band, 1]
    DEBUG: [punk, 1]
    DEBUG: [indonesiapunkrock, 1]

## <a name="next-steps"></a>次のステップ
これで、トポロジをローカルでテストできました。次はこのトポロジをデプロイする方法、「[HDInsight での Apache Storm トポロジの展開と管理](hdinsight-storm-deploy-monitor-topology.md)」をご確認ください。

必要に応じて次の Storm 関連のトピックもご覧ください。

* [HDInsight での Apache Storm の Java ベース トポロジの開発](hdinsight-storm-develop-java-topology.md)
* [Visual Studio を使用して HDInsight で Storm の C# トポロジを開発する](hdinsight-storm-develop-csharp-visual-studio-topology.md)

HDInsight での Storm のその他の例:

* [HDInsight 上の Storm に関するトポロジ例](hdinsight-storm-example-topology.md)



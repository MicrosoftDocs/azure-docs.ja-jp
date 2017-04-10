---
title: "Log Analytics のワイヤ データ ソリューション | Microsoft Docs"
description: "ワイヤ データとは、Operations Manager エージェントや Windows に接続されたエージェントなどの OMS エージェントがインストールされたコンピューターからのネットワーク データとパフォーマンス データを統合したものです。 ネットワーク データをログ データと結び付けると、データを相関させるのに役立ちます。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 15858f7b7436536e6bae7fcfd6a50c722d2d04a2
ms.openlocfilehash: be00cb9b1e8ba5d9d8368695ca8d448d466e8f47


---
# <a name="wire-data-solution-in-log-analytics"></a>Log Analytics のワイヤ データ ソリューション
ワイヤ データとは、Operations Manager エージェントや Windows に接続されたエージェントなどの OMS エージェントがインストールされたコンピューターからのネットワーク データとパフォーマンス データを統合したものです。 ネットワーク データをログ データと結び付けると、データを相関させるのに役立ちます。 IT インフラストラクチャ内のコンピューターにインストールされている OMS エージェントでは、使用されるさまざまなプロトコルやポートなど、 [OSI モデル](https://en.wikipedia.org/wiki/OSI_model) のネットワーク レベル 2 ～ 3 について、これらのコンピューターとの間で送受信されたネットワーク データを監視します。

> [!NOTE]
> ワイヤ データ ソリューションは、現在、ワークスペースに追加できません。 ワイヤ データ ソリューションを既に有効にしているユーザーは、ワイヤ データ ソリューションを引き続き使用できます。
>
>

既定では、OMS は、Windows に組み込まれているカウンターから CPU、メモリ、ディスク、ネットワークのパフォーマンス データについてログに記録されたデータを収集します。 コンピューターで使用されているサブネットやアプリケーション レベルのプロトコルを始めとするネットワークなどのデータは、エージェントごとにリアルタイムで収集されます。 [ログ] タブの [設定] ページでは、その他のパフォーマンス カウンターを追加できます。

[sFlow](http://www.sflow.org/) などのソフトウェアと [Cisco の NetFlow プロトコル](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html)を使用したことがある場合は、ワイヤ データから表示される統計情報とデータはよく知っているはずです。

組み込みのログ検索クエリの種類には、次のようなものがあります。

* ワイヤ データを提供するエージェント
* ワイヤ データを提供するエージェントの IP アドレス
* IP アドレスによる外部への送信
* アプリケーション プロトコルによって送信されたバイト数
* アプリケーション サービスによって送信されたバイト数
* さまざまなプロトコルで受信したバイト数
* IP によって送受信された合計バイト数
* 10.0.0.0/8 サブネット上のエージェントと通信した IP アドレス
* 確実に測定された接続の平均待機時間
* ネットワーク トラフィックを開始または受信したコンピューター プロセス数
* プロセスのネットワーク トラフィックの量

ワイヤ データを使って検索する場合は、データのフィルター処理とグループ化によって、上位のエージェントと上位のプロトコルに関する情報を表示できます。 また、特定のコンピューター (IP アドレスまたは MAC アドレス) が相互に通信した時点、通信の長さ、送信されたデータ量について詳しく調べることもできます。基本的には、ネットワーク トラフィックに関するメタデータが検索ベースで表示されます。

ただし、表示されるのはメタデータであるため、必ずしも詳細なトラブルシューティングに役立つとは限りません。 OMS のワイヤ データは、ネットワーク データを完全にキャプチャしたものではありません。 したがって、パケット レベルの詳細なトラブルシューティングを目的としたものではありません。
その他の収集方法と比較した場合、エージェントを使う利点は、アプライアンスのインストール、ネットワーク スイッチの再構成、複雑な構成作業の実行が必要ないことです。 ワイヤ データは単純にエージェント ベースであるため、コンピューターにエージェントをインストールすると、そのエージェント自体のネットワーク トラフィックが監視されます。 もう 1 つの利点は、クラウド プロバイダー、ホスティング サービス プロバイダー、Microsoft Azure など、ユーザーがファブリック レイヤーを所有しない場所で実行されるワークロードを監視できることです。

それとは対照的に、ネットワーク インフラストラクチャ内のすべてのコンピューターにエージェントをインストールしない限り、ネットワーク上で何が発生しているかを完全に把握することはできません。

## <a name="installing-and-configuring-the-solution"></a>ソリューションのインストールと構成
次の情報を使用して、ソリューションをインストールおよび構成します。

* ワイヤ データ ソリューションでは、Windows Server 2012 R2、Windows 8.1 以降のオペレーティング システムを実行しているコンピューターからデータを取得します。
* ワイヤ データの取得元となるコンピューターには、Microsoft .NET Framework 4.0 以降が必要です。
* 「 [ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」で説明されている手順に従ってワイヤ データ ソリューションを OMS ワークスペースに追加します。  さらに手動で構成する必要はありません。
* 特定のソリューションのワイヤ データを表示する場合は、そのソリューションが既に OMS ワークスペースに追加されている必要があります。

## <a name="wire-data-data-collection-details"></a>ワイヤ データのデータ収集の詳細
ワイヤ データでは、有効になっているエージェントを使用して、ネットワーク トラフィックについてのメタデータを収集します。

次の表は、ワイヤ データのデータ収集手段とデータ収集方法に関する各種情報をまとめたものです。

| プラットフォーム | 直接エージェント | SCOM エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows (2012 R2/8.1 以降) |![はい](./media/log-analytics-wire-data/oms-bullet-green.png) |![あり](./media/log-analytics-wire-data/oms-bullet-green.png) |![なし](./media/log-analytics-wire-data/oms-bullet-red.png) |![いいえ](./media/log-analytics-wire-data/oms-bullet-red.png) |![なし](./media/log-analytics-wire-data/oms-bullet-red.png) |1 分ごと |

## <a name="combining-wire-data-with-other-solution-data"></a>ワイヤ データを他のソリューションのデータと組み合わせる
上記で説明した組み込みクエリから返されるデータは、それ単独でも興味深い情報です。 しかし、ワイヤ データの有用性は、他の OMS ソリューションからの情報と組み合わせて初めて実現します。 たとえば、セキュリティと監査ソリューションで収集したセキュリティ イベント データをワイヤ データと組み合わせて使うと、名前付きプロセスに対するネットワーク ログオンの異常な試行を検索できます。  この例では、IN 演算子と DISTINCT 演算子を使って、検索クエリのデータ ポイントを結合します。

要件: 次の例を使うには、セキュリティと監査ソリューションをインストールしておく必要があります。 ただし、他のソリューションからのデータをワイヤ データと組み合わせて使っても、同様の結果が得られます。

### <a name="to-combine-wire-data-with-security-events"></a>ワイヤ データをセキュリティ イベントと組み合わせるには
1. [概要] ページの **[WireData]** タイルをクリックします。
2. **[WireData の一般的なクエリ]** の一覧で **[プロセスごとのネットワーク トラフィックの量 (バイト単位)]** をクリックして、返されるプロセスの一覧を参照します。
    ![WireData のクエリ](./media/log-analytics-wire-data/oms-wiredata-01.png)
3. プロセスの一覧が長すぎて見づらい場合は、次の例と同じように検索クエリを変更できます。

    ```
    Type WireData | measure count() by ProcessName | where AggregatedValue <40
    ```
    次の例では、DancingPigs.exe という名前のプロセスがあり、これが疑わしいように見えます。
    ![WireData の検索結果](./media/log-analytics-wire-data/oms-wiredata-02.png)
4. 一覧として返されたデータから、名前付きプロセスをクリックします。 この例では、DancingPigs.exe をクリックしました。 次に示す結果では、各種プロトコルでの送信などのネットワーク トラフィックの種類が示されています。
    ![名前付きプロセスが表示された WireData の結果](./media/log-analytics-wire-data/oms-wiredata-03.png)
5. セキュリティと監査ソリューションがインストールされているため、検索クエリの IN 演算子と DISTINCT 演算子を使って検索クエリの結果を絞り込むと、ProcessName フィールドの値が同じセキュリティ イベントを調べることができます。 これを行うことができるのは、ワイヤ データと他のソリューション ログの値がどちらも同じ形式である場合です。 次の例と同じように検索クエリを変更します。

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName}
    ```    

    ![wiredata results showing combined data](./media/log-analytics-wire-data/oms-wiredata-04.png)
6. 上記の結果には、アカウント情報が示されます。 これで、次の例と同じように、検索クエリを絞り込み、そのアカウントがプロセスによって使われた頻度を、セキュリティと監査のデータから示すことができます。        

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName} | measure count() by Account
    ```

    ![wiredata results showing account data](./media/log-analytics-wire-data/oms-wiredata-05.png)

## <a name="next-steps"></a>次のステップ
* [ログを検索](log-analytics-log-searches.md) して、詳細なワイヤ データ検索レコードを確認します。
* Dan の「 [Using Wire Data in Operations Management Suite Log Search (Operations Management Suite Log Search でのワイヤ データの使用)](http://blogs.msdn.com/b/dmuscett/archive/2015/09/09/using-wire-data-in-operations-management-suite.aspx) 」というブログ投稿では、データが収集される頻度や、Operations Manager エージェントの収集プロパティを変更する方法について、付加的な情報が提供されています。



<!--HONumber=Nov16_HO3-->



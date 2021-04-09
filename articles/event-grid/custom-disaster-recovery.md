---
title: Azure Event Grid のカスタム トピック用のディザスター リカバリー
description: このチュートリアルでは、Event Grid サービスがリージョンで異常な状態になった場合に復旧するためのイベント処理アーキテクチャを設定する方法について説明します。
ms.topic: tutorial
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e37cb6a0679ee2e249de4ed8fa31c40d5082ea4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020145"
---
# <a name="build-your-own-disaster-recovery-for-custom-topics-in-event-grid"></a>Event Grid のカスタム トピック用に独自のディザスター リカバリーを構築する
ディザスター リカバリーは、アプリケーションの機能の深刻な損失からの復旧に重点を置きます。 このチュートリアルでは、Event Grid サービスが特定のリージョンで異常な状態になった場合に復旧するためのイベント処理アーキテクチャを設定する方法について説明します。

このチュートリアルでは、Event Grid のカスタム トピック用のアクティブ/パッシブ フェールオーバー アーキテクチャを作成する方法を学習します。 お客様のトピックとサブスクリプションを 2 つのリージョンの間でミラーリングしておき、トピックの異常発生時にフェールオーバーを管理することで、フェールオーバーを実現します。 このチュートリアルのアーキテクチャでは、すべての新しいトラフィックがフェールオーバーされます。 この設定では、被害のあったリージョンがもう一度正常になるまで、既に送信中のイベントが復旧されないことを理解しておくのが重要です。

> [!NOTE]
> Event Grid は現在、サーバー側での自動 geo ディザスター リカバリー (GeoDR) をサポートしています。 フェールオーバー プロセスをさらに細かく制御したい場合には、クライアント側のディザスター リカバリー ロジックを実装することもできます。 自動 GeoDR の詳細については、「[Server-side geo disaster recovery in Azure Event Grid (Azure Event Grid 内のサーバー側 geo ディザスター リカバリー)](geo-disaster-recovery.md)」を参照してください。

## <a name="create-a-message-endpoint"></a>メッセージ エンドポイントの作成

お客様のフェールオーバー構成をテストするには、お客様のイベントを受信する場所となるエンドポイントが必要です。 エンドポイントはお客様のフェールオーバー インフラストラクチャの一部ではありませんが、イベント ハンドラーとして機能し、テストを容易にします。

テストを簡略化するために、イベント メッセージを表示する[既成の Web アプリ](https://github.com/Azure-Samples/azure-event-grid-viewer)をデプロイします。 デプロイされたソリューションには、App Service プラン、App Service Web アプリ、および GitHub からのソース コードが含まれています。

1. **[Deploy to Azure]\(Azure にデプロイ\)** を選択して、ソリューションをサブスクリプションにデプロイします。 Azure portal で、パラメーターの値を指定します。

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Aquent." /></a>

1. デプロイが完了するまでに数分かかる場合があります。 デプロイが成功した後で、Web アプリを表示して、実行されていることを確認します。 Web ブラウザーで `https://<your-site-name>.azurewebsites.net` にアクセスします
この URL は後で必要になるのでメモしておいてください。

1. サイトは表示されますが、イベントはまだ送信されていません。

   ![新しいサイトを表示する](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]


## <a name="create-your-primary-and-secondary-topics"></a>プライマリ トピックとセカンダリ トピックの作成

最初に、Event Grid トピックを 2 つ作成します。 これらのトピックは、お客様のプライマリとセカンダリとして機能します。 既定では、イベントはお客様のプライマリ トピック経由で流れます。 プライマリ リージョンでサービスが停止した場合、お客様のセカンダリに引き継がれます。

1. [Azure portal](https://portal.azure.com) にサインインする 

1. Azure のメイン メニューの左上隅で **[すべてのサービス]** を選択し、「**Event Grid**」を検索して、 **[Event Grid トピック]** を選択します。

   ![[Event Grid トピック] メニュー](./media/custom-disaster-recovery/select-topics-menu.png)

    今後アクセスしやすいように、[Event Grid トピック] の横にある星を選択してこれをリソース メニューに追加します。

1. [Event Grid トピック] メニューで、 **[+ 追加]** を選択してプライマリ トピックを作成します。

   * トピックに論理名を付け、"-primary" をサフィックスとして追加し、追跡しやすくします。
   * このトピックのリージョンが、お客様のプライマリ リージョンになります。

     ![Event Grid トピックのプライマリ作成ダイアログ](./media/custom-disaster-recovery/create-primary-topic.png)

1. トピックが作成されたら、そこに移動して **[トピック エンドポイント]** をコピーします。 後で URI が必要になります。

    ![Event Grid のプライマリ トピック](./media/custom-disaster-recovery/get-primary-topic-endpoint.png)

1. トピックのアクセス キーを取得します。これも後で必要になります。 リソース メニューの **[アクセス キー]** をクリックして、キー 1 をコピーします。

    ![プライマリ トピックのキーの取得](./media/custom-disaster-recovery/get-primary-access-key.png)

1. トピックのブレードで **+ [イベント サブスクリプション]** をクリックして、チュートリアルの前提条件で作成したサブスクライブ中のイベント レシーバー Web サイトに接続するサブスクリプションを作成します。

   * イベント サブスクリプションに論理名を付け、"-primary" をサフィックスとして追加し、追跡しやすくします。
   * [エンドポイントの種類] で [webhook] を選択します。
   * エンドポイントをお客様のイベント レシーバーのイベント URL に設定します。これは `https://<your-event-reciever>.azurewebsites.net/api/updates` のようになります。

     ![[イベント サブスクリプションの作成] の [基本] ページのスクリーンショット。[名前]、[エンドポイントの種類]、および [エンドポイント] の値が強調表示されています。](./media/custom-disaster-recovery/create-primary-es.png)

1. 同じフローを繰り返して、セカンダリのトピックとサブスクリプションを作成します。 今回は、"-primary" サフィックスを "-secondary" に置き換えて追跡しやすくします。 最後に、別の Azure リージョンにそれを配置してください。 これは好きな場所に配置できますが、[Azure のペアになっているリージョン](../best-practices-availability-paired-regions.md)の使用が推奨されます。 セカンダリのトピックとサブスクリプションを別のリージョンに配置すると、プライマリ リージョンがダウンしてもお客様の新しいイベントが流れるようになります。

これで以下を用意できました。

   * テスト用のイベント レシーバー Web サイト。
   * お客様のプライマリ リージョン内のプライマリ トピック。
   * お客様のプライマリ トピックをイベント レシーバー Web サイトに接続するプライマリ イベント サブスクリプション。
   * お客様のセカンダリ リージョン内のセカンダリ トピック。
   * お客様のプライマリ トピックをイベント レシーバー Web サイトに接続するセカンダリ イベント サブスクリプション。

## <a name="implement-client-side-failover"></a>クライアント側のフェールオーバーの実装

リージョンで冗長なトピックとサブスクリプションのペアを設定できたところで、クライアント側のフェールオーバーを実装する準備が整いました。 これを実現するにはいくつかの方法があります。しかし、すべてのフェールオーバー実装には共通の特徴があります。あるトピックが正常でなくなったら、トラフィックが別のトピックにリダイレクトされるという点です。

### <a name="basic-client-side-implementation"></a>基本的なクライアント側の実装

次のサンプル コードは、常にお客様のプライマリ トピックへの発行を最初に試行するシンプルな .NET 発行元です。 成功しなかった場合は、セカンダリ トピックにフェールオーバーされます。 どちらの場合でも、`https://<topic-name>.<topic-region>.eventgrid.azure.net/api/health` に対して GET を実行することで、他のトピックの正常性 API がチェックされます。 正常なトピックは、 **/api/health** エンドポイントに対して GET が実行された場合に常に **200 OK** で応答します。

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using Microsoft.Azure.EventGrid;
using Microsoft.Azure.EventGrid.Models;
using Newtonsoft.Json;

namespace EventGridFailoverPublisher
{
    // This captures the "Data" portion of an EventGridEvent on a custom topic
    class FailoverEventData
    {
        [JsonProperty(PropertyName = "teststatus")]
        public string TestStatus { get; set; }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // TODO: Enter the endpoint each topic. You can find this topic endpoint value
            // in the "Overview" section in the "Event Grid Topics" blade in Azure Portal..
            string primaryTopic = "https://<primary-topic-name>.<primary-topic-region>.eventgrid.azure.net/api/events";
            string secondaryTopic = "https://<secondary-topic-name>.<secondary-topic-region>.eventgrid.azure.net/api/events";

            // TODO: Enter topic key for each topic. You can find this in the "Access Keys" section in the
            // "Event Grid Topics" blade in Azure Portal.
            string primaryTopicKey = "<your-primary-topic-key>";
            string secondaryTopicKey = "<your-secondary-topic-key>";

            string primaryTopicHostname = new Uri( primaryTopic).Host;
            string secondaryTopicHostname = new Uri(secondaryTopic).Host;

            Uri primaryTopicHealthProbe = new Uri("https://" + primaryTopicHostname + "/api/health");
            Uri secondaryTopicHealthProbe = new Uri("https://" + secondaryTopicHostname + "/api/health");

            var httpClient = new HttpClient();

            try
            {
                TopicCredentials topicCredentials = new TopicCredentials(primaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(primaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to primary Event Grid topic.");

                HttpResponseMessage health = httpClient.GetAsync(secondaryTopicHealthProbe).Result;
                Console.Write("\n\nSecondary Topic health " + health);
            }
            catch (Microsoft.Rest.Azure.CloudException e)
            {
                TopicCredentials topicCredentials = new TopicCredentials(secondaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(secondaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to secondary Event Grid topic. Reason for primary topic failure:\n\n" + e);

                HttpResponseMessage health = httpClient.GetAsync(primaryTopicHealthProbe).Result;
                Console.Write("\n\nPrimary Topic health " + health);
            }

            Console.ReadLine();
        }

        static IList<EventGridEvent> GetEventsList()
        {
            List<EventGridEvent> eventsList = new List<EventGridEvent>();

            for (int i = 0; i < 5; i++)
            {
                eventsList.Add(new EventGridEvent()
                {
                    Id = Guid.NewGuid().ToString(),
                    EventType = "Contoso.Failover.Test",
                    Data = new FailoverEventData()
                    {
                        TestStatus = "success"
                    },
                    EventTime = DateTime.Now,
                    Subject = "test" + i,
                    DataVersion = "2.0"
                });
            }

            return eventsList;
        }
    }
}
```

### <a name="try-it-out"></a>試してみる

お客様のコンポーネントをすべて正しく用意できたところで、お客様のフェールオーバー実装をテストできます。 Visual Studio Code または任意の環境で、上記のサンプルを実行します。 次の 4 つの値を、お客様のトピックのエンドポイントとキーに置き換えます。

   * primaryTopic - お客様のプライマリ トピックのエンドポイント。
   * secondaryTopic - お客様のセカンダリ トピックのエンドポイント。
   * primaryTopicKey - お客様のプライマリ トピックのキー。
   * secondaryTopicKey - お客様のセカンダリ トピックのキー。

イベント発行元の実行を試してみます。 以下のように、テスト イベントが Event Grid ビューアーに到達したことがわかるはずです。

![Event Grid のプライマリ イベント サブスクリプション](./media/custom-disaster-recovery/event-grid-viewer.png)

お客様のフェールオーバーが機能することを確認するために、プライマリ トピックのキーの文字をいくつか変更して、それを無効にできます。 イベント発行元の実行をもう一度試してみます。 今回も Event Grid ビューアーに新しいイベントが表示されるはずです。ただし、コンソールを確認すると、今度はセカンダリ トピック経由でそれらが発行されていることがわかります。

### <a name="possible-extensions"></a>拡張の可能性

お客様のニーズに基づいてこのサンプルを拡張する方法は多数あります。 高負荷のシナリオでは、定期的にトピックの正常性 API を個別にチェックするとよいでしょう。 そうすることで、もしトピックがダウンしても、発行するたびにそれをチェックする必要がなくなります。 トピックが正常でないことがわかったら、セカンダリ トピックへの発行を既定にすることができます。

同様に、お客様の特定のニーズに基づいてフェールバック ロジックを実装するとよいでしょう。 最も近いデータ センターに発行することが、待ち時間を短縮するうえで非常に重要である場合、フェールオーバーしたトピックの正常性 API を定期的にプローブできます。 再び正常になったら、最も近いデータ センターに安心してフェールバックできることがわかります。

## <a name="next-steps"></a>次のステップ

- [http エンドポイントでイベントを受信](./receive-events.md)する方法について学習する
- [イベントをハイブリッド接続にルーティング](./custom-event-to-hybrid-connection.md)する方法を理解する
- [Azure DNS と Traffic Manager を使用したディザスター リカバリー](../networking/disaster-recovery-dns-traffic-manager.md)について学習する

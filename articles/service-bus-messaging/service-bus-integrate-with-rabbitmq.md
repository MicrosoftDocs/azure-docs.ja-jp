---
title: RabbitMQ を Azure Service Bus と統合する方法
description: RabbitMQ を Azure Service Bus と統合するためのステップ バイ ステップ ガイド
services: Azure Service Bus
author: videlalvaro
ms.topic: how-to
ms.service: service-bus
ms.date: 07/02/2020
ms.author: alvidela
ms.openlocfilehash: 6366824b8dc7f63f99ebda2a542d95d3eb1c6146
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91301107"
---
# <a name="how-to-integrate-rabbitmq-with-azure-service-bus"></a>RabbitMQ を Azure Service Bus と統合する方法

このガイドでは、RabbitMQ から Azure Service Bus にメッセージを送信する方法について説明します。

これらの機能は、次のようなシナリオで利用できます。

- **エッジ セットアップ**:RabbitMQ にメッセージを送信しているエッジ セットアップがありますが、これらのメッセージをさらに処理するために [Azure Service Bus](./service-bus-messaging-overview.md) に転送して、[Azure ビッグ データ機能](/azure/architecture/guide/architecture-styles/big-data)の多くを利用できるようにしたいと考えています。
- **ハイブリッド クラウド**:あなたの会社が最近買収したサード パーティは、メッセージングのニーズに RabbitMQ を使用しています。 彼らは別のクラウドを使用しています。 RabbitMQ から Azure Service Bus にブリッジすることで、Azure への移行と同時にデータの共有を開始できます。
- **サード パーティの統合**:RabbitMQ をブローカーとして使用している組織外のサード パーティからデータを受け取る必要があります。 彼らに SAS キーを提供し、Azure Service Bus キューの限られたセットへのアクセスを許可することで、そこにメッセージを転送してもらうことができます。

このリストはさらに続きますが、これらのユース ケースのほとんどは、RabbitMQ から Azure にブリッジすることで解決できます。

最初に、[ここ](https://azure.microsoft.com/free/)でサインアップして、無料の Azure アカウントを作成する必要があります。

アカウントにサインインしたら、[Azure portal](https://portal.azure.com/) にアクセスし、新しい Azure Service Bus [名前空間](./service-bus-create-namespace-portal.md)を作成します。 名前空間は、キューやトピックなどのメッセージング コンポーネントが存在するスコープ コンテナーです。

## <a name="adding-a-new-azure-service-bus-namespace"></a>新しい Azure Service Bus 名前空間を追加する

Azure portal で、大きいプラス ボタンをクリックして新しいリソースを追加します。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-resource.png" alt-text="リソースの作成":::

次に、[統合] を選択し、[Azure Service Bus] をクリックして、メッセージング名前空間を作成します。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/integration.png" alt-text="[Azure Service Bus] の選択":::

名前空間の情報を入力するように求められます。 使用する Azure サブスクリプションを選択します。 [リソース グループ](../azure-resource-manager/management/manage-resource-groups-portal.md)がない場合は、新しいものを作成できます。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace.png" alt-text="名前空間の作成":::

[`Namespace name`] には [`rabbitmq`] を使用しますが、任意のものを指定できます。 次に、場所を [`East US`] に設定します。 価格レベルとして [`Basic`] を選択します。

すべてを正しく設定すると、次のような確認画面が表示されます。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace-confirm.png" alt-text="名前空間の作成の確認":::

Azure portal に戻ると、新しい `rabbitmq` 名前空間が一覧に表示されています。 これをクリックしてリソースにアクセスし、キューを追加します。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/resource-view-with-namespace.png" alt-text="新しい名前空間が表示されたリソースの一覧":::

## <a name="creating-our-azure-service-bus-queue"></a>Azure Service Bus キューを作成する

Azure Service Bus 名前空間が用意できたので、左側の [`Entities`] の下にある [`Queues`] ボタンをクリックして、新しいキューを追加します。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-queue.png" alt-text="キューの作成":::

メッセージの送信元がわかるように、キューの名前は「`from-rabbitmq`」になります。 その他のオプションはすべて既定値のままでかまいませんが、ご自分のアプリのニーズに合わせて変更することもできます。

## <a name="enabling-the-rabbitmq-shovel-plugin"></a>RabbitMQ Shovel プラグインを有効にする

RabbitMQ から Azure Service Bus にメッセージを配信するには、RabbitMQ のパッケージに含まれている [Shovel プラグイン](https://www.rabbitmq.com/shovel.html)を使用します。 次のコマンドを使用して、プラグインとそのビジュアル インターフェイスを有効にすることができます。

```bash
rabbitmq-plugins enable rabbitmq_shovel_management
```

>必要に応じて、このコマンドは root として実行します。

次に、RabbitMQ を Azure に接続するために必要な資格情報を取得します。

## <a name="connecting-rabbitmq-to-azure-service-bus"></a>RabbitMQ を Azure Service Bus に接続する

キューの[共有アクセス ポリシー](../storage/common/storage-sas-overview.md) (SAS) を作成する必要があります。これにより、RabbitMQ がメッセージをそのキューに発行できるようになります。 SAS ポリシーでは、外部パーティに対して実行を許可する、リソースでの操作を指定できます。 たとえば、RabbitMQ に対してメッセージの送信を許可し、キューのリッスンや管理は許可しないといった指定が可能です。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-sas-policy.png" alt-text="SAS ポリシーの追加":::

[`Send`] ボックスをオンにした後、[`Create`] をクリックして SAS ポリシーを設定します。

ポリシーが作成されたら、それをクリックして **プライマリ接続文字列** を確認します。 これを使用して、RabbitMQ が Azure Service Bus と通信できるようにします。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/sas-policy-key.png" alt-text="SAS ポリシーの取得":::

この接続文字列を使用するには、RabbitMQ の AMQP 接続形式に変換する必要があります。 [接続文字列変換ツール](https://red-mushroom-0f7446a0f.azurestaticapps.net/)にアクセスし、接続文字列をフォームに貼り付けて、[変換] をクリックします。 RabbitMQ に対応した接続文字列が表示されます (この Web サイトでは、ローカルのブラウザー内ですべての処理が行われます。入力したデータがネットワーク経由で送信されることはありません)。 ソース コードには [GitHub](https://github.com/videlalvaro/connstring_to_amqp) でアクセスできます。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/converter.png" alt-text="接続文字列の変換":::

次に、ブラウザーで RabbitMQ 管理プラグインを開き (`http://localhost:15672/#/dynamic-shovels`)、[`Admin -> Shovel Management` ] に移動します。ここで、RabbitMQ キューから Azure Service Bus キューへのメッセージ送信を処理する新しい Shovel を追加できます。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/add-shovel.png" alt-text="RabbitMQ Shovel の追加":::

ここでは、`azure` という Shovel を呼び出し、ソース プロトコルとして [`AMQP 0.9.1`] を選択します。 このスクリーンショットにある `amqp://` は、ローカルの RabbitMQ サーバーに接続する既定の URI です。 これは、現在のデプロイに合うように変更してください。

キュー側の設定で、キューの名前として [`azure`] を使用できます。 このキューが存在しない場合、RabbitMQ によって作成されます。 また、既存のキューの名前を選択することもできます。 他のオプションは既定値のままでかまいません。

次に、`destination` 側の設定で、プロトコルとして [`AMQP 1.0`] を選択します。 [`URI`] フィールドに、前の手順で変換した接続文字列を入力します (Azure の接続文字列を RabbitMQ 形式に変換したもの)。 次のようになります。

```
amqps://rabbitmq-shovel:StringOfRandomChars@rabbitmq.servicebus.windows.net:5671/?sasl=plain
```

[`Address`] フィールドに、**Azure Service Bus キュー** の名前を入力します。この例では「`from-rabbitmq`」です。 [`Add Shovel`] をクリックすると、セットアップでメッセージの受信を開始する準備が整います。

## <a name="publishing-messages-from-rabbitmq-to-azure-service-bus"></a>RabbitMQ から Azure Service Bus へのメッセージの発行

RabbitMQ Management インターフェイスで、[`Queues`] にアクセスし、[`azure`] キューを選択して、[`Publish message`] パネルを検索します。 フォームが表示され、そこからキューに直接メッセージを発行することができます。 この例では、[`Payload`] に「`fist message`」を追加して [`Publish Message`] をクリックします。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/first-message.png" alt-text="最初のメッセージの発行":::

Azure に戻り、キューを調べます。 左のパネルで `Service Bus Explorer` をクリックし、 _[クイック表示する]_ ボタンをクリックします。 何も問題がなければ、キューにメッセージが 1 つ表示されます。 おめでとうございます!

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/service-bus-queue.png" alt-text="Azure Service Bus キュー":::

念のため、メッセージが RabbitMQ から送信されたものであることを確認しましょう。 [`Peek`] タブを選択し、[`Peek`] ボタンをクリックして、キュー内の最後のメッセージを取得します。 メッセージをクリックして、内容を確認します。 次の画像のように、`first message` が一覧に表示されます。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/peek.png" alt-text="キューのプレビュー":::

## <a name="lets-recap"></a>まとめ

おめでとうございます! これで完了です。 RabbitMQ から Azure Service Bus にメッセージを送信することができました。手順をおさらいしましょう。

1. Azure Service Bus 名前空間を作成する
2. 名前空間にキューを追加する
3. SAS ポリシーをキューに追加する
4. キューの接続文字列を取得する
5. RabbitMQ Shovel プラグインと管理インターフェイスを有効にする
6. Azure Service Bus 接続文字列を RabbitMQ の AMQP 形式に変換する
7. 新しい Shovel を RabbitMQ に追加し、それを Azure Service Bus に接続する
8. メッセージを発行する

上記の手順に従って、Azure の外部にある組織の領域を統合しました。 Shovel プラグインを使用することで、RabbitMQ から Azure Service Bus にメッセージを送信できました。 信頼されたサード パーティがアプリを Azure デプロイに接続できるようになったため、これには非常に大きな利点があります。

最後に、メッセージングとは接続を可能にすることであり、この手法によって新しい接続が開かれました。

## <a name="next-steps"></a>次の手順

- [Azure Service Bus](./service-bus-messaging-overview.md) の詳細
- 詳細については、「[Service Bus での AMQP 1.0 サポート](./service-bus-amqp-overview.md)」を参照してください。

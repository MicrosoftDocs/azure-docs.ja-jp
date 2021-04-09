---
title: Azure Event Grid のイベント ドメイン
description: この記事では、イベント ドメインを使用して、さまざまなビジネス組織、顧客、アプリケーションへのカスタム イベントのフローを管理する方法について説明します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 46a50a8ecc50bd1b80efcba41228564df1c36c9f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198678"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Event Grid トピックを管理するためのイベント ドメインについて

この記事では、イベント ドメインを使用して、さまざまなビジネス組織、顧客、アプリケーションへのカスタム イベントのフローを管理する方法について説明します。 イベント ドメインを使用して、次のことを行います。

* 大規模なマルチテナントのイベント処理アーキテクチャを管理する。
* 承認と認証を管理する。
* 各トピックを個別に管理するのではなく、パーティション分割する。
* 各トピック エンドポイントへの個別の発行を回避する。

## <a name="event-domain-overview"></a>イベント ドメインの概要

イベント ドメインは、同じアプリケーションに関連する数多くの Event Grid トピック用の管理ツールです。 数千の個々のトピックを含めることができるメタ トピックと考えることができます。

イベント ドメインにより、ユーザーは、Azure サービス (Storage や IoT Hub など) によって使用されているものと同じアーキテクチャ を使用して、イベントを発行できます。 数千のトピックにイベントを発行できます。 また、ドメインでは、テナントをパーティション分割できるように各トピックの承認と認証を制御できます。

## <a name="example-use-case"></a>ユース ケースの例
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="access-management"></a>アクセス管理

ドメインでは、Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、各トピックの認可と認証を細かく制御することができます。 これらのロールを使用することで、アプリケーションの各テナントを、アクセスを許可するトピックにのみ制限することができます。

イベント ドメインでの Azure RBAC は、他の Event Grid や Azure での[マネージド アクセス制御](security-authorization.md)の動作と同じように動作します。 Azure RBAC を使用して、イベント ドメインでカスタム ロール定義を作成して適用します。

### <a name="built-in-roles"></a>組み込みロール

Event Grid には、Azure RBAC がイベント ドメインでより簡単に動作するように、2 つの組み込みロール定義があります。 **EventGrid EventSubscription 共同作成者 (プレビュー)** ロールと、**EventGrid EventSubscription 閲覧者 (プレビュー)** ロールです。 イベント ドメインでトピックをサブスクライブする必要があるユーザーに、これらのロールを割り当てます。 ロールの割り当てのスコープは、ユーザーがサブスクライブする必要があるトピックだけにします。

これらのロールについては、[Event Grid の組み込みロール](security-authorization.md#built-in-roles)に関する記事をご覧ください。

## <a name="subscribing-to-topics"></a>トピックのサブスクライブ

イベント ドメイン内のトピックでイベントをサブスクライブすることは、[カスタム トピックでイベント サブスクリプションを作成](./custom-event-quickstart.md)したり、Azure サービスからイベントをサブスクライブしたりすることと同じです。

### <a name="domain-scope-subscriptions"></a>ドメイン スコープ サブスクリプション

イベント ドメインでは、ドメイン スコープ サブスクリプションも許可されます。 イベント ドメインのイベント サブスクリプションでは、イベントの送信先となるトピックに関係なく、ドメインに送信されるすべてのイベントが受信されます。 ドメイン スコープ サブスクリプションは、管理や監査の目的で役立ちます。

## <a name="publishing-to-an-event-domain"></a>イベント ドメインへの発行

イベント ドメインを作成するときに、Event Grid でトピックを作成した場合と同じような発行エンドポイントが与えられます。 

イベント ドメインの任意のトピックにイベントを発行するには、[カスタム トピックの場合と同じように](./post-to-custom-topic.md)、ドメインのエンドポイントにイベントをプッシュします。 唯一の違いは、イベントの配信先となるトピックを指定する必要があることです。

たとえば、次のイベントの配列を発行すると、`"id": "1111"` のイベントがトピック `foo` に送信され、`"id": "2222"` のイベントはトピック `bar` に送信されます。

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

イベント ドメインにより、トピックへの発行が自動的に処理されます。 管理する各トピックに個別にイベントを発行するのではなく、すべてのイベントをドメインのエンドポイントに対して発行することができます。 Event Grid によって、各イベントは正しいトピックに確実に送信されます。

## <a name="limits-and-quotas"></a>制限とクォータ
イベント ドメインに関連した制限とクォータを次に示します。

- イベント ドメインあたり 100,000 トピック 
- Azure サブスクリプションあたり 100 イベント ドメイン 
- イベント ドメイン内のトピックあたり 500 イベント サブスクリプション
- 50 のドメイン スコープ サブスクリプション 
- 毎秒 5,000 イベントのインジェスト速度 (ドメインへの取り込み)

これらの制限が実情に沿わない場合は、サポート チケットを開くか、[askgrid@microsoft.com](mailto:askgrid@microsoft.com) 宛てにメールを送信して製品チームにご相談ください。 

## <a name="pricing"></a>価格
イベント ドメインでは、Event Grid の他のすべての機能で使用されるのと同じ[操作価格](https://azure.microsoft.com/pricing/details/event-grid/)が使用されます。

イベント ドメインでの操作は、カスタム トピックの場合と同じように動作します。 イベント ドメインへのイベントの各取り込みは 1 回の操作であり、イベントの各配信試行も 1 回の操作となります。



## <a name="next-steps"></a>次のステップ

* イベント ドメインの設定、トピックの作成、イベント サブスクリプションの作成、イベントの発行の詳細については、[イベント ドメインの管理](./how-to-event-domains.md)に関するページを参照してください。

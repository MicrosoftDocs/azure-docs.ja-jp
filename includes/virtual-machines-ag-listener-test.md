---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2689e81b089147dfc913fb119e0a499d60574b60
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050319"
---
この手順では、同じネットワーク上で実行されているクライアント アプリケーションを使用して、可用性グループ リスナーをテストします。

クライアント接続の要件は次のとおりです。

* リスナーへのクライアント接続は、AlwaysOn 可用性レプリカをホストするクラウド サービスとは異なるクラウド サービス内に存在するマシンから接続する必要があります。
* AlwaysOn レプリカが別のサブネットにある場合、クライアントは接続文字列で *MultisubnetFailover=True* を指定する必要があります。 この条件により、さまざまなサブネット内のレプリカへのパラレル接続が試行されます。 このシナリオには、AlwaysOn 可用性グループのリージョンをまたがるデプロイが含まれています。

たとえば、同じ Azure 仮想ネットワーク内のいずれかの VM (ただしレプリカをホストしていないもの) からリスナーに接続する場合がこれに当たります。 このテストを完了する簡単な方法は、SQL Server Management Studio を可用性グループ リスナーに接続してみることです。 もう 1 つの簡単な方法は、次のように [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx) を実行することです。

```console
sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15
```

> [!NOTE]
> EndpointPort 値が *1433* の場合、呼び出しでこれを指定する必要はありません。 前の呼び出しでも、クライアント コンピューターが同じドメインに参加していて、データベースへのアクセス許可が Windows 認証を使用して呼び出し元に付与されていることを想定しています。
> 
> 

リスナーをテストする場合は、クライアントがフェールオーバー間でリスナーに接続できるように、可用性グループのフェールオーバーを必ず実行します。


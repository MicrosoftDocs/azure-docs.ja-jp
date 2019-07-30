---
title: Azure Cloud Services の定義LoadBalancerProbe スキーマ | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 14
author: georgewallace
ms.author: gwallace
ms.openlocfilehash: 6f82406772f650b4565f2c9240efe580545dcad9
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360614"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Azure Cloud Services の定義: LoadBalancerProbe スキーマ
ロード バランサー プローブは、ロール インスタンスのエンドポイントと UDP エンドポイントに対して利用者が定義する正常性プローブです。 `LoadBalancerProbe` はスタンドアロン要素ではなく、サービス定義ファイルで Web ロールまたは worker ロールと組み合わされます。 1 つの `LoadBalancerProbe` を複数のロールで使用することができます。

サービス定義ファイルの既定の拡張子は .csdef です。

## <a name="the-function-of-a-load-balancer-probe"></a>ロード バランサー プローブの関数
Azure Load Balancer の役割は、受信トラフィックを対象のロール インスタンスにルーティングすることです。 どのインスタンスがトラフィックを受信するかは、ロード バランサーが、各インスタンスを定期的にプローブしてインスタンスの正常性を調べることによって判断されます。 ロード バランサーは、すべてのインスタンスを 1 分間に複数回プローブします。 インスタンスの正常性をロード バランサーに伝えるには、2 とおりの方法があります。1 つは既定のロード バランサー プローブ、もう 1 つはカスタム ロード バランサー プローブで、.csdef ファイルの LoadBalancerProbe を定義することによって実装されます。

既定のロード バランサー プローブは、仮想マシン内のゲスト エージェントを利用します。ゲスト エージェントが、プローブをリッスンし、そのインスタンスが準備完了状態である (ビジー、リサイクル中、停止中のいずれの状態でもない) ときにだけ HTTP 200 OK 応答を返します。 ゲスト エージェントが HTTP 200 OK で応答できない場合、Azure Load Balancer はそのインスタンスを応答不能と見なし、インスタンスへのトラフィックの送信を停止します。 その後も Azure Load Balancer はインスタンスに ping を送信し続け、そのゲスト エージェントが HTTP 200 で応答した場合、そのインスタンスへのトラフィックの送信を再開します。 Web ロールを使用する場合、Web サイト コードは通常、Azure ファブリックやゲスト エージェントでは監視されない w3wp.exe で実行されます。つまり、w3wp.exe が失敗 ( HTTP 500 応答など) してもゲスト エージェントにレポートされず、ロード バランサーはそのインスタンスがローテーションから除外されたことを認識しません。

カスタム ロード バランサー プローブは、既定のゲスト エージェント プローブをオーバーライドするので、ユーザーは独自のカスタム ロジックを作成してロール インスタンスの正常性を特定することができます。 ロード バランサーは、エンドポイントを定期的にプローブします (既定では 15 秒ごと)。タイムアウト期間内 (既定値は 31 秒) にインスタンスが TCP ACK または HTTP 200 で応答した場合は、ローテーション内にあると見なされます。 これを利用して、ロード バランサーのローテーションからインスタンスを除外するユーザー独自のロジックを実装することができます。たとえば、インスタンスの CPU 使用率が 90% を超えた場合に 200 以外の状態を返すことも可能です。 これは、w3wp.exe を使用する Web ロールについて、Web サイトが自動的に監視されるという意味でもあります。Web サイト コードに障害が発生するとロード バランサー プローブに 200 以外の状態が返されるためです。 一方、.csdef ファイルで LoadBalancerProbe を定義しなかった場合は、前述したロード バランサーの既定の動作が使用されます。

カスタム ロード バランサー プローブを使用する場合は、そのロジックで RoleEnvironment.OnStop メソッドを考慮に入れる必要があります。 既定のロード バランサー プローブを使用しているときは、インスタンスをローテーションから外したうえで OnStop が呼び出されますが、カスタム ロード バランサー プローブの場合は、OnStop イベントの発生中でも 200 OK を返し続けることができます。 キャッシュのクリーンアップやサービスの停止など、サービス実行時の動作に影響を与えうる何らかの変更を OnStop イベントで行う場合は、カスタム ロード バランサー プローブのロジックで確実にそのインスタンスをローテーションから除外する必要があります。

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>ロード バランサー プローブの基本サービス定義スキーマ
 ロード バランサー プローブが含まれるサービス定義ファイルの基本形式は次のとおりです。

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>スキーマ要素
サービス定義ファイルの `LoadBalancerProbes` 要素には、次の要素が含まれています。

- [LoadBalancerProbes 要素](#LoadBalancerProbes)
- [LoadBalancerProbe 要素](#LoadBalancerProbe)

##  <a name="LoadBalancerProbes"></a> LoadBalancerProbes 要素
`LoadBalancerProbes` 要素は、ロード バランサー プローブのコレクションを表します。 この要素は、[LoadBalancerProbe 要素](#LoadBalancerProbe)の親要素です。 

##  <a name="LoadBalancerProbe"></a> LoadBalancerProbe 要素
モデルの正常性プローブは `LoadBalancerProbe` 要素で定義します。 複数のロード バランサー プローブを定義することができます。 

以下の表に、`LoadBalancerProbe` 要素の属性を示します。

|Attribute|Type|説明|
| ------------------- | -------- | -----------------|
| `name`              | `string` | 必須。 ロード バランサー プローブの名前です。 名前は一意である必要があります。|
| `protocol`          | `string` | 必須。 エンド ポイントのプロトコルを指定します。 指定できる値は `http` または `tcp` です。 `tcp` を指定した場合、プローブが成功するためには ACK の受信が必要となります。 `http` を指定した場合、プローブが成功するためには、指定された URI から応答として 200 OK が返される必要があります。|
| `path`              | `string` | VM の正常性状態を要求する目的で使用される URI。 `protocol` を `http` に設定した場合、`path` が必須となります。 それ以外の場合は、許可されません。<br /><br /> 既定値はありません。|
| `port`              | `integer` | 省略可能。 プローブ通信用ポート。 エンドポイントにかかわらず省略することができます。その場合、同じポートがプローブに使用されます。 プローブ用に異なるポートを構成することもできます。 指定できる値は 1 以上 65535 以下です。<br /><br /> 既定値はエンドポイントによって設定されます。|
| `intervalInSeconds` | `integer` | 省略可能。 どのぐらいの頻度でエンドポイントに正常性状態をプローブするかを表す間隔 (秒)。 この間隔は、割り当てられているタイムアウト期間 (秒) の 1/2 よりもわずかに短くするのが一般的です。そうすることで、2 回のプローブを完全に実施したうえで、インスタンスをローテーションから除外することができます。<br /><br /> 既定値は 15、最小値は 5 です。|
| `timeoutInSeconds`  | `integer` | 省略可能。 プローブに適用されるタイムアウト期間 (秒)。この期間を過ぎると、応答がないと見なされ、そのエンドポイントへのトラフィックのルーティングが停止されます。 エンドポイントをローテーションから除外するタイミングは、この値を指定することで、Azure で使用される標準的な時間 (既定値) よりも早めたり遅らせたりすることができます。<br /><br /> 既定値は 31、最小値は 11 です。|

## <a name="see-also"></a>関連項目
[Cloud Service (クラシック) 定義スキーマ](schema-csdef-file.md)
---
title: Azure Spring Cloud アプリのログをリアルタイムでストリーム配信する
description: ログ ストリーミングを使用してアプリケーション ログを即座に表示する方法
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 1eeb291c7a058efd8905e95ebf1ea14fed046691
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877448"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Azure Spring Cloud アプリのログをリアルタイムでストリーム配信する

**この記事の適用対象:** ✔️ Java ✔️ C#

Azure Spring Cloud では Azure CLI のログ ストリーミングを使ってトラブルシューティングのためにリアルタイムのアプリケーション コンソール ログを取得できます。 さらに、[診断設定でログとメトリックを分析する](./diagnostic-services.md)こともできます。

## <a name="prerequisites"></a>前提条件

* Spring Cloud (最小バージョン 0.2.0) 用の [Azure CLI 拡張機能](/cli/azure/install-azure-cli)のインストール。
* [Spring Cloud アプリ](./spring-cloud-quickstart.md)などのアプリケーションを実行する **Azure Spring Cloud** のインスタンス。

> [!NOTE]
>  ASC CLI 拡張機能はバージョン 0.2.0 から 0.2.1 に更新されました。 この変更によってログ ストリーミングのコマンドの構文が影響を受けます。`az spring-cloud app logs` が `az spring-cloud app log tail` に取って代わります。 コマンド: `az spring-cloud app log tail` は今後のリリースで非推奨になります。 バージョン 0.2.0 を使用している場合、0.2.1 にアップグレードできます。 まず、コマンド `az extension remove -n spring-cloud` を使用して古いバージョンを削除します。  次に、コマンド `az extension add -n spring-cloud` で 0.2.1 をインストールします。

## <a name="use-cli-to-tail-logs"></a>ログ末尾に CLI を使用する

リソース グループとサービス インスタンス名を繰り返し指定するのを避けるには、既定のリソース グループ名とクラスター名を設定します。
```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
次の例では、コマンドでリソース グループとサービス名を省略しています。

### <a name="tail-log-for-app-with-single-instance"></a>1 つのインスタンスを持つアプリのログ末尾
auth-service という名前のアプリにインスタンスが 1 つしかない場合は、次のコマンドを使用してアプリ インスタンスのログを表示できます。
```azurecli
az spring-cloud app logs -n auth-service
```
これにより、次のログが返されます。
```output
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>複数のインスタンスがあるアプリのログ末尾
`auth-service` という名前のアプリに対して複数のインスタンスが存在する場合は、`-i/--instance` オプションを使用してインスタンスのログを表示できます。 

まず、次のコマンドを使用して、アプリ インスタンス名を取得できます。

```azurecli
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
結果:

```output
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
次に、オプション `-i/--instance` オプションを使用して、アプリ インスタンスのログをストリーミングできます。

```azurecli
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

アプリ インスタンスの詳細は、Azure portal から取得することもできます。  Azure Spring Cloud サービスの左側のナビゲーション ウィンドウで **[アプリ]** を選択した後、 **[App Instances]\(アプリ インスタンス\)** を選択します。

### <a name="continuously-stream-new-logs"></a>新しいログを継続的にストリーミングする
既定では、`az spring-cloud ap log tail` は、アプリ コンソールにストリーミングされた既存のログのみを出力し、終了します。 新しいログをストリーミングする場合は、次のように -f (--follow) を追加します。  

```azurecli
az spring-cloud app logs -n auth-service -f
``` 
サポートされているすべてのログ オプションを確認するには:
```azurecli
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>次のステップ
* [クイック スタート: ログ、メトリック、トレースを使用した Azure Spring Cloud アプリの監視](spring-cloud-quickstart-logs-metrics-tracing.md)
* [診断設定でログとメトリックを分析する](./diagnostic-services.md)


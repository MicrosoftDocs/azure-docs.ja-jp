---
title: Azure Cache for Redis のサンプル
description: コード サンプル (キャッシュへの接続、キャッシュに対するデータの読み取りと書き込み、ASP.NET Azure Cache for Redis プロバイダー) での Azure Cache for Redis の使用方法について説明します。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-dotnet
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 553850173f463a05b13768eb3b9e17703bfa2886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88212296"
---
# <a name="azure-cache-for-redis-samples"></a>Azure Cache for Redis のサンプル
このトピックでは、キャッシュへの接続、キャッシュに対するデータの読み取りと書き込み、ASP.NET の Azure Cache for Redis プロバイダーの使用などのシナリオを対象とする、Azure Cache for Redis のサンプルの一覧を示します。 サンプルの一部はダウンロード可能なプロジェクトです。ステップ バイ ステップ ガイダンスが用意されているサンプルやコード スニペットが含まれているサンプルもありますが、ダウンロード可能なプロジェクトにはリンクしていません。

## <a name="hello-world-samples"></a>Hello world サンプル
このセクションのサンプルでは、Azure Cache for Redis インスタンスへの接続と、キャッシュに対する、さまざまな言語と Redis クライアントを使用したデータの読みと書きの基本を示しています。

[Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) サンプルは .NET 向けの Redis クライアントである [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) を利用してさまざまなキャッシュ操作を実行する方法を示しています。

このサンプルは次の方法を示しています。

* さまざまな接続オプションを使用する
* 同期操作と非同期操作を使用し、キャッシュ間でオブジェクトを読み書きする
* Redis MGET/MSET コマンドを使用し、指定されたキーの値を返す
* Redis トランザクション操作を実行する
* Redis リストと並べ替えられたセットを使用する
* JsonConvert シリアライザーを使用し、.NET オブジェクトを保存する
* Redis セットを使用し、タグ付けを実装する
* Redis クラスターの操作

詳細については、GitHub の「[StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis)」ドキュメントを参照してください。その他の使用シナリオについては、「[StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests)」ユニット テストを参照してください。

[Python で Azure Cache for Redis を使用する方法](cache-python-get-started.md)に関するページでは、Python と [redis-py](https://github.com/andymccurdy/redis-py) クライアントを使用して Azure Cache for Redis の使用を開始する方法を説明しています。

「[キャッシュ内で .NET オブジェクトを使用する](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)」では、.NET オブジェクトをシリアル化し、それらのオブジェクトを Azure Cache for Redis インスタンスとの間で読み書きできるようにする方法の 1 つを示しています。 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>ASP.NET SignalR のスケールアウト バックプレーンとしての Azure Cache for Redis の使用
「[ASP.NET SignalR のスケール アウト バックプレーンとしての Azure Cache for Redis の使用](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane)」サンプルでは、SignalR のバックプレーンとして Azure Cache for Redis を使用する方法を示しています。 バックプレーンの詳細については、「 [Redis を使用した SignalR のスケールアウト](https://www.asp.net/signalr/overview/performance/scaleout-with-redis)」を参照してください。

## <a name="azure-cache-for-redis-customer-query-sample"></a>Azure Cache for Redis の顧客クエリのサンプル
このサンプルでは、キャッシュ内のデータへのアクセスと永続ストレージ内のデータへのアクセスとのパフォーマンスを比較します。 このサンプルには 2 つのプロジェクトがあります。

* [Azure Cache for Redis でデータをキャッシュしてパフォーマンスを向上させる方法のデモ](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [デモ用のデータベースおよびキャッシュのシード](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET セッション状態と出力キャッシュ
[Azure Cache for Redis を使用して ASP.NET SessionState と OutputCache を格納する](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching)サンプルでは、Azure Cache for Redis を使用して、Redis の SessionState プロバイダーと OutputCache プロバイダーで ASP.NET セッションと出力キャッシュを格納する方法を示しています。

## <a name="manage-azure-cache-for-redis-with-maml"></a>MAML で Azure Cache for Redis を管理する
[Azure 管理ライブラリを使用して Azure Cache for Redis を管理する](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)サンプルでは、Azure 管理ライブラリを使用してキャッシュを管理 (作成/更新/削除) する方法を示しています。 

## <a name="custom-monitoring-sample"></a>カスタム監視のサンプル
[Azure Cache for Redis の監視データにアクセスする](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)サンプルでは、Azure portal の外部から Azure Cache for Redis の監視データにアクセスする方法を示しています。

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>PHP と Redis を使用して記述された Twitter 形式の複製
[Retwis](https://github.com/SyntaxC4-MSFT/retwis) サンプルは「Redis Hello World」です。 [Predis](https://github.com/nrk/predis) クライアントを使用し、Redis と PHP で記述された最小の Twitter 形式のソーシャル ネットワーク クローンです。 このソース コードは非常に単純に設計されており、同時にさまざまな Redis データ構造を示しています。

## <a name="bandwidth-monitor"></a>帯域幅モニター
「 [帯域幅モニター](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) 」サンプルでは、クライアントで使用されている帯域幅を監視できます。 帯域幅を測定するには、キャッシュ クライアント コンピューターでサンプルを実行し、キャッシュを呼び出し、帯域幅モニターのサンプルで報告された帯域幅を観察します。

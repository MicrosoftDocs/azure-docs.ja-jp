---
title: 'Azure App Configuration とは '
description: Azure App Configuration サービスの概要をお読みください。 App Configuration を使用する理由と、その使用方法について説明します。
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/19/2020
ms.openlocfilehash: 9786ff20c1d3f467e217d5fe34518fcc9494863e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96930111"
---
# <a name="what-is-azure-app-configuration"></a>Azure App Configuration とは 

Azure App Configuration は、アプリケーション設定と機能フラグを一元的に管理するためのサービスを提供します。 近年のプログラム、特にクラウドで実行されるプログラムは、その性質上、分散されたコンポーネントが多数存在するのが一般的です。 これらのコンポーネント全体に構成設定を分散させることは、トラブルシューティングすることの難しいエラーがアプリケーションのデプロイ中に発生する原因となります。 App Configuration を使用すると、アプリケーションのすべての設定を 1 か所に格納して、そのアクセスをセキュリティで保護することができます。

## <a name="why-use-app-configuration"></a>App Configuration を使用する理由

クラウドベースのアプリケーションは、複数リージョンにわたる複数の仮想マシンまたはコンテナーで実行され、複数の外部サービスを使用することが少なくありません。 分散環境で堅牢かつスケーラブルなアプリケーションを作成することは、大きな課題です。

アプリケーション構築の増大する複雑さに取り組む開発者には、さまざまなプログラミング手法が役立ちます。 たとえば、[12 ファクター アプリ](https://12factor.net/)には、クラウド アプリケーションで使用される多くの十分にテストされたアーキテクチャ パターンとベスト プラクティスが示されています。 このガイドの主な推奨事項の 1 つは、コードから構成を切り離すことです。 アプリケーションの構成設定をその実行可能ファイルとは別の場所に置いておき、その実行環境または外部ソースから読み取るようにする必要があります。

どのアプリケーションでも App Configuration を利用できますが、以下の例は、それを使用することにメリットがある種類のアプリケーションです。

* Azure Kubernetes Service、Azure Service Fabric、または 1 つ以上の地域にデプロイされるその他のコンテナー化されたアプリをベースにしたマイクロサービス
* サーバーレス アプリ (Azure Functions や、その他のイベント駆動型のステートレスなコンピューティング アプリなど)
* 継続的デプロイ パイプライン

App Configuration には次の利点があります。

* 数分で設定できるフル マネージド サービス
* 柔軟性に優れたキーによる表現とマッピング
* ラベルを使用したタグ付け
* 特定時点の設定の再生
* 専用の UI で機能フラグを管理
* 独自に定義したディメンションでの 2 つの構成群の比較
* Azure のマネージド ID で強化されたセキュリティ
* 保存時および転送中の機密情報の暗号化
* 広く使われているフレームワークとのネイティブ統合

App Configuration は、アプリケーションのシークレットを格納するために使用される [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) を補完します。 App Configuration を使用すると、以下のシナリオを簡単に実装できます。

* さまざまな環境や地域に対応した階層型構成データの管理と配布を一元化する
* アプリケーションを再デプロイまたは再起動することなく、アプリケーション設定を動的に変更する
* 機能の可用性をリアルタイムで制御する

## <a name="use-app-configuration"></a>App Configuration の使用

App Configuration ストアを自分のアプリケーションに追加する最も簡単な方法は、Microsoft が提供するクライアント ライブラリを使用することです。 選択した言語とフレームワークに応じて、次の方法を使用して自分のアプリケーションに接続できます

| プログラミング言語とフレームワーク | 接続する方法 |
|---|---|
| .NET Core および ASP.NET Core | .NET Core 用 App Configuration プロバイダー |
| .NET Framework および ASP.NET | .NET 用 App Configuration ビルダー |
| Java Spring | Spring Cloud 用 App Configuration クライアント |
| その他 | App Configuration の REST API |

## <a name="next-steps"></a>次の手順

* [ASP.NET Core のクイック スタート](./quickstart-aspnet-core-app.md)
* [.NET Core のクイック スタート](./quickstart-dotnet-core-app.md)
* [.NET Framework のクイック スタート](./quickstart-dotnet-app.md)
* [Azure Functions のクイックスタート](./quickstart-azure-functions-csharp.md)
* [Java Spring のクイック スタート](./quickstart-java-spring-app.md)
* [ASP.NET Core の機能フラグのクイック スタート](./quickstart-feature-flag-aspnet-core.md)
* [Spring Boot の機能フラグのクイックスタート](./quickstart-feature-flag-spring-boot.md)

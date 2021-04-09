---
title: C# コードを使用して Gen1 環境からデータのクエリを実行する - Azure Time Series Insights Gen1 | Microsoft Docs
description: C# で記述されたカスタム アプリを使用して Azure Time Series Insights Gen1 環境からデータのクエリを実行する方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 724bc80f8887ff2c47db93ecfc4cd2e20be7e7f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95020046"
---
# <a name="query-data-from-the-azure-time-series-insights-gen1-environment-using-c-sharp"></a>C# を使用して Azure Time Series Insights Gen1 環境からデータのクエリを実行する

> [!CAUTION]
> これは Gen1 の記事です。

この C# サンプルでは、[Gen1 Query API](/rest/api/time-series-insights/gen1-query) を使用して Azure Time Series Insights Gen1 環境からデータのクエリを実行する方法について説明します。

> [!TIP]
> [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample) で Gen1 C# コード サンプルを表示します。

## <a name="summary"></a>まとめ

次のサンプル コードでは、以下の機能が示されます。

* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) を使用して、Azure Active Directory を通じてアクセス トークンを取得する方法。

* 取得したアクセス ークンを後続のクエリ API 要求の `Authorization` ヘッダーで渡す方法。

* このサンプルでは、それぞれの Gen1 Query API を呼び出して、以下に対する HTTP 要求がどのように行われるかを示します。
  * [Get Environments API](/rest/api/time-series-insights/gen1-query-api#get-environments-api) (ユーザーがアクセスできる環境を返します)
  * [Get Environment Availability API](/rest/api/time-series-insights/gen1-query-api#get-environment-availability-api)
  * [Get Environment Metadata API](/rest/api/time-series-insights/gen1-query-api#get-environment-metadata-api) (環境メタデータを取得します)
  * [Get Environments Events API](/rest/api/time-series-insights/gen1-query-api#get-environment-events-api)
  * [Get Environment Aggregates API](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api)

* 以下に対するメッセージ処理のために、WSS を使用して Gen1 Query API と対話する方法。

  * [Get Environment Events Streamed API](/rest/api/time-series-insights/gen1-query-api#get-environment-events-streamed-api)
  * [Get Environment Aggregates Streamed API](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>前提条件と設定

サンプル コードをコンパイルして実行する前に、次の手順を実行します。

1. [Gen1 Azure Time Series Insights](./time-series-insights-get-started.md) 環境をプロビジョニングします。
1. [認証と承認](time-series-insights-authentication-and-authorization.md)に関するページにある説明に基づき、Azure Active Directory で Azure Time Series Insights 環境を設定します。
1. 必要なプロジェクト依存関係をインストールします。
1. 各 **#DUMMY#** を該当する環境識別子に置換し、下のサンプル コードを編集します。
1. Visual Studio 内でコードを実行します。

## <a name="project-dependencies"></a>プロジェクト依存関係

最新バージョンの Visual Studio を使用することをお勧めします。

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - バージョン 16.4.2+

サンプル コードには必須の依存関係が 2 つあります。

* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) - 3.13.9 パッケージ。
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) - 9.0.1 パッケージ。

**[ビルド]**  >  **[ソリューションのビルド]** オプションを順に選択して、Visual Studio 2019 にパッケージをダウンロードします。

または、[NuGet 2.12+](https://www.nuget.org/) を使用してパッケージを追加します。

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C# サンプル コード

[Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs) リポジトリを参照し、C# サンプル コードにアクセスします。

## <a name="next-steps"></a>次のステップ

* クエリの詳細については、[クエリ API リファレンス](/rest/api/time-series-insights/gen1-query-api)を参照してください。

* Time Series Insights に [クライアント SDK を使用して JavaScript アプリを接続する](https://github.com/microsoft/tsiclient)方法について確認します。
Azure-Samples/Azure-Time-Series-Insights/gen1-sample/csharp-tsi-gen1-sample/Program.cs
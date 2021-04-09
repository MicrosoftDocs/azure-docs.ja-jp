---
title: C# を使用して Gen2 環境からデータを照会する - Azure Time Series Insights | Microsoft Docs
description: C# で記述されたアプリを使用して Azure Time Series Insights Gen2 環境からデータを照会する方法を学びます。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: aecd18fd0d568904f9704b749525204ced05f3ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463427"
---
# <a name="query-data-from-the-azure-time-series-insights-gen2-environment-using-c-sharp"></a>C# を使用して Azure Time Series Insights Gen2 環境からデータを照会する

この C# サンプルでは、Azure Time Series Insights Gen2 環境にある[Gen2 のデータ アクセス API](/rest/api/time-series-insights/reference-data-access-overview) からデータを照会する方法を示しています。

> [!TIP]
> [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) で Gen2 C# コード サンプルを確認します。

## <a name="summary"></a>まとめ

次のサンプル コードでは、以下の機能が示されます。

* [Azure AutoRest ](https://github.com/Azure/AutoRest)からの SDK 自動生成のサポート。
* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) を使用して、Azure Active Directory を通じてアクセス トークンを取得する方法。
* 取得したアクセス トークンを後続のデータ アクセス API 要求の `Authorization` ヘッダー内で渡す方法。
* サンプルでは、以下に対して HTTP 要求が行われる方法を示すコンソール インターフェイスを提供しています。
  * [Gen2 Environment API](/rest/api/time-series-insights/reference-environments-apis)
    * [Get Environments Availability API ](/rest/api/time-series-insights/dataaccessgen2/query/getavailability)および [Get Event Schema API](/rest/api/time-series-insights/dataaccessgen2/query/geteventschema)
  * [Gen2 Query API](/rest/api/time-series-insights/reference-query-apis)
    * [Get Events API](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)、[Get Series API](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)、および[ Get Aggregate Series API](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
  * [タイム シリーズ モデル API](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
    * [Get Hierarchies API](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies) と [Hierarchies Batch API](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    * [Get Types API](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes) と [Types Batch API](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    * [Get Instances API](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances) と [Instances Batch API](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)

* 高度な[検索](/rest/api/time-series-insights/reference-model-apis#search-features)および [TSX](/rest/api/time-series-insights/reference-time-series-expression-syntax) の機能。

## <a name="prerequisites-and-setup"></a>前提条件と設定

サンプル コードをコンパイルして実行する前に、次の手順を実行します。

1. [Gen2 Azure Time Series Insights](./how-to-create-environment-using-portal.md) 環境をプロビジョニングします。
1. [認証と承認](time-series-insights-authentication-and-authorization.md)に関するページにある説明に基づき、Azure Active Directory で Azure Time Series Insights 環境を設定します。
1. [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/Readme.md) に示されているように [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/GenerateCode.bat) を実行して、Azure Time Series Insights Gen2 クライアントの依存関係を生成します。
1. Visual Studio 上で `TSIPreviewDataPlaneclient.sln` ソリューションを開き、`DataPlaneClientSampleApp` を既定のプロジェクトとして設定します。
1. [以下](#project-dependencies)に示された手順を使用して必要なプロジェクトの依存関係をインストールし、サンプルを実行可能な `.exe` ファイルにコンパイルします。
1. `.exe` ファイルをダブルクリックして、実行します。

## <a name="project-dependencies"></a>プロジェクト依存関係

最新バージョンの Visual Studio を使用することをお勧めします。

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - バージョン 16.4.2+

サンプル コードには、[packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/packages.config) ファイル内で確認できる必須の依存関係がいくつかあります。

**[ビルド]**  >  **[ソリューションのビルド]** オプションを順に選択して、Visual Studio 2019 にパッケージをダウンロードします。

または、[NuGet 2.12+ ](https://www.nuget.org/)を使用して、各パッケージを追加します。 次に例を示します。

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C# サンプル コード

[Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) リポジトリを参照し、C# サンプル コードにアクセスします。

> [!NOTE]
>
> * コード サンプルは、既定の環境変数を変更せずに実行できます。
> * コード サンプルは、.NET 実行可能ファイルのコンソール アプリにコンパイルされます。

## <a name="next-steps"></a>次のステップ

* クエリの詳細については、[クエリ API リファレンス](/rest/api/time-series-insights/reference-query-apis)を参照してください。

* Azure Time Series Insights に [クライアント SDK を使用して JavaScript アプリを接続する](https://github.com/microsoft/tsiclient)方法について確認します。
---
title: Azure SignalR による ASP.NET Core SignalR のスケーリング
description: ASP.NET Core SignalR アプリケーションをスケーリングするための Azure SignalR サービスの使用の概要。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 90853b5ff769b710c6c95e4f6e62b3a4aa19fadf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151082"
---
# <a name="scale-aspnet-core-signalr-applications-with-azure-signalr-service"></a>Azure SignalR サービスによる ASP.NET Core SignalR アプリケーションのスケーリング

## <a name="developing-signalr-apps"></a>SignalR のアプリの開発

現時点では、Web アプリケーションで使用できる SignalR には [2 つのバージョン](/aspnet/core/signalr/version-differences)があります。SignalR for ASP.NET と、最新バージョンの ASP.NET Core SignalR です。 Azure SignalR サービスは、ASP.NET Core SignalR 上に構築された Azure のマネージド サービスです。

ASP.NET Core SignalR は、以前のバージョンのリライトです。 そのため、ASP.NET Core SignalR は、以前のバージョンの SignalR と下位互換性がありません。 API および動作が異なります。 ASP.NET Core SignalR SDK は .NET Standard を対象としているため、.NET Framework と共に使用することができます。 ただし、以前の API ではなく新しい API を使用する必要があります。 SignalR を使用していて、ASP.NET Core SignalR または Azure SignalR サービスに移行する場合は、API の違いを処理するためにコードを変更する必要があります。

Azure SignalR サービスでは、ASP.NET Core SignalR のサーバー側コンポーネントは Azure にホストされます。 ただし、テクノロジは ASP.NET Core 上に組み込まれているので、[Azure App Service](../app-service/overview.md)、[IIS](/aspnet/core/host-and-deploy/iis/index)、[Nginx](/aspnet/core/host-and-deploy/linux-nginx)、[Apache](/aspnet/core/host-and-deploy/linux-apache)、[Docker](/aspnet/core/host-and-deploy/docker/index) でホストしているときに、複数のプラットフォーム (Windows、Linux、MacOS) で実際の Web アプリケーションを実行できます。 独自のプロセスで自己ホストを使用することもできます。

アプリケーションの目標に、リアルタイムでのコンテンツ更新により Web クライアントを更新するための最新機能のサポート、複数のプラットフォーム (Azure、Windows、Linux、macOS) での実行、異なる環境へのホストが含まれる場合は、Azure SignalR サービスの利用が最適な選択肢である可能性があります。

## <a name="why-not-deploy-signalr-myself"></a>なぜ SignalR を自分でデプロイしないのか

ASP.NET Core SignalR をバックエンド コンポーネントとしてサポートする独自の Azure Web アプリを全体的な Web アプリケーションにデプロイするアプローチは依然として有効です。

Azure SignalR サービスを使用する主な理由の 1 つは、シンプルさです。 Azure SignalR サービスでは、パフォーマンス、スケーラビリティ、可用性などの問題を処理する必要がありません。 これらの問題は、99.9% のサービス レベル アグリーメントで対処されます。

また、WebSocket は、通常はリアルタイムのコンテンツ更新をサポートするための優先手法です。 ただし、多数の永続的な WebSocket 接続の負荷分散は、スケーリングの際に解決するのが複雑な問題になります。 一般的なソリューションでは、DNS 負荷分散、ハードウェア ロード バランサー、ソフトウェア負荷分散を利用します。 Azure SignalR サービスがこの問題を処理します。

別の理由は、Web アプリケーションを実際にホストする必要がまったくない可能性があることです。 Web アプリケーションのロジックでは、[サーバーレス コンピューティング](https://azure.microsoft.com/overview/serverless-computing/)を利用できます。 たとえば、[Azure Functions](../azure-functions/index.yml) トリガーでコードをオンデマンドでのみホストおよび実行できる可能性があります。 このシナリオは、コードがオンデマンドでのみ実行され、クライアントとの接続が長時間維持されないため、注意が必要になる可能性があります。 Azure SignalR サービスでは、サービスが既に接続を管理しているため、このような状況に対処できます。 詳細は、 [Azure Functions を使用して SignalR サービスを使用する方法の概要](signalr-concept-azure-functions.md)を参照してください。

## <a name="how-does-it-scale"></a>スケーリングの方法

通常は、SQL Server、Azure Service Bus、または Azure Cache for Redis で SignalR をスケーリングします。 Azure SignalR サービスによってスケーリング アプローチが処理されます。 パフォーマンスとコストはこれらのアプローチと同等で、これらの他のサービスを扱う複雑さを伴いません。 行う必要があるは、サービスのユニット数の更新だけです。 各ユニットでは、最大 1000 のクライアント接続がサポートされます。

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Azure SignalR でのチャット ルームの作成](signalr-quickstart-dotnet-core.md)
---
title: Azure SignalR サービスとは
description: Azure SignalR サービスの概要。
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 198eb0ff6c9f8de311cc2d39ba8fb7c8b6ed3a11
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552089"
---
# <a name="what-is-azure-signalr-service"></a>Azure SignalR サービスとは

Azure SignalR サービスは、リアルタイムの Web 機能を HTTP 経由でアプリケーションに追加するプロセスを簡略化します。 このリアルタイム機能は、サービスが、接続されているクライアントにシングル ページ Web やモバイル アプリケーションなどのコンテンツの更新をプッシュできるようにします。 その結果、クライアントは、サーバーをポーリングしたり更新プログラムについて新しい HTTP 要求を送信したりしなくても更新されます。

この記事では、Azure SignalR サービスの概要について説明します。

## <a name="what-is-azure-signalr-service-used-for"></a>Azure SignalR サービスの用途

リアルタイムのコンテンツ更新を必要とするアプリケーションの種類は多数あります。 以下に、Azure SignalR サービスを使用するのに適した候補例を示します。

* サーバーからの頻繁な更新が必要なアプリ。 たとえば、ゲーム、投票、オークション、マップ、GPS などのアプリです。
* ダッシュボードと監視アプリ。 たとえば、会社のダッシュボードや売上の即時更新などです。
* コラボレーション アプリ。 ホワイトボード アプリとチーム会議ソフトウェアは、コラボレーション アプリの例です。
* 通知を必要とするアプリ。 ソーシャル ネットワーク、電子メール、チャット、ゲーム、トラベル アラート、その他の多くのアプリは通知を使用します。

SignalR では、リアルタイム Web アプリケーションの構築に使用される多くの手法の抽象化が提供されます。 [WebSocket](https://wikipedia.org/wiki/WebSocket) が最適なトランスポートですが、他のオプションを使用できないときは、[Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) や長いポーリングなどの他の手法が使用されます。 SignalR はサーバーとクライアントでサポートされる機能に基づいて、適切なトランスポートを自動的に検出し、初期化します。

SignalR はさらに、サーバーが、すべての接続、または特定のユーザーに属しているか任意のグループに配置された接続のサブセットにメッセージを送信できるようにするリアルタイム アプリケーションのプログラミング モデルを提供します。

## <a name="how-to-use-azure-signalr-service"></a>Azure SignalR Service の使用方法

現在、Azure SignalR サービスを使用する方法は 3 つあります。

- **[ASP.NET Core SignalR アプリをスケールする](signalr-concept-scale-aspnet-core.md)** - Azure SignalR サービスを ASP.NET Core SignalR アプリケーションと統合し、数十万の接続までスケール アウトします。
- **[サーバーレスのリアルタイム アプリを構築する](signalr-concept-azure-functions.md)** - Azure Functions の Azure SignalR サービスとの統合を使用して、JavaScript、C#、Java などの言語でサーバーレスのリアルタイム アプリケーションを構築します。
- **[REST API を介してサーバーからクライアントにメッセージを送信する](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** - Azure SignalR サービスには、アプリケーションが、SignalR サービスで接続されているクライアントに、REST 対応の任意のプログラミング言語でメッセージを投稿できるようにする REST API が用意されています。
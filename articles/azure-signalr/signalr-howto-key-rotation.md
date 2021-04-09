---
title: Azure SignalR Service のアクセス キーのローテーション方法
description: 顧客がアクセス キーを定期的にローテーションする必要がある理由と、Azure portal の GUI と Azure CLI を使用してこれを実行する方法の概要。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 1f80742d48fc46c8c5e337794100ca5901818c88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184391"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>Azure SignalR Service のアクセス キーのローテーション方法

それぞれの Azure SignalR Service インスタンスには、プライマリ キーとセカンダリ キーというアクセス キーのペアがあります。 これらのキーは、サービスへの要求が行われるときに、SignalR クライアントを認証するために使用されます。 キーは、インスタンス エンドポイントの url に関連付けられます。 キーは安全に保ち、定期的にローテーションしてください。 アクセス キーは 2 つ提供されるため、1 つのキーを使用して接続を保ったまま、もう 1 つのキーを再生成できます。

## <a name="why-rotate-access-keys"></a>アクセス キーをローテーションする理由

セキュリティ上の理由とコンプライアンス要件により、定期的にアクセス キーをローテーションしてください。

## <a name="regenerate-access-keys"></a>アクセス キーを再生成する

1. [Azure portal](https://portal.azure.com/) に移動し、資格情報を使用してサインインします。

1. 再生成するキーがある Azure SignalR Service インスタンスで **[キー]** セクションを見つけます。

1. ナビゲーション メニューで **[キー]** を選択します。

1. **[プライマリ キーの再生成]** または **[セカンダリ キーの再生成]** を選択します。

   新しいキーとそれに対応する接続文字列が作成されて表示されます。

   ![キーの再生成](media/signalr-howto-key-rotation/regenerate-keys.png)

[Azure CLI](/cli/azure/signalr/key#az-signalr-key-renew) を使用して、キーを再生成することもできます。

## <a name="update-configurations-with-new-connection-strings"></a>新しい接続文字列を使用した構成の更新

1. 新しく生成された接続文字列をコピーします。

1. 新しい接続文字列を使用するようにすべての構成を更新します。

1. 必要に応じて、アプリケーションを再起動します。

## <a name="forced-access-key-regeneration"></a>強制的なアクセス キーの再生成

Azure SignalR Service では、特定の状況下で必須のアクセス キーの再生成が強制的に実行されることがあります。 このサービスでは、電子メールとポータル通知を使用して顧客への通知が行われます。 この通知を受信した場合や、アクセス キーを原因とするサービス エラーが発生した場合は、このガイドの手順に従ってキーをローテーションしてください。

## <a name="next-steps"></a>次のステップ

適切なセキュリティ プラクティスとして、アクセス キーを定期的にローテーションします。

このガイドでは、アクセス キーを再生成する方法について学習しました。 OAuth または Azure Functions を使用した認証に関する次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ASP.NET Core Identity と統合する](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [認証を使用してサーバーレスのリアルタイム アプリをビルドする](./signalr-tutorial-authenticate-azure-functions.md)
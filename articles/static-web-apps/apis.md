---
title: Azure Functions による Azure Static Web Apps での API のサポート
description: Azure Static Web Apps でサポートされる API の機能について説明します
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 09daffa74ccd279c8187391ba3b86063aed7d204
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607020"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>Azure Functions による Azure Static Web Apps プレビューでの API のサポート

Azure Static Web Apps では、[Azure Functions](../azure-functions/functions-overview.md) を介してサーバーレス API エンドポイントが提供されています。 Azure Functions を利用することで、API は需要に基づいて動的にスケーリングされ、API には次の機能があります。

- ユーザー[認証とロールベースの承認](user-information.md)データに直接アクセスできる**統合セキュリティ**。
- カスタム CORS ルールを必要とせずに、Web アプリで安全に _api_ ルートを使用できるようにする**シームレスなルーティング**。
- Node.js 12 と互換性がある **Azure Functions** v3。
- **HTTP トリガー**と出力バインディング。

## <a name="configuration"></a>構成

Web アプリでは、_api_ ルートを介して API エンドポイントを使用できます。 このルートは固定ですが、関連付けられる Azure Functions アプリを配置するフォルダーを制御できます。 リポジトリの _.github/workflows_ フォルダーにある[ワークフロー YAML ファイルを編集する](github-actions-workflow.md#build-and-deploy)ことにより、この場所を変更できます。

## <a name="constraints"></a>制約

Azure Static Web Apps では、Azure Functions を通じて API が提供されます。 Azure Functions の機能では、Web アプリ用の API を作成し、Web アプリが API に安全に接続できるようにする特定の機能セットに、焦点が当てられています。 これらの機能には、次のようないくつかの制約があります。

- API ルートのプレフィックスは、_api_ である必要があります。
- API 関数アプリは JavaScript で記述されている必要があります。
- API 関数のルート規則では、[リダイレクト](routes.md#redirects)と[ロールによるルートのセキュリティ保護](routes.md#securing-routes-with-roles)のみがサポートされます。
- トリガーは、[HTTP](../azure-functions/functions-bindings-http-webhook.md) に限定されています。
  - 入力と出力の[バインディング](../azure-functions/functions-triggers-bindings.md#supported-bindings)がサポートされています。
- ログは、関数アプリに [Application Insights](../azure-functions/functions-monitoring.md) を追加した場合にのみ使用できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [API を追加する](add-api.md)

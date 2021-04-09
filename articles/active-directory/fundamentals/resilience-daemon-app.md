---
title: 開発するデーモン アプリケーションで認証と認可の回復性を向上させる
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォームを使用してデーモン アプリケーションで認証と認可の回復性を向上させるためのガイダンス
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: a7b8f893026bb96c8d768d2e6d07d0240ecb81fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724843"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-daemon-applications-you-develop"></a>開発するデーモン アプリケーションで認証と認可の回復性を向上させる

この記事では、開発者が Microsoft ID プラットフォームと Azure Active Directory を使用して、デーモン アプリケーションの回復性を向上させる方法に関するガイダンスを提供します。 これには、バックグラウンド プロセス、サービス、サーバー間アプリ、ユーザーのいないアプリケーションなどが含まれます。

![Microsoft ID への呼び出しを行うデーモン アプリケーション](media/resilience-daemon-app/calling-microsoft-identity.png)

## <a name="use-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID を使用する

Microsoft Azure でデーモン アプリを構築している開発者は、[Azure リソースのマネージド ID](../managed-identities-azure-resources/overview.md) を使用できます。 マネージド ID により、開発者はシークレットと資格情報を管理する必要がなくなります。 この機能を使用すると、証明書の有効期限、ローテーション エラー、または信頼に関連したミスを回避することによって回復性が向上します。 また、特に回復性の向上を目的としたいくつかの組み込み機能も用意されています。

マネージド ID は、Microsoft ID からの有効期限の長いアクセス トークンと情報を使用して、既存のトークンの有効期限が切れる前の長い期間内に新しいトークンを事前に取得します。 新しいトークンの取得を試行している間、アプリは継続して実行できます。

マネージド ID では、リージョン外エラーに対するパフォーマンスと回復性を向上させるために、リージョン エンドポイントも使用されます。 リージョン エンドポイントを使用すると、すべてのトラフィックを地理的領域内に維持することができます。 たとえば、Azure リソースが WestUS2 内にある場合は、Microsoft ID によって生成されたトラフィックを含むすべてのトラフィックが WestUS2 内にとどまります。 これにより、サービスの依存関係を統合することで、潜在的障害点を取り除くことができます。

## <a name="use-the-microsoft-authentication-library"></a>Microsoft Authentication Library を使用する

マネージド ID を使用しないデーモン アプリの開発者は、[Microsoft Authentication Library (MSAL)](../develop/msal-overview.md) を使用できます。これにより、認証と認可の実装が単純化され、回復性のためのベスト プラクティスが自動的に使用されます。 MSAL を使用すると、必要なクライアント資格情報を提供するプロセスが容易になります。 たとえば、証明書ベースの資格情報を使用するときに、アプリケーションで JSON Web トークン アサーションの作成と署名を実装する必要がありません。

### <a name="use-microsoftidentityweb-for-net-developers"></a>.NET 開発者向けの Microsoft.Identity.Web を使用する

ASP.NET Core でデーモン アプリを構築している開発者は、[Microsoft Identity.Web](../develop/microsoft-identity-web.md) ライブラリを使用できます。 このライブラリは、MSAL 上に構築されており、ASP.NET Core アプリでの認可の実装をより簡単に行うことができます。 これには、複数のリージョンで実行できる分散アプリ向けの[分散トークン キャッシュ](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#distributed-token-cache)戦略がいくつか含まれています。

## <a name="cache-and-store-tokens"></a>トークンをキャッシュおよび格納する

認証と認可の実装に MSAL を使用していない場合は、トークンのキャッシュと格納に関するいくつかのベスト プラクティスを実装できます。 MSAL は、これらのベスト プラクティスを自動的に実装して従います。

アプリケーションは ID プロバイダーからトークンを取得して、保護された API を呼び出すことをアプリケーションに認可します。 アプリがトークンを受け取ると、トークンが含まれている応答には "expires\_in" プロパティも含まれています。これによって、トークンをキャッシュし、再利用する期間がアプリケーションに通知されます。 アプリケーションで "expires\_in" プロパティを使用して、トークンの有効期間が決定されることが重要です。 アプリケーションによって API アクセス トークンのデコードが試行されてはなりません。 キャッシュされたトークンを使用すると、アプリと Microsoft ID 間の不要なトラフィックを防止できます。 ユーザーは、そのトークンの有効期間中、アプリケーションにサインインした状態を維持できます。

## <a name="properly-handle-service-responses"></a>サービスの応答を適切に処理する

最後に、アプリケーションはすべてのエラー応答を処理する必要がありますが、応答によっては、回復性に影響を与える可能性があるものもあります。 アプリケーションが HTTP 429 応答コード (Too Many Requests) を受信した場合、Microsoft ID によって要求が調整されています。 アプリによって行われる要求が多すぎる状態が続くと、トークンを受信できないように、アプリは調整されたままになります。 [Retry-After]\(再試行までの時間\) 応答フィールドの時間 (秒単位) が経過するまでは、アプリケーションはトークンの再取得を試行するべきではありません。 429 応答の受信は、多くの場合、アプリケーションでトークンのキャッシュと再利用が正しく行われていないことを示しています。 開発者は、アプリケーションでどのようにトークンがキャッシュされて再利用されているかを確認する必要があります。

アプリケーションが HTTP 5xx 応答コードを受信した場合、アプリは高速再試行ループに入ってはなりません。 存在する場合、アプリケーションは、429 応答の場合と同じ Retry-After (再試行までの時間) 処理に従う必要があります。 応答によって "Retry-After" ヘッダーが提供されていない場合は、応答後 5 秒以上経過してから最初の再試行を行うようにして、指数バックオフ再試行を実装することをお勧めします。

要求がタイムアウトになったときに、アプリケーションでは直ちに再試行すべきではありません。 応答から 5 秒以上経過した後に最初の再試行を行うようにして、指数バックオフ再試行を実装します。

## <a name="next-steps"></a>次のステップ

- [ユーザーをサインインするアプリケーションで回復性を強化する](resilience-client-app.md)
- [ID およびアクセス管理インフラストラクチャで回復性を強化する](resilience-in-infrastructure.md)
- [CIAM システムで回復性を強化する](resilience-b2c.md)
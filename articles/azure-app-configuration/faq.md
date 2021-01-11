---
title: Azure App Configuration の FAQ
description: Azure App Configuration のよくあるご質問 (FAQ) に対して、それが Azure Key Vault とどのように違うかなど、回答します。
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: b1483230313b9e1b8e59cafea478b14ba0dfcc70
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587346"
---
# <a name="azure-app-configuration-faq"></a>Azure App Configuration の FAQ

この記事では、Azure App Configuration についてよく寄せられる質問に回答します。

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>App Configuration と Azure Key Vault は何が違うのですか?

App Configuration は、開発者がアプリケーション設定を管理したり機能の利用の可否を制御したりするのに役立ちます。 複雑な構成データの扱いに伴うさまざまなタスクを単純化することを目的としています。

App Configuration では、以下がサポートされています。

- 階層型名前空間
- ラベル付け
- 広範なクエリ
- 一括取得
- 特殊化された管理操作
- 機能管理のユーザー インターフェイス

App Configuration は Key Vault を補完するものであり、両者はほとんどのアプリケーションのデプロイにおいて一緒に使用する必要があります。

## <a name="should-i-store-secrets-in-app-configuration"></a>App Configuration にはシークレットを格納すべきなのでしょうか?

App Configuration は強固なセキュリティを備えていますが、それでもアプリケーション シークレットの保管場所としては Key Vault が最も優れています。 Key Vault では、ハードウェアレベルの暗号化、粒度の細かいアクセス ポリシー、管理操作 (証明書のローテーションなど) が利用できます。

キー コンテナーに格納されているシークレットを参照する App Configuration 値を作成できます。 詳細については、「[ASP.NET Core アプリで Key Vault 参照を使用する](./use-key-vault-references-dotnet-core.md)」を参照してください。

## <a name="does-app-configuration-encrypt-my-data"></a>App Configuration によってデータが暗号化されるのですか?

はい。 App Configuration は、そこに格納されているすべてのキー値を暗号化し、ネットワーク通信を暗号化します。 キー名とラベルは、構成データを取得するためのインデックスとして使用され、暗号化されません。

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>App Configuration と Azure App Service 設定は何が違うのですか?

Azure App Service では、App Service インスタンスごとにアプリ設定を定義できます。 これらの設定は、環境変数としてアプリケーション コードに渡されます。 必要に応じて、特定のデプロイ スロットに設定を関連付けることができます。 詳細については、「[アプリ設定の構成](/azure/app-service/configure-common#configure-app-settings)」を参照してください。

対照的に、Azure App Configuration では、複数のアプリ間で共有できる設定を定義できます。 これには、App Service で実行されるアプリや、その他のプラットフォームが含まれます。 ご利用のアプリケーション コードからこれらの設定へのアクセスは、(.NET と Java の場合) 構成プロバイダーから、Azure SDK から、あるいは REST API 経由で直接、行われます。

App Service と App Configuration の間で設定をインポート/エクスポートすることもできます。 この機能を使用すると、既存の App Service 設定に基づいて新しい App Configuration ストアをすばやく設定できます。 App Service 設定に依存する既存のアプリと構成を共有することもできます。

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>App Configuration に格納されているキーや値に対するサイズ制限はありますか?

1 つのキー値項目に対して 10 KB の制限があります。

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>複数の環境 (テスト、ステージング、本番など) の構成は、どのように格納すべきでしょうか?

ストアごとのレベルで App Configuration にアクセスできるユーザーを制御します。 異なるアクセス許可を必要とする環境ごとに、個別のストアを使用してください。 セキュリティ分離上、このアプローチが最も優れています。

環境間でセキュリティ分離が必要ない場合は、ラベルを使用して構成値を区別できます。 「[ラベルを使用して異なる環境に対する異なる構成を有効にする](./howto-labels-aspnet-core.md)」に、完全な例が挙げられています。

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>App Configuration のおすすめの使い方を教えてください。

[ベスト プラクティス](./howto-best-practices.md)を参照してください。

## <a name="how-much-does-app-configuration-cost"></a>App Configuration にはどのぐらいのコストがかかりますか?

次の 2 つの価格レベルがあります。

- Free レベル
- Standard レベル

Standard レベルの導入前にストアを作成した場合、それは一般公開時に自動的に Free レベルに移行されます。 Standard レベルへのアップグレードか Free レベルの維持を選択できます。

ストアを Standard レベルから Free レベルにダウングレードすることはできません。 Free レベルで新しいストアを作成してから、そのストアに構成データをインポートすることができます。

## <a name="which-app-configuration-tier-should-i-use"></a>どの App Configuration レベルを使用すればよいですか?

App Configuration のどちらのレベルにも、構成設定、機能フラグ、Key Vault 参照、基本的な管理操作、メトリック、ログなどのコア機能が用意されています。

レベルを選択する際の考慮事項を次に示します。

- **サブスクリプションあたりのリソース数**: リソースは、1 つの構成ストアで構成されます。 Free レベルでは、各サブスクリプションの構成ストアは 1 つに制限されています。 Standard レベルでは、サブスクリプションに無制限の数の構成ストアを含めることができます。
- **リソースごとのストレージ**: Free レベルでは、各構成ストアのストレージは 10 MB に制限されています。 Standard レベルでは、各構成ストアで最大 1 GB のストレージを使用できます。
- **キー履歴**: App Configuration では、キーに加えられたすべての変更の履歴が格納されます。 Free レベルでは、この履歴は 7 日間保存されます。 Standard レベルでは、この履歴は 30 日間保存されます。
- **1 日あたりの要求数**: Free レベルのストアでは、1 日あたりの要求数が 1000 件に制限されています。 要求数が 1000 件に達すると、ストアからは、UTC の午前 0 時まですべての要求に対して HTTP 状態コード 429 が返されます。

    Standard レベルのストアの場合、毎日最初の 20 万件の要求が 1 日あたりの料金に含まれます。 追加の要求は超過分として課金されます。

- **サービス レベル アグリーメント**: Standard レベルには、99.9% の可用性の SLA があります。 Free レベルには SLA がありません。
- **セキュリティ機能**: どちらのレベルにも、Microsoft のマネージド キーを使用した暗号化、HMAC または Azure Active Directory を介した認証、RBAC サポート、およびマネージド ID などの基本的なセキュリティ機能が含まれています。 Standard レベルでは、Private Link のサポートや、カスタマー マネージド キーによる暗号化など、より高度なセキュリティ機能が提供されます。
- **コスト**: Standard レベルのストアには、毎日の使用料金があります。 また、1 日の割り当てを超えた要求に対しては超過料金が発生します。 Free レベルのストアを使用する場合、コストはかかりません。

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>ストアを Free レベルから Standard レベルにアップグレードすることはできますか? ストアを Standard レベルから Free レベルにダウングレードすることはできますか?

いつでも Free レベルから Standard レベルにアップグレードすることができます。

ストアを Standard レベルから Free レベルにダウングレードすることはできません。 Free レベルで新しいストアを作成してから、[そのストアに構成データをインポート](howto-import-export-data.md)することができます。

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>App Configuration に対する要求の数に制限はありますか?

Free レベルの構成ストアでは、1 日あたりの要求数が 1000 件に制限されています。 Standard レベルの構成ストアでは、要求レートが 1 時間あたり 2 万要求を超えると、一時的な調整が発生する場合があります。

ストアの上限に達すると、期間が終了するまでに行われたすべての要求に対して HTTP 状態コード 429 が返されます。 応答の `retry-after-ms` ヘッダーは、要求を再試行するまでの推奨される待機時間 (ミリ秒) を示します。

アプリケーションで HTTP 状態コード 429 の応答が定期的に発生する場合は、行われる要求の数を減らすために、アプリケーションを再設計することを検討してください。 詳細については、「[App Configuration に対する要求を減らす](./howto-best-practices.md#reduce-requests-made-to-app-configuration)」を参照してください

## <a name="my-application-receives-http-status-code-429-responses-why"></a>アプリケーションで HTTP 状態コード 429 応答を受信します。 なぜですか?

HTTP 状態コード 429 応答は、次のような状況で返されます。

* Free レベルのストアの 1 日あたりの要求上限を超えている。
* Standard レベルのストアに対する要求レートが高いための一時的な調整。
* 過剰な帯域幅の使用。
* ストレージの見積もりを超えたときにキーを作成または変更しようとしている。

要求が失敗した具体的な理由について、429 応答の本文を確認します。

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>App Configuration に関する新しいリリースやその他の情報についてのお知らせを受け取るにはどうすればよいですか?

[GitHub のお知らせ用のリポジトリ](https://github.com/Azure/AppConfiguration-Announcements)にサブスクライブしてください。

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>問題のレポート方法または提案の送信方法を教えてください。

[GitHub](https://github.com/Azure/AppConfiguration/issues) から直接お寄せいただけます。

## <a name="next-steps"></a>次のステップ

* [Azure App Configuration について](./overview.md)

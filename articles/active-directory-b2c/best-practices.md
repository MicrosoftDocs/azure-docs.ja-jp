---
title: Azure AD B2C のベスト プラクティス
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C (Azure AD B2C) を使用する際に考慮すべき推奨事項とベスト プラクティス。
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: d8c0a5ce6f3befd41c0e1399363fd73726693837
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96750222"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Azure Active Directory B2C の推奨事項とベスト プラクティス

次のベスト プラクティスと推奨事項では、Azure Active Directory (Azure AD) B2C を既存のまたは新規のアプリケーション環境に統合する際の主要な側面のいくつかについて説明します。

## <a name="fundamentals"></a>基礎

| ベスト プラクティス | 説明 |
|--|--|
| ほとんどのシナリオでユーザー フローを選択する | Azure AD B2C の Identity Experience Framework は、サービスの中核となる強みです。 ポリシーには、サインアップ、サインイン、プロファイル編集などの ID エクスペリエンスが完全に記述されています。 最も一般的な ID タスクを設定しやすくするために、Azure AD B2C ポータルには、ユーザー フローという事前定義済みで構成できるポリシーが用意されています。 ユーザー フローを使用すると、数回クリックするだけで、非常に優れたユーザー エクスペリエンスを数分で作成できます。 [ユーザー フローとカスタム ポリシーを使用するタイミングについて確認してください](custom-policy-overview.md#comparing-user-flows-and-custom-policies)。|
| アプリの登録 | セキュリティで保護するすべてのアプリケーション (Web、ネイティブ) と API を Azure AD B2C に登録する必要があります。 アプリに iOS および Android の Web バージョンとネイティブ バージョンの両方が含まれている場合は、同じクライアント ID を使用して Azure AD B2C に 1 つのアプリケーションとして登録できます。 [OIDC、SAML、Web、ネイティブの各アプリを登録](./tutorial-register-applications.md?tabs=applications)する方法について確認してください。 [Azure AD B2C で使用できるアプリケーションの種類](./application-types.md)の詳細について確認してください。 |
| 月間アクティブ ユーザーの課金に移行する | Azure AD B2C は、月間アクティブ認証から月間アクティブ ユーザー (MAU) の課金に移行されました。 ほとんどの顧客は、これがコスト効率の高いモデルであることに気付くでしょう。 [月間アクティブ ユーザーの課金の詳細について確認してください](https://azure.microsoft.com/updates/mau-billing/)。 |

## <a name="planning-and-design"></a>計画と設計

使用するアプリケーションとサービスのアーキテクチャを定義し、現在のシステムのインベントリを実行し、Azure AD B2C への移行を計画します。

| ベスト プラクティス | 説明 |
|--|--|
| エンドツーエンド ソリューションを設計する | Azure AD B2C 統合を計画するときには、ご利用のアプリケーションのすべての依存関係を含めます。 ご利用の環境内に現在存在している、またはソリューションに追加することが必要と考えられるすべてのサービスおよび製品 (Azure Functions、カスタマー リレーションシップ マネジメント (CRM) システム、Azure API Management ゲートウェイ、ストレージ サービスなど) について検討してください。 すべてのサービスについてセキュリティとスケーラビリティを考慮してください。 |
| ユーザーのエクスペリエンスをドキュメント化する | お客様のアプリケーションでお客様の顧客が体験できるすべてのユーザー体験を詳細に説明します。 お客様のアプリケーションの ID およびプロファイルの側面を顧客が操作する際に表示される可能性のあるすべての画面と分岐フローを記載します。 使いやすさ、アクセシビリティ、およびローカライズを計画に含めます。 |
| 適切な認証プロトコルを選択する |  さまざまなアプリケーション シナリオと、推奨される認証フローの詳細については、「[シナリオとサポートされている認証フロー](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)」を参照してください。 |
| 概念実証 (POC) エンドツーエンド ユーザー エクスペリエンスをパイロット運用する | [Microsoft コード サンプル](code-samples.md)および[コミュニティ サンプル](https://github.com/azure-ad-b2c/samples)から開始します。 |
| 移行計画を作成する |事前に計画を作成することによって、移行を円滑に進めることができます。 [ユーザー移行](user-migration.md)の詳細について確認してください。|
| 使いやすさとセキュリティ | ソリューションでは、アプリケーションの使いやすさと組織におけるリスクの許容レベルとの間で適切なバランスを取る必要があります。 |
| オンプレミスの依存関係をクラウドに移行する | ソリューションの回復性を確保できるようにするために、クラウドへの既存のアプリケーションの依存関係の移行を検討してください。 |
| 既存のアプリを b2clogin.com に移行する | login.microsoftonline.com の廃止は、2020 年 12 月 4 日にすべての Azure AD B2C テナントに対して有効になります。 [詳細については、こちらを参照してください](b2clogin.md)。 |
| Identity Protection と条件付きアクセスを使用する | これらの機能を使用すると、危険な認証やアクセス ポリシーをきわめて厳密に制御できます。 Azure AD B2C Premium P2 が必要です。 [詳細については、こちらを参照してください](conditional-access-identity-protection-overview.md)。 |

## <a name="implementation"></a>実装

実装フェーズでは、次の推奨事項を検討します。

| ベスト プラクティス | 説明 |
|--|--|
| Visual Studio Code 用 Azure AD B2C 拡張機能を使用してカスタム ポリシーを編集する | Visual Studio Code と、このコミュニティによって構築された[拡張機能を Visual Studio Code Marketplace から](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)ダウンロードします。 Microsoft の公式製品ではありませんが、Visual Studio Code 用の Azure AD B2C 拡張機能には、カスタム ポリシーの操作をより簡単にするのに役立つ機能がいくつか含まれています。 |
| Azure AD B2C のトラブルシューティングを行う方法について確認する | 開発時に[カスタム ポリシーのトラブルシューティング](./troubleshoot-custom-policies.md?tabs=applications)を行う方法について確認してください。 通常の認証フローとはどのようになるか確認し、異常やエラーを検出するためのツールを使用します。 たとえば、[Application Insights](troubleshoot-with-application-insights.md) を使用して、ユーザー体験の出力ログを確認します。 |
| 実績のあるカスタム ポリシー パターンからなる Microsoft のライブラリを活用する | 強化された Azure AD B2C カスタマー ID およびアクセス管理 (CIAM) ユーザー体験のいくつかの[サンプル](https://github.com/azure-ad-b2c/samples)を確認してください。 |

## <a name="testing"></a>テスト

使用する Azure AD B2C 実装をテストして自動化します。

| ベスト プラクティス | 説明 |
|--|--|
| グローバル トラフィックを考慮する | 異なるグローバル アドレスからのトラフィック ソースを使用して、パフォーマンスとローカライズの要件をテストします。 すべての HTML、CSS、および依存関係がパフォーマンスのニーズを満たしていることを確認します。 |
| 機能および UI のテスト | ユーザー フローをエンドツーエンドでテストします。 Selenium、VS Web Test などを使用して、数分ごとに合成テストを追加します。 |
| 侵入テスト | ソリューションを公開する前に、侵入テストの演習を実行して、サードパーティの依存関係を含むすべてのコンポーネントが安全であることを確認します。 アクセス トークンを使用して API をセキュリティで保護していること、さらにアプリケーションのシナリオに適した認証プロトコルを使用していることを確認します。 [侵入テスト](../security/fundamentals/pen-testing.md)、および [Microsoft Cloud 統合侵入テストの活動規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)の詳細を確認してください。 |
| A/B テスト | ご利用の母集団全体にロール アウトする前に、少量のランダムなユーザー セットを使用して新機能をフライト化します。 Azure AD B2C で JavaScript を有効にすると、Optimizely や Clarity などの A/B テスト ツールと統合できます。 |
| ロード テスト | Azure AD B2C はスケーリングすることができます。しかし、ご利用のアプリケーションのスケーリングは、その依存関係のすべてをスケーリングできる場合にのみ可能です。 ご利用の API と CDN のロード テストを行います。 |
| Throttling |  短時間に同じソースから送信される要求が多すぎる場合、Azure AD B2C によってトラフィックが調整されます。 ロード テスト中にいくつかのトラフィック ソースを使用し、ご利用のアプリケーション内で `AADB2C90229` エラー コードを適切に処理します。 |
| オートメーション | 継続的インテグレーションと配信 (CI/CD) パイプラインを使用して、テストとデプロイを自動化します ([Azure DevOps](deploy-custom-policies-devops.md) など)。 |

## <a name="operations"></a>操作

ご利用の Azure AD B2C 環境を管理する

| ベスト プラクティス | 説明 |
|--|--|
| 複数の環境を作成する | 操作とデプロイのロールアウトをより簡単に行えるように、開発、テスト、実稼働前、運用向けに個別の環境を作成します。 それぞれのために Azure AD B2C テナントを作成します。 |
| カスタム ポリシーにバージョン管理を使用する | Azure AD B2C カスタム ポリシーに対して、GitHub、Azure Repos、またはその他のクラウドベースのバージョン管理システムの使用を検討してください。 |
| Microsoft Graph API を使用して B2C テナントの管理を自動化する | Microsoft Graph API:<br/>[Identity Experience Framework](/graph/api/resources/trustframeworkpolicy?preserve-view=true&view=graph-rest-beta) (カスタム ポリシー) を管理する<br/>[[キー]](/graph/api/resources/trustframeworkkeyset?preserve-view=true&view=graph-rest-beta)<br/>[ユーザー フロー](/graph/api/resources/identityuserflow?preserve-view=true&view=graph-rest-beta) |
| Azure DevOps との統合 | [CI、CD パイプライン](deploy-custom-policies-devops.md) を使用すると、異なる環境間でのコードの移行が簡単になり、常に運用対応性を確保することができます。   |
| Azure Monitor との統合 | [監査ログ イベント](view-audit-logs.md)は 7 日間のみ保持されます。 [Azure Monitor と統合する](azure-monitor.md)ことにより、そのログを、長期的な使用のために保持したり、サードパーティのセキュリティ情報およびイベント管理 (SIEM) ツールと統合してご利用の環境の分析情報を取得したりすることができます。 |
| アクティブなアラートと監視のセットアップ | Application Insights を使用して Azure AD B2C での[ユーザーの動作を追跡](./analytics-with-application-insights.md)します。 |

## <a name="support-and-status-updates"></a>サポートとステータスの更新

サービスの最新の状態を把握し、サポート オプションを見つけてください。

| ベスト プラクティス | 説明 |
|--|--|
| [サービスの更新情報](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Azure AD B2C 製品の最新の更新プログラムとお知らせを入手します。 |
| [Microsoft サポート](support-options.md) | Azure AD B2C の技術的な問題に対するサポート リクエストを提出します。 課金とサブスクリプション管理のサポートは無料で提供されます。 |
| [Azure の状態](https://status.azure.com/status) | すべての Azure サービスの現在の正常性状態を表示します。 |
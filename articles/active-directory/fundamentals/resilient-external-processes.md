---
title: Azure AD B2C を使用する外部プロセスとのインターフェイスの回復性の向上 | Microsoft Docs
description: 外部プロセスとのインターフェイスの回復性を高める方法
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ef522f5b048db0ef120625d9e894c8e14c070e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724409"
---
# <a name="resilient-interfaces-with-external-processes"></a>外部プロセスとのインターフェイスの回復性の向上

この記事では、ユーザー体験で RESTFul API を計画して実装する方法と、API エラーに対するアプリケーションの回復性を高める方法について説明します。

![外部プロセス コンポーネントとのインターフェイスを示す図](media/resilient-external-processes/external-processes-architecture.png)

## <a name="ensure-correct-placement-of-the-apis"></a>API が正しく配置されていることを確認する

Identity Experience Framework (IEF) ポリシーを使用すると、 [RESTful API 技術プロファイル](../../active-directory-b2c/restful-technical-profile.md)を使用して外部システムを呼び出すことができます。 外部システムは IEF ランタイム環境によって制御されないため、障害点となる可能性があります。

### <a name="how-to-manage-external-systems-using-apis"></a>API を使用して外部システムを管理する方法

- 特定のデータにアクセスするためのインターフェイスを呼び出している間に、データで認証の決定が促進されるかどうかを確認します。 その情報が、アプリケーションのコア機能に不可欠かどうかを評価します。 たとえば、e コマースや、管理などの二次的な機能などです。 情報が認証に必要ではなく、二次的なシナリオにのみ必要な場合は、呼び出しをアプリケーション ロジックに移動することを検討します。

- 認証に必要なデータが比較的静的で小規模であり、ディレクトリから外部化されることに、他のビジネス上の理由がない場合は、ディレクトリに格納することを検討してください。

- 可能な場合は必ず、事前認証済みのパスから API 呼び出しを削除します。 削除できない場合は、API の前で、サービス拒否 (DoS) および分散型サービス拒否 (DDoS) 攻撃からの厳格な保護を配置する必要があります。 攻撃者はサインイン ページを読み込み、API に大量の DoS 攻撃を実行し、アプリケーションを機能停止状態にすることができます。 たとえば、サインインで CAPTCHA を使用すると、サインアップ フローが役に立ちます。

- 可能な場合は必ず、[組み込みのサインアップ ユーザー フローの API コネクタ](../../active-directory-b2c/api-connectors-overview.md)を使用して、ID プロバイダーでサインインした後、またはユーザーを作成する前に Web API と統合します。 ユーザー フローは既に広範囲でテストされているため、ユーザー フロー レベルで機能、パフォーマンス、またはスケールのテストを実行する必要はない可能性があります。 その場合でも、アプリケーションの機能、パフォーマンス、およびスケールをテストする必要があります。

- Azure AD RESTFul API [技術プロファイル](../../active-directory-b2c/restful-technical-profile.md)では、キャッシュ動作は提供されません。 代わりに、RESTFul API プロファイルは、ポリシーに組み込まれている再試行ロジックとタイムアウトを実装します。

- データの書き込みが必要な API の場合は、タスクをキューに入れて、バックグラウンド ワーカーがタスクを実行するようにします。 [Azure キュー](../../storage/queues/storage-queues-introduction.md)などのサービスを使用できます。 これにより、API が効率よく返されるようになり、ポリシー実行パフォーマンスが向上します。  

## <a name="api-error-handling"></a>API エラー処理

API は Azure AD B2C システムの外部に存在するため、技術プロファイル内で適切なエラー処理を行う必要があります。 エンド ユーザーに適切な情報が提供され、アプリケーションがエラーをスムーズに処理できることを確認してください。

### <a name="how-to-gracefully-handle-api-errors"></a>API エラーをスムーズに処理する方法

- API には、さまざまな理由でエラーが発生する可能性があるため、それらのエラーに対するアプリケーションの回復性を高めます。 API が要求の処理を完了できない場合は、[HTTP 4XX エラー メッセージを返します](../../active-directory-b2c/restful-technical-profile.md#returning-validation-error-message)。 Azure AD B2C ポリシーで、API の機能停止をスムーズに処理し、場合によっては、動作を縮小することを試みます。

- [一時的なエラーをスムーズに処理します](../../active-directory-b2c/restful-technical-profile.md#error-handling)。 RESTFul API プロファイルを使用すると、さまざまな [サーキット ブレーカー](/azure/architecture/patterns/circuit-breaker)のエラー メッセージを構成できます。

- 予防的に監視します。また、継続的インテグレーション/継続的デリバリー (CICD) を使用して、[技術プロファイル エンジン](../../active-directory-b2c/restful-technical-profile.md)で使用されるパスワードや証明書などの API アクセス資格情報をローテーションします。

## <a name="api-management---best-practices"></a>API 管理 - ベスト プラクティス

REST API をデプロイし、RESTful 技術プロファイルを構成するときは、推奨されるベスト プラクティスに従うと、一般的な誤りや見落としを防ぐために役立ちます。

### <a name="how-to-manage-apis"></a>API を管理する方法

- API Management (APIM) は、API の公開、管理、および分析を行います。 APIM は、バックエンドのサービスとマイクロサービスへの安全なアクセスを提供するための認証も処理します。 API ゲートウェイを使用して、API のデプロイ、キャッシュ、負荷分散をスケールアウトします。

- 各 API を複数回呼び出すのではなく、ユーザー体験の開始時に適切なトークンを取得し、[Azure APIM API をセキュリティで保護](../../active-directory-b2c/secure-api-management.md?tabs=app-reg-ga)することをお勧めします。

## <a name="next-steps"></a>次のステップ

- [Azure AD B2C 開発者向けの回復性に関するリソース](resilience-b2c.md)
  - [エンドユーザー エクスペリエンスの回復性向上](resilient-end-user-experience.md)
  - [開発者のベスト プラクティスによる回復性向上](resilience-b2c-developer-best-practices.md)
  - [監視と分析による回復性](resilience-with-monitoring-alerting.md)
- [認証インフラストラクチャで回復性を強化する](resilience-in-infrastructure.md)
- [アプリケーションで認証と認可の回復性を向上させる](resilience-app-development-overview.md)
---
title: Azure Front Door - アプリケーション層セキュリティ | Microsoft Docs
description: この記事は、Azure Front Door を使用することで、アプリケーション バックエンドをどのように安全に保護できるかを理解するのに役立ちます
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 4ee50b4c7da27df3630c1b4d263f076da44189bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89399941"
---
# <a name="application-layer-security-with-front-door"></a>Front Door によるアプリケーション層セキュリティ
Azure Front Door では Wet アプリケーションの保護機能が提供されます。この機能を使用することで、SQL インジェクションやクロス サイト スクリプティング (XSS) のようなネットワーク攻撃および一般的な Web の脆弱性の悪用からご利用の Web アプリケーションを保護できます。 http(s) フロント エンドに対して有効にすることで、Front Door のアプリケーション層セキュリティがグローバルに分散され、常時接続となり、バックエンドから遠く離れた場所にある、Azure のネットワーク エッジで悪意のある攻撃を防ぐことができます。 セキュリティの強化とパフォーマンスの最適化により、Front Door ではエンド ユーザーに高速かつ安全な Web エクスペリエンスが提供されます。

## <a name="application-protection"></a>アプリケーション保護
Front Door のアプリケーション保護は、アプリケーションに応じて、世界中の各エッジ環境で構成され、http(s) 以外のトラフィックが Web アプリケーションに到達しないように自動的にブロックします。 マルチテナント分散アーキテクチャでは、パフォーマンスを犠牲にすることなく、大規模なグローバル保護を行うことができます。 http(s) ワークロードでは、Front Door の Web アプリケーション保護サービスにより、カスタム ルール用の豊富なルール エンジン、一般的な攻撃に対する構成済みのルールセット、およびルールに一致するすべての要求の詳細なログが提供されます。 許可、ブロック、ログのみなどのフレキシブルなアクションがサポートされます。

## <a name="custom-access-control-rules"></a>カスタム アクセス制御ルール
- **IP 許可一覧とブロック一覧:** クライアント IP アドレスのリストに基づいて、Web アプリケーションへのアクセスを制御するカスタム ルールを構成できます。 IPv4 と lPv6 の両方がサポートされています
- **地理ベースのアクセス制御:** クライアント IP が含まれている国番号に基づいて、Web アプリケーションへのアクセスを制御するカスタム ルールを構成できます
- **HTTP パラメーターのフィルター処理:** ヘッダー、URL、およびクエリ文字列を含む、一致する http(s) 要求パラメーターに基づいて、カスタム アクセス ルールを構成することができます

## <a name="azure-managed-rules"></a>Azure で管理されるルール
- 一般的な上位の OWASP 脆弱性に対する一連の構成済みルールは、既定で有効になります。 プレビューでは、一連のルールに sqli および xss 要求の確認が含まれます。 ルールはさらに追加されます。 アプリケーションに関する構成済みルールが期待どおりに動作していることを確認するために、ログのみのアクションから始めることができます 

## <a name="rate-limiting"></a>レート制限
- レート制御ルールでは、クライアント IP からの異常に多いトラフィックが制限されます。  1 分間にクライアント IP によって許可される Web 要求の数にしきい値を設定することができます。

## <a name="centralized-protection-policy"></a>一元的な保護ポリシー
- いくつかの保護ルールを定義し、それらを優先順位に従ってポリシーに追加することができます。 カスタム ルールの優先順位は、実行を許可する管理対象のルールセットより高くなります。 Web アプリケーションには単一のポリシーが関連付けられます。  同じ Web アプリケーション保護ポリシーがすべての場所にあるすべてのエッジ サーバーにレプリケートされ、すべてのリージョンで一貫性のあるセキュリティ ポリシーが確保されます

## <a name="configuration"></a>構成
- プレビュー期間中は、REST API、PowerShell、または CLI を使用して、Front Door のアプリケーション保護ルールとポリシーを作成してデプロイすることができます。 ポータル アクセスは、サービスが一般公開される前にサポートされます。 


## <a name="monitoring"></a>監視
Front Door では、アラートを追跡して傾向を簡単に監視するための Azure Monitor と統合されたリアル タイムのメトリックを使用して、攻撃に対する Web アプリケーションを監視する機能が提供されます。

## <a name="pricing"></a>価格
Front Door のアプリケーション層セキュリティは、プレビュー中は無料です。


## <a name="next-steps"></a>次のステップ

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。

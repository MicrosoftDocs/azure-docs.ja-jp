---
title: Azure AD B2C を使用した開発者のベスト プラクティスによる回復性 | Microsoft Docs
description: Azure AD B2C を使用した顧客 ID およびアクセス管理での開発者のベスト プラクティスによる回復性
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
ms.openlocfilehash: ff7505e7c47b93f32efd9de60463873026247329
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724927"
---
# <a name="resilience-through-developer-best-practices"></a>開発者のベスト プラクティスによる回復性

この記事では、Microsoft が大規模なお客様との連携作業から得た経験に基づく知識をいくつか共有します。 サービスの設計と実装において、これらの推奨事項を考慮されることをお勧めします。

![画像は、開発者エクスペリエンスのコンポーネントを示しています](media/resilience-b2c-developer-best-practices/developer-best-practices-architecture.png)

## <a name="use-the-microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL) を使用する

[Microsoft Authentication Library (MSAL)](../develop/msal-overview.md) と [ASP.NET 用 Microsoft identity Web 認証ライブラリ](../develop/reference-v2-libraries.md)によって、アプリケーションに必要なトークンの取得、管理、キャッシュ、および更新が簡素化されます。 これらのライブラリは、アプリケーションの回復性を向上させる機能を含め、特に Microsoft ID をサポートするために最適化されています。

開発者は、MSAL の最新リリースを採用し、最新の状態を維持する必要があります。 アプリケーションで[認証と認可の回復性を向上させる方法](resilience-app-development-overview.md)に関する記事を参照してください。 可能であれば、独自の認証スタックを実装することは避け、適切に確立されたライブラリを使用してください。

## <a name="optimize-directory-reads-and-writes"></a>ディレクトリの読み取りと書き込みを最適化する

Microsoft Azure AD B2C ディレクトリ サービスによって、1 日に何十億もの認証がサポートされています。 これは、1 秒あたりの高い読み取り率に対応するように設計されています。 書き込みを最適化して、依存関係を最小限に抑え、回復性を向上させます。

### <a name="how-to-optimize-directory-reads-and-writes"></a>ディレクトリの読み取りと書き込みを最適化する方法

- **サインイン時のディレクトリへの書き込み関数を避ける**: カスタム ポリシーで前提条件 (if 句) を使用せずにサインイン時に書き込みを実行しないでください。 サインイン時に書き込みを必要とするユース ケースの 1 つとして、[ユーザー パスワードの Just-In-Time 移行](https://github.com/azure-ad-b2c/user-migration/tree/master/seamless-account-migration)があります。 サインインのたびに書き込みを必要とするシナリオは避けてください。

  - ユーザー体験での[前提条件](../../active-directory-b2c/userjourneys.md)は、次のようになります。

  ``
  <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 
  <Value>requiresMigration</Value>
  ...
  < Precondition/>
  ``
  - [CAPTCHA システムと統合して](https://github.com/azure-ad-b2c/samples/tree/master/policies/captcha-integration)、ボットによるサインアップに対する抵抗力を構築します。

  - [ロード テストのサンプル](../../active-directory-b2c/best-practices.md#testing)を使用して、サインアップとサインインをシミュレートします。 

- **調整について理解する**: ディレクトリでは、アプリケーションとテナント両方のレベルの調整ルールが実装されています。 Read/GET、Write/POST、Update/PUT、および Delete/DELETE の操作にはさらにレート制限があり、各操作には異なる制限があります。

  - サインイン時の書き込みは、新規ユーザーの場合は POST に、既存ユーザーの場合は PUT に分類されます。

  - サインインのたびにユーザーが作成または更新されるカスタム ポリシーを使用すると、アプリケーション レベルの PUT または POST のレート制限に達する可能性があります。 Azure AD または Microsoft Graph を使用してディレクトリ オブジェクトを更新する場合にも同じ制限が適用されます。 同様に、読み取りを調べて、毎回のサインインでの読み取り回数を最小限に抑えます。

  - ピーク時の負荷を推定して、ディレクトリの書き込みレートを予測し、調整を回避します。 ピーク時のトラフィックの推定には、サインアップ、サインイン、多要素認証 (MFA) などのアクションの推定値を含める必要があります。 必ず Azure AD B2C システムとお使いのアプリケーションの両方でピーク時のトラフィックに関するテストを行ってください。 ダウンストリーム アプリケーションまたはサービスが対応できない場合は、調整を行わずに Azure AD B2C が負荷を処理できる可能性があります。

  - 移行タイムラインを理解して、計画します。 Microsoft Graph を使用して Azure AD B2C にユーザーを移行する計画を立てる場合は、アプリケーションとテナントの制限を考慮して、ユーザーの移行を完了するために必要な時間を計算してください。 2 つのアプリケーションを使用してユーザー作成ジョブまたはスクリプトを分割する場合は、アプリケーションごとの制限を使用できます。 それでも、テナントごとのしきい値内に収める必要があります。

  - 他のアプリケーションに対する移行ジョブの影響を理解します。 他の依存アプリケーションによって処理されるライブ トラフィックを考慮して、テナント レベルでの調整やライブ アプリケーション用のリソース不足を引き起こさないようにします。 詳しくは、「[Microsoft Graph 調整ガイド](/graph/throttling)」をご覧ください。
  
## <a name="extend-token-lifetimes"></a>トークンの有効期間を延長する

まれに、Azure AD B2C 認証サービスが新しいサインアップとサインインを完了できない場合でも、サインインしているユーザーに対して軽減策を提供できます。 [構成](../../active-directory-b2c/configure-tokens.md)を使用すると、既にサインインしているユーザーがアプリケーションからサインアウトするか、アクティビティがないために[セッション](../../active-directory-b2c/session-behavior.md)がタイムアウトになるまで、中断を認識することなくアプリケーションの使用を継続できるようにすることが可能です。

ビジネス要件と必要なエンドユーザー エクスペリエンスによって、Web およびシングルページ アプリケーション (SPA) のトークン更新の頻度が決まります。

### <a name="how-to-extend-token-lifetimes"></a>トークンの有効期間を延長する方法

- **Web アプリケーション**: サインインの開始時に認証トークンが検証される Web アプリケーションの場合、アプリケーションはセッション Cookie に依存して、セッションの有効期限の延長を継続します。

  - ユーザー アクティビティに基づいてセッションの更新を継続するローリング セッション時間を実装することで、ユーザーがサインインしたままでいられるようにします。 長期間トークンの発行が停止されている場合は、アプリケーションで 1 回限りの構成として、これらのセッション時間をさらに増加させることができます。 セッションの有効期間は、許可されている最大値に維持します。

- **SPA**: SPA は、API を呼び出すためにアクセス トークンに依存する場合があります。 従来、SPA では、更新トークンが生成されない暗黙的なフローが使用されます。 SPA は、ブラウザーにまだ Azure AD B2C とのアクティブ セッションがある場合、非表示の iframe を使用して、承認エンドポイントに対して新しいトークン要求を実行できます。 SPA の場合、ユーザーがアプリケーションを継続して使用できるようにするために使用できるオプションがいくつかあります。

  - アクセス トークンの有効期間をビジネス要件に合わせて延長します。

  - API ゲートウェイを認証プロキシとして使用するようにアプリケーションを構築します。 この構成では、SPA は認証なしで読み込まれ、API ゲートウェイに対して API 呼び出しが行われます。 API ゲートウェイは、ポリシーに基づく[承認コードの付与](https://oauth.net/2/grant-types/authorization-code/)を使用して、サインイン プロセスを経てユーザーを送信し、ユーザーを認証します。 その後、API ゲートウェイとクライアント間の認証セッションは、認証 Cookie を使用して維持されます。 API は、API ゲートウェイによって取得されたトークン、または証明書、クライアント資格情報、API キーなどの他の直接認証方法を使用して、API ゲートウェイから処理されます。

  - Proof Key for Code Exchange (PKCE) とクロスオリジン リソース共有 (CORS) のサポートを使用して、[SPA を暗黙的な許可](https://developer.microsoft.com/identity/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/)から[承認コード付与のフロー](../../active-directory-b2c/implicit-flow-single-page-application.md)に移行します。 アプリケーションを MSAL.js 1.x から MSAL.js 2.x に移行して、Web アプリケーションの回復性を実現します。

  - モバイル アプリケーションの場合は、更新とアクセス トークンの両方の有効期間を延長することをお勧めします。

- **バックエンドまたはマイクロサービス アプリケーション**: バックエンド (デーモン) アプリケーションは非対話型であり、ユーザー コンテキスト内にないため、トークンが盗まれる可能性は大幅に減少します。 セキュリティと有効期間との間のバランスを取り、トークンの有効期間を長く設定することをお勧めします。

## <a name="configure-single-sign-on"></a>シングル サインオンを構成する

[シングル サインオン (SSO)](../manage-apps/what-is-single-sign-on.md)を使用すると、ユーザーは 1 つのアカウントで 1 回サインインすると、複数のアプリケーションにアクセスできます。 アプリケーションは、プラットフォームまたはドメイン名に関係なく、Web、モバイル、シングル ページ アプリケーション (SPA) のいずれも可能です。 ユーザーが初めてアプリケーションにサインインすると、Azure AD B2C によって [Cookie ベースのセッション](../../active-directory-b2c/session-behavior.md)が保持されます。

それ以降の認証要求では、Azure AD B2C によって Cookie ベースのセッションが読み取られて検証され、アクセス トークンが発行されます。ユーザーにサインインを求めるメッセージが再び表示されることはありません。 SSO がポリシーまたはアプリケーションで、制限されたスコープを使用して構成されている場合、後で他のポリシーやアプリケーションにアクセスするには、新しい認証が必要になります。

### <a name="how-to-configure-sso"></a>SSO を構成する方法

テナント内の複数のアプリケーションおよびユーザー フローで同じユーザー セッションを共有できるように、テナント全体 (既定) を対象とするように [SSO を構成](../hybrid/how-to-connect-sso-quick-start.md)します。 テナント全体の構成では、新しい認証に対して最も高い回復性が提供されます。  

## <a name="safe-deployment-practices"></a>安全なデプロイ プラクティス

サービスを中断させる最も一般的な要因は、コードと構成の変更です。 継続的インテグレーションと継続的デリバリー (CI/CD) のプロセスとツールを導入することで、迅速なデプロイを大規模に行うことが可能になり、テストおよび運用環境へのデプロイ時の人的エラーが減少します。 エラーの削減、効率、および整合性のために CI/CD を導入します。 [Azure Pipelines](/azure/devops/pipelines/apps/cd/azure/cicd-data-overview) は、CI/CD の 1 つの例です。

## <a name="web-application-firewall"></a>Web アプリケーション ファイアウォール

分散型サービス拒否 (DDoS) 攻撃、SQL インジェクション、クロスサイト スクリプティング、リモート コード実行、および [OWASP トップ 10](https://owasp.org/www-project-top-ten/) に記載されている他の多くのものなどの既知の脆弱性からアプリケーションを保護します。 Web アプリケーション ファイアウォール (WAF) をデプロイすることで、一般的な悪用と脆弱性から防御できます。

- Azure [WAF](../../web-application-firewall/overview.md) を使用します。これにより、攻撃に対する一元的な保護が提供されます。

- Azure AD B2C を使用している場合は、WAF を Azure AD [Identity Protection および条件付きアクセスと共に使用して、多層保護を提供](../../active-directory-b2c/conditional-access-identity-protection-overview.md)します。  

## <a name="secrets-rotation"></a>シークレットのローテーション

Azure AD B2C では、アプリケーション、API、ポリシー、および暗号化にシークレットが使用されます。 シークレットにより、認証、外部の相互作用、およびストレージがセキュリティで保護されます。 米国国立標準技術研究所 (NIST) は、正当なエンティティによる特定のキーの使用が許可される期間を暗号化期間と呼んでいます。 ビジネスのニーズに合った[暗号化期間](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final)の長さを選択してください。 開発者は、有効期限を手動で設定し、有効期限の前に余裕をもってシークレットをローテーションする必要があります。

### <a name="how-to-implement-secret-rotation"></a>シークレットのローテーションを実装する方法

- サポートされているリソースに対して[マネージド ID](../managed-identities-azure-resources/overview.md) を使用して、Azure AD 認証がサポートされているサービスに対して認証を行います。 マネージド ID を使用すると、資格情報のローテーションを含め、リソースの管理を自動的に行うことができます。

- Azure AD B2C に構成されているすべての[キーと証明書](../../active-directory-b2c/policy-keys-overview.md)のインベントリを取得します。 この一覧には、カスタム ポリシー、[API](../../active-directory-b2c/secure-rest-api.md)、署名 ID トークン、および SAML の証明書で使用されるキーが含まれている可能性が高いです。

- CI/CD を使用して、予想されるピーク シーズンから 2 か月以内に期限が切れることになっているシークレットをローテーションします。 証明書に関連付けられている秘密キーの推奨される最大暗号化期間は 1 年です。

- パスワードや証明書などの API アクセス資格情報を事前に監視してローテーションします。

## <a name="test-rest-apis"></a>REST API をテストする

回復性の観点から、REST API のテストには、HTTP コード、応答ペイロード、ヘッダー、およびパフォーマンスの検証を含める必要があります。 テストには、ハッピー パス テストだけでなく、API によって問題のシナリオが適切に処理されるかどうかのチェックも含めてください。

### <a name="how-to-test-apis"></a>API をテストする方法

テスト計画には、[包括的な API テスト](../../active-directory-b2c/best-practices.md#testing)を含めることをお勧めします。 プロモーションや休日のトラフィックによって今後見込まれる急上昇に対応した計画を立てる場合は、新しい推定値を使用してロード テストを修正する必要があります。 API とコンテンツ配信ネットワーク (CDN) のロード テストは、運用ではなく、開発者環境で実施してください。

## <a name="next-steps"></a>次のステップ

- [Azure AD B2C 開発者向けの回復性に関するリソース](resilience-b2c.md)
  - [回復性があるエンドユーザー エクスペリエンス](resilient-end-user-experience.md)
  - [回復性がある外部プロセスとのインターフェイス](resilient-external-processes.md)
  - [監視と分析による回復性](resilience-with-monitoring-alerting.md)
- [認証インフラストラクチャで回復性を強化する](resilience-in-infrastructure.md)
- [アプリケーションで認証と認可の回復性を向上させる](resilience-app-development-overview.md)
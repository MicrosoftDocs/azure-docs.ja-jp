---
title: 認証 - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Threat Modeling Tool での認証の対応策について説明します。 軽減策の情報を参照し、コード例を確認します。
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: has-adal-ref, devx-track-js, devx-track-csharp
ms.openlocfilehash: ee4dd70faab9ed44b1aa6ca8ca0ec517c7746f66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94832532"
---
# <a name="security-frame-authentication--mitigations"></a>セキュリティ フレーム:認証 | 対応策

| 製品/サービス | [アーティクル] |
| --------------- | ------- |
| **Web アプリケーション**    | <ul><li>[標準の認証メカニズムを使用して Web アプリケーションに対する認証を行うことを検討する](#standard-authn-web-app)</li><li>[認証失敗のシナリオをアプリケーションが安全に処理する](#handle-failed-authn)</li><li>[ステップアップまたはアダプティブ認証を有効にする](#step-up-adaptive-authn)</li><li>[管理インターフェイスのロックが適切にロックダウンされていることを確認する](#admin-interface-lockdown)</li><li>[パスワードの再設定機能を安全に実装する](#forgot-pword-fxn)</li><li>[パスワードとアカウント ポリシーが実装されていることを確認する](#pword-account-policy)</li><li>[ユーザー名が列挙されないコントロールを実装する](#controls-username-enum)</li></ul> |
| **[データベース]** | <ul><li>[可能であれば Windows 認証を使用して SQL Server に接続する](#win-authn-sql)</li><li>[可能であれば Azure Active Directory 認証を使用して SQL Database に接続する](#aad-authn-sql)</li><li>[SQL 認証モードを使用する場合は、SQL サーバーでアカウントとパスワード ポリシーが適用されていることを確認する](#authn-account-pword)</li><li>[包含データベースでは SQL 認証を使用しない](#autn-contained-db)</li></ul> |
| **Azure Event Hub** | <ul><li>[SaS トークンを使用したデバイスごとの認証資格情報を使用する](#authn-sas-tokens)</li></ul> |
| **Azure の信頼の境界** | <ul><li>[Azure 管理者用の Azure AD Multi-Factor Authentication を有効にする](#multi-factor-azure-admin)</li></ul> |
| **Service Fabric の信頼の境界** | <ul><li>[Service Fabric クラスターへの匿名アクセスを制限する](#anon-access-cluster)</li><li>[Service Fabric のクライアントとノード間の証明書が、ノード間の証明書と異なることを確認する](#fabric-cn-nn)</li><li>[AAD を使用して Service Fabric クラスターに対してクライアントを認証する](#aad-client-fabric)</li><li>[承認された証明機関 (CA) から取得された Service fabric 証明書であることを確認する](#fabric-cert-ca)</li></ul> |
| **Identity Server** | <ul><li>[Identity Server でサポートされる標準的な認証シナリオを使用する](#standard-authn-id)</li><li>[既定の Identity Server トークン キャッシュをスケーラブルな代替手段でオーバーライドする](#override-token)</li></ul> |
| **コンピューターの信頼の境界** | <ul><li>[デプロイされたアプリケーションのバイナリがデジタル署名されていることを確認する](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[WCF で MSMQ キューに接続するときに認証を有効にする](#msmq-queues)</li><li>[WCF - メッセージ clientCredentialType を none に設定しない](#message-none)</li><li>[WCF - トランスポート clientCredentialType を none に設定しない](#transport-none)</li></ul> |
| **Web API** | <ul><li>[標準的な認証手法によって Web API がセキュリティで保護されていることを確認する](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Azure Active Directory でサポートされる標準的な認証シナリオを使用する](#authn-aad)</li><li>[既定の ADAL トークン キャッシュをスケーラブルな代替手段でオーバーライドする](#adal-scalable)</li><li>[ADAL 認証トークンのリプレイを防ぐために TokenReplayCache が使用されていることを確認する](#tokenreplaycache-adal)</li><li>[ADAL ライブラリを使用して、OAuth2 クライアントから AAD (またはオンプレミス AD) へのトークン要求を管理する](#adal-oauth2)</li></ul> |
| **IoT フィールド ゲートウェイ** | <ul><li>[フィールド ゲートウェイに接続しているデバイスを認証する](#authn-devices-field)</li></ul> |
| **IoT クラウド ゲートウェイ** | <ul><li>[クラウド ゲートウェイに接続しているデバイスが認証されていることを確認する](#authn-devices-cloud)</li><li>[デバイスごとの認証資格情報を使用する](#authn-cred)</li></ul> |
| **Azure ストレージ** | <ul><li>[必要なコンテナーと BLOB のみに匿名読み取りアクセスが付与されていることを確認する](#req-containers-anon)</li><li>[SAS または SAP を使用して Azure Storage のオブジェクトへの制限付きアクセスを許可する](#limited-access-sas)</li></ul> |

## <a name="consider-using-a-standard-authentication-mechanism-to-authenticate-to-web-application"></a><a id="standard-authn-web-app"></a>標準の認証メカニズムを使用して Web アプリケーションに対する認証を行うことを検討する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| 詳細 | <p>認証は、エンティティがその ID を証明するプロセスで、通常はユーザー名、パスワードなどの資格情報を使用します。 使用を検討できる認証プロトコルは複数あります。 その一部を次に示します。</p><ul><li>[クライアント証明書]</li><li>Windows ベース</li><li>フォーム ベース</li><li>フェデレーション - ADFS</li><li>フェデレーション - Azure AD</li><li>フェデレーション - Identity Server</li></ul><p>標準の認証メカニズムを使用してソース プロセスを特定することを検討します</p>|

## <a name="applications-must-handle-failed-authentication-scenarios-securely"></a><a id="handle-failed-authn"></a>認証失敗のシナリオをアプリケーションが安全に処理する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| 詳細 | <p>ユーザーを明示的に認証するアプリケーションでは、認証失敗シナリオを安全に処理する必要があります。認証メカニズムでは以下の処理が必要です。</p><ul><li>認証が失敗したときに特権リソースへのアクセスを拒否する</li><li>認証が失敗し、アクセス拒否が発生した後に、一般的なエラー メッセージを表示する</li></ul><p>以下をテストします。</p><ul><li>ログイン失敗後に特権リソースが保護されていること</li><li>認証失敗およびアクセス拒否イベントで一般的なエラー メッセージが表示されること</li><li>失敗した試行数が多くなりすぎたときにアカウントが無効になること</li><ul>|

## <a name="enable-step-up-or-adaptive-authentication"></a><a id="step-up-adaptive-authn"></a>ステップアップまたはアダプティブ認証を有効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| 詳細 | <p>機密情報にアクセスしようとしているユーザーが、簡単にアクセスできないように、アプリケーションで追加承認 (SMS や電子メールでの OTP 送信、再認証を求めるメッセージといった多要素認証を介したステップアップ認証、アダプティブ認証など) が実施されていることを確認します。 このルールは、アカウントまたはアクションに対して重大な変更を加えるときにも適用されます</p><p>つまり、状況依存の承認がアプリケーションによって正しく実施され、パラメーターの改ざんなどによる未承認の操作が許可されないように、認証を実装する必要があります</p>|

## <a name="ensure-that-administrative-interfaces-are-appropriately-locked-down"></a><a id="admin-interface-lockdown"></a>管理インターフェイスのロックが適切にロックダウンされていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| 詳細 | 最初は特定のソース IP 範囲から管理インターフェイスへのアクセスのみを許可します。 このソリューションで対処できない場合、管理インターフェイスにログインするときは必ずステップアップまたはアダプティブ認証を実施することをお勧めします |

## <a name="implement-forgot-password-functionalities-securely"></a><a id="forgot-pword-fxn"></a>パスワードの再設定機能を安全に実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| 詳細 | <p>まず、パスワード再設定およびその他の復旧パスが送信するリンクに、パスワード自体ではなく、期限付きのライセンス認証トークンが含まれることを確認します。 リンクの送信前に、ソフト トークン (SMS トークン、ネイティブのモバイル アプリケーションなど) に基づく追加認証を要求することもできます。 また、新しいパスワードの取得プロセスの進行中は、ユーザー アカウントをロックアウトしないでください。</p><p>攻撃者が自動攻撃によってユーザーを意図的にロックアウトしようとした場合、これがサービス拒否攻撃につながる可能性があります。 3 番目に気を付けるのは、新しいパスワードの設定が進行中のときに表示するメッセージは、ユーザー名が列挙されないように一般化する、という点です。 4 番目として、以前のパスワードは使用できないようにします。また、強力なパスワード ポリシーを実装してください。</p> |

## <a name="ensure-that-password-and-account-policy-are-implemented"></a><a id="pword-account-policy"></a>パスワードとアカウント ポリシーが実装されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| 詳細 | <p>組織のポリシーとベスト プラクティスに準拠しているパスワードおよびアカウント ポリシーを実装する必要があります。</p><p>ブルート フォース攻撃や辞書ベース推測を防ぐには、ユーザーが必ず複雑なパスワード (例: 12 文字以上、英数字と特殊文字) を設定するように、強力なパスワード ポリシーを実装します。</p><p>アカウント ロックアウト ポリシーは次のように実装されます。</p><ul><li>**ソフト ロックアウト:** ブルート フォース攻撃からのユーザー保護に適したオプションです。 たとえば、ユーザーが間違ったパスワードを 3 回入力するたびに、アプリケーションによりアカウントが 1 分間ロックダウンされます。これにより、パスワードのブルート フォース攻撃速度が低下し、攻撃を続行するメリットが少なくなります。 これに対してハード ロックアウト対策を実装しようとすると、アカウントを完全にロックして "DoS" を行うことになります。 アプリケーションが OTP (One Time Password) を生成し、それをアウトオブバンド (電子メール、SMS などで) でユーザーに送信することもできます。 また、失敗回数がしきい値を超えた時点で CAPTCHA を実装する方法もあります。</li><li>**ハード ロックアウト:** アプリケーションを攻撃しているユーザーを検出し、そのアカウントを、対応チームがフォレンジクス調査を行う時間を確保できるまで完全にロックアウトすることで対抗する場合は、必ずこの種類のロックアウトを適用します。 このプロセスの後に、ユーザーのアカウントを元に戻すか、そのユーザーに対して法的手段を取るかを決めることができます。 この方法では、攻撃者が、それ以上アプリケーションとインフラストラクチャに侵入できません。</li></ul><p>既定および予測可能なアカウントへの攻撃を防ぐには、すべてのキーやパスワードが変更可能で、インストール後に生成または変更されたことを確認します。</p><p>アプリケーションがパスワードを自動生成する必要がある場合は、自動生成されたパスワードがランダムで、エントロピーが高いことを確認します。</p>|

## <a name="implement-controls-to-prevent-username-enumeration"></a><a id="controls-username-enum"></a>ユーザー名が列挙されないコントロールを実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | ユーザー名が列挙されないようにすべてのエラー メッセージを一般化します。 また、登録ページなどの機能では情報漏えいが避けられない場合もあります。 このような場合は CAPTCHA などのレート制限方法を使用して、攻撃者による自動攻撃を防ぐ必要があります。 |

## <a name="when-possible-use-windows-authentication-for-connecting-to-sql-server"></a><a id="win-authn-sql"></a>可能であれば Windows 認証を使用して SQL Server に接続する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | OnPrem |
| **属性**              | SQL バージョン - すべて |
| **参照**              | [SQL Server - 認証モードの選択](/sql/relational-databases/security/choose-an-authentication-mode) |
| **手順** | Windows 認証では、Kerberos セキュリティ プロトコルを利用し、強力なパスワードに対して複雑な検証を行うという点に関して、パスワード ポリシーが強化されています。また、アカウント ロックアウトの機能を提供し、パスワード有効期限にも対応しています。|

## <a name="when-possible-use-azure-active-directory-authentication-for-connecting-to-sql-database"></a><a id="aad-authn-sql"></a>可能であれば Azure Active Directory 認証を使用して SQL Database に接続する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | SQL Azure |
| **属性**              | SQL バージョン - V12 |
| **参照**              | [Azure Active Directory の認証を使用して、SQL データベースに接続します。](../../azure-sql/database/authentication-aad-overview.md) |
| **手順** | **最小バージョン:** Azure SQL Database で Microsoft Directory に対する AAD 認証を使用するには、Azure SQL Database V12 が必要です |

## <a name="when-sql-authentication-mode-is-used-ensure-that-account-and-password-policy-are-enforced-on-sql-server"></a><a id="authn-account-pword"></a>SQL 認証モードを使用する場合は、SQL サーバーでアカウントとパスワード ポリシーが適用されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [SQL Server のパスワード ポリシー](/previous-versions/sql/sql-server-2012/ms161959(v=sql.110)) |
| **手順** | SQL Server 認証を使用する場合は、Windows ユーザー アカウントに基づいていない SQL Server でログインが作成されます。 ユーザー名とパスワードの両方が SQL Server を使用して作成され、SQL Server に格納されます。 SQL Server は Windows のパスワード ポリシー メカニズムに対応しています。 また、SQL Server 内部で使用されるパスワードに、Windows で使用されているものと同じ複雑性ポリシーおよび有効期限ポリシーを適用できます。 |

## <a name="do-not-use-sql-authentication-in-contained-databases"></a><a id="autn-contained-db"></a>包含データベースでは SQL 認証を使用しない

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | OnPrem、SQL Azure |
| **属性**              | SQL バージョン - MSSQL2012、SQL バージョン - V12 |
| **参照**              | [包含データベースでのセキュリティのベスト プラクティス](/sql/relational-databases/databases/security-best-practices-with-contained-databases) |
| **手順** | パスワード ポリシーが適用されていないと、包含データベースで脆弱な資格情報が作成される可能性が高くなる場合があります。 Windows 認証を使用してください。 |

## <a name="use-per-device-authentication-credentials-using-sas-tokens"></a><a id="authn-sas-tokens"></a>SaS トークンを使用したデバイスごとの認証資格情報を使用する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Event Hub |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Event Hubs の認証とセキュリティ モデルの概要](../../event-hubs/authenticate-shared-access-signature.md) |
| **手順** | <p>Event Hubs のセキュリティ モデルは、Shared Access Signature (SAS) トークンとイベント パブリッシャーの組み合わせに基づいています。 パブリッシャー名は、トークンを受け取る DeviceID を表します。 これは、生成されたトークンと各デバイスを関連付けるうえで役に立ちます。</p><p>すべてのメッセージが、サービス側の発信元でタグ付けされており、ペイロード内配信元なりすまし試行を検出できます。 デバイスを認証するときに、一意のパブリッシャーを対象とした SAS トークンをデバイスごとに生成します。</p>|

## <a name="enable-azure-ad-multi-factor-authentication-for-azure-administrators"></a><a id="multi-factor-azure-admin"></a>Azure 管理者用の Azure AD Multi-Factor Authentication を有効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure の信頼の境界 |
| **SDL フェーズ**               | デプロイ |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Azure AD Multi-Factor Authentication とは](../../active-directory/authentication/concept-mfa-howitworks.md) |
| **手順** | <p>Multi-Factor Authentication (MFA) は、複数の確認方法を要求することで、ユーザーのサインインとトランザクションにさらなる重要なセキュリティ レイヤーを追加する認証方法です。 これらは、次の確認方法のうち 2 つ以上を要求することで機能します。</p><ul><li>ユーザーが知っている情報 (一般にパスワード)</li><li>ユーザーの所持品 (複製が困難な、携帯電話などの信頼されているデバイス)</li><li>ユーザー自身 (生体認証)</li><ul>|

## <a name="restrict-anonymous-access-to-service-fabric-cluster"></a><a id="anon-access-cluster"></a>Service Fabric クラスターへの匿名アクセスを制限する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Service Fabric の信頼の境界 |
| **SDL フェーズ**               | デプロイ |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 環境 - Azure  |
| **参照**              | [Service Fabric クラスターのセキュリティに関するシナリオ](../../service-fabric/service-fabric-cluster-security.md) |
| **手順** | <p>クラスターは、特に運用ワークロードが実行されている場合などに、許可なくユーザーがクラスターに接続するのを防ぐために常にセキュリティで保護する必要があります。</p><p>Service Fabric クラスターを作成することはできますが、セキュリティ モードは "セキュリティ保護" に設定し、必要な X.509 証明書を構成する必要があります。 "セキュリティ保護されていない" クラスターを作成すると、パブリック インターネットへの管理エンドポイントを公開している場合、すべての匿名ユーザーがそのクラスターに接続できるようになります。</p>|

## <a name="ensure-that-service-fabric-client-to-node-certificate-is-different-from-node-to-node-certificate"></a><a id="fabric-cn-nn"></a>Service Fabric のクライアントとノード間の証明書が、ノード間の証明書と異なることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Service Fabric の信頼の境界 |
| **SDL フェーズ**               | デプロイ |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 環境 - Azure、環境、 - スタンドアロン |
| **参照**              | [Service Fabric クライアントとノードの間の証明書セキュリティ](../../service-fabric/service-fabric-cluster-security.md#client-to-node-certificate-security)、[クライアント証明書を使用したセキュリティ保護されたクラスターへの接続](../../service-fabric/service-fabric-connect-to-secure-cluster.md) |
| **手順** | <p>クライアントとノードの間の証明書セキュリティを構成するには、Azure Portal、Resource Manager テンプレート、またはスタンドアロン JSON テンプレートでクラスターを作成するときに、管理用クライアント証明書やユーザー クライアント証明書を指定します。</p><p>指定する管理用クライアント証明書とユーザー クライアント証明書は、ノード間のセキュリティに指定するプライマリ証明書とセカンダリ証明書とは異なります。</p>|

## <a name="use-aad-to-authenticate-clients-to-service-fabric-clusters"></a><a id="aad-client-fabric"></a>AAD を使用して Service Fabric クラスターに対してクライアントを認証する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Service Fabric の信頼の境界 |
| **SDL フェーズ**               | デプロイ |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 環境 - Azure |
| **参照**              | [クラスターのセキュリティ シナリオ - セキュリティに関する推奨事項](../../service-fabric/service-fabric-cluster-security.md#security-recommendations) |
| **手順** | Azure で実行されているクラスターは、クライアント証明書とは別に、Azure Active Directory (AAD) を使用して、管理エンドポイントへのアクセスをセキュリティで保護することもできます。 Azure クラスターについては、クライアントの認証に AAD セキュリティ、ノード間のセキュリティに証明書を使用することをお勧めします。|

## <a name="ensure-that-service-fabric-certificates-are-obtained-from-an-approved-certificate-authority-ca"></a><a id="fabric-cert-ca"></a>承認された証明機関 (CA) から取得された Service Fabric 証明書であることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Service Fabric の信頼の境界 |
| **SDL フェーズ**               | デプロイ |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 環境 - Azure |
| **参照**              | [X.509 証明書と Service Fabric](../../service-fabric/service-fabric-cluster-security.md#x509-certificates-and-service-fabric) |
| **手順** | <p>Service Fabric では、ノードとクライアントの認証に X.509 サーバー証明書が使用されます。</p><p>Service Fabric で証明書を使用するうえでの重要な考慮事項をいくつか次に示します。</p><ul><li>運用環境のワークロードを実行しているクラスターに使用する証明書は、正しく構成された Windows Server 証明書サービスを使用して作成するか、認定済みの 証明機関 (CA) から取得する必要があります。 CA として利用できるのは、承認済みの外部 CA、または適切に管理された内部公開キー基盤 (PKI) です</li><li>運用環境では、MakeCert.exe などのツールで作成した一時証明書またはテスト証明書を使用しないでください</li><li>自己署名入りの証明書は使用できますが、運用環境のクラスターではなく、テスト環境のクラスターにのみ使用してください</li></ul>|

## <a name="use-standard-authentication-scenarios-supported-by-identity-server"></a><a id="standard-authn-id"></a>Identity Server でサポートされる標準的な認証シナリオを使用する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Identity Server |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [IdentityServer3 - 概要](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **手順** | <p>Identity Server でサポートされている標準的なやり取りを次に示します。</p><ul><li>ブラウザーが Web アプリケーションと通信する</li><li>Web アプリケーションが (自身で、またはユーザーの代わりに) Web API と通信する</li><li>ブラウザー ベースのアプリケーションが Web API と通信する</li><li>ネイティブ アプリケーションが Web API と通信する</li><li>サーバー ベースのアプリケーションが Web API と通信する</li><li>Web API が (自身で、またはユーザーの代わりに) Web API と通信する</li></ul>|

## <a name="override-the-default-identity-server-token-cache-with-a-scalable-alternative"></a><a id="override-token"></a>既定の Identity Server トークン キャッシュをスケーラブルな代替手段でオーバーライドする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Identity Server |
| **SDL フェーズ**               | デプロイ |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Identity Server のデプロイ - キャッシュ](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **手順** | <p>Identity Server にはシンプルなメモリ内キャッシュが組み込まれています。 これは小規模なネイティブ アプリには適していますが、次の理由により、中間層およびバックエンド アプリケーション用に拡張されません。</p><ul><li>こうしたアプリケーションには、一度に多数のユーザーがアクセスします。 大きな規模で動作しているときに、すべてのアクセス トークンを同じストアに保存すると分離の問題が発生し、課題が生じます。ユーザーが多数存在するほか、それぞれのユーザーに、そのユーザーのアプリがアクセスするリソースと同数のトークンがあります。つまり、その数は膨大で、参照操作にコストがかかるということです</li><li>こうしたアプリケーションは、通常、分散トポロジにデプロイされます。このトポロジでは、複数のノードが同じキャッシュにアクセスしなければなりません</li><li>プロセスがリサイクルおよび非アクティブ化されても、キャッシュされたトークンを保持する必要があります</li><li>上記のすべての理由により、Web アプリの実装中、既定の Identity Server のトークン キャッシュを、Azure Cache for Redis などのスケーラブルな代替手段でオーバーライドすることをお勧めします</li></ul>|

## <a name="ensure-that-deployed-applications-binaries-are-digitally-signed"></a><a id="binaries-signed"></a>デプロイされたアプリケーションのバイナリがデジタル署名されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | コンピューターの信頼の境界 |
| **SDL フェーズ**               | デプロイ |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | バイナリの整合性を検証できるように、デプロイされたアプリケーションのバイナリがデジタル署名されていることを確認します|

## <a name="enable-authentication-when-connecting-to-msmq-queues-in-wcf"></a><a id="msmq-queues"></a>WCF で MSMQ キューに接続するときに認証を有効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック、NET Framework 3 |
| **属性**              | 該当なし |
| **参照**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)) |
| **手順** | MSMQ キューに接続するときに認証を有効にできないと、攻撃者が、処理を実行するためのメッセージを匿名でキューに送信できます。 別のプログラムにメッセージを配信するときに使用する MSMQ キューに、認証を使用しないで接続すると、攻撃者が、悪意のある匿名メッセージを送信する可能性があります。|

### <a name="example"></a>例
以下の WCF 構成ファイルの `<netMsmqBinding/>` 要素は、メッセージ配信のために MSMQ キューに接続するときに認証を無効にします。
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
すべての受信または送信メッセージに対して、常に Windows ドメインまたは証明書認証を要求するように MSMQ を構成します。

### <a name="example"></a>例
以下の WCF 構成ファイルの `<netMsmqBinding/>` 要素は、MSMQ キューに接続するときに証明書認証を有効にします。 クライアントは、X.509 証明書を使用して認証されます。 クライアント証明書が、サーバーの証明書ストアに存在する必要があります。
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a name="wcf-do-not-set-message-clientcredentialtype-to-none"></a><a id="message-none"></a>WCF - メッセージ clientCredentialType を none に設定しない

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | .NET Framework 3 |
| **属性**              | クライアント資格情報の種類 - なし |
| **参照**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10))、[Fortify](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **手順** | 認証が存在しないということは、すべてのユーザーがこのサービスにアクセスできるということです。 クライアントを認証しないサービスでは、すべてのユーザーへのアクセスが許可されます。 アプリケーションは、クライアントの資格情報に対して認証を行うように構成してください。 これを行うには、メッセージ clientCredentialType を Windows または証明書に設定します。 |

### <a name="example"></a>例
```
<message clientCredentialType=""Certificate""/>
```

## <a name="wcf-do-not-set-transport-clientcredentialtype-to-none"></a><a id="transport-none"></a>WCF - トランスポート clientCredentialType を none に設定しない

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック、NET Framework 3 |
| **属性**              | クライアント資格情報の種類 - なし |
| **参照**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10))、[Fortify](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **手順** | 認証が存在しないということは、すべてのユーザーがこのサービスにアクセスできるということです。 クライアントを認証しないサービスでは、すべてのユーザーがその機能にアクセスできます。 アプリケーションは、クライアントの資格情報に対して認証を行うように構成してください。 これを行うには、トランスポート clientCredentialType を Windows または証明書に設定します。 |

### <a name="example"></a>例
```
<transport clientCredentialType=""Certificate""/>
```

## <a name="ensure-that-standard-authentication-techniques-are-used-to-secure-web-apis"></a><a id="authn-secure-api"></a>標準的な認証手法によって Web API がセキュリティで保護されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web API |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [ASP.NET Web API での認証と権限承認](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api)、[ASP.NET Web API (C#) の外部認証サービス](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **手順** | <p>認証は、エンティティがその ID を証明するプロセスで、通常はユーザー名、パスワードなどの資格情報を使用します。 使用を検討できる認証プロトコルは複数あります。 その一部を次に示します。</p><ul><li>[クライアント証明書]</li><li>Windows ベース</li><li>フォーム ベース</li><li>フェデレーション - ADFS</li><li>フェデレーション - Azure AD</li><li>フェデレーション - Identity Server</li></ul><p>「参照」セクションのリンクは、認証スキームを実装して Web API をセキュリティで保護する方法について、細かなレベルの詳細情報をスキームごとに提供します。</p>|

## <a name="use-standard-authentication-scenarios-supported-by-azure-active-directory"></a><a id="authn-aad"></a>Azure Active Directory でサポートされる標準的な認証シナリオを使用する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure AD |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Azure AD の認証シナリオ](../../active-directory/develop/authentication-vs-authorization.md)、[Azure Active Directory のコード例](../../active-directory/azuread-dev/sample-v1-code.md)、[Azure Active Directory 開発者ガイド](../../active-directory/develop/index.yml) |
| **手順** | <p>Azure Active Directory (Azure AD) は、OAuth 2.0 や OpenID Connect などの業界標準プロトコルをサポートする Identity as a Service を提供することで、開発者のために認証を簡素化します。 Azure AD でサポートされる 5 つの主要なアプリケーション シナリオは、次のとおりです。</p><ul><li>Web ブラウザー対 Web アプリケーション: ユーザーは、Azure AD によって保護された Web アプリケーションにサインインする必要があります</li><li>シングル ページ アプリケーション (SPA): ユーザーは、Azure AD によって保護されたシングル ページ アプリケーションにサインインする必要があります</li><li>ネイティブ アプリケーション対 Web API: スマートフォン、タブレット、または PC で実行されるネイティブ アプリケーションは、Azure AD によって保護された Web API からリソースを取得するために、ユーザーを認証する必要があります</li><li>Web アプリケーション対 Web API: Web アプリケーションは、Azure AD によって保護された Web API からリソースを取得する必要があります</li><li>デーモンまたはサーバー アプリケーション対 Web API: Web ユーザー インターフェイスを備えていないデーモン アプリケーションまたはサーバー アプリケーションは、Azure AD によって保護された Web API からリソースを取得する必要があります</li></ul><p>細かなレベルの実装の詳細については、「参照」セクションのリンクを参照してください</p>|

## <a name="override-the-default-adal-token-cache-with-a-scalable-alternative"></a><a id="adal-scalable"></a>既定の ADAL トークン キャッシュをスケーラブルな代替手段でオーバーライドする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure AD |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Web アプリケーション用 Azure Active Directory による最新の認証](/archive/blogs/microsoft_press/new-book-modern-authentication-with-azure-active-directory-for-web-applications)、[ADAL トークン キャッシュとしての Redis の使用](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **手順** | <p>ADAL (Active Directory 認証ライブラリ) が使用する既定のキャッシュは、プロセス全体で使用できる静的ストアに依存するメモリ内キャッシュです。 ネイティブ アプリケーションには有用ですが、次の理由により、中間層およびバックエンド アプリケーション用に拡張されません。</p><ul><li>こうしたアプリケーションには、一度に多数のユーザーがアクセスします。 大きな規模で動作しているときに、すべてのアクセス トークンを同じストアに保存すると分離の問題が発生し、課題が生じます。ユーザーが多数存在するほか、それぞれのユーザーに、そのユーザーのアプリがアクセスするリソースと同数のトークンがあります。つまり、その数は膨大で、参照操作にコストがかかるということです</li><li>こうしたアプリケーションは、通常、分散トポロジにデプロイされます。このトポロジでは、複数のノードが同じキャッシュにアクセスしなければなりません</li><li>プロセスがリサイクルおよび非アクティブ化されても、キャッシュされたトークンを保持する必要があります</li></ul><p>上記のすべての理由により、Web アプリの実装中、既定の ADAL のトークン キャッシュを、Azure Cache for Redis などのスケーラブルな代替手段でオーバーライドすることをお勧めします。</p>|

## <a name="ensure-that-tokenreplaycache-is-used-to-prevent-the-replay-of-adal-authentication-tokens"></a><a id="tokenreplaycache-adal"></a>ADAL 認証トークンのリプレイを防ぐために TokenReplayCache が使用されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure AD |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Web アプリケーション用 Azure Active Directory による最新の認証](/archive/blogs/microsoft_press/new-book-modern-authentication-with-azure-active-directory-for-web-applications) |
| **手順** | <p>TokenReplayCache プロパティを使用すると、開発者がトークン リプレイ キャッシュ (複数回使用できるトークンが存在しないことを確認する目的でのトークン保存に使用できるストア) を定義できます。</p><p>これは、一般的な攻撃、つまり、サインイン時に送信されたトークンを攻撃者が傍受し、そのトークンをアプリに再送信 ("リプレイ") して新しいセッションを確立しようとする、トークン リプレイ攻撃への対抗手段です。 たとえば、OIDC コード付与フローでは、ユーザー認証に成功した後、証明書利用者の "/signin oidc" エンドポイントへの要求が、"id_token"、"code"、および "state" パラメーターを使用して行われます。</p><p>証明書利用者はこの要求を検証し、新しいセッションを確立します。 敵がこの要求をキャプチャし、リプレイすると、セッションを確立し、ユーザーになりすますことができます。 OpenID Connect に nonce が存在すると、攻撃された環境を制限できますが、完全には排除できません。 アプリケーションを保護するために、開発者は ITokenReplayCache を実装し、インスタンスを TokenReplayCache に割り当てることができます。</p>|

### <a name="example"></a>例
```csharp
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>例
ITokenReplayCache インターフェイスの実装例を次に示します (カスタマイズして、プロジェクト固有のキャッシュ フレームワークを実装してください)
```csharp
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
実装されたキャッシュは、次のように "TokenValidationParameters" プロパティを使用して、OIDC オプションで参照する必要があります。
```csharp
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

この構成の有効性をテストするには、ローカル OIDC で保護されているアプリケーションにログインし、fiddler で `"/signin-oidc"` エンドポイントへの要求をキャプチャします。 保護されていない場合は、fiddler でこの要求をリプレイすると、新しいセッション cookie が設定されます。 TokenReplayCache 保護が追加された後、要求がリプレイされると、次のように例外がスローされます。`SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a name="use-adal-libraries-to-manage-token-requests-from-oauth2-clients-to-aad-or-on-premises-ad"></a><a id="adal-oauth2"></a>ADAL ライブラリを使用して、OAuth2 クライアントから AAD (またはオンプレミス AD) へのトークン要求を管理する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure AD |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [ADAL](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) |
| **手順** | <p>Azure AD 認証ライブラリ (ADAL) を使用すると、クライアント アプリケーション開発者は、クラウドまたはオンプレミスの Active Directory (AD) に対して簡単にユーザーを認証し、API 呼び出しを保護するためのアクセス トークンを取得できます。</p><p>ADAL には、非同期のサポート、アクセス トークンと更新トークンを格納する構成可能なトークン キャッシュ、アクセス トークンの有効期限が切れたときに更新トークンを使用できる場合のトークンの自動更新など、開発者向けに認証を容易にする多くの機能が用意されています。</p><p>複雑な部分のほとんどが ADAL によって処理されるため、開発者はアプリケーションでビジネス ロジックに集中し、セキュリティの専門家でなくても簡単にリソースを保護できます。 個別のライブラリは、.NET、JavaScript (クライアントと Node.js)、Python、iOS、Android、および Java で使用できます。</p>|

## <a name="authenticate-devices-connecting-to-the-field-gateway"></a><a id="authn-devices-field"></a>フィールド ゲートウェイに接続しているデバイスを認証する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT フィールド ゲートウェイ |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | 各デバイスがフィールド ゲートウェイによって認証されていることを確認したうえで、そのデバイスからデータを受け入れて、クラウド ゲートウェイとのアップ ストリーム通信を容易にします。 また、個別のデバイスを一意に識別できるように、デバイスごとの資格情報でデバイスが接続されていることを確認します。|

## <a name="ensure-that-devices-connecting-to-cloud-gateway-are-authenticated"></a><a id="authn-devices-cloud"></a>クラウド ゲートウェイに接続しているデバイスが認証されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT クラウド ゲートウェイ |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック、C#、Node.JS、  |
| **属性**              | 該当なし、ゲートウェイの選択 - Azure IoT Hub |
| **参照**              | 該当なし、[.NET での Azure IoT Hub](../../iot-hub/quickstart-send-telemetry-dotnet.md)、[IoT Hub と Node.JS の概要](../../iot-hub/quickstart-send-telemetry-node.md)、[SAS と証明書による IoT のセキュリティ保護](../../iot-hub/iot-hub-devguide-security.md)、[Git リポジトリ](https://github.com/Azure/azure-iot-sdks/) |
| **手順** | <ul><li>**ジェネリック:** トランスポート層セキュリティ (TLS) または IPSec を使ってデバイスを認証します。 完全な非対称暗号を扱うことのできないデバイスでは、インフラストラクチャが事前共有キー (PSK) の使用をサポートしている必要があります。 Azure AD の OAuth をご利用ください。</li><li>**C#:** DeviceClient インスタンスを作成するとき、既定では、Create メソッドは、IoT Hub と通信するために AMQP プロトコルを使用する DeviceClient インスタンスを作成します。 HTTPS プロトコルを使用するには、プロトコルを引数として受け取る、Create メソッドのオーバーライドを使用します。 HTTPS プロトコルを使用する場合は、`Microsoft.AspNet.WebApi.Client` NuGet パッケージをプロジェクトに追加して、`System.Net.Http.Formatting` 名前空間を含める必要もあります。</li></ul>|

### <a name="example"></a>例
```csharp
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>例
**Node.JS: 認証**
#### <a name="symmetric-key"></a>対称キー
* Azure で IoT ハブを作成します
* デバイスの ID レジストリにエントリを作成します
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* シミュレート対象デバイスを作成します
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
  #### <a name="sas-token"></a>SAS トークン
* 対称キーを使用しているときに内部的に生成されますが、明示的に生成して使用することもできます
* プロトコルを定義します: `var Http = require('azure-iot-device-http').Http;`
* SAS トークンを作成します:
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
  + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* SAS トークンを使用して接続します:
    ```javascript
    Client.fromSharedAccessSignature(sas, Http);
    ```
  #### <a name="certificates"></a>証明書
* OpenSSL などのツールを使用して自己署名 X509 証明書を生成し、証明書を格納する .cert ファイルと、キーを格納する .key ファイルを生成します
* 証明書を使用してセキュリティで保護された接続を受け入れるデバイスをプロビジョニングします。
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* 証明書を使用してデバイスを接続します
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    };
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a name="use-per-device-authentication-credentials"></a><a id="authn-cred"></a>デバイスごとの認証資格情報を使用する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT クラウド ゲートウェイ  |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | ゲートウェイの選択 - Azure IoT Hub |
| **参照**              | [Azure IoT Hub セキュリティ トークン](../../iot-hub/iot-hub-devguide-security.md) |
| **手順** | IoT Hub レベルの共有アクセス ポリシーではなく、デバイス キーまたはクライアント証明書に基づく SaS トークンを使用したデバイスごとの認証資格情報を使用します。 これにより、デバイスまたはフィールド ゲートウェイ認証トークンを、別のデバイスやフィールド ゲートウェイが再利用できなくなります |

## <a name="ensure-that-only-the-required-containers-and-blobs-are-given-anonymous-read-access"></a><a id="req-containers-anon"></a>必要なコンテナーと BLOB のみに匿名読み取りアクセスが付与されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Storage |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | StorageType - BLOB |
| **参照**              | [コンテナーと BLOB への匿名読み取りアクセスを管理する](../../storage/blobs/anonymous-read-access-configure.md)、[Shared Access Signatures、第 1 部: SAS モデルについて](../../storage/common/storage-sas-overview.md) |
| **手順** | <p>既定では、コンテナーとコンテナー内の BLOB には、上位のストレージ アカウントの所有者のみがアクセスできます。 コンテナーとその BLOB に対する読み取りアクセス許可を匿名ユーザーに付与する場合は、コンテナーのアクセス許可を設定し、パブリック アクセスを許可できます。 パブリック アクセスが許可されたコンテナー内の BLOB は、匿名ユーザーが読み取ることができ、その際に要求の認証は不要です。</p><p>コンテナーへのアクセスは次のように管理できます。</p><ul><li>パブリック読み取りフル アクセス: コンテナーと BLOB のデータを匿名要求で読み取ることができます。 クライアントは匿名要求でコンテナー内の BLOB を列挙できますが、ストレージ アカウント内のコンテナーを列挙することはできません。</li><li>BLOB に限定したパブリック読み取りアクセス: 該当するコンテナー内の BLOB データは匿名要求で読み取り可能ですが、コンテナー データは参照できません。 クライアントはコンテナー内の BLOB を匿名要求で列挙することはできません</li><li>パブリック読み取りアクセスなし: コンテナーと BLOB のデータはアカウント所有者に限り読み取ることができます</li></ul><p>匿名アクセスは、匿名読み取りアクセスで特定の BLOB を常に使用する必要があるシナリオに最適です。 詳細な制御では、さまざまなアクセス許可を使用し、指定された期間において、制限付きアクセスを委任するための Shared Access Signature を作成できます。 機密データを含む可能性があるコンテナーと BLOB に、匿名アクセスが誤って付与されていないことを確認してください</p>|

## <a name="grant-limited-access-to-objects-in-azure-storage-using-sas-or-sap"></a><a id="limited-access-sas"></a>SAS または SAP を使用して Azure Storage のオブジェクトへの制限付きアクセスを許可する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Storage |
| **SDL フェーズ**               | Build |
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし |
| **参照**              | [Shared Access Signature、第 1 部: SAS モデルについて](../../storage/common/storage-sas-overview.md)、[Shared Access Signature、第 2 部: Blob Storage での SAS の作成と使用](../../storage/common/storage-sas-overview.md)、[Shared Access Signature と Stored Access Policy を使用してアカウントのオブジェクトに対するアクセス権を委任する方法](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **手順** | <p>Shared Access Signature (SAS) の使用は、アカウント アクセス キーを知らせずに、自分のストレージ アカウントのオブジェクトへの制限付きアクセスを他のクライアントに許可するための優れた方法です。 SAS とは、ストレージ リソースへの認証アクセスに必要なすべての情報をクエリ パラメーター内に含む URI です。 クライアントは、SAS 内で適切なコンストラクターまたはメソッドに渡すだけで、SAS でストレージ リソースにアクセスできます。</p><p>SAS は、自分のアカウント キーを知らせたくないクライアントに、自分のストレージ アカウント内のリソースへのアクセスを許可する場合に使用できます。 ストレージ アカウント キーには、プライマリ キーとセカンダリ キーの両方が含まれており、これらによって、アカウントとそのすべてのリソースへの管理アクセスが付与されます。 これらのアカウント キーのいずれかを知らせると、悪意で、または誤ってアカウントが使用される可能性が生じます。 Shared Access Signature は、アカウント キーが不要で安全な代替方法です。この方法で、他のクライアントは、付与されたアクセス許可に従ってストレージ アカウント内のデータの読み取り、書き込み、削除を実行できます。</p><p>毎回の論理パラメーター セットが類似している場合は、Stored Access Policy (SAP) を使用することをお勧めします。 Stored Access Policy から派生した SAS を使用すると、その SAS を即時に無効にすることができるので、可能な限り常に Stored Access Policy を使用するベスト プラクティスが推奨されます。</p>|
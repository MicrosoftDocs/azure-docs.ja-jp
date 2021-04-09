---
title: Azure Functions のセキュリティ保護
description: 一般的な攻撃に対して、Azure で実行される関数コードのセキュリティを強化する方法について説明します。
ms.date: 4/13/2020
ms.topic: conceptual
ms.openlocfilehash: 351bdca7ff94b6c058b5ab62fd9c16d707e7dc78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368491"
---
# <a name="securing-azure-functions"></a>Azure Functions のセキュリティ保護

サーバーレス関数の開発、デプロイ、操作に関するセキュリティ計画は、多くの点において Web ベースやクラウド ホスト型のアプリケーションのものと非常に似通っています。 [Azure App Service](../app-service/index.yml) には、関数アプリ向けのホスティング インフラストラクチャが用意されています。 この記事では、関数コードを実行する場合のセキュリティ戦略と、App Service を利用して関数をセキュリティで保護する方法について説明します。 

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

[Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)に準拠した一連のセキュリティ推奨事項については、「[Azure Functions 用の Azure セキュリティ ベースライン](security-baseline.md)」を参照してください。

## <a name="secure-operation"></a>操作をセキュリティで保護する 

このセクションでは、関数アプリの構成および実行を可能な限り安全に行う方法について説明します。 

### <a name="security-center"></a>Security Center

ポータルでは、Security Center が関数アプリと統合されています。 これにより、構成に関連する潜在的なセキュリティ脆弱性を、無料ですばやく評価することができます。 専用プランで実行される関数アプリでは、追加コストを払えば Security Center のリアルタイム セキュリティ機能も利用できます。 詳細については、「[Azure App Service Web アプリと API を保護する](../security-center/defender-for-app-service-introduction.md)」を参照してください。 

### <a name="log-and-monitor"></a>ログ記録と監視を行う

攻撃を検出する方法の 1 つが、アクティビティの監視とログ分析を利用することです。 Functions は Application Insights と統合されており、関数アプリのログ、パフォーマンス、エラー データが収集されます。 Application Insights はパフォーマンスの異常を自動的に検出するほか、問題の診断や、関数の使用状況の理解に役立つ強力な分析ツールを備えています。 詳細については、「[Azure Functions を監視する](functions-monitoring.md)」を参照してください。

Functions は Azure Monitor のログとも統合されており、関数アプリのログをシステム イベントと統合して分析を容易に進めることができます。 診断設定を使用すると、選択した送信先 (Logs Analytics ワークスペースなど) に対する関数のプラットフォーム ログおよびメトリックのストリーミング エクスポートを構成できます。 詳細については、「[Azure Monitor ログを使用した Azure Functions の監視](functions-monitor-log-analytics.md)」を参照してください。 

エンタープライズレベルで脅威検出と対応を自動化するには、Logs Analytics ワークスペースにログとイベントをストリーム配信します。 その後、このワークスペースに Azure Sentinel を接続します。 詳細については、「[Azure Sentinel とは](../sentinel/overview.md)」を参照してください。  

監視に関するその他のセキュリティ推奨事項については、「[Azure Functions 用の Azure セキュリティ ベースライン](security-baseline.md#logging-and-monitoring)」を参照してください。 

### <a name="require-https"></a>HTTPS を必須にする

既定では、クライアントは HTTP と HTTPS のどちらを使用しても関数エンドポイントに接続できます。 HTTPS では、SSL/TLS プロトコルによりセキュリティで保護された接続 (暗号化と認証の両方が実施されたもの) が提供されるため、HTTP を HTTPS にリダイレクトしてください。 方法については、「[HTTPS の適用](../app-service/configure-ssl-bindings.md#enforce-https)」を参照してください。

HTTPS を必須にする場合は、最新の TLS バージョンも必須にする必要があります。 方法については、「[TLS バージョンを適用する](../app-service/configure-ssl-bindings.md#enforce-tls-versions)」を参照してください。

詳細については、[安全な接続 (TLS)](../app-service/overview-security.md#https-and-certificates) に関するページを参照してください。

### <a name="function-access-keys"></a>関数のアクセス キー

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

#### <a name="system-key"></a>システム キー 

一部の拡張機能では、Webhook エンドポイントにアクセスするために、システムで管理されるキーが必要になることがあります。 システム キーは、内部コンポーネントにより呼び出される拡張機能固有の関数エンドポイント向けに設計されています。 たとえば、[Event Grid トリガー](functions-bindings-event-grid-trigger.md)を使用するには、トリガー エンドポイントの呼び出し時にサブスクリプションでシステム キーを使用する必要があります。 また、Durable Functions でも、[Durable Task 拡張機能 API](durable/durable-functions-http-api.md) の呼び出しにシステム キーを使用します。 

システム キーのスコープは拡張機能によって決まりますが、一般的には関数アプリ全体に適用されます。 システム キーは特定の拡張機能によってのみ作成可能であり、その値を明示的に設定することはできません。 他のキーと同様に、ポータルから、またはキー API を使用してキーの新しい値を生成することは可能です。

#### <a name="keys-comparison"></a>キーの比較

次の表に、さまざまなアクセス キーの使用方法を比較して示します。

| アクション                                        | Scope                    | 有効なキー         |
|-----------------------------------------------|--------------------------|--------------------|
| 関数の実行                            | 当該の関数        | 機能           |
| 関数の実行                            | すべての関数             | 関数またはホスト   |
| 管理エンドポイントの呼び出し                        | 関数アプリ             | ホスト (マスターのみ) |
| Durable Task 拡張機能 API の呼び出し              | 関数アプリ<sup>1</sup> | システム<sup>2</sup> |
| 拡張機能固有の Webhook の呼び出し (内部) | 関数アプリ<sup>1</sup> | システム<sup>2</sup> |

<sup>1</sup>スコープは拡張機能によって決まります。  
<sup>2</sup>固有の名前が拡張機能で設定されます。

アクセス キーの詳細については、[HTTP トリガーのバインドに関する記事](functions-bindings-http-webhook-trigger.md#obtaining-keys)を参照してください。


#### <a name="secret-repositories"></a>シークレット リポジトリ

既定では、キーは、`AzureWebJobsStorage` 設定によって指定されたアカウントの Blob Storage コンテナーに格納されます。 特定のアプリケーション設定を使用して、この動作をオーバーライドし、キーを別の場所に格納することができます。

|場所  |設定 | 値 | 説明  |
|---------|---------|---------|---------|
|別のストレージ アカウント     |  `AzureWebJobsSecretStorageSas`       | `<BLOB_SAS_URL` | 指定された SAS URL に基づいて、2 番目のストレージ アカウントの Blob Storage にキーを格納します。 キーは、関数アプリに固有のシークレットを使用して、格納される前に暗号化されます。 |
|ファイル システム   | `AzureWebJobsSecretStorageType`   |  `files`       | キーはファイル システムに保存されており、関数アプリに固有のシークレットを使用して、格納の前に暗号化されます。 |
|Azure Key Vault  | `AzureWebJobsSecretStorageType`<br/>`AzureWebJobsSecretStorageKeyVaultName` | `keyvault`<br/>`<VAULT_NAME>` | コンテナーには、ホスティング リソースのシステム割り当てマネージド ID に対応するアクセス ポリシーが必要です。 アクセス ポリシーでは、`Get`、`Set`、`List`、`Delete` というシークレットのアクセス許可を、その ID に付与することを必要としています。 <br/>ローカルで実行している場合は、開発者 ID が使用され、設定は [local.settings.json file](functions-run-local.md#local-settings-file) に含まれている必要があります。 | 
|Kubernetes シークレット  |`AzureWebJobsSecretStorageType`<br/>`AzureWebJobsKubernetesSecretName` (省略可) | `kubernetes`<br/>`<SECRETS_RESOURCE>` | Kubernetes で Functions ランタイムを実行する場合にのみサポートされます。 `AzureWebJobsKubernetesSecretName` が設定されていない場合、リポジトリは読み取り専用と見なされます。 この場合は、デプロイの前に値を生成する必要があります。 Kubernetes にデプロイすると、Azure Functions Core Tools によって値が自動的に生成されます。|

### <a name="authenticationauthorization"></a>認証/承認

関数キーによって、望ましくないアクセスをある程度軽減することはできますが、関数エンドポイントを完全に保護する唯一の方法は、関数にアクセスするクライアントについて肯定的な認証を実装することです。 そのうえで、ID に基づいて承認の判断を行えます。  

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

### <a name="permissions"></a>アクセス許可

他のアプリケーションやサービスと同じく、目標は、可能な限り低いアクセス許可で関数アプリを実行することです。 

#### <a name="user-management-permissions"></a>ユーザー管理のアクセス許可

関数では、組み込みの [Azure のロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) がサポートされます。 関数でサポートされる Azure ロールは、[共同作成者](../role-based-access-control/built-in-roles.md#contributor)、[所有者](../role-based-access-control/built-in-roles.md#owner)、および[閲覧者](../role-based-access-control/built-in-roles.md#owner)です。 

アクセス許可は、関数アプリ レベルで有効です。 ほとんどの関数アプリレベルのタスクを実行するには、共同作成者ロールが必要です。 関数アプリの削除は、所有者ロールでのみ行えます。 

#### <a name="organize-functions-by-privilege"></a>関数を権限別に整理する 

アプリケーションの設定に格納されている接続文字列やそれ以外の資格情報では、関数アプリに含まれるすべての関数に対して、関連リソース内で同一のアクセス許可一式が付与されます。 こうした資格情報を使用しない関数は別の関数アプリに移動して、特定の資格情報にアクセスできる関数の数を最小限に抑えることを検討してください。 異なる関数アプリの関数間でのデータの受け渡しは、[関数チェーン](/learn/modules/chain-azure-functions-data-using-bindings/)などの手法を使用すればいつでも行えます。  

#### <a name="managed-identities"></a>マネージド ID

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

マネージド ID は、一部のトリガーとバインドから接続するために、シークレットの代わりに使用できます。 「[ID ベースの接続](#identity-based-connections)」を参照してください。

詳細については、「[App Service と Azure Functions でマネージド ID を使用する方法](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json)」を参照してください。

#### <a name="restrict-cors-access"></a>CORS アクセスを制限する

[クロスオリジン リソース共有 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) を利用すると、別のドメインで実行されている Web アプリから HTTP トリガー エンドポイントに対して要求を行うことができます。 App Service には、HTTP 要求で必須の CORS ヘッダーを渡すサポート機能が組み込まれています。 CORS のルールは、関数アプリ レベルで定義されます。  

すべてのサイトからのエンドポイントへのアクセスを可能にするワイルドカードは、非常に便利です。 しかし、これを使用すると、クロスサイト スクリプティング攻撃を防止するという CORS の目的が損なわれます。 代わりに、エンドポイントへのアクセスが必要な各 Web アプリのドメイン用に、個別の CORS エントリを追加してください。 

### <a name="managing-secrets"></a>シークレットを管理する 

関数アプリで、コードの実行に必要な各種サービスやリソースに接続するには、接続文字列やサービス キーなどのシークレットにアクセスできる必要があります。 このセクションでは、関数に必要なシークレットを格納する方法について説明します。

関数コード内にはシークレットを格納しないでください。 

#### <a name="application-settings"></a>アプリケーションの設定

既定では、関数アプリとバインディングで使用する接続文字列およびシークレットは、アプリケーション設定として格納します。 こうすると、関数コード、および関数で使用するさまざまなバインディングの両方で、これらの資格情報を利用できます。 実際の値 (シークレット) を取得するには、アプリケーション設定 (キー) の名前を使用します。 

たとえば、すべての関数アプリには、ランタイムで使用される関連ストレージ アカウントが必要です。 既定では、このストレージ アカウントへの接続は、`AzureWebJobsStorage` という名前のアプリケーション設定に格納されます。

アプリ設定と接続文字列は、Azure で暗号化されて格納されます。 これらの暗号化は、アプリの起動時にアプリのプロセス メモリに挿入される前にのみ解除されます。 暗号化キーは定期的に回転されます。 セキュリティで保護されたシークレットのストレージを自分で管理したい場合は、アプリ設定で Azure Key Vault を参照する必要があります。 

ローカル コンピューターで関数を開発するとき、local.settings.json ファイルで既定で設定を暗号化することもできます。 詳細については、[ローカル設定ファイル](functions-run-local.md#local-settings-file)の `IsEncrypted` プロパティを参照してください。  

#### <a name="key-vault-references"></a>Key Vault の参照

ほとんどの関数ではアプリケーション設定で十分ですが、複数サービスにわたって同一のシークレットを共有する必要がある場合もあります。 この場合、シークレットのストレージが余分にあると、潜在的な脆弱性が高まります。 より安全な方法としては、一元的なシークレット ストレージ サービスを利用し、シークレット自体ではなくこのサービスを参照します。      

[Azure Key Vault](../key-vault/general/overview.md) は、アクセス ポリシーと監査履歴を完全制御する、一元化されたシークレット管理を提供するサービスです。 アプリケーション設定の接続文字列またはキーの代わりに、Key Vault 参照を使用できます。 詳細については、「[App Service と Azure Functions の Key Vault 参照を使用する](../app-service/app-service-key-vault-references.md?toc=%2fazure%2fazure-functions%2ftoc.json)」を参照してください。

### <a name="identity-based-connections"></a>ID ベースの接続

一部のリソースに接続するために、シークレットの代わりに ID を使用できます。 これには、シークレットの管理を必要としないという利点があり、よりきめ細かなアクセスの制御と監査が提供されます。 

[Azure AD 認証をサポートしている Azure サービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)への接続を作成するコードを記述する場合は、シークレットや接続文字列の代わりに ID を使用することを選択できます。 両方の接続方法の詳細については、各サービスのドキュメントを参照してください。

一部の Azure Functions トリガーおよびバインド拡張機能は、ID ベースの接続を使用して構成できます。 現時点では、これには [Azure BLOB](./functions-bindings-storage-blob.md) と [Azure Queue](./functions-bindings-storage-queue.md) の拡張機能が含まれます。 ID を使用するようにこれらの拡張機能を構成する方法については、[Azure Functions で ID ベースの接続を使用する方法](./functions-reference.md#configure-an-identity-based-connection)に関する記事を参照してください。

### <a name="set-usage-quotas"></a>使用量クォータを設定する

従量課金プランで実行する関数については、使用量クォータを設定することを検討してください。 関数アプリの関数の合計実行回数に対して日次 GB 秒の制限を設定すると、この制限に達したときに実行が停止されます。 これによって、悪意のあるコードによる関数の実行を抑えられる可能性があります。 関数のコストを見積もる方法については、「[従量課金プランのコストの見積もり](functions-consumption-costs.md)」を参照してください。 

### <a name="data-validation"></a>データ検証

関数で使用するトリガーとバインドでは、追加のデータ検証は行われません。 トリガーまたは入力バインドから受け取るデータはすべて、コードで検証する必要があります。 アップストリーム サービスが侵害された場合、未検証の入力が関数に渡されないようにする必要があります。 たとえば、リレーショナル データベースの Azure Storage キューからのデータを関数に格納するのであれば、SQL インジェクション攻撃を防ぐために、データを検証し、コマンドをパラメーター化する必要があります。 

関数に送信されるデータが既に検証またはサニタイズ済みであると思わないでください。 また、出力バインドに書き込まれるデータが妥当であるか検証することもお勧めします。 

### <a name="handle-errors"></a>エラーを処理する

基本的なことですが、関数に適切なエラー処理を記述することが重要です。 未処理のエラーはホストにまで伝播し、ランタイムによって処理されます。 バインドによって、エラーの処理は異なります。 詳細については、「[Azure Functions のエラー処理](functions-bindings-error-pages.md)」を参照してください。

### <a name="disable-remote-debugging"></a>リモート デバッグの無効化

関数のデバッグを積極的に行う場合を除き、リモート デバッグは無効にしてください。 リモート デバッグは、ポータルの関数アプリの **[構成]** の **[全般設定]** タブで無効にできます。 

### <a name="restrict-cors-access"></a>CORS アクセスを制限する

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

許可されるオリジンの一覧でワイルドカードを使用しないでください。 代わりに、要求の取得元になる特定のドメインを記載してください。

### <a name="store-data-encrypted"></a>暗号化したデータを格納する

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="secure-deployment"></a>セキュリティで保護されたデプロイ

Azure Functions は統合機能を備えており、Azure にローカルの関数プロジェクト コードを容易に発行できます。 Azure Functions トポロジのセキュリティについて検討する場合、デプロイのしくみを理解することが重要です。   

### <a name="deployment-credentials"></a>デプロイ資格情報

App Service をデプロイするには、一連のデプロイ資格情報が必要です。 これらの資格情報は、関数アプリのデプロイを保護するために使用されます。 デプロイ資格情報は App Service プラットフォームにより管理され、保存時に暗号化されます。 

デプロイ資格情報には次の 2 種類があります。

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

現時点では、デプロイ資格情報では Key Vault はサポートされていません。 デプロイ資格情報の管理の詳細については、「[Azure App Service のデプロイ資格情報の構成](../app-service/deploy-configure-credentials.md)」を参照してください。

### <a name="disable-ftp"></a>FTP を無効にする

既定では、関数アプリごとに FTP エンドポイントが有効になります。 FTP エンドポイントには、デプロイ資格情報を使用してアクセスします。 

関数コードをデプロイする場合、FTP は推奨されません。 FTP デプロイは手動であり、実行するにはトリガーを同期する必要があります。 詳細については、[FTP デプロイ](functions-deployment-technologies.md#ftp)に関するページを参照してください。 

FTP を使用する予定がない場合は、ポータルで無効にしてください。 FTP を使用する場合は、[FTPS を強制してください](../app-service/deploy-ftp.md#enforce-ftps)。

### <a name="secure-the-scm-endpoint"></a>scm エンドポイントをセキュリティで保護する

各アプリには対応する `scm` サービス エンドポイントがあり、これらのエンドポイントは、高度なツール (Kudu) サービスによってデプロイに使用されるほか、他の App Service [サイト拡張機能](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)に使用されます。 関数アプリの scm エンドポイントは、常に `https://<FUNCTION_APP_NAME.scm.azurewebsites.net>` という形式の URL になります。 ネットワークの分離を使用して関数を保護する場合は、このエンドポイントについても考慮する必要があります。 

scm エンドポイントを個別に用意することで、仮想ネットワーク内で隔離または実行される他の関数アプリ向けの高度なツール機能、およびデプロイを制御できます。 scm エンドポイントでは、(デプロイ資格情報を使用した) 認証および Azure portal 資格情報を使用したシングル サインオンの両方がサポートされます。 詳細については、「[Kudu サービスへのアクセス](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)」を参照してください。 

### <a name="continuous-security-validation"></a>継続的なセキュリティ検証

セキュリティについては開発プロセスのあらゆる段階で検討する必要があるので、継続的デプロイ環境でセキュリティ検証を実装することもお勧めします。 これは、DevSecOps とも呼ばれます。 デプロイ パイプラインに Azure DevOps を使用すると、デプロイ プロセスに検証を統合できます。 詳細については、「[CI/CD パイプラインに継続的なセキュリティ検証を追加する方法](/azure/devops/migrate/security-validation-cicd-pipeline)」を参照してください。  

## <a name="network-security"></a>ネットワークのセキュリティ

関数アプリに対するネットワーク アクセスを制限することで、関数エンドポイントにアクセス可能なユーザーを制御できます。 Functions では App Service インフラストラクチャを利用しており、インターネットのルーティング可能なアドレスを使用したり、関数エンドポイントへのインターネット アクセスを制限したりすることなく、関数からリソースにアクセスできます。 これらのネットワーク オプションの詳細については、「[Azure Functions のネットワーク オプション](functions-networking-options.md)」を参照してください。

### <a name="set-access-restrictions"></a>アクセス制限を設定する

アクセス制限を使用すると、許可規則および拒否規則の一覧を定義して、アプリへのトラフィックを制御できます。 規則は、優先度に従って評価されます。 規則が定義されていない場合、アプリはあらゆるアドレスからのトラフィックを受け入れます。 詳細については、「[Azure App Service のアクセス制限](../app-service/app-service-ip-restrictions.md?toc=%2fazure%2fazure-functions%2ftoc.json)」を参照してください。

### <a name="private-site-access"></a>プライベート サイトへのアクセス

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

### <a name="deploy-your-function-app-in-isolation"></a>関数アプリを分離してデプロイする

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

### <a name="use-a-gateway-service"></a>ゲートウェイ サービスを使用する

[Azure Application Gateway](../application-gateway/overview.md) や [Azure Front Door](../frontdoor/front-door-overview.md) などのゲートウェイ サービスを使用すると、Web アプリケーション ファイアウォール (WAF) を設定できます。 WAF 規則は検出された攻撃の監視またはブロックに使用され、関数の保護のための新しい層になります。 WAF を設定するには、関数アプリを ASE 内で実行するか、プライベート エンドポイント (プレビュー) を使用して実行する必要があります。 詳細については、[プライベート エンドポイントの使用](../app-service/networking/private-endpoint.md)に関するページを参照してください。    

## <a name="next-steps"></a>次のステップ

+ [Azure Functions 用の Azure セキュリティ ベースライン](security-baseline.md)
+ [Azure Functions 診断](functions-diagnostics.md)

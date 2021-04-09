---
title: レジストリ ログインのトラブルシューティング
description: Azure Container Registry にログインするときの一般的な問題の現象、原因、対処法
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 5deb1717cf3886d8ea9c021d92afa358946b16dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052080"
---
# <a name="troubleshoot-registry-login"></a>レジストリ ログインのトラブルシューティング

Azure Container Registry にログインするときに発生する可能性のある問題のトラブルシューティングに役立ちます。 

## <a name="symptoms"></a>現象

次のうち 1 つ以上が含まれる場合があります。

* `docker login`、`az acr login`、またはその両方を使用してレジストリにログインすることができない
* レジストリにログインできず、`unauthorized: authentication required` または `unauthorized: Application not registered with AAD` のエラーが発生する
* レジストリにログインできず、`Could not connect to the registry login server` の Azure CLI エラーが発生する
* イメージをプッシュまたはプルできず、`unauthorized: authentication required` の Docker エラーが発生する
* Azure Kubernetes Service、Azure DevOps、または別の Azure サービスからレジストリにアクセスできない
* レジストリにアクセスできず、`Error response from daemon: login attempt failed with status: 403 Forbidden` エラーが発生する - 「[レジストリに関するネットワークの問題のトラブルシューティング](container-registry-troubleshoot-access.md)」を参照
* Azure portal でレジストリ設定にアクセスまたは表示できないか、Azure CLI を使用してレジストリを管理できない

## <a name="causes"></a>原因

* Docker が環境で適切に構成されていない - [対処法](#check-docker-configuration)
* レジストリが存在しないか、名前が正しくない - [対処法](#specify-correct-registry-name)
* レジストリの資格情報が有効でない - [対処法](#confirm-credentials-to-access-registry)
* 資格情報がプッシュ、プル、または Azure Resource Manager 操作に対して承認されていない - [対処法](#confirm-credentials-are-authorized-to-access-registry)
* 資格情報の有効期限が切れている - [対処法](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>詳しい診断 

[az acr check-health](/cli/azure/acr#az-acr-check-health) コマンドを実行して、レジストリ環境の正常性に関する詳細情報を取得し、必要に応じてターゲット レジストリにアクセスします。 たとえば、Docker 構成エラーや Azure Active Directory ログインの問題を診断します。 

コマンドの例については、「[Azure コンテナー レジストリの正常性のチェック](container-registry-check-health.md)」を参照してください。 エラーが報告された場合は、推奨される対処法について、[エラー リファレンス](container-registry-health-error-reference.md)と次のセクションを確認してください。

Azure Kubernetes Service でレジストリを使用しているときに問題が発生した場合は、[az aks check-acr](/cli/azure/aks#az_aks_check_acr) コマンドを実行して、AKS クラスターからレジストリにアクセスできることを確認します。

> [!NOTE]
> また、一部の認証または承認エラーは、レジストリへのアクセスを妨げるファイアウォールまたはネットワーク構成がある場合に発生する可能性があります。 「[レジストリに関するネットワークの問題のトラブルシューティング](container-registry-troubleshoot-access.md)」を参照してください。

## <a name="potential-solutions"></a>対処法

### <a name="check-docker-configuration"></a>Docker の構成を確認する

ほとんどの Azure Container Registry 認証フローで、イメージのプッシュやプルなどの操作のためにレジストリで認証できるように、ローカルの Docker がインストールされている必要があります。 ご使用の環境で Docker CLI クライアントとデーモン (Docker エンジン) が実行されていることを確認します。 Docker クライアント バージョン 18.03 以降が必要です。

関連リンク:

* [認証の概要](container-registry-authentication.md#authentication-options)
* [Container Registry に関する FAQ](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>正しいレジストリ名を指定する

`docker login` を使用する場合は、*myregistry.azurecr.io* など、レジストリの完全なログイン サーバー名を指定します。 必ず小文字のみを使用してください。 例:

```console
docker login myregistry.azurecr.io
```

[az acr login](/cli/azure/acr#az-acr-login) を Azure Active Directory ID と共に使用する場合は、まず [Azure CLI にサインイン](/cli/azure/authenticate-azure-cli)し、次にレジストリの Azure リソース名を指定します。 リソース名は、*myregistry* (ドメイン サフィックスはなし) のように、レジストリの作成時に指定された名前です。 例:

```azurecli
az acr login --name myregistry
```

関連リンク:

* [az acr ログインは成功するが、Docker が "権限がありません: 認証が必要です" というエラーで失敗する](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required )

### <a name="confirm-credentials-to-access-registry"></a>レジストリにアクセスするための資格情報を確認する

シナリオに使用する、レジストリ所有者から提供された資格情報が有効であることを確認します。 いくつかの問題が考えられます。

* Active Directory サービス プリンシパルを使用している場合は、Active Directory テナントで確実に正しい資格情報を使用します。
  * ユーザー名 - サービス プリンシパルのアプリケーション ID (*クライアント ID* とも呼ばれます)
  * パスワード - サービス プリンシパルのパスワード (*クライアント シークレット* とも呼ばれます)
* Azure Kubernetes Service や Azure DevOps などの Azure サービスを使用してレジストリにアクセスする場合は、サービスのレジストリ構成を確認します。 
* `--expose-token` オプションを指定して `az acr login` を実行し、Docker デーモンを使用せずにレジストリをログインできるようにする場合は、確実にユーザー名 `00000000-0000-0000-0000-000000000000` で認証します。
* レジストリが[匿名プル アクセス](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)用に構成されている場合、以前の Docker ログインから格納されている既存の Docker 資格情報により匿名アクセスを防ぐことができます。 レジストリに対して匿名のプル操作を実行する前に、 `docker logout` を実行してください。

関連リンク:

* [認証の概要](container-registry-authentication.md#authentication-options)
* [Azure AD での個々のログイン](container-registry-authentication.md#individual-login-with-azure-ad)
* [サービス プリンシパルを使用したログイン](container-registry-auth-service-principal.md)
* [マネージド ID を使用したログイン](container-registry-authentication-managed-identity.md)
* [リポジトリスコープのトークンを使用したログイン](container-registry-repository-scoped-permissions.md)
* [管理者アカウントを使用したログイン](container-registry-authentication.md#admin-account)
* [Azure AD 認証と承認のエラー コード](../active-directory/develop/reference-aadsts-error-codes.md)
* [az acr login](/cli/azure/acr#az-acr-login) リファレンス

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>資格情報がレジストリへのアクセスが許可されていることを確認する

レジストリからイメージをプルする `AcrPull` Azure ロールや、イメージをプッシュする `AcrPush` ロールなど、資格情報に関連付けられているレジストリのアクセス許可を確認します。 

Azure CLI を使用してポータルまたはレジストリ管理でレジストリにアクセスするには、Azure Resource Manager 操作を実行するために少なくとも `Reader` ロールまたは同等のアクセス許可が必要です。

ポータルを経由したレジストリへのアクセスを許可するようにアクセス許可が最近変更された場合は、ブラウザーでシークレットまたはプライベート セッションを試して、古いブラウザー キャッシュや Cookie を回避することが必要になる場合があります。

ロールの割り当てを追加または削除するには、自分またはレジストリ所有者がサブスクリプションで十分な権限を持っている必要があります。

関連リンク:

* [Azure のロールとアクセス許可 - Azure Container Registry](container-registry-roles.md)
* [リポジトリスコープのトークンを使用したログイン](container-registry-repository-scoped-permissions.md)
* [Azure portal を使用して Azure ロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-portal.md)
* [リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../active-directory/develop/howto-create-service-principal-portal.md)
* [新しいアプリケーション シークレットを作成する](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Azure AD 認証と承認のコード](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>資格情報の有効期限が切れていないことを確認する

トークンと Active Directory 資格情報は、定義された期間が経過すると期限切れになり、レジストリにアクセスできなります。 アクセスを有効にするには、資格情報のリセットまたは再生成が必要になる可能性があります。

* 個々の AD ID、マネージド ID、またはレジストリ ログイン用のサービス プリンシパルを使用する場合、AD トークンは 3 時間後に期限切れになります。 レジストリにもう一度ログインします。  
* 期限切れのクライアント シークレットを持つ AD サービス プリンシパルを使用する場合、サブスクリプション所有者またはアカウント管理者は、資格情報をリセットするか、新しいサービス プリンシパルを生成する必要があります。
* [リポジトリスコープのトークン](container-registry-repository-scoped-permissions.md)を使用する場合は、レジストリ所有者がパスワードをリセットするか、新しいトークンを生成する必要がある可能性があります。

関連リンク:

* [サービス プリンシパルの資格情報のリセット](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
* [トークンのパスワードを再生成する](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Azure AD での個々のログイン](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>高度なトラブルシューティング

レジストリで[リソース ログの収集](container-registry-diagnostics-audit-logs.md)が有効になっている場合は、ContainterRegistryLoginEvents ログを確認します。 このログには、受信 ID や IP アドレスを含む、認証イベントと状態が格納されています。 ログで[レジストリ認証エラー](container-registry-diagnostics-audit-logs.md#registry-authentication-failures)のクエリを実行します。 

関連リンク:

* [診断の評価と監査のためのログ](container-registry-diagnostics-audit-logs.md)
* [Container Registry に関する FAQ](container-registry-faq.md)
* [Azure Container Registry のベスト プラクティス](container-registry-best-practices.md)

## <a name="next-steps"></a>次のステップ

ここで問題を解決できない場合は、次のオプションを参照してください。

* レジストリのその他のトラブルシューティングのトピックには、次のものがあります。
  * [レジストリに関するネットワークの問題のトラブルシューティング](container-registry-troubleshoot-access.md)
  * [レジストリのパフォーマンスのトラブルシューティング](container-registry-troubleshoot-performance.md)
* [コミュニティ サポート](https://azure.microsoft.com/support/community/) オプション
* [Microsoft Q&A](/answers/products/)
* [サポート チケットを開く](https://azure.microsoft.com/support/create-ticket/) - 入力した情報に基づいて、レジストリで認証エラーが発生した場合にクイック診断が実行される場合があります
---
title: Azure Container Registry におけるコンテンツの信頼
description: Azure Container Registry でコンテンツの信頼を有効にし、署名済みのイメージをプッシュ/プルする方法について説明します。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: danlep
ms.openlocfilehash: d2132f060076aefe6ae0eccb6d5300c78c96ece5
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356538"
---
# <a name="content-trust-in-azure-container-registry"></a>Azure Container Registry におけるコンテンツの信頼

Azure Container Registry では、Docker の[コンテンツの信頼][docker-content-trust]モデルを実装し、署名済みのイメージのプッシュとプルを有効にします。 この記事では、コンテナー レジストリ内でコンテンツの信頼を有効にする方法について説明します。

> [!NOTE]
> コンテンツの信頼は、Azure Container Registry の [Premium SKU](container-registry-skus.md) の機能です。

## <a name="how-content-trust-works"></a>コンテンツの信頼の動作概念

セキュリティを考慮して設計されたあらゆる分散システムにとって大切なことは、システムに入力されるデータの "*ソース*" と "*整合性*" の両方を確認することです。 データのコンシューマーは、データの公開元 (ソース) を確認できること、またデータが公開後に改変されていないこと (整合性) を確認できることが必要です。 

レジストリにプッシュするイメージには、イメージの発行者がコンテンツの信頼を通じて**署名**することができます。 イメージのコンシューマー (レジストリからイメージをプルする人またはシステム) は、署名済みのイメージ*のみ*をプルするように各自のクライアントを構成することができます。 署名済みのイメージをそのコンシューマーがプルすると、Docker クライアントによってイメージの整合性が確認されます。 このモデルでは、レジストリに存在する署名済みのイメージが確かに本人によって発行され、また発行された後に改変されていないという確信をコンシューマーは得ることができます。

### <a name="trusted-images"></a>信頼済みのイメージ

コンテンツの信頼は、リポジトリ内の**タグ**と連動します。 イメージ リポジトリは、署名済みのタグを持ったイメージと署名されていないタグを持ったイメージの両方を格納できます。 たとえば、署名の対象を `myimage:stable` と `myimage:latest` のイメージに限定し、`myimage:dev` には署名しないといったことが考えられます。

### <a name="signing-keys"></a>署名キー

コンテンツの信頼は、一連の暗号署名キーを使って管理されます。 これらのキーは、レジストリ内の特定のリポジトリ関連付けられます。 Docker クライアントとご利用のレジストリがリポジトリ内のタグに対する信頼を管理する際に使用する署名キーには、いくつかの種類があります。 コンテンツの信頼を有効にしてコンテナー発行/消費パイプラインに統合するときは、それらのキーを慎重に管理する必要があります。 詳細については、この記事の後半にある「[キー管理](#key-management)」と Docker ドキュメントの「[Manage keys for content trust][docker-manage-keys]」 (コンテンツの信頼に使用するキーの管理) を参照してください。

> [!TIP]
> これは Docker のコンテンツ信頼モデルをごく簡単に説明したものです。 コンテンツの信頼についての詳細な解説については、「[Content trust in Docker][docker-content-trust]」 (Docker におけるコンテンツの信頼) を参照してください。

## <a name="enable-registry-content-trust"></a>レジストリのコンテンツの信頼を有効にする

まず、コンテンツの信頼をレジストリ レベルで有効にしましょう。 コンテンツの信頼を有効にした後、クライアント (ユーザーまたはサービス) は、署名済みのイメージをレジストリにプッシュすることができます。 レジストリに対してコンテンツの信頼を有効にしたからといって、コンテンツの信頼を有効にしているコンシューマーにレジストリの使用が限定されることはありません。 コンテンツの信頼を有効にしていないコンシューマーも引き続き、通常どおりにレジストリを使うことができます。 一方、クライアントでコンテンツの信頼を有効にしているコンシューマーは、レジストリ内の署名済みのイメージ*のみ*を表示することができます。

レジストリに対してコンテンツの信頼を有効にするには、まず Azure portal で目的のレジストリに移動します。 **[ポリシー]** で、 **[コンテンツの信頼]**  >  **[有効]**  >  **[保存]** の順に選択します。

![Azure portal でレジストリに対するコンテンツの信頼を有効にする][content-trust-01-portal]

## <a name="enable-client-content-trust"></a>クライアントのコンテンツの信頼を有効にする

信頼済みのイメージを取り扱うためには、イメージの発行者とコンシューマーの双方が、その Docker クライアントに対してコンテンツの信頼を有効にする必要があります。 パブリッシャーは、コンテンツの信頼が有効になっているレジストリに関して、そこにプッシュするイメージに署名することができます。 コンシューマーは、コンテンツの信頼を有効にすることで、レジストリの表示を署名済みのイメージのみに限定することができます。 Docker クライアントでは、コンテンツの信頼が既定では無効になっていますが、シェル セッションごと、またはコマンドごとにコンテンツの信頼を有効にすることができます。

シェル セッションに関してコンテンツの信頼を有効にするには、`DOCKER_CONTENT_TRUST` 環境変数を **1** に設定します。 たとえば、Bash シェルで次のように入力します。

```bash
# Enable content trust for shell session
export DOCKER_CONTENT_TRUST=1
```

一方、個々のコマンドに関してコンテンツの信頼を有効または無効にしたい場合、いくつかの Docker コマンドでは、`--disable-content-trust` 引数がサポートされています。 個々のコマンドでコンテンツの信頼を有効にするには、次のようにします。

```bash
# Enable content trust for single command
docker build --disable-content-trust=false -t myacr.azurecr.io/myimage:v1 .
```

シェル セッションに関してコンテンツの信頼を有効にした場合で、かつ特定のコマンドについてはコンテンツの信頼を無効にしたい場合は、次のようにします。

```bash
# Disable content trust for single command
docker build --disable-content-trust -t myacr.azurecr.io/myimage:v1 .
```

## <a name="grant-image-signing-permissions"></a>イメージに署名するためのアクセス許可を与える

信頼済みのイメージをレジストリにプッシュできるのは、アクセス許可が与えられたユーザーまたはシステムだけです。 信頼済みのイメージをプッシュするアクセス許可をユーザー (またはサービス プリンシパルを使用するシステム) に与えるには、その Azure Active Directory ID に `AcrImageSigner` ロールを与えます。 レジストリにイメージをプッシュするために必要な `AcrPush` (または同等の) ロールとは別に、これを追加することになります。 詳細については、「[Azure Container Registry のロールとアクセス許可](container-registry-roles.md)」を参照してください。

以降、Azure portal と Azure CLI から `AcrImageSigner` ロールを付与する方法について詳しく説明します。

### <a name="azure-portal"></a>Azure ポータル

Azure portal でレジストリに移動し、 **[アクセス制御 (IAM)]**  >  **[ロール割り当ての追加]** を選択します。 **[ロール割り当ての追加]** の **[ロール]** で [`AcrImageSigner`] を選択し、ユーザーまたはサービス プリンシパルを**選択**して (複数可)、 **[保存]** を選択します。

この例では、`AcrImageSigner` ロールが 2 つのエンティティに割り当てられています。"service-principal" という名前のサービス プリンシパルと "Azure User" という名前のユーザーです。

![Azure portal でレジストリに対するコンテンツの信頼を有効にする][content-trust-02-portal]

### <a name="azure-cli"></a>Azure CLI

署名のためのアクセス許可を Azure CLI を使ってユーザーに与えるには、特定のレジストリを有効範囲として `AcrImageSigner` ロールをユーザーに割り当てます。 コマンドの形式は次のとおりです。

```azurecli
az role assignment create --scope <registry ID> --role AcrImageSigner --assignee <user name>
```

たとえば、このロールを自分自身に与えるには、認証済みの Azure CLI セッションで次のコマンドを実行します。 `REGISTRY` の値は、実際の Azure Container Registry の名前に合わせて変更してください。

```bash
# Grant signing permissions to authenticated Azure CLI user
REGISTRY=myregistry
USER=$(az account show --query user.name --output tsv)
REGISTRY_ID=$(az acr show --name $REGISTRY --query id --output tsv)

az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee $USER
```

信頼済みのイメージをレジストリにプッシュするための権限を[サービス プリンシパル](container-registry-auth-service-principal.md)に与えることもできます。 サービス プリンシパルは、信頼済みのイメージをレジストリにプッシュする必要のある無人のシステム (ビルド システムなど) で使用できます。 その形式は、ユーザーにアクセス許可を与えるときと同様ですが、`--assignee` の値に指定するのはサービス プリンシパル ID となります。

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee <service principal ID>
```

`<service principal ID>` には、サービス プリンシパルの **appId**、**objectId**、またはその **servicePrincipalNames** を指定できます。 サービス プリンシパルと Azure Container Registry の取り扱いについて詳しくは、「[サービス プリンシパルによる Azure Container Registry 認証](container-registry-auth-service-principal.md)」をご覧ください。

ロールが変更されたら、新しいロールを有効にするために、`az acr login` を実行して Azure CLI のローカル ID トークンを更新します。

## <a name="push-a-trusted-image"></a>信頼済みのイメージをプッシュする

信頼済みのイメージのタグをコンテナー レジストリにプッシュするには、コンテンツの信頼を有効にし、`docker push` でイメージをプッシュします。 署名済みのタグを初めてプッシュするとき、ルート署名キーとリポジトリ署名キーの両方のパスフレーズを作成するように求められます。 ルート キーとリポジトリ キーは、どちらもマシンのローカルで生成されて格納されます。

```console
$ docker push myregistry.azurecr.io/myimage:v1
The push refers to repository [myregistry.azurecr.io/myimage]
ee83fc5847cb: Pushed
v1: digest: sha256:aca41a608e5eb015f1ec6755f490f3be26b48010b178e78c00eac21ffbe246f1 size: 524
Signing and pushing trust metadata
You are about to create a new root signing key passphrase. This passphrase
will be used to protect the most sensitive key in your signing system. Please
choose a long, complex passphrase and be careful to keep the password and the
key file itself secure and backed up. It is highly recommended that you use a
password manager to generate the passphrase and keep it safe. There will be no
way to recover this key. You can find the key in your config directory.
Enter passphrase for new root key with ID 4c6c56a:
Repeat passphrase for new root key with ID 4c6c56a:
Enter passphrase for new repository key with ID bcd6d98:
Repeat passphrase for new repository key with ID bcd6d98:
Finished initializing "myregistry.azurecr.io/myimage"
Successfully signed myregistry.azurecr.io/myimage:v1
```

コンテンツの信頼を有効にした状態で初めて `docker push` を実行した後、それ以降のプッシュには、同じルート キーが Docker クライアントで使用されます。 それ以降は、同じリポジトリに対してプッシュするたびに、リポジトリ キーのみが求められます。 信頼済みイメージのプッシュ先となるリポジトリが変わると、その都度、新しいリポジトリ キーのパスフレーズを入力するよう求められます。

## <a name="pull-a-trusted-image"></a>信頼済みのイメージをプルする

信頼済みのイメージをプルするには、コンテンツの信頼を有効にして、通常どおりに `docker pull` コマンドを実行します。 通常のユーザーが信頼済みのイメージをプルするには、`AcrPull` ロールがあれば十分です。 `AcrImageSigner` ロールなどの追加のロールは不要です。 コンテンツの信頼を有効にしているコンシューマーは、署名済みタグの付いたイメージだけをプルすることができます。 以下に示したのは、署名済みのタグをプルする例です。

```console
$ docker pull myregistry.azurecr.io/myimage:signed
Pull (1 of 1): myregistry.azurecr.io/myimage:signed@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b: Pulling from myimage
8e3ba11ec2a2: Pull complete
Digest: sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Status: Downloaded newer image for myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Tagging myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b as myregistry.azurecr.io/myimage:signed
```

コンテンツの信頼を有効にしているクライアントが、署名されていないタグをプルしようとしても、その操作は失敗します。

```console
$ docker pull myregistry.azurecr.io/myimage:unsigned
No valid trust data for unsigned
```

### <a name="behind-the-scenes"></a>バックグラウンド処理

`docker pull` を実行すると、Docker クライアントは、プル対象のタグについて、[Notary CLI][docker-notary-cli] で使われているものと同じライブラリを使って、タグから SHA-256 ダイジェストへのマッピングを要求します。 クライアントは信頼データの署名を確認した後、"ダイジェストでプル" するよう Docker Engine に命令を出します。 このエンジンが、プルの実行中、SHA-256 チェックサムをコンテンツのアドレスとして使い、Azure Container Registry にイメージのマニフェストを要求して検証します。

## <a name="key-management"></a>キー管理

信頼済みのイメージを初めてプッシュするときの `docker push` 出力に記載されているように、ルート キーはきわめて機微な情報です。 ルート キーは必ずバックアップして、安全な場所に保管してください。 ルート キーは必ずバックアップを作成して、安全な場所に保管してください。

```sh
~/.docker/trust/private
```

ルート キーとリポジトリ キーをバックアップする際は、それらを圧縮してアーカイブし、安全な場所に保管してください。 たとえば Bash の場合は次のようになります。

```bash
umask 077; tar -zcvf docker_private_keys_backup.tar.gz ~/.docker/trust/private; umask 022
```

信頼済みのイメージをプッシュするとき、ローカルで生成されるルート キーとリポジトリ キーの他に、いくつかのキーが Azure Container Registry により生成されて格納されます。 Docker の実装によるコンテンツの信頼で用いられる各種キーに関して、管理上の詳しいガイダンスを含む詳細については、Docker のドキュメントの「[Manage keys for content trust][docker-manage-keys]」 (コンテンツの信頼に使用するキーの管理) を参照してください。

### <a name="lost-root-key"></a>ルート キーの紛失

ルート キーが利用できなくなった場合、そのキーでタグが署名されているリポジトリ内の署名済みのタグにはアクセスできなくなります。 紛失したルート キーで署名されたイメージ タグへのアクセスを Azure Container Registry で復元することはできません。 レジストリのすべての信頼データ (署名) を削除するには、まずそのレジストリに対するコンテンツの信頼を無効にしてから再度有効にしてください。

> [!WARNING]
> レジストリのコンテンツの信頼を無効にしてから再度有効にすると、**そのレジストリの各リポジトリに存在するすべての署名済みタグの信頼データがすべて削除されます**。 この操作を元に戻すことはできません。つまり削除された信頼データを Azure Container Registry で復旧することはできません。 コンテンツの信頼を無効にしても、イメージそのものは削除されません。

レジストリに対してコンテンツの信頼を無効にするには、Azure portal で目的のレジストリに移動します。 **[ポリシー]** で、 **[コンテンツの信頼]**  >  **[無効]**  >  **[保存]** の順に選択します。 レジストリ内の署名がすべて失われるという警告が表示されます。 そのレジストリ内のすべての署名を完全に削除する場合は、 **[OK]** を選択してください。

![Azure portal でレジストリに対するコンテンツの信頼を無効にする][content-trust-03-portal]

## <a name="next-steps"></a>次の手順

* コンテンツの信頼の詳細については、[Docker のコンテンツの信頼][docker-content-trust]に関するページを参照してください。 この記事では要点を絞って説明しましたが、コンテンツの信頼は広範囲に及ぶテーマです。Docker のドキュメントで、さらに踏み込んだ情報を得ることができるでしょう。

* Docker イメージをビルドし、プッシュするとき、コンテンツの信頼を利用する例については、[Azure Pipelines](/azure/devops/pipelines/build/content-trust) のドキュメントを参照してください。

<!-- IMAGES> -->
[content-trust-01-portal]: ./media/container-registry-content-trust/content-trust-01-portal.png
[content-trust-02-portal]: ./media/container-registry-content-trust/content-trust-02-portal.png
[content-trust-03-portal]: ./media/container-registry-content-trust/content-trust-03-portal.png

<!-- LINKS - external -->
[docker-content-trust]: https://docs.docker.com/engine/security/trust/content_trust
[docker-manage-keys]: https://docs.docker.com/engine/security/trust/trust_key_mng/
[docker-notary-cli]: https://docs.docker.com/notary/getting_started/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli

---
title: インクルード ファイル
description: インクルード ファイル
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/04/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 404fd10c3e3610671d2b6e5dbc6aba8bcaa70046
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100019578"
---
## <a name="push-image-to-registry"></a>レジストリにイメージをプッシュする

Azure Container Registry にイメージをプッシュするには、まずイメージを用意する必要があります。 ローカル コンテナー イメージがまだない場合は、次の [docker pull][docker-pull] コマンドを実行して、既存のパブリック イメージをプルします。 この例では、Microsoft Container Registry から `hello-world` イメージをプルします。

```
docker pull mcr.microsoft.com/hello-world
```

イメージをレジストリにプッシュするには、レジストリのログイン サーバーの完全修飾名を使用して、そのイメージにタグを付けておく必要があります。 ログイン サーバー名は、 *\<registry-name\>.azurecr.io* (すべて小文字にする必要があります) という形式です (*mycontainerregistry.azurecr.io* など)。

[docker tag][docker-tag] コマンドを使用してイメージにタグ付けします。 `<login-server>` を ACR インスタンスのログイン サーバー名で置き換えます。

```
docker tag mcr.microsoft.com/hello-world <login-server>/hello-world:v1
```

例:

```
docker tag mcr.microsoft.com/hello-world mycontainerregistry.azurecr.io/hello-world:v1
```


最後に、[docker push][docker-push] を使用して、レジストリ インスタンスにイメージをプッシュします。 `<login-server>` をレジストリ インスタンスのログイン サーバー名で置き換えます。 この例では、**hello-world** レポジトリを作成します。これには、`hello-world:v1` イメージが含まれています。

```
docker push <login-server>/hello-world:v1
```

コンテナー レジストリにイメージをプッシュした後は、ローカルの Docker 環境から `hello-world:v1` イメージを削除します (この [docker rmi][docker-rmi] コマンドは、Azure コンテナー リポジトリ内の **hello-world** リポジトリからイメージを削除しないことに注意してください)。

```
docker rmi <login-server>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->


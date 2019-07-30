---
title: インクルード ファイル
description: インクルード ファイル
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 09eaf9465ec3912dea6e1f3ee1693f6bfed50abc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181731"
---
## <a name="push-image-to-registry"></a>レジストリにイメージをプッシュする

Azure Container Registry にイメージをプッシュするには、まずイメージを用意する必要があります。 ローカル コンテナー イメージがまだない場合は、次の [docker pull][docker-pull] コマンドを実行して、既存のイメージを Docker Hub からプルします。 この例では、`hello-world` イメージをプルします。

```
docker pull hello-world
```

イメージをレジストリにプッシュするには、ACR ログイン サーバーの完全修飾名を使用して、そのイメージにタグを付けておく必要があります。 ログイン サーバー名は、 *\<registry-name\>.azurecr.io* (すべて小文字) という形式です。たとえば、*mycontainerregistry007.azurecr.io* などです。

[docker tag][docker-tag] コマンドを使用してイメージにタグ付けします。 `<acrLoginServer>` を ACR インスタンスのログイン サーバー名で置き換えます。

```
docker tag hello-world <acrLoginServer>/hello-world:v1
```

最後に、[docker push][docker-push] を使用して、ACR インスタンスにイメージをプッシュします。 `<acrLoginServer>` を ACR インスタンスのログイン サーバー名で置き換えます。 この例では、**hello-world** レポジトリを作成します。これには、`hello-world:v1` イメージが含まれています。

```
docker push <acrLoginServer>/hello-world:v1
```

コンテナー レジストリにイメージをプッシュした後は、ローカルの Docker 環境から `hello-world:v1` イメージを削除します (この [docker rmi][docker-rmi] コマンドは、Azure コンテナー リポジトリ内の **hello-world** リポジトリからイメージを削除しないことに注意してください)。

```
docker rmi <acrLoginServer>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->


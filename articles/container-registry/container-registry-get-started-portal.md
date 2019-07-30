---
title: クイック スタート - Azure でのプライベート Docker レジストリの作成 - Azure portal
description: Azure Portal を使用してプライベート Docker コンテナー レジストリを作成する方法を簡単に説明します。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: c9e8c7fe4d32a44e8c0831154f02eda1f82aaff3
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309481"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>クイック スタート:Azure portal を使用したプライベート コンテナー レジストリの作成

Azure Container Registry は、プライベート Docker コンテナー イメージを保存および管理する Azure のプライベート Docker レジストリです。 このクイックスタートでは、Azure Portal を使用してコンテナー レジストリを作成します。 次に、Docker コマンドを使用してコンテナー イメージをレジストリにプッシュし、最後にレジストリからイメージをプルして実行します。

レジストリにログインし、コンテナー イメージを操作するために、このクイック スタートでは、Azure CLI を実行している必要があります (バージョン 2.0.55 以降を推奨)。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

Docker もローカルにインストールする必要があります。 Docker では、[Mac][docker-mac], [Windows][docker-windows]、または [Linux][docker-linux] システムで Docker を簡単に構成できるパッケージが提供されています。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

**[リソースの作成]**  >  **[コンテナー]**  >  **[コンテナー レジストリ]** の順に選択します。

![Azure Portal でコンテナー レジストリを作成する][qs-portal-01]

**[レジストリ名]** と **[リソース グループ]** に値を入力します。 レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 このクイック スタートでは、`myResourceGroup` という名前の新しいリソース グループを `West US` に作成し、 **[SKU]** には [Basic] を選択します。 **[作成]** を選択して ACR インスタンスをデプロイします。

![Azure portal でコンテナー レジストリを作成する][qs-portal-03]

このクイック スタートでは、*Basic* レジストリを作成します。これは、Azure Container Registry について学習している開発者にとって、コストが最適なオプションです。 利用可能なサービス レベルの詳細については、[コンテナー レジストリの SKU][container-registry-skus] に関するページを参照してください。

**"デプロイメントに成功しました"** というメッセージが表示されたら、ポータルでコンテナー レジストリを選択します。 

![Azure portal でのコンテナー レジストリの概要][qs-portal-05]

**ログイン サーバー**の値を書き留めておきます。 後の手順で、Azure CLI および Docker を使用してレジストリを操作するときに、この値を使用します。

## <a name="log-in-to-registry"></a>レジストリへのログイン

コンテナー イメージをプッシュしたりプルしたりするには、あらかじめ ACR インスタンスにログインしておく必要があります。 オペレーティング システムでコマンド シェルを開き、Azure CLI で [az acr login][az-acr-login] コマンドを使用します。

```azurecli
az acr login --name <acrName>
```

このコマンドは、完了すると `Login Succeeded` を返します。 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>コンテナー イメージの一覧表示

レジストリ内のイメージを一覧表示するには、ポータルでレジストリに移動し、 **[リポジトリ]** を選択してから、`docker push` で作成したリポジトリを選択します。

この例では、**hello-world** リポジトリを選択したので、 **[タグ]** に `v1` タグが付いたイメージが表示されます。

![Azure portal でコンテナー イメージを一覧表示する][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソースをクリーンアップするには、ポータルで **myResourceGroup** リソース グループに移動します。 リソース グループが読み込まれたら、 **[リソース グループの削除]** をクリックして、リソース グループ、コンテナー レジストリ、およびそこに格納されているコンテナー イメージを削除します。

![Azure portal でリソース グループを削除する][qs-portal-08]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure portal を使って Azure Container Registry を作成し、コンテナー イメージをプッシュしてから、レジストリからイメージをプルして実行しました。 Azure Container Registry のチュートリアルに進んで、ACR についての理解を深めましょう。

> [!div class="nextstepaction"]
> [Azure Container Registry のチュートリアル][container-registry-tutorial-quick-task]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login

---
title: Azure Portal の Azure Container Registry リポジトリ
description: Azure Portal で Azure Container Registry リポジトリを表示する方法
services: container-registry
author: cristy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 685c978ff206e75d770918f2528a826ad522b706
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64710191"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Azure Portal でコンテナー レジストリ リポジトリを表示する

Azure Container Registry を使用すると、リポジトリに Docker コンテナー イメージを保存できます。 リポジトリにイメージを保存することで、別々の環境にイメージ (またはイメージのバージョン) のグループを配置できます。 これらのリポジトリは、レジストリにイメージをプッシュするときに指定できます。また、Azure Portal でそのコンテンツを確認できます。

## <a name="prerequisites"></a>前提条件

* **コンテナー レジストリ**:コンテナー レジストリを Azure サブスクリプションで作成します。 たとえば、[Azure Portal](container-registry-get-started-portal.md) または [Azure CLI](container-registry-get-started-azure-cli.md) を使用します。
* **Docker CLI**:[Docker][docker-install] をローカル コンピューターにインストールします。これで、Docker コマンド ライン インターフェイスを使用できるようになります。
* **コンテナー イメージ**:コンテナー レジストリにイメージをプッシュします。 イメージのプッシュ方法とプル方法については、[イメージのプッシュとプル](container-registry-get-started-docker-cli.md)に関するページを参照してください。

## <a name="view-repositories-in-azure-portal"></a>Azure Portal でリポジトリを表示する

Azure Portal には、イメージをホストするリポジトリの一覧とイメージ タグが表示されます。

[イメージのプッシュとプル](container-registry-get-started-docker-cli.md)に関するページの手順を実行し、その後にイメージを削除しなかった場合は、コンテナー レジストリに Nginx イメージが残っています。 その記事の手順では、イメージに名前空間 (`/samples/nginx` では "sample") のタグを付けるように指定されていました。 その記事で指定した [docker push][docker-push] コマンドを念のため紹介します。

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Azure Container Registry はこのようなマルチレベル リポジトリ名前空間をサポートしているため、特定のアプリまたはアプリのコレクションに関連するイメージのコレクションにスコープを指定して、さまざまな開発チームや運用チームに提供できます。 コンテナー レジストリ内のリポジトリの詳細については、[Azure でのプライベート Docker コンテナー レジストリ](container-registry-intro.md)に関する記事をご覧ください。

リポジトリを表示するには:

1. [Azure Portal][portal] にサインインします
1. Nginx イメージをプッシュした **Azure Container Registry** を選択します
1. **[リポジトリ]** を選択し、レジストリ内のイメージを含むリポジトリの一覧を表示します
1. リポジトリを選択し、リポジトリ内のイメージ タグを表示します

たとえば、[イメージのプッシュとプル](container-registry-get-started-docker-cli.md)に関するページの手順に従って Nginx イメージをプッシュした場合は、次のように表示されます。

![Portal でのリポジトリ](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>次の手順

ここでは、ポータルでリポジトリを表示し、使用する方法の基本を学んだので、Azure Container Registry と [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md) クラスターを使用してみてください。

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com

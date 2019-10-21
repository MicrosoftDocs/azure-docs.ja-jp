---
title: Azure Cache for Redis を Azure Spring Cloud アプリケーションにバインドする方法 | Microsoft Docs
description: Azure Cache for Redis を Azure Spring Cloud アプリケーションにバインドする方法について説明します
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d8fa31207baecc80674fb11b492927800676b8a2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038241"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>チュートリアル:Azure サービスを Azure Spring Cloud アプリケーションにバインドする: Azure Cache for Redis

Azure Spring Cloud では、Spring Boot アプリケーションを手動で構成するのではなく、選択した Azure サービスをアプリケーションに自動的にバインドすることができます。 この記事では、アプリケーションを Azure Cache for Redis にバインドする方法を示します。

## <a name="prerequisites"></a>前提条件

* デプロイ済みの Azure Spring Cloud インスタンス
* Azure Cache for Redis サービス インスタンス
* Azure CLI 用の Azure Spring Cloud 拡張機能

必要な場合は、次のコマンドを使用して Azure CLI 用の Azure Spring Cloud 拡張機能をインストールします。

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。  最新バージョンの Git、JDK、Maven、Azure CLI など、一般的な Azure ツールがプレインストールされています。 Azure サブスクリプションにログインしている場合は、shell.azure.com から [Azure Cloud Shell](https://shell.azure.com) を起動します。  Azure Cloud Shell の詳細については、[ドキュメントを参照](../cloud-shell/overview.md)してください

## <a name="bind-azure-cache-for-redis"></a>Azure Cache for Redis をバインドする

1. 次の依存関係を自分のプロジェクトの `pom.xml` に追加します

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. もしあれば、`application.properties` ファイルで `spring.redis.*` プロパティを削除します

1. `az spring-cloud app update` を使用して現在のデプロイを更新するか、`az spring-cloud app deployment create` を使用して新しいデプロイを作成します。

1. Azure portal で、自分のAzure Spring Cloud サービスのページに移動します。 **アプリケーション ダッシュボード**を探し、Azure Cache for Redis にバインドするアプリケーションを選択します。  これは、前の手順で更新またはデプロイしたのと同じアプリケーションです。 次に、[`Service binding`]\(サービスのバインド\) を選択し、[`Create service binding`]\(サービス バインドの作成\) ボタンを選択します。 **バインドの種類** `Azure Cache for Redis`、自分の Redis サーバー、主キーのオプションを必ず選択するようにして、フォームに入力します。 

1. アプリを再起動すると、このバインドが機能するようになります。

1. サービスのバインドが正常であることを確認するには、バインド名を選択し、その詳細を確認します。 `property` フィールドはこのようになります。
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Spring Cloud アプリケーションを Azure Redis キャッシュにバインドする方法について学習しました。  アプリケーションへのバインド サービスについて詳しく学習するには、MySQL DB へのアプリケーションのバインドに関するチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure MySql サービスを Azure Spring Cloud サービスにバインドする方法について学習する](spring-cloud-tutorial-bind-mysql.md)。
---
title: "Azure コンテナー レジストリによる認証 | Microsoft Docs"
description: "Azure Active Directory サービス プリンシパルまたは管理者アカウントを使用して Azure コンテナー レジストリにログインする方法"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 128a937a-766a-415b-b9fc-35a6c2f27417
ms.service: container-registry
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 774c844375df89864274e3376ed07dd565c8ecff
ms.lasthandoff: 03/27/2017

---
# <a name="authenticate-with-a-private-docker-container-registry"></a>プライベート Docker コンテナー レジストリによる認証
Azure コンテナー レジストリ内のコンテナー イメージを操作するには、`docker login` コマンドを使用してログインします。 **[Azure Active Directory サービス プリンシパル](../active-directory/active-directory-application-objects.md)**またはレジストリに固有の**管理者アカウント**を使用してログインできます。 この記事では、これらの ID について詳しく説明します。



## <a name="service-principal"></a>サービス プリンシパル

レジストリに[サービス プリンシパルを割り当て](container-registry-get-started-azure-cli.md#assign-a-service-principal)て、Docker の基本認証用に使用します。 ほとんどのシナリオに対しては、サービス プリンシパルの使用をお勧めします。 次の例に示すように、サービス プリンシパルのアプリ ID とパスワードを `docker login` コマンドに指定します。

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

いったんログインすると Docker によって資格情報がキャッシュに格納されるため、アプリ ID を覚えておく必要はありません。

> [!TIP]
> 必要に応じて、`az ad sp reset-credentials` コマンドを実行してサービス プリンシパルのパスワードを再生成することができます。
>


サービス プリンシパルを使用すると、レジストリへの[ロールベースのアクセス](../active-directory/role-based-access-control-configure.md)が可能になります。 使用可能なロールは、閲覧者 (プルのみアクセス)、共同作成者 (プルおよびプッシュ)、および所有者 (プル、プッシュ、および他のユーザーへのロールの割り当て) です。 サービス プリンシパルは 1 つのレジストリに対して複数個割り当てることができます。これにより、異なるユーザーまたはアプリケーションのアクセスを定義できます。 また、サービス プリンシパルを使用すると、次のような開発者または DevOps のシナリオでレジストリへの "ヘッドレス" 接続を可能にします。

  * レジストリから DC/OS、Docker Swarm、Kubernetes などのオーケストレーション システムへのコンテナーのデプロイ。 さらに、[Container Service](../container-service/index.md)、[App Service](../app-service/index.md)、[Batch](../batch/index.md)、[Service Fabric](../service-fabric/index.md) などの関連する Azure サービスにコンテナー レジストリをプルすることもできます。

  * コンテナー イメージを作成してレジストリにプッシュする継続的インテグレーションおよびデプロイ ソリューション (Visual Studio Team Services、Jenkins など)。





## <a name="admin-account"></a>管理者アカウント
ユーザーが作成するレジストリごとに管理者アカウントが自動的に作成されます。 既定でこのアカウントは無効になっていますが、[ポータル](container-registry-get-started-portal.md#manage-registry-settings)や [Azure CLI 2.0 コマンド](container-registry-get-started-azure-cli.md#manage-admin-credentials)などを使ってこのアカウントを有効にし、資格情報を管理できます。 アカウントが有効になっている場合、ユーザー名とパスワードを `docker login` コマンドに渡してレジストリに対する基本認証を行うことができます。 次に例を示します。

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword
```

> [!IMPORTANT]
> 管理者アカウントは、1 人のユーザーが主にテスト目的でレジストリにアクセスするように設計されています。 管理者アカウントの資格情報を複数のユーザーで共有することはお勧めしません。 すべてのユーザーはレジストリに対して 1 人のユーザーとして表示されます。 このアカウントを変更または無効にすると、この資格情報を使用するすべてのユーザーのレジストリ アクセスが無効になります。
>


### <a name="next-steps"></a>次のステップ
* [Docker CLI を使用した最初のイメージのプッシュ](container-registry-get-started-docker-cli.md)。
* Container Registry プレビューでの認証の詳細については、[ブログの投稿](https://blogs.msdn.microsoft.com/stevelasker/2016/11/17/azure-container-registry-user-accounts/)を参照してください。


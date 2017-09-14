---
title: "Azure Service Fabric CLI を使用した Azure Service Fabric アプリケーションの管理"
description: "Azure Service Fabric CLI を使用して Azure Service Fabric クラスターにアプリケーションをデプロイまたは Azure Service Fabric クラスターからアプリケーションを削除する方法について説明します。"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 08/22/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: c3a2eb3e6e54f952ef963bb2a0292d9ad7b53bc5
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli"></a>Azure Service Fabric CLI を使用した Azure Service Fabric アプリケーションの管理

Azure Service Fabric クラスターで実行されているアプリケーションを作成および削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

* Service Fabric CLI をインストールします。 次に、Service Fabric クラスターを選択します。 詳しくは、[Service Fabric CLI の概要](service-fabric-cli.md)に関する記事をご覧ください。

* Service Fabric アプリケーション パッケージをデプロイできるように準備します。 アプリケーションを作成してパッケージ化する方法について詳しくは、[Service Fabric アプリケーション モデル](service-fabric-application-model.md)に関する記事をご覧ください。

## <a name="overview"></a>概要

新しいアプリケーションをデプロイするには、次の手順を実行します。

1. アプリケーション パッケージを Service Fabric イメージ ストアにアップロードします。
2. アプリケーションの種類をプロビジョニングします。
3. アプリケーションを指定して作成します。
4. サービスを指定して作成します。

既存のアプリケーションを削除するには、次の手順を実行します。

1. アプリケーションを削除します。
2. 関連付けられているアプリケーションの種類のプロビジョニングを解除します。
3. イメージ ストアのコンテンツを削除します。

## <a name="deploy-a-new-application"></a>新しいアプリケーションのデプロイ

新しいアプリケーションをデプロイするには、次のタスクを実行します。

### <a name="upload-a-new-application-package-to-the-image-store"></a>新しいアプリケーション パッケージをイメージ ストアにアップロードする

アプリケーションを作成する前に、アプリケーション パッケージを Service Fabric イメージ ストアにアップロードします。

たとえば、アプリケーション パッケージが `app_package_dir` ディレクトリにある場合は、次のコマンドを使用してディレクトリをアップロードします。

```azurecli
sfctl application upload --path ~/app_package_dir
```

大規模なアプリケーション パッケージの場合は、`--show-progress` オプションを指定するとアップロードの進行状況を確認できます。

### <a name="provision-the-application-type"></a>アプリケーションの種類をプロビジョニングする

アップロードが完了したら、アプリケーションをプロビジョニングします。 アプリケーションをプロビジョニングするには、次のコマンドを使用します。

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

`application-type-build-path` の値は、アプリケーション パッケージをアップロードしたディレクトリの名前です。

### <a name="create-an-application-from-an-application-type"></a>アプリケーションの種類からアプリケーションを作成する

アプリケーションがプロビジョニングされたら、次のコマンドを使用して、アプリケーションに名前を付けて作成できます。

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` はアプリケーション インスタンスに対して使用する名前です。 追加のパラメーターは、以前にプロビジョニングしたアプリケーション マニフェストから取得できます。

アプリケーション名の先頭にはプレフィックス `fabric:/` が必要です。

### <a name="create-services-for-the-new-application"></a>新しいアプリケーションのサービスを作成する

アプリケーションを作成したら、そのアプリケーションからサービスを作成します。 次の例では、アプリケーションから新しいステートレス サービスを作成します。 アプリケーションから作成できるサービスは、プロビジョニング済みのアプリケーション パッケージ内のサービス マニフェストで定義されています。

```azurecli
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>アプリケーションのデプロイと正常性を確認する

すべてが正常な状態にあることを確認するには、次に示す正常性のコマンドを使用します。

```azurecli
sfctl application list
sfctl service list --application-id TestApp
```

サービスが正常な状態にあることを確認するには、同様のコマンドを使用して、サービスとアプリケーションの両方の正常性を取得します。

```azurecli
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

サービスとアプリケーションが正常な状態であれば、`HealthState` の値が `Ok` になっています。

## <a name="remove-an-existing-application"></a>既存のアプリケーションの削除

アプリケーションを削除するには、次のタスクを実行します。

### <a name="delete-the-application"></a>アプリケーションを削除する

アプリケーションを削除するには、次のコマンドを使用します。

```azurecli
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>アプリケーションの種類のプロビジョニングを解除する

アプリケーションを削除したら、不要になったアプリケーションの種類のプロビジョニングを解除できます。 アプリケーションの種類のプロビジョニングを解除するには、次のコマンドを実行します。

```azurecli
sfctl application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

指定するアプリケーションの種類の名前とバージョンは、以前にプロビジョニングしたアプリケーション マニフェストにある名前およびバージョンと一致している必要があります。

### <a name="delete-the-application-package"></a>アプリケーション パッケージを削除する

アプリケーションの種類のプロビジョニングを解除したら、不要になったアプリケーション パッケージをイメージ ストアから削除できます。 アプリケーション パッケージを削除すると、ディスク領域が解放されます。 

アプリケーション パッケージをイメージ ストアから削除するには、次のコマンドを使用します。

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path` はアプリケーションを作成したときにアップロードしたディレクトリの名前である必要があります。

## <a name="upgrade-application"></a>アプリケーションのアップグレード

アプリケーションを作成した後で、同じ手順を繰り返すと、アプリケーションの 2 番目のバージョンをプロビジョニングできます。 次に、Service Fabric アプリケーションのアップグレードにより、アプリケーションの 2 番目のバージョンの実行に移ることができます。 詳しくは、「[Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md)」をご覧ください。

アップグレードを実行するには、まず、前と同じコマンドを使用してアプリケーションの次のバージョンをプロビジョニングします。

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
```

その後、監視付きの自動アップグレードを実行することをお勧めします。次のコマンドを実行してアップグレードを行います。

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

アップグレードでは、指定されているセットによって既存のパラメーターが上書きされます。 必要な場合には、アプリケーション パラメーターを引数としてアップグレード コマンドに渡してください。 アプリケーション パラメーターは JSON オブジェクトとしてエンコードする必要があります。

以前指定したパラメーターを取得するには、`sfctl application info` コマンドを使用できます。

アプリケーションのアップグレードが進行しているときは、`sfctl application upgrade-status` コマンドを使用して状態を取得できます。

また、進行中のアップグレードをキャンセルする必要がある場合は、`sfctl application upgrade-rollback` を使用してアップグレードをロールバックできます。

## <a name="next-steps"></a>次のステップ

* [Service Fabric CLI の基本](service-fabric-cli.md)
* [Linux 上の Service Fabric の概要](service-fabric-get-started-linux.md)
* [Service Fabric アプリケーションのアップグレードの開始](service-fabric-application-upgrade.md)


---
title: Pivotal Cloud Foundry 上のアプリケーションで Open Service Broker for Azure データベースを使用する
author: zr-msft
manager: jeconnoc
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 01/11/2019
ms.topic: tutorial
description: Open Service Broker for Azure データベースを使用するように Pivotal Cloud Foundry アプリケーションを構成する方法について説明します。
keywords: Pivotal Cloud Foundry, Cloud Foundry, Open Service Broker, Open Service Broker for Azure
tags: Cloud-Foundry
ms.openlocfilehash: d553cd09ef42e47e3a10fb96039063b8aae665cb
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258875"
---
# <a name="tutorial-use-an-open-service-broker-for-azure-database-with-an-application-on-pivotal-cloud-foundry"></a>チュートリアル:Pivotal Cloud Foundry 上のアプリケーションで Open Service Broker for Azure データベースを使用する

Pivotal Cloud Foundry インスタンスと共に Open Service Broker for Azure を使用すると、Cloud Foundry CLI やお客様の Pivotal Cloud Foundry インスタンスから直接 Azure にサービス (データベースなど) をプロビジョニングできます。 また、お客様の Pivotal Cloud Foundry インスタンスで実行されているアプリケーションにそれらのサービスをバインドすることもできます。 この方法でアプリケーションをサービスにバインドすれば、お客様のアプリケーション内のコードや構成を更新する必要はありません。 この記事では、Pivotal Cloud Foundry 内のアプリケーションと連携するデータベースに Open Service Broker for Azure サービスを使用する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Pivotal Cloud Foundry にアプリケーション スペースを準備する。
> * GitHub からサンプル アプリケーション ソースを複製する。
> * デプロイするアプリケーションを準備する。
> * アプリケーションをデプロイします。
> * Open Service Broker for Azure を使用してデータベースを作成する。
> * データベースをアプリケーションにバインドする。

## <a name="prerequisites"></a>前提条件

次に進む前に、以下を満たしておく必要があります。

* [Pivotal Cloud Foundry のインストールと構成が済んでいること](create-cloud-foundry-on-azure.md)
* お客様の Cloud Foundry インスタンスと共に [Open Service Broker for Azure のインストールと構成が済んでいること](https://network.pivotal.io/products/azure-open-service-broker-pcf)

以下に示すのは、Pivotal Cloud Foundry Ops Manager 画面の例です。Open Service Broker for Azure のタイルがインストールされ、構成が済んでいます。

![Pivotal Cloud Foundry と Open Service Broker for Azure がインストールされている](media/pcf-ops-manager.png)

## <a name="prepare-your-application-space-in-pivotal-cloud-foundry"></a>Pivotal Cloud Foundry にアプリケーション スペースを準備する

お客様の Pivotal Cloud Foundry インスタンスにお客様のアプリケーションをデプロイするためには、`cf` コマンドライン ツールでログインする必要があります。 また、目的の組織とスペースをターゲットにする必要があります。

ログインしていることを確認し、ターゲットにしているスペースを表示するには、`cf target` を使用します。 以下の例では、ユーザーが既に *admin* としてログインしています。組織として *myorg* が、ターゲットとして *dev* スペースが使用されています。

```cmd
$ cf target

api endpoint:   https://api.system.40.85.111.222.cf.pcfazure.com
api version:    2.120.0
user:           admin
org:            myorg
space:          dev
```

サインインするには、`cf login` を使用します。

```cmd
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

新しい組織とスペースを作成するには、`cf create-org` と `cf create-space` を使用します。

```cmd 
cf create-org myorg
cf create-space dev -o myorg
```

スペースをターゲットにするには、`cf target` を使用します。

```cmd
cf target -o myorg -s dev
```

## <a name="get-application-code"></a>アプリケーションのコードを入手する

アプリケーションによって Open Service Broker for Azure が使用されるには、そのアプリケーションでデータソース (データベースなど) として使用される必要があります。 この記事では、[Cloud Foundry の Spring Music サンプル アプリケーション](https://github.com/cloudfoundry-samples/spring-music)を使って、データソースが使用されるアプリケーションのデモを行います。

GitHub からアプリケーションを複製して、そのディレクトリに移動します。

```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring music
```

> [!TIP]
> このアプリケーションのデータソースを確認するには、[src/main/resources/application.yml](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/application.yml) ファイルを開いてください。


## <a name="prepare-the-application-for-deployment"></a>デプロイするアプリケーションを準備する

お客様の Pivotal Cloud Foundry インスタンスにアプリケーションをデプロイするには、それをビルドする必要があります。 デモという目的上、いくつかの *DEBUG* ログも有効にします。これによってデータソースの接続情報がログに記録されます。

データベース接続情報に関する *DEBUG* ログを有効にするには、*application.yml* の最後に、次の yaml プロパティを追加します。

```yaml
---
logging:
  level:
    com.zaxxer.hikari: DEBUG
```

サンプル アプリケーションでは、Gradle を使用してアプリケーションを Spring Boot の実行可能 jar にビルドします。 お客様の Pivotal Cloud Foundry インスタンスには、この実行可能 jar をデプロイすることになります。 アプリケーションをビルドするには:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```


## <a name="deploy-your-application"></a>アプリケーションをデプロイする

アプリケーションをお客様の Pivotal Cloud Foundry インスタンスにデプロイするには、`cf push` コマンドを使用します。

```cmd
cf push

Pushing from manifest to org myorg / space dev as admin...
Using manifest file /path/to/spring-music/manifest.yml
Getting app info...
Creating app with these attributes...
+ name:       spring-music
...
Waiting for app to start...

name:              spring-music
requested state:   started
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
     state     since                  cpu    memory         disk           details
#0   running   2018-12-06T21:24:06Z   0.0%   313.3M of 1G   170.7M of 1G
```

`cf push` の出力に表示された *routes* の値をコピーします。 このルートは、実行中のアプリケーションにアクセスするために使用する URL です。 例: 

```cmd
...
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
```


お客様のアプリケーションがデプロイされたら、アプリケーションのログを見て、アプリケーションによって使用された接続 URL を確認できます。 以下のコマンドでは、アプリケーションのログを表示すると共に、`grep` を使用して、*jdbcUrl* 構成を検索しています。

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
```

アプリケーションで *h2:mem:testdb* が使用されていることに注目してください。これはメモリ内データベースです。 メモリ内データベースの依存関係が classpath 上に存在し、なおかつ[自動構成](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-auto-configuration.html)が有効になっている場合、Spring アプリケーションは自動的に、メモリ内データベースを使用するように構成されます。 サンプル アプリケーションには [h2 というメモリ内データベースの依存関係が構成](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L49)されており、また、[src/main/java/org/cloudfoundry/samples/music/Application.java](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/java/org/cloudfoundry/samples/music/Application.java#L8) で自動構成が有効になっています。

アプリケーションのルートを使用して、ブラウザーでそこに移動します。 ルート (URL) は、`cf push` コマンドの出力に表示されます。

> [!TIP]
> `cf apps` を実行することによって、アプリケーションの URL を表示することもできます。

ブラウザーを使用してアプリケーションに移動したら、既にあるいくつかのアルバムを削除したり新しいものを作成したりして、それを操作してみましょう。 サンプル アプリケーションでは、お客様の変更の保存にメモリ内データベースを使用しています。 また、アプリケーションには、いくつかの[既定のデータ](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json)が事前設定されていることがわかります。 

![Spring Music アプリと既定のデータ](media/music-app.png)

お客様のアプリケーションで使用されているのはメモリ内データベースであるため、お客様のアプリケーションを再起動したり再デプロイしたりした場合、変更は保持されません。 変更が保持されないことを確認するために、いくつか変更を加えた後、`cf restage` を使用してお客様のアプリケーションを再ステージングしてみましょう。

```cmd
cf restage spring-music
```

アプリケーションの再ステージング後、ブラウザーで同じ URL を使用してそこに移動します。 先ほど行った変更が失われて、既定のデータが表示されることがわかります。

![Spring Music アプリと既定のデータ](media/music-app.png)

## <a name="create-a-database"></a>データベースを作成する

Open Service Broker を使用して Azure に永続的なデータベースを作成するには、`cf create-service` コマンドを使用します。 以下のコマンドでは、Azure の *eastus* リージョンの *MyResourceGroup* リソース グループに PostgreSQL データベースをプロビジョニングします。 *resourceGroup* や *location* など、Azure 固有の JSON パラメーターについて詳しくは、[PostgreSQL モジュールのリファレンス ドキュメント](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md#provision)を参照してください。

```cmd
cf create-service azure-postgresql-9-6 general-purpose mypgsql -c '{"resourceGroup":"MyResourceGroup", "location":"eastus"}'
```

データベースは、*mypgsql* という名前のサービスとしてお客様の Pivotal Cloud Foundry インスタンスに公開されます。 お客様のデータベースのプロビジョニング (数分かかります) が済んだら、お客様のアプリケーションをそこにバインドすることができます。 お客様のデータベースのプロビジョニングが完了したことを確認するには、`cf services` コマンドを使用します。

```cmd
cf services

Getting services in org myorg / space dev as admin...

name      service                plan              bound apps     last operation
mypgsql   azure-postgresql-9-6   general-purpose                  create succeeded
```

プロビジョニングが完了していれば、お客様のサービスの *last operation* 値が *create succeeded* になります。

## <a name="bind-the-database-to-your-application"></a>データベースをアプリケーションにバインドする

お客様のアプリケーションにサービスをバインドするには、`bind-service` コマンドを使用します。

```cmd
cf bind-service spring-music mypgsql
```

変更を有効にするためには、お客様のアプリケーションにサービスをバインドした後、アプリケーションを再ステージングする必要があります。

```cmd
cf restage spring-music
```

アプリケーションは [Spring Cloud Connectors を使用するように構成](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L45...L46)されています。そのため、お客様の Pivotal Cloud Foundry インスタンスで、お客様のアプリケーションのデータソースに対して[自動再構成](https://docs.cloudfoundry.org/buildpacks/java/configuring-service-connections/spring-service-bindings.html#auto)を使用できます。 このケースでは、お客様のアプリケーションが再ステージングされたときに、そのバインド先のサービスをデータソースに使用するよう、お客様の Pivotal Cloud Foundry インスタンスによってアプリケーションが自動的に再構成されます。

再ステージングされたお客様のアプリケーションでは、メモリ内データベースではなく *mypgsql* を使用してデータが格納されるようになります。

これから行う変更はすべて、再起動後や再デプロイ後も保持されます。 さらに、再度アプリケーションのログを見れば、アプリケーションによって使用されている接続 URL を確認することができます。

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
2018-12-07T14:48:58.10-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:48:58.107 DEBUG 16 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:postgresql://12345678-aaaa-bbbb-cccc-1234567890ab.postgres.database.azure.com:5432/123456789?user=aabbcc1122@12345678-aaaa-bbbb-cccc-1234567890ab&password=<masked>&&sslmode=require
```

次の 2 つのエントリがあることに注目してください。

* 元の *h2:mem:testdb* の値。
* お客様のアプリケーションが再ステージングされたときに更新されたお客様の PostgreSQL データベースの値。

PostgreSQL データベースにデータが保持されていることを確認するために、ブラウザーでアプリケーションに移動し、何か変更を加えた後、お客様のアプリケーションを再ステージングしてみましょう。

```cmd
cf restage spring-music
```

アプリケーションの再ステージング後、ブラウザーで同じ URL を使用してそこに移動します。 行った変更が保持されていることがわかります。

## <a name="cleanup"></a>クリーンアップ

お客様のアプリケーションをデータベースから切断したい場合、`cf unbind-service` コマンドを使用してそれをバインド解除できます。

```cmd
cf unbind-service spring-music mypgsql
```

お客様のアプリケーションをサービスにバインドする際と同様に、この変更を有効にするためには、お客様のアプリケーションを再ステージングする必要があります。 この操作を実行しても、*mypgsql* とお客様のアプリケーションはいずれも変更されません。ただし、お客様のアプリケーションでは、*mypgsql* ではなくメモリ内データベースが使用されるようになります。

お客様のデータベースを削除するには、`cf delete-service` コマンドを使用します。 "*この操作は元に戻すことはできませんので、本当にデータベースを削除してよいか確認したうえで続行してください。*"

```cmd
cf delete-service mypgsql
```

お客様の Pivotal Cloud Foundry インスタンスからアプリケーションを削除するには:
 
```cmd
cf delete spring-music
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Pivotal Cloud Foundry へのアプリケーションのデプロイと、Open Service Broker for Azure を使用したデータベースの作成について説明しました。 また、お客様の Pivotal Cloud Foundry インスタンス内でデータベースをアプリケーションにバインドする方法について説明ました。 Azure 上の Cloud Foundry にアプリケーションをデプロイする方法の詳細については、以下を参照してください。

* [Azure での Cloud Foundry](../virtual-machines/linux/cloudfoundry-get-started.md)
* [Microsoft Azure の Cloud Foundry に最初のアプリをデプロイする](../virtual-machines/linux/cloudfoundry-deploy-your-first-app.md)
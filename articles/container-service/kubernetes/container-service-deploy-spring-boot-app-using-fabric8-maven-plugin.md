---
title: "fabric8 Maven プラグインを使用して Spring Boot アプリをデプロイする"
description: "このチュートリアルでは、Microsoft Azure で Apache Maven 用 Fabric8 プラグインを使用して Spring Boot アプリケーションをデプロイする方法について説明します。"
services: container-service
documentationcenter: java
author: rmcmurray
manager: routlaw
editor: 
ms.assetid: 
ms.service: container-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 09/15/2017
ms.author: yuwzho;robmcm
ms.custom: 
ms.openlocfilehash: d368e71866406f6011c5cfa75eba13461e8e4ca4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-spring-boot-app-using-the-fabric8-maven-plugin"></a>Fabric8 Maven プラグインを使用して Spring Boot アプリをデプロイする

**[Fabric8]** は **[Kubernetes]** 上に構築されたオープン ソース ソリューションであり、開発者が Linux コンテナーでアプリケーションを作成するために役立ちます。

このチュートリアルでは、Maven 用 Fabric8 プラグインを使用してアプリケーションを開発し、[Azure Container Service (ACS)] の Linux ホストにデプロイする手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を完了するには、次の前提条件を満たす必要があります。

* Azure サブスクリプション。Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典]を有効にするか、または[無料の Azure アカウント]にサインアップできます。
* [Azure コマンド ライン インターフェイス (CLI)]。
* 最新の [Java Developer Kit (JDK)]。
* Apache の [Maven] 構築ツール (バージョン 3)。
* [Git] クライアント。
* [Docker] クライアント。

> [!NOTE]
>
> このチュートリアルには仮想化要件があるため、仮想マシンでこの記事の手順を実行することはできません。仮想化機能を有効にした物理コンピューターを使用する必要があります。
>

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Spring Boot on Docker Getting Started Web アプリを作成する

次の手順で、Spring Boot Web アプリケーションをビルドしてローカルでテストします。

1. コマンド プロンプトを開き、アプリケーションを保持するためのローカル ディレクトリを作成し、そのディレクトリに変更します。次に例を示します。
   ```shell
   md /home/GenaSoto/SpringBoot
   cd /home/GenaSoto/SpringBoot
   ```
   -- または --
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```

1. [Spring Boot on Docker Getting Started] サンプル プロジェクトを、ディレクトリに複製します。
   ```shell
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. 完成したプロジェクトにディレクトリを変更します。次に例を示します。
   ```shell
   cd gs-spring-boot-docker/complete
   ```
   -- または --
   ```shell
   cd gs-spring-boot-docker\complete
   ```

1. Maven を使用してサンプル アプリをビルドして実行します。
   ```shell
   mvn clean package spring-boot:run
   ```

1. http://localhost:8080 を参照するか次の `curl` コマンドを使用して、Web アプリをテストします。
   ```shell
   curl http://localhost:8080
   ```

   「**Hello Docker World**」というメッセージが表示されるはずです。

   ![サンプル アプリケーションをローカルで参照する][SB01]


## <a name="install-the-kubernetes-command-line-interface-and-create-an-azure-resource-group-using-the-azure-cli"></a>Kubernetes コマンド ライン インターフェイスをインストールし、Azure CLI を使用して Azure リソース グループを作成します

1. コマンド プロンプトを開きます。

1. 次のコマンドを入力して、Azure アカウントにログインします。
   ```azurecli
   az login
   ```
   指示に従って、ログインを完了します
   
   次のように、Azure CLI にアカウントの一覧が表示されます。

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "00000000-0000-0000-0000-000000000000",
       "isDefault": false,
       "name": "Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "00000000-0000-0000-0000-000000000000",
       "user": {
         "name": "Gena.Soto@wingtiptoys.com",
         "type": "user"
       }
     }
   ]
   ```

1. まだ Kubernetes コマンド ライン インターフェイス (`kubectl`) がインストールされていない場合、次のように、Azure CLI を使用してインストールできます。
   ```azurecli
   az acs kubernetes install-cli
   ```

   > [!NOTE]
   >
   > Kubernetes CLI は `/usr/local/bin` にデプロイされるため、Linux ユーザーはこのコマンドの前に `sudo` を付けなければならない場合があります。
   >
   > `kubectl` がもうインストールされていて `kubectl` のバージョンが古すぎる場合、この記事の後半で説明する手順を完了しようとすると、次の例のようなエラー メッセージが表示される場合があります。
   >
   > ```
   > error: group map[autoscaling:0x0000000000 batch:0x0000000000 certificates.k8s.io
   > :0x0000000000 extensions:0x0000000000 storage.k8s.io:0x0000000000 apps:0x0000000
   > 000 authentication.k8s.io:0x0000000000 policy:0x0000000000 rbac.authorization.k8
   > s.io:0x0000000000 federation:0x0000000000 authorization.k8s.io:0x0000000000 comp
   > onentconfig:0x0000000000] is already registered
   > ```
   >
   > この場合、`kubectl` を再インストールしてバージョンを更新する必要があります。
   >

1. 次のように、このチュートリアルで使用する Azure リソースのリソース グループを作成します。
   ```azurecli
   az group create --name=wingtiptoys-kubernetes --location=westeurope
   ```
   各値の説明:  
      * *wingtiptoys-kubernetes* は、リソース グループの一意な名前です  
      * *westeurope* は、アプリケーションの適切な地理的な場所です  

   次のように、Azure CLI に、リソース グループ作成の結果が表示されます。  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes",
     "location": "westeurope",
     "managedBy": null,
     "name": "wingtiptoys-kubernetes",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```


## <a name="create-a-kubernetes-cluster-using-the-azure-cli"></a>Azure CLI を使用して Kubernetes クラスターを作成する

1. 次のように、新しいリソース グループ内に Kubernetes クラスターを作成します。  
   ```azurecli 
   az acs create --orchestrator-type kubernetes --resource-group wingtiptoys-kubernetes --name wingtiptoys-cluster --generate-ssh-keys --dns-prefix=wingtiptoys
   ```
   各値の説明:  
      * *wingtiptoys-kubernetes* は、この記事の前半のリソース グループの名前です  
      * *wingtiptoys-cluster* は、Kubernetes クラスターの一意な名前です
      * *wingtiptoys* は、アプリケーションの一意な DNS 名です

   次のように、Azure CLI にクラスター作成の結果が表示されます。  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.Resources/deployments/azurecli0000000000.00000000000",
     "name": "azurecli0000000000.00000000000",
     "properties": {
       "correlationId": "00000000-0000-0000-0000-000000000000",
       "debugSetting": null,
       "dependencies": [],
       "mode": "Incremental",
       "outputs": {
         "masterFQDN": {
           "type": "String",
           "value": "wingtiptoysmgmt.westeurope.cloudapp.azure.com"
         },
         "sshMaster0": {
           "type": "String",
           "value": "ssh azureuser@wingtiptoysmgmt.westeurope.cloudapp.azure.com -A -p 22"
         }
       },
       "parameters": {
         "clientSecret": {
           "type": "SecureString"
         }
       },
       "parametersLink": null,
       "providers": [
         {
           "id": null,
           "namespace": "Microsoft.ContainerService",
           "registrationState": null,
           "resourceTypes": [
             {
               "aliases": null,
               "apiVersions": null,
               "locations": [
                 "westeurope"
               ],
               "properties": null,
               "resourceType": "containerServices"
             }
           ]
         }
       ],
       "provisioningState": "Succeeded",
       "template": null,
       "templateLink": null,
       "timestamp": "2017-09-15T01:00:00.000000+00:00"
     },
     "resourceGroup": "wingtiptoys-kubernetes"
   }
   ```

1. 次のようにして、新しい Kubernetes クラスターの資格情報をダウンロードします。  
   ```azurecli 
   az acs kubernetes get-credentials --resource-group=wingtiptoys-kubernetes --name wingtiptoys-cluster
   ```

1. 次のコマンドで接続を検証します。
   ```shell 
   kubectl get nodes
   ```

   次の例のように、ノードと状態の一覧が表示されるはずです。

   ```shell
   NAME                    STATUS                     AGE       VERSION
   k8s-agent-00000000-0    Ready                      5h        v1.6.6
   k8s-agent-00000000-1    Ready                      5h        v1.6.6
   k8s-agent-00000000-2    Ready                      5h        v1.6.6
   k8s-master-00000000-0   Ready,SchedulingDisabled   5h        v1.6.6
   ```


## <a name="create-a-private-azure-container-registry-using-the-azure-cli"></a>Azure CLI を使用してプライベート Azure コンテナー レジストリを作成する

1. Docker イメージをホストするために、プライベート Azure コンテナー レジストリをリソース グループ内に作成します。次に例を示します。
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoys-kubernetes --location westeurope --name wingtiptoysregistry --sku Basic
   ```
   各値の説明:  
      * *wingtiptoys-kubernetes* は、この記事の前半のリソース グループの名前です  
      * *wingtiptoysregistry* は、プライベート レジストリの一意な名前です
      * *westeurope* は、アプリケーションの適切な地理的な場所です  

   次のように、Azure CLI にレジストリ作成の結果が表示されます。  

   ```json
   {
     "adminUserEnabled": true,
     "creationDate": "2017-09-15T01:00:00.000000+00:00",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.ContainerRegistry/registries/wingtiptoysregistry",
     "location": "westeurope",
     "loginServer": "wingtiptoysregistry.azurecr.io",
     "name": "wingtiptoysregistry",
     "provisioningState": "Succeeded",
     "resourceGroup": "wingtiptoys-kubernetes",
     "sku": {
       "name": "Basic",
       "tier": "Basic"
     },
     "storageAccount": {
       "name": "wingtiptoysregistr000000"
     },
     "tags": {},
     "type": "Microsoft.ContainerRegistry/registries"
   }
   ```

1. Azure CLI からコンテナー レジストリのパスワードを取得します。
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```

   次のように、Azure CLI にレジストリのパスワード結果が表示されます。  

   ```json
   {
     "name": "password",
     "value": "AbCdEfGhIjKlMnOpQrStUvWxYz"
   }
   ```

1. Maven インストールの構成ディレクトリ (既定では ~/.m2/ または C:\Users\username\.m2) に移動し、*settings.xml* ファイルをテキスト エディターで開きます。

1. *settings.xml* ファイルの新しい `<server>` コレクションに Azure Container Registry の URL、ユーザー名、およびパスワードを追加します。
`id` と `username` がレジストリの名前になります。 前のコマンドで取得した `password` の値を (引用符なしで) 使用します。

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry.azurecr.io</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

1. Spring Boot アプリケーションの完了プロジェクト ディレクトリ ("*C:\SpringBoot\gs-spring-boot-docker\complete*" や "*/home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete*" など) に移動し、*pom.xml* ファイルをテキスト エディターで開きます。

1. *pom.xml*ファイル内の `<properties>` コレクションを、Azure Container Registry のログイン サーバーの値で更新します。

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. *pom.xml*ファイル内の `<plugins>` コレクションを、`<plugin>` にログイン サーバーのアドレスと Azure Container Registry のレジストリ名が含まれるように更新します。

   ```xml
   <plugin>
     <groupId>com.spotify</groupId>
     <artifactId>dockerfile-maven-plugin</artifactId>
     <version>1.3.4</version>
     <configuration>
       <repository>${docker.image.prefix}/${project.artifactId}</repository>
       <serverId>${docker.image.prefix}</serverId>
       <registryUrl>https://${docker.image.prefix}</registryUrl>
     </configuration>
   </plugin>
   ```

1. Spring Boot アプリケーション用の完了プロジェクト ディレクトリに移動し、次の Maven コマンドを実行して Docker コンテナーを作成し、そのイメージをレジストリにプッシュします。

   ```shell
   mvn package dockerfile:build -DpushImage
   ```

   次のように、Maven にビルドの結果が表示されます。  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 38.113 s
   [INFO] Finished at: 2017-09-15T02:00:00-07:00
   [INFO] Final Memory: 47M/338M
   [INFO] ----------------------------------------------------
   ```


## <a name="configure-your-spring-boot-app-to-use-the-fabric8-maven-plugin"></a>Fabric8 Maven プラグインを使用するように Spring Boot アプリを構成する

1. Spring Boot アプリケーションの完了プロジェクト ディレクトリ ("*C:\SpringBoot\gs-spring-boot-docker\complete*" や "*/home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete*" など) に移動し、*pom.xml* ファイルをテキスト エディターで開きます。

1. *pom.xml* ファイル内の `<plugins>` コレクションを更新して、Fabric8 Maven プラグインを追加します。

   ```xml
   <plugin>
     <groupId>io.fabric8</groupId>
     <artifactId>fabric8-maven-plugin</artifactId>
     <version>3.5.30</version>
     <configuration>
       <ignoreServices>false</ignoreServices>
       <registry>${docker.image.prefix}</registry>
     </configuration>
   </plugin>
   ```

1. Spring Boot アプリケーションのメイン ソース ディレクトリ ("*C:\SpringBoot\gs-spring-boot-docker\complete\src\main*" や "*/home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete/src/main*" など) に移動し、"*fabric8*" という名前の新しいフォルダーを作成します。

1. 新しい *fabric8* フォルダー内に 3 つの YAML フラグメント ファイルを作成します。

   a. **deployment.yml** という名前のファイルを作成し、内容を次のようにします。
      ```yaml
      apiVersion: extensions/v1beta1
      kind: Deployment
      metadata:
        name: ${project.artifactId}
        labels:
          run: gs-spring-boot-docker
      spec:
        replicas: 1
        selector:
          matchLabels:
            run: gs-spring-boot-docker
        strategy:
          rollingUpdate:
            maxSurge: 1
            maxUnavailable: 1
          type: RollingUpdate
        template:
          metadata:
            labels:
              run: gs-spring-boot-docker
          spec:
            containers:
            - image: ${docker.image.prefix}/${project.artifactId}:latest
              name: gs-spring-boot-docker
              imagePullPolicy: Always
              ports:
              - containerPort: 8080
            imagePullSecrets:
              - name: mysecrets
      ```

   b. **secrets.yml** という名前のファイルを作成し、内容を次のようにします。
      ```yaml
      apiVersion: v1
      kind: Secret
      metadata:
        name: mysecrets
        namespace: default
        annotations:
          maven.fabric8.io/dockerServerId:  ${docker.image.prefix}
      type: kubernetes.io/dockercfg
      ```

   c. **service.yml** という名前のファイルを作成し、内容を次のようにします。
      ```yaml
      apiVersion: v1
      kind: Service
      metadata:
        name: ${project.artifactId}
        labels:
          expose: "true"
      spec:
        ports:
        - port: 80
          targetPort: 8080
        type: LoadBalancer
      ```

1. 次の Maven コマンドを実行して、Kubernetes リソース リスト ファイルを構築します。

   ```shell
   mvn fabric8:resource
   ```

   このコマンドは、*src/main/fabric8* フォルダー下のすべての Kubernetes リソース yaml ファイルを、Kubernetes リソース リストを含む YAML ファイルにマージします。このマージ後のファイルを Kubernetes クラスターに直接適用、または Helm グラフにエクスポートすることができます。

   次のように、Maven にビルドの結果が表示されます。  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 16.744 s
   [INFO] Finished at: 2017-09-15T02:35:00-07:00
   [INFO] Final Memory: 30M/290M
   [INFO] ----------------------------------------------------
   ```

1. 次の Maven コマンドを実行して、リソース リスト ファイルを Kubernetes クラスターに適用します。

   ```shell
   mvn fabric8:apply
   ```

   次のように、Maven にビルドの結果が表示されます。  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 14.814 s
   [INFO] Finished at: 2017-09-15T02:41:00-07:00
   [INFO] Final Memory: 35M/288M
   [INFO] ----------------------------------------------------
   ```

1. クラスターにアプリがデプロイされたら、次のように、`kubectl` アプリケーションを使用して外部 IP アドレスを照会します。
   ```shell
   kubectl get svc -w
   ```

   次のように、内部および外部の IP アドレスが `kubectl` に表示されます。
   
   ```shell
   NAME                    CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
   kubernetes              10.0.0.1     <none>        443/TCP        19h
   gs-spring-boot-docker   10.0.242.8   13.65.196.3   80:31215/TCP   3m
   ```
   
   外部 IP アドレスを使用して、Web ブラウザーでアプリケーションを開くことができます。

   ![サンプル アプリケーションを外部で参照する][SB02]

## <a name="delete-your-kubernetes-cluster"></a>Kubernetes クラスターの削除

Kubernetes クラスターが不要になったら、`az group delete` コマンドを使用してリソース グループを削除することができます。これにより、その関連リソースがすべて削除されます。次に例を示します。

   ```azurecli
   az group delete --name wingtiptoys-kubernetes --yes --no-wait
   ```

## <a name="next-steps"></a>次のステップ

Azure での Spring Boot アプリケーションの使用の詳細については、次の記事を参照してください。

* [Spring Boot アプリケーションを Azure App Service にデプロイする](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)
* [Azure Container Service で Spring Boot アプリケーションを Linux にデプロイする](container-service-deploy-spring-boot-app-on-linux.md)
* [Azure Container Service で Spring Boot アプリケーションを Kubernetes クラスターにデプロイする](container-service-deploy-spring-boot-app-on-kubernetes.md)

Java での Azure の使用の詳細については、 [Azure Java デベロッパー センター] と[Java Tools for Visual Studio Team Services] を参照してください。

Docker サンプル プロジェクトでの Spring Boot の詳細については、[Spring Boot on Docker Getting Started]に関するページを参照してください。

独自の Spring Boot アプリケーションの使用開始に関するヘルプについては、「**Spring Initializr**」(<https://start.spring.io/>) を参照してください。

単純な Spring Boot アプリケーションの作成の詳細については、「Spring Initializr」(<https://start.spring.io/>) を参照してください。

Azure でカスタム Docker イメージを使用する方法に関するその他の例については、「[Azure Web App on Linux 向けのカスタム Docker イメージを使用する]」を参照してください。

<!-- URL List -->

[Azure コマンド ライン インターフェイス (CLI)]: /cli/azure/overview
[Azure Container Service (ACS)]: https://azure.microsoft.com/services/container-service/
[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[Azure portal]: https://portal.azure.com/
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Azure Web App on Linux 向けのカスタム Docker イメージを使用する]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[Fabric8]: https://fabric8.io/
[無料の Azure アカウント]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Kubernetes]: https://kubernetes.io/
[Maven]: http://maven.apache.org/
[MSDN サブスクライバーの特典]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-using-fabric8-maven-plugin/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-using-fabric8-maven-plugin/SB02.png

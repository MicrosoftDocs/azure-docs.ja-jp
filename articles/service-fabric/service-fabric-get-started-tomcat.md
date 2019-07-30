---
title: Linux 上で Apache Tomcat サーバー用の Azure Service Fabric コンテナーを作成する | Microsoft Docs
description: Linux コンテナーを作成して、Azure Service Fabric で Apache Tomcat サーバー上で実行しているアプリケーションを公開します。 ご利用のアプリケーションと Apache Tomcat サーバーを使用して Docker イメージを構築し、そのイメージをコンテナー レジストリにプッシュし、Service Fabric コンテナー アプリケーションをビルドおよびデプロイします。
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/08/2018
ms.author: chackdan
ms.openlocfilehash: f1717cfb7980fc481f01c51c04d076aa2ca0f67d
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876492"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Linux 上で Apache Tomcat を実行して Service Fabric コンテナーを作成する
Apache Tomcat は、Java サーブレットと Java サーバー テクノロジの一般的なオープンソースの実装です。 この記事では、Apache Tomcat とシンプルな Web アプリケーションでコンテナーをビルドし、Linux を実行している Service Fabric クラスターにコンテナーをデプロイして、Web アプリケーションに接続する方法を示します。  

Apache Tomcat の詳細については、[Apache Tomcat のホームページ](https://tomcat.apache.org/)を参照してください。 

## <a name="prerequisites"></a>前提条件
* 次のものを実行している開発コンピューター。
  * [Service Fabric SDK およびツール](service-fabric-get-started-linux.md)。
  * [Docker CE for Linux](https://docs.docker.com/engine/installation/#prior-releases)。 
  * [Service Fabric CLI](service-fabric-cli.md)

* Azure Container Registry 内のコンテナー レジストリ。 [Azure portal](../container-registry/container-registry-get-started-portal.md) または [Azure CLI](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry) を使用して、Azure サブスクリプションでコンテナー レジストリを作成できます。 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Tomcat イメージを構築してローカルで実行する
このセクションの手順に従って、Apache Tomcat イメージとシンプルな Web アプリに基づいて Docker イメージを構築し、ローカル システム上のコンテナーで実行します。 
 
1. 開発用コンピューター上で [Java を使用した Service Fabric の概要](https://github.com/Azure-Samples/service-fabric-java-getting-started)のサンプル リポジトリを複製します。

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. ディレクトリを Apache Tomcat サーバーのサンプル ディレクトリ (*service-fabric-java-getting-started/container-apache-tomcat-web-server-sample*) に変更します。

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Docker Hub にある公式の [Tomcat イメージ](https://hub.docker.com/_/tomcat/)と Tomcat サーバーのサンプルに基づいて Docker ファイルを作成します。 *service-fabric-java-getting-started/container-apache-tomcat-web-server-sample* ディレクトリで、*Dockerfile* という名前のファイル (ファイル拡張子なし) を作成します。 *Dockerfile* に次のコードを追加して変更を保存します。

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   詳細については、[Dockerfile のリファレンス](https://docs.docker.com/engine/reference/builder/)を参照してください。


4. `docker build` コマンドを実行して、Web アプリケーションを実行するイメージを作成します。

   ```bash
   docker build . -t tomcattest
   ```

   このコマンドでは、Dockerfile の手順を使用して新しいイメージを構築し、`tomcattest` という名前を付けます (-t のタグを付けます)。 コンテナー イメージを構築するために、まず、基本イメージが Docker Hub からダウンロードされ、アプリケーションが追加されます。 

   ビルド コマンドが完了したら、`docker images` コマンドを実行して、新しいイメージの情報を確認します。

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. コンテナー レジストリにプッシュする前に、コンテナー化されたアプリケーションがローカルで実行されていることを確認します。
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` コンテナーに名前を付けて、その ID ではなく、フレンドリ名を使用して参照できるようにします。
   * `-p` コンテナーとホスト OS の間のポート マッピングを指定します。 

   > [!Note]
   > `-p` パラメーターを使って開くポートは、Tomcat アプリケーションで要求をリッスンするポートである必要があります。 現在の例では、ポート 8080 で HTTP 要求をリッスンするように、*ApacheTomcat/conf/server.xml* ファイルで構成されたコネクターがあります。 このポートは、ホスト上のポート 8080 にマッピングされます。 

   その他のパラメーターについては、[Docker の実行に関するドキュメント](https://docs.docker.com/engine/reference/commandline/run/)を参照してください。

1. コンテナーをテストするには、ブラウザーを開いて、次の URL のいずれかを入力します。 各 URL のようこそ画面である "Hello World!" のバリアントが表示されます。

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Hello world /sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. コンテナーを停止して、デプロイ コンピューターから削除します。

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>コンテナー レジストリに Tomcat イメージをプッシュする
Tomcat イメージがデプロイ コンピューター上のコンテナーで実行されていることを確認して、コンテナー レジストリのリポジトリにプッシュします。 この記事では、Azure Container Registry を使用してイメージを保存しますが、手順の一部を変更して、選択した任意のコンテナー レジストリを使用することができます。 この記事では、レジストリ名は *myregistry* であると推定され、完全なレジストリ名は myregistry.azurecr.io になります。 ご自分のシナリオに合わせてこれらを適切に変更してください。 

1. `docker login` を実行し、ご自分の[レジストリの資格情報](../container-registry/container-registry-authentication.md)を使用してお使いのコンテナー レジストリにサインインします。

   次の例では、Azure Active Directory [サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)の ID とパスワードを渡します。 たとえば、自動化シナリオのために、レジストリにサービス プリンシパルを割り当てることができます。 または、ご自分のレジストリのユーザー名とパスワードを使ってサインインすることもできます。

   ```bash
   docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
   ```

2. 次のコマンドでは、レジストリへの完全修飾パスを使用して、イメージのタグまたはエイリアスを作成します。 この例では、レジストリのルートが煩雑にならないように、イメージを `samples` 名前空間に配置しています。

   ```bash
   docker tag tomcattest myregistry.azurecr.io/samples/tomcattest
   ```

3. コンテナー レジストリにイメージをプッシュします。

   ```bash
   docker push myregistry.azurecr.io/samples/tomcattest
   ```

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Service Fabric コンテナー アプリケーションのビルドとデプロイ
Tomcat イメージをコンテナー レジストリにプッシュして、レジストリから Tomcat イメージをプルし、クラスター内でコンテナー化されたサービスとして実行する Service Fabric コンテナー アプリケーションをビルドおよびデプロイすることができるようになりました。 

1. ローカル複製の外部 (*service-fabric-java-getting-started* ディレクトリ ツリーの外部) に新しいディレクトリを作成します。 そのディレクトリに切り替えて Yeoman を使用し、コンテナー アプリケーション用にスキャフォールディングを作成します。 

   ```bash
   yo azuresfcontainer 
   ```
   入力を求められたら、次の値を入力します。

   * [Name your application]\(アプリケーションの名前\): ServiceFabricTomcat
   * [Name of the application service]\(アプリケーション サービスの名前\): TomcatService
   * [Input the Image Name]\(イメージ名を入力\): コンテナー レジストリにあるコンテナー イメージの URL を指定します (例: myregistry.azurecr.io/samples/tomcattest)。
   * コマンド:空白のままにします。 このイメージには定義済みのワークロード エントリ ポイントがあるため、入力コマンド (コンテナーの起動後にコンテナーの実行を維持するためにコンテナー内で実行されるコマンド) を明示的に指定する必要はありません。
   * [Number of instances of guest container application]\(ゲスト コンテナー アプリケーションのインスタンス数\): 1

   ![コンテナー用 Service Fabric Yeoman ジェネレーター](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. サービス マニフェスト (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*) で、ルートの **ServiceManfest** タグの下に次の XML を追加して、使用するアプリケーションで要求をリッスンしているポートを開きます。 **Endpoint** タグでは、エンドポイントのプロトコルとポートを宣言します。 この記事では、コンテナー化されたサービスがポート 8080 でリッスンします。 

   ```xml
   <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
       listen. Please note that if your service is partitioned, this port is shared with 
       replicas of different partitions that are placed in your code. -->
      <Endpoint Name="endpointTest" Port="8080" Protocol="tcp"/>
    </Endpoints>
   </Resources>
   ```

11. アプリケーション マニフェスト (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*) では、**ServiceManifestImport** タグの下に、次の XML を追加します。 **RepositoryCredentials** タグ内の **AccountName** と **Password** をご自分のコンテナー レジストリの名前とそこにサインインするために必要なパスワードに置き換えます。

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   **ContainerHostPolicies** タグでは、コンテナー ホストをアクティブ化するためのポリシーを指定します。
    
   * **PortBinding** タグでは、コンテナー ポートからホスト ポートへのマッピング ポリシーを構成します。 コンテナーでは Dockerfile で指定されたポート 8080 を公開するため、**ContainerPort** 属性は 8080 に設定されます。 **EndpointRef** 属性は "endpointTest" に設定されます。エンドポイントは前の手順のサービス マイフェストで定義されています。 そのため、ポート 8080 で受信するサービスへの要求は、コンテナー上のポート 8080 にマッピングされています。 
   * **RepositoryCredentials** タグでは、コンテナーでイメージをプルする (プライベート) リポジトリを使用して認証する必要がある資格情報を指定します。 画像がパブリックなリポジトリからプルされる場合、このポリシーは必要ありません。
    

12. *ServiceFabricTomcat* フォルダーで、使用する Service Fabric クラスターに接続します。 

   * ローカルの Service Fabric クラスターに接続するには、次を実行します。

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * セキュリティで保護された Azure クラスターに接続するには、クライアント証明書が *ServiceFabricTomcat* ディレクトリ内の .pem ファイルがあることを確認して、次を実行します。 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     上記のコマンドで、`your-certificate.pem` をご自分のクライアント証明書ファイルの名前に置き換えます。 開発環境およびテスト環境では、多くの場合、クラスター証明書がクライアント証明書として使用されます。 証明書が自己署名されていない場合、`-no-verify` パラメーターを省略します。 
       
     通常、クラスター証明書は、ローカルに .pfx ファイルとしてダウンロードされます。 まだ PEM 形式の証明書がない場合は、次のコマンドを実行すると、.pfx ファイルから .pem ファイルを作成できます。

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     .pfx ファイルがパスワードで保護されていない場合は、最後のパラメーターとして `-passin pass:` を使用します。


13. テンプレートで指定されたインストール スクリプトを実行して、アプリケーションをご利用のクラスターにデプロイします。 スクリプトは、クラスターのイメージ ストアにアプリケーション パッケージをコピーし、アプリケーションの種類を登録し、アプリケーションのインスタンスを作成します。

     ```bash
     ./install.sh
     ```

   インストール スクリプトを実行した後、ブラウザーを開いて、Service Fabric Explorer に移動します。
    
   * ローカル クラスター上で、 `http://localhost:19080/Explorer` を使用します (Mac OS X で Vagrant を使用している場合は、*localhost* を VM のプライベート IP に置き換えます)。
   * セキュリティで保護された Azure クラスターで、 `https://PublicIPorFQDN:19080/Explorer` を使用します。 
    
   **Applications** ノードを展開し、アプリケーションの種類 (**ServiceFabricTomcatType**) のエントリと、その種類の最初のインスタンスのエントリができたことを確認します。 アプリケーションが完全にデプロイされるには数分かかる場合があるので、お待ちください。

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Tomcat サーバー上のアプリケーションにアクセスするには、ブラウザー ウィンドウを開いて、次の URL のいずれかを入力します。 ローカル クラスターにデプロイした場合、*PublicIPorFQDN* に *localhost* を使用します。 各 URL のようこそ画面である "Hello World!" のバリアントが表示されます。

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>クリーンアップ
クラスターからアプリケーション インスタンスを削除し、その種類のアプリケーションの登録を解除するには、テンプレートに指定されているアンインストール スクリプトを使用します。

```bash
./uninstall.sh
```

コンテナー レジストリにイメージをプッシュした後は、開発コンピューターからローカルのイメージを削除できます。

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>次の手順
* Linux コンテナーの追加機能の簡単な手順については、「[Linux で初めての Service Fabric コンテナー アプリケーションを作成する](service-fabric-get-started-containers-linux.md)」を参照してください。
* Linux コンテナーの詳細な手順については、[Linux コンテナー アプリの作成に関するチュートリアル](service-fabric-tutorial-create-container-images.md)をご覧ください。
* [Service Fabric でのコンテナー](service-fabric-containers-overview.md)の実行について確認します。



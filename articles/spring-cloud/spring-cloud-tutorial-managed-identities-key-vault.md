---
title: チュートリアル - Key Vault を Azure Spring Cloud アプリに接続するためのマネージド ID
description: Key Vault を Azure Spring Cloud アプリに接続するためのマネージド ID を設定します
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: fc803cbe3dd1ec57b6cd286513efe8393a1471e9
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297129"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-spring-cloud-app"></a>チュートリアル:マネージド ID を使用して Key Vault を Azure Spring Cloud アプリに接続する

この記事では、Azure Spring Cloud アプリ用のマネージド ID を作成し、それを使用して Azure Key Vault にアクセスする方法を説明します。

Azure Key Vault を使用すると、対象のアプリのトークン、パスワード、証明書、API キー、その他のシークレットを安全に格納し、それらへのアクセスを厳密に制御できます。 Azure Active Directory (AAD) でマネージド ID を作成したうえで、Key Vault を含め、AAD 認証をサポートする任意のサービスに対して認証を行うことができます。資格情報をコードに表示する必要はありません。

## <a name="prerequisites"></a>前提条件

* [Azure サブスクリプションにサインアップする](https://azure.microsoft.com/free/)
* [Azure CLI バージョン 2.0.67 以上をインストールする](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Maven 3.0 以上をインストールする](https://maven.apache.org/download.cgi)

## <a name="create-a-resource-group"></a>リソース グループを作成する
リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 コマンド [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) を使用して、Key Vault と Spring Cloud の両方を含むリソース グループを作成します。

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>キー コンテナーを設定する
キー コンテナーを作成するには、コマンド [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) を使用します。

> [!Important]
> 各キー コンテナーには一意の名前が必要です。 次の例の <your-keyvault-name> は、ご自分のキー コンテナーの名前に置き換えてください。

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

"https://<your-keyvault-name>.vault.azure.net" 形式で返される `vaultUri` を書き留めます。 これは、後の手順で使用します。

これで、コマンド [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) を使用して、対象のキー コンテナーにシークレットを格納できるようになりました。

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" \
    --name "connectionString" \
    --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
```

## <a name="create-azure-spring-cloud-service-and-app"></a>Azure Spring Cloud サービスおよびアプリを作成する
対応する拡張機能をインストールした後、Azure CLI コマンド `az spring-cloud create` を使用して Azure Spring Cloud インスタンスを作成します。 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create -n "myspringcloud" -g "myResourceGroup"
```
次の例では、`--assign-identity` パラメーターの要求どおりに、システム割り当てマネージド ID を持つ `springapp` という名前のアプリを作成します。

```azurecli
az spring-cloud app create -n "springapp" -s "myspringcloud" -g "myResourceGroup" --is-public true --assign-identity
export SERVICE_IDENTITY=$(az spring-cloud app show --name "springapp" -s "myspringcloud" -g "myResourceGroup" | jq -r '.identity.principalId')
```

"https://<your-app-name>.azuremicroservices.io" 形式で返される `url` を書き留めます。 これは、後の手順で使用します。


## <a name="grant-your-app-access-to-key-vault"></a>Key Vault へのアクセス許可をアプリに付与する
Key Vault への適切なアクセス権をアプリに付与するには、`az keyvault set-policy` を使用します。
```azurecli
az keyvault set-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY} --secret-permissions set get list
```

## <a name="build-a-sample-spring-boot-app-with-spring-boot-starter"></a>Spring Boot スターターを使用してサンプルの Spring Boot アプリをビルドする
このアプリは、Azure Key Vault からシークレットを取得するためのアクセス権を持つことになります。 次のスターター アプリを使用します: [Azure Key Vault Secrets Spring ブート スターター](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets)。  Azure Key Vault は、Spring **PropertySource** のインスタンスとして追加されます。  Azure Key Vault に格納されているシークレットは、ファイル内のプロパティなどの外部化された構成プロパティと同様に、簡単にアクセスして使用できます。 

1. Azure Key Vault Spring スターターを使用して、start.spring.io からサンプル プロジェクトを生成します。 
    ```azurecli
    curl https://start.spring.io/starter.tgz -d dependencies=web,azure-keyvault-secrets -d baseDir=springapp -d bootVersion=2.3.1.RELEASE -d javaVersion=1.8 | tar -xzvf -
    ```

2. 対象のアプリで使用するキー コンテナーを指定します。 

    ```azurecli
    cd springapp
    vim src/main/resources/application.properties
    ```

    Azure Spring Cloud アプリでマネージド ID を使用するには、次の内容のプロパティを src/main/resources/application.properties に追加します。

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```
    > [!Note] 
    > 上記のように、`application.properties` にキー コンテナーの URL を追加する必要があります。 そうしないと、キー コンテナーの URL が実行時にキャプチャされない場合があります。

3. コード例を src/main/java/com/example/demo/DemoApplication.java に追加します。 これにより、キー コンテナーから接続文字列が取得されます。 

    ```Java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @SpringBootApplication
    @RestController
    public class DemoApplication implements CommandLineRunner {

        @Value("${connectionString}")
        private String connectionString;

        public static void main(String[] args) {
          SpringApplication.run(DemoApplication.class, args);
        }

        @GetMapping("get")
        public String get() {
          return connectionString;
        }

        public void run(String... varl) throws Exception {
          System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
        }
      }
    ```

    pom.xml を開くと、`azure-keyvault-secrets-spring-boot-starter` の依存関係を確認できます。 この依存関係を pom.xml 内の自分のプロジェクトに追加します。 

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
    </dependency>
    ```

4. サンプル アプリをパッケージ化します。 

    ```azurecli
    mvn clean package
    ```

5. これで、Azure CLI コマンド `az spring-cloud app deploy` を使用して対象のアプリを Azure にデプロイできます。 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/demo-0.0.1-SNAPSHOT.jar
    ```

6.  対象のアプリをテストするには、パブリック エンドポイントまたはテスト エンドポイントにアクセスします。

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/get
    ```

    "Successfully got the value of secret connectionString from Key Vault https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE; (キー コンテナー https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE; からシークレットの connectionString の値を正常に取得しました)" というメッセージが表示されます。

## <a name="build-sample-spring-boot-app-with-java-sdk"></a>Java SDK を使用してサンプルの Spring Boot アプリをビルドする

このサンプルでは、シークレットを Azure Key Vault に設定したり、Azure Key Vault から取得したりできます。 [Java 用 Azure Key Vault シークレット クライアント ライブラリ](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-secrets-readme?view=azure-java-stablelibrary)は、Azure SDK 全体で Azure Active Directory トークン認証のサポートを提供します。 これには、AAD トークン認証をサポートする Azure SDK クライアントを構築するために使用できる **TokenCredential** 実装のセットが用意されています。

Azure Key Vault シークレット クライアント ライブラリを使用すると、トークン、パスワード、API キー、その他のシークレットを安全に格納し、それらへのアクセスを厳密に制御できます。 このライブラリは、シークレットとその各種バージョンを作成、取得、更新、削除、パージ、バックアップ、復元、および一覧表示する操作を提供します。

1. サンプル プロジェクトを複製します。 

    ```azurecli
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. 対象のアプリで使用するキー コンテナーを指定します。 

    ```azurecli
    cd Azure-Spring-Cloud-Samples/managed-identity-keyvault
    vim src/main/resources/application.properties
    ```

    Azure Spring Cloud アプリでマネージド ID を使用するには、次の内容のプロパティを *src/main/resources/application.properties* に追加します。

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

3. Azure Active Directory からトークンを取得するための [ManagedIdentityCredentialBuilder](https://docs.microsoft.com/java/api/com.azure.identity.managedidentitycredentialbuilder?view=azure-java-stable) と、キー コンテナーからシークレットを設定または取得するための [SecretClientBuilder](https://docs.microsoft.com/java/api/com.azure.security.keyvault.secrets.secretclientbuilder?view=azure-java-stable) を対象のコードに含めます。

    複製されたサンプル プロジェクトの [MainController.java](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/src/main/java/com/microsoft/azure/MainController.java#L28) から例を取得します。

    さらに、依存関係として `azure-identity` と `azure-security-keyvault-secrets` を対象の pom.xml に含めます。 複製されたサンプル プロジェクトの [pom.xml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/pom.xml#L21) から例を取得します。 

4. サンプル アプリをパッケージ化します。 

    ```azurecli
    mvn clean package
    ```

5. 次に、Azure CLI コマンド `az spring-cloud app deploy` を使用してアプリを Azure にデプロイします。 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/asc-managed-identity-keyvault-sample-0.1.0.jar
    ```

6. パブリック エンドポイントまたはテスト エンドポイントにアクセスして、対象のアプリをテストします。 

    最初に、Azure Key Vault で設定したシークレットの値を取得します。 
    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/connectionString
    ```

    "Successfully got the value of secret connectionString from Key Vault https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE; (キー コンテナー https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE; からシークレットの connectionString の値を正常に取得しました)" というメッセージが表示されます。

    次に、シークレットを作成し、Java SDK を使用してそれを取得します。 
    ```azurecli
    curl -X PUT https://myspringcloud-springapp.azuremicroservices.io/secrets/test?value=success

    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/test
    ```

    "Successfully got the value of secret test from Key Vault https://<your-keyvault-name>.vault.azure.net: success (キー コンテナー https://<your-keyvault-name>.vault.azure.net からシークレット テストの値を正常に取得しました: 成功)" というメッセージが表示されます。 

## <a name="next-steps"></a>次のステップ

* [Azure Spring Cloud でマネージド ID を使用して Storage Blob にアクセスする方法](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Azure Spring Cloud アプリケーションのシステム割り当てマネージド ID を有効にする方法](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity)
* [Azure リソース用マネージド ID の詳細](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [GitHub Actions で Key Vault を使用して Azure Spring Cloud を認証する](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-github-actions-key-vault)

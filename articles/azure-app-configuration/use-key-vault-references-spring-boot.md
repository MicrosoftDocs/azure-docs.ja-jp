---
title: Java Spring Boot アプリで Azure App Configuration の Key Vault 参照を使用するためのチュートリアル | Microsoft Docs
description: このチュートリアルでは、Java Spring Boot アプリから Azure App Configuration の Key Vault 参照を使用する方法について説明します。
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: alkemper
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 04d9c7a343570349851a206fd69fdda822f790a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99981478"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>チュートリアル:Java Spring アプリで Key Vault 参照を使用する

このチュートリアルでは、Azure App Configuration サービスを Azure Key Vault と共に使用する方法について説明します。 App Configuration と Key Vault は補完的なサービスであり、ほとんどのアプリケーションのデプロイで併用されます。

これらをまとめて使用できるように、App Configuration では、Key Vault に格納されている値を参照するキーを作成できます。 App Configuration でこのようなキーを作成すると、App Configuration には、その値そのものではなく、Key Vault の値の URI が格納されます。

App Configuration に格納されているその他のキーの場合と同様、お使いのアプリケーションは App Configuration クライアント プロバイダーを使用して Key Vault 参照を取得します。 この場合、App Configuration に格納された値は、Key Vault の値を参照する URI です。 これらは、Key Vault の値でも資格情報でもありません。 クライアント プロバイダーはキーを Key Vault 参照として認識し、Key Vault を使用して値を取得します。

アプリケーションの役割は、App Configuration と Key Vault の両方に正しく認証されることです。 この 2 つのサービスが直接通信することはありません。

このチュートリアルでは、自分のコードに Key Vault 参照を実装する方法について説明します。 これは、クイック スタートで紹介されている Web アプリに基づいています。 先に進む前に、[App Configuration を使用した Java Spring アプリの作成](./quickstart-java-spring-app.md)を済ませておいてください。

このチュートリアルの手順は、任意のコード エディターを使用して実行できます。 たとえば、[Visual Studio Code](https://code.visualstudio.com/) は、Windows、macOS、および Linux の各オペレーティング システムで使用できるクロスプラットフォーム コード エディターです。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Key Vault に格納されている値を参照する App Configuration キーを作成する。
> * Java Spring アプリケーションからこのキーの値にアクセスする。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
* バージョン 8 を含む、サポートされている [Java Development Kit (JDK)](/java/azure/jdk)。
* [Apache Maven](https://maven.apache.org/download.cgi) バージョン 3.0 以降。

## <a name="create-a-vault"></a>コンテナーの作成

1. Azure Portal の左上隅にある **[リソースの作成]** オプションを選択します。

    ![Azure portal の [リソースの作成] オプションを示すスクリーンショット。](./media/quickstarts/search-services.png)
1. 検索ボックスに「**Key Vault**」と入力します。
1. 結果リストで、左側の **[キー コンテナー]** を選択します。
1. **[キー コンテナー]** で、 **[追加]** を選択します。
1. **[キー コンテナーの作成]** の右側に、次の情報を入力します。
    * **[サブスクリプション]** を選択してサブスクリプションを選択します。
    * **[リソース グループ]** で、 **[新規作成]** を選択し、リソース グループの名前を入力します。
    * **[キー コンテナー名]** では、一意の名前が必要です。 このチュートリアルでは、「**Contoso-vault2**」と入力します。
    * **[リージョン]** ドロップダウン リストで、場所を選択します。
1. **[キー コンテナーの作成]** オプションは既定値のままにしておきます。
1. **［作成］** を選択します

この時点で、お使いの Azure アカウントが、この新しいコンテナーへのアクセスが承認されている唯一のアカウントになります。

![キー コンテナーを示すスクリーンショット。](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Key Vault にシークレットを追加する

シークレットをコンテナーに追加するには、いくつかの追加の手順を行う必要があります。 この場合、Key Vault の取得をテストするために使用できるメッセージを追加します。 このメッセージは **Message** という名前で、それには "Hello from Key Vault" という値が格納されます。

1. Key Vault のプロパティ ページで、 **[シークレット]** を選択します。
1. **[Generate/Import]\(生成/インポート\)** を選択します。
1. **[シークレットの作成]** ウィンドウで、次の値を入力します。
    * **[アップロード オプション]** :「**Manual**」と入力します。
    * **Name**:「**Message**」と入力します。
    * **値**: 「**Hello from Key Vault**」と入力します。
1. **[シークレットの作成]** の他のプロパティは既定値のままにしておきます。
1. **［作成］** を選択します

## <a name="add-a-key-vault-reference-to-app-configuration"></a>App Configuration に Key Vault 参照を追加する

1. [Azure portal](https://portal.azure.com) にサインインします。 **[すべてのリソース]** を選択し、クイック スタートで作成した App Configuration ストア インスタンスを選択します。

1. **[構成エクスプローラー]** を選択します。

1. **[+ 作成]** 、 **[キー コンテナー参照]** の順に選択し、次の値を指定します。
    * **[キー]** : **[/application/config.keyvaultmessage]** を選択します。
    * **ラベル**:この値は空白のままにしておきます。
    * **[サブスクリプション]** 、 **[リソース グループ]** 、 **[キー コンテナー]** : 前のセクションで作成したキー コンテナーの値に対応する値を入力します。
    * **[シークレット]** : 前のセクションで作成した、**Message** という名前のシークレットを選択します。

## <a name="connect-to-key-vault"></a>Key Vault に接続する

1. このチュートリアルでは、Key Vault の認証にサービス プリンシパルを使用します。 このサービス プリンシパルを作成するには、Azure CLI の [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) コマンドを使用します。

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    この操作では、一連のキーと値のペアが返されます。

    ```json
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. 次のコマンドを実行して、サービス プリンシパルがキー コンテナーにアクセスできるようにします。

    ```azurecli
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. 次のコマンドを実行して object-id を取得し、それを App Configuration に追加します。

    ```azurecli
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. **AZURE_CLIENT_ID**、**AZURE_CLIENT_SECRET**、および **AZURE_TENANT_ID** 環境変数を作成します。 前の手順で表示されたサービス プリンシパルの値を使用します。 コマンド ラインで次のコマンドを実行してコマンド プロンプトを再起動し、変更が反映されるようにします。

    ```cmd
    setx AZURE_CLIENT_ID "clientId"
    setx AZURE_CLIENT_SECRET "clientSecret"
    setx AZURE_TENANT_ID "tenantId"
    ```

    Windows PowerShell を使用する場合は、次のコマンドを実行します。

    ```azurepowershell
    $Env:AZURE_CLIENT_ID = "clientId"
    $Env:AZURE_CLIENT_SECRET = "clientSecret"
    $Env:AZURE_TENANT_ID = "tenantId"
    ```

    macOS または Linux を使用する場合は、次のコマンドを実行します。

    ```cmd
    export AZURE_CLIENT_ID ='clientId'
    export AZURE_CLIENT_SECRET ='clientSecret'
    export AZURE_TENANT_ID ='tenantId'
    ```


> [!NOTE]
> これらの Key Vault 資格情報は、お使いのアプリケーション内でのみ使用されます。  アプリケーションの認証は、App Configuration サービスを必要とせず、これらの資格情報を使用して Key Vault に対して直接行われます。  Key Vault は、キーを共有したり公開したりすることなく、アプリケーションと App Configuration サービスの両方の認証を提供します。

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Key Vault 参照を使用するようコードを更新する

1. **APP_CONFIGURATION_ENDPOINT** という環境変数を作成します。 その値を App Configuration ストアのエンドポイントに設定します。 エンドポイントは、Azure portal の **[アクセス キー]** ブレードで確認できます。 変更を有効にするために、コマンド プロンプトを再起動します。 


1. *resources* フォルダー内の *bootstrap.properties* を開きます。 **APP_CONFIGURATION_ENDPOINT** 値を使用するようにこのファイルを更新します。 このファイル内の接続文字列への参照をすべて削除します。 

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].endpoint= ${APP_CONFIGURATION_ENDPOINT}
    ```

1. *MessageProperties.java* を開きます。 *keyVaultMessage* という新しい変数を追加します。

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. *HelloController.java* を開きます。 Key Vault から取得したメッセージが含まれるように *getMessage* メソッドを更新します。

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. *AzureCredentials.java* という新しいファイルを作成し、次のコードを追加します。

    ```java
    package com.example.demo;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.EnvironmentCredentialBuilder;
    import com.microsoft.azure.spring.cloud.config.AppConfigurationCredentialProvider;
    import com.microsoft.azure.spring.cloud.config.KeyVaultCredentialProvider;

    public class AzureCredentials implements AppConfigurationCredentialProvider, KeyVaultCredentialProvider{

        @Override
        public TokenCredential getKeyVaultCredential(String uri) {
            return getCredential();
        }

        @Override
        public TokenCredential getAppConfigCredential(String uri) {
            return getCredential();
        }

        private TokenCredential getCredential() {
            return new EnvironmentCredentialBuilder().build();
        }

    }
    ```

1. *AppConfiguration.java* という新しいファイルを作成します。 さらに、次のコードを追加します。

    ```java
    package com.example.demo;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class AppConfiguration {

        @Bean
        public AzureCredentials azureCredentials() {
            return new AzureCredentials();
        }
    }
    ```

1. リソースの META-INF ディレクトリに、*spring.factories* という新しいファイルを作成し、次のコードを追加します。

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.demo.AppConfiguration
    ```

1. Spring Boot アプリケーションを Maven でビルドし、実行します。次に例を示します。

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. アプリケーションが実行されたら、*curl* を使用してアプリケーションをテストできます。次に例を示します。

      ```shell
      curl -X GET http://localhost:8080/
      ```

    App Configuration ストアに入力したメッセージが表示されます。 Key Vault に入力したメッセージも表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Key Vault に格納されている値を参照する App Configuration キーを作成しました。 Java Spring アプリケーションで機能フラグを使用する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [マネージド ID の統合](./quickstart-feature-flag-spring-boot.md)

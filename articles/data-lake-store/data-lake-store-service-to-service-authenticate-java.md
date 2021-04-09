---
title: サービス間認証 - Data Lake Storage Gen1 - Java SDK
description: Java から Azure Active Directory を使用して Azure Data Lake Storage Gen1 に対するサービス間認証を行う方法について説明します
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: 0e320557a7372af6a41038d9b3196db23d2496c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000387"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Azure Data Lake Storage Gen1 に対する Java を使用したサービス間認証

> [!div class="op_single_selector"]
> * [Java の使用](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK の使用](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python の使用](data-lake-store-service-to-service-authenticate-python.md)
> * [REST API の使用](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

この記事では、Java SDK を使用して、Azure Data Lake Storage Gen1 に対するサービス間認証を行う方法について説明します。 Java SDK を使った Azure Data Lake Storage Gen1 に対するエンド ユーザー認証はサポートされません。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Active Directory "Web" アプリケーションを作成します**。 [Data Lake Storage Gen1 に対する Azure Active Directory を使用したサービス間認証](data-lake-store-service-to-service-authenticate-using-active-directory.md)の手順を完了している必要があります。

* [Maven](https://maven.apache.org/install.html)。 このチュートリアルでは、ビルドとプロジェクトの依存関係に Maven を使用します。 Maven や Gradle などのビルド システムを使用しなくてもビルドすることはできますが、これらのシステムを使用すると、依存関係の管理が容易になります。

* (オプション) [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) や [Eclipse](https://www.eclipse.org/downloads/) などの IDE。

## <a name="service-to-service-authentication"></a>サービス間認証

1. コマンド ラインで [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) を使用するか、IDE を使用して、Maven プロジェクトを作成します。 IntelliJ を使用して Java プロジェクトを作成する方法については、[こちら](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html)をご覧ください。 Eclipse を使用してプロジェクトを作成する方法については、[こちら](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm)をご覧ください。

2. Maven の **pom.xml** ファイルに次の依存関係を追加します。 **\</project>** タグの前に次のスニペットを追加します。

    ```xml
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-data-lake-store-sdk</artifactId>
        <version>2.2.3</version>
      </dependency>
      <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.21</version>
      </dependency>
    </dependencies>
    ```

    最初の依存関係では、maven リポジトリから Data Lake Storage Gen1 SDK (`azure-data-lake-store-sdk`) を使用します。 2 番目の依存関係では、このアプリケーションで使用するログ記録フレームワーク (`slf4j-nop`) を指定します。 Data Lake Storage Gen1 SDK では、[slf4j](https://www.slf4j.org/) ログ ファサードを使用します。slf4j を使用すると、log4j、Java ログ、logback などの多数の一般的なログ記録フレームの中から選択することも、ログを記録しないようにすることもできます。 この例ではログを無効にするため、**slf4j-nop** バインドを使用します。 アプリケーションで他のログ オプションを使用する場合は、[こちら](https://www.slf4j.org/manual.html#projectDep)をご覧ください。

3. アプリケーションに次の import ステートメントを追加します。

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;
    ```

4. Java アプリケーションから次のスニペットを使用して、以前に作成した Active Directory Web アプリケーションのトークンを取得するには、`AccessTokenProvider` のサブクラスの 1 つを使用します (次の例では、`ClientCredsTokenProvider` を使用します)。 トークン プロバイダーは、メモリ内のトークンを取得するために使用する資格情報をキャッシュし、そのトークンの有効期限が切れそうになった場合に自動的に更新します。 トークンがカスタム トークンで取得されるように `AccessTokenProvider` のサブクラスを独自に作成することもできますが、 ここでは、SDK に用意されているものを使ってみましょう。

    **FILL-IN-HERE** を、Azure Active Directory Web アプリケーションの実際の値に置き換えます。

    ```java
    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   
    ```

Data Lake Storage Gen1 SDK には、Data Lake Storage Gen1 アカウントとの対話に必要なセキュリティ トークンを管理できる便利な方法が用意されています。 ただし、使用する方法はこれらに限定されるわけではありません。 [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) や独自のカスタム コードの使用など、トークンを取得するその他の方法も使用できます。

## <a name="next-steps"></a>次のステップ

この記事では、Data Lake Storage Gen1 に対し、Java SDK からエンドユーザー認証を使って認証を行う方法について説明しました。 これで、Java SDK を使用して Data Lake Storage Gen1 を使用する方法について説明した次の記事に進めるようになりました。

* [Java SDK を使用した Data Lake Storage Gen1 に対するデータ操作](data-lake-store-get-started-java-sdk.md)

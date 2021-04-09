---
title: エンドユーザー認証 - Java と Data Lake Storage Gen1 - Azure
description: Java と Azure Active Directory を使用した Azure Data Lake Storage Gen1 によるエンドユーザー認証を行う方法について説明します
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: ece996f7edb8d37cea47756c5b7e635e02409b35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87318863"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Azure Data Lake Storage Gen1 による Java を使用したエンドユーザー認証
> [!div class="op_single_selector"]
> * [Java の使用](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK の使用](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python の使用](data-lake-store-end-user-authenticate-python.md)
> * [REST API の使用](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

この記事では、Java SDK を使用して、Azure Data Lake Storage Gen1 によるエンドユーザー認証を行う方法について説明します。 Java SDK を使用した Data Lake Storage Gen1 によるサービス間認証については、[Java を使用した Data Lake Storage Gen1 によるサービス間認証](data-lake-store-service-to-service-authenticate-java.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Active Directory "ネイティブ" アプリケーションを作成します**。 「[End-user authentication with Data Lake Storage Gen1 using Azure Active Directory (Azure Active Directory を使用した Data Lake Storage Gen1 でのエンドユーザー認証)](data-lake-store-end-user-authenticate-using-active-directory.md)」のステップを完了している必要があります。

* [Maven](https://maven.apache.org/install.html)。 このチュートリアルでは、ビルドとプロジェクトの依存関係に Maven を使用します。 Maven や Gradle などのビルド システムを使用しなくてもビルドすることはできますが、これらのシステムを使用すると、依存関係の管理が容易になります。

* (オプション) [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) や [Eclipse](https://www.eclipse.org/downloads/) などの IDE。

## <a name="end-user-authentication"></a>エンドユーザー認証
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
   
    最初の依存関係では、maven リポジトリから Data Lake Storage Gen1 SDK (`azure-data-lake-store-sdk`) を使用します。 2 番目の依存関係では、このアプリケーションで使用するログ記録フレームワーク (`slf4j-nop`) を指定します。 Data Lake Storage Gen1 SDK では、[SLF4J](https://www.slf4j.org/) ログ ファサードを使用します。それを使用すると、Log4j、Java ログ、Logback などの多数の一般的なログ記録フレームの中から選択することも、ログを記録しないようにすることもできます。 この例ではログを無効にするため、**slf4j-nop** バインドを使用します。 アプリケーションで他のログ オプションを使用する場合は、[こちら](https://www.slf4j.org/manual.html#projectDep)をご覧ください。

3. アプリケーションに次の import ステートメントを追加します。

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;
    ```

4. Java アプリケーションから次のスニペットを使用して、`DeviceCodeTokenProvider` を使用して以前に作成した Active Directory ネイティブ アプリケーションのトークンを取得します。 **FILL-IN-HERE** を、Azure Active Directory ネイティブ アプリケーションの実際の値に置き換えます。

    ```java
    private static String nativeAppId = "FILL-IN-HERE";
            
    AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   
    ```

Data Lake Storage Gen1 SDK には、Data Lake Storage Gen1 アカウントとの対話に必要なセキュリティ トークンを管理できる便利な方法が用意されています。 ただし、使用する方法はこれらに限定されるわけではありません。 [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) や独自のカスタム コードの使用など、トークンを取得するその他の方法も使用できます。

## <a name="next-steps"></a>次のステップ
この記事では、エンドユーザー認証を使って、Java SDK を使用して Azure Data Lake Storage Gen1 により認証する方法を説明しました。 これで、Java SDK を使用して Azure Data Lake Storage Gen1 を操作する方法について説明した次の記事に進めるようになりました。

* [Java SDK を使用した Data Lake Storage Gen1 に対するデータ操作](data-lake-store-get-started-java-sdk.md)



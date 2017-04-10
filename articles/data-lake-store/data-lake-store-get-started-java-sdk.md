---
title: "Java SDK を使用して Azure Data Lake Store でアプリケーションを開発する | Microsoft Docs"
description: "Azure Data Lake Store Java SDK を使用して、Data Lake Store アカウントを作成し、Data Lake Store で基本的な操作を実行します"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 3e4302e7c5b54a46994eccaa8cf9ecbb0a84446d
ms.lasthandoff: 03/03/2017


---
# <a name="get-started-with-azure-data-lake-store-using-java"></a>Java で Azure Data Lake Store の使用を開始する
> [!div class="op_single_selector"]
> * [ポータル](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Azure Data Lake Store Java SDK を使用して、フォルダーの作成、データ ファイルのアップロードとダウンロードなどの基本操作を実行する方法について説明します。Data Lake の詳細については、[Azure Data Lake Store](data-lake-store-overview.md) に関する記事をご覧ください。

[Azure Data Lake Store Java API ドキュメント](https://azure.github.io/azure-data-lake-store-java/javadoc/)で、Azure Data Lake Store に関する Java SDK API ドキュメントにアクセスできます。

## <a name="prerequisites"></a>前提条件
* Java Development Kit (JDK 7 以降、Java バージョン 1.7 以降を使用)。
* Azure Data Lake Store アカウント。 「 [Azure ポータルで Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。
* [Maven](https://maven.apache.org/install.html)。 このチュートリアルでは、ビルドとプロジェクトの依存関係に Maven を使用します。 Maven や Gradle などのビルド システムを使用しなくてもビルドすることはできますが、これらのシステムを使用すると、依存関係の管理が容易になります。
* (オプション) [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) や [Eclipse](https://www.eclipse.org/downloads/) などの IDE。

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Azure Active Directory を使用して認証する方法
このチュートリアルでは、Azure AD アプリケーションのクライアント シークレットを使用して、Azure Active Directory トークンを取得します (サービス間認証)。 このトークンを使用して、ファイルとディレクトリの操作を実行する Data Lake Store クライアント オブジェクトを作成します。 クライアント シークレットを使用して Azure Data Lake Store で認証する際に実行する大まかな手順は次のとおりです。

1. Azure AD Web アプリケーションを作成します。
2. Azure AD Web アプリケーションのクライアント ID、クライアント シークレット、トークン エンドポイントを取得します。
3. 作成する Java アプリケーションからアクセスする Data Lake Store ファイル/フォルダーで、Azure AD Web アプリケーションのアクセスを構成します。

これらの手順を実行する方法については、「[Create an Active Directory application (Active Directory アプリケーションを作成する)](data-lake-store-authenticate-using-active-directory.md)」をご覧ください。

Azure Active Directory には、トークンを取得するための他のオプションも用意されています。 ブラウザーで実行されるアプリケーション、デスクトップ アプリケーションとして配布されるアプリケーション、オンプレミスまたは Azure 仮想マシンで実行されるサーバー アプリケーションなど、実際のシナリオに合わせてさまざまな認証メカニズムの中から選択できます。 また、パスワード、証明書、2 要素認証などの各種資格情報の中から選択することもできます。さらに、Azure Active Directory では、オンプレミスの Active Directory ユーザーをクラウドと同期することも可能です。 詳細については、「[Azure AD の認証シナリオ](../active-directory/active-directory-authentication-scenarios.md)」をご覧ください。 

## <a name="create-a-java-application"></a>Java アプリケーションの作成
[GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) で入手できるコード サンプルは、ストアでのファイルの作成、ファイルの連結、ファイルのダウンロード、ストアでのファイルの削除のプロセスを示しています。 このセクションでは、コードの主要部分について説明します。

1. コマンド ラインで [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) を使用するか、IDE を使用して、Maven プロジェクトを作成します。 IntelliJ を使用して Java プロジェクトを作成する方法については、[こちら](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html)をご覧ください。 Eclipse を使用してプロジェクトを作成する方法については、[こちら](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm)をご覧ください。 
2. Maven の **pom.xml** ファイルに次の依存関係を追加します。 **\</version>** タグと **\</project>** タグの間に、次のテキストのスニペットを追加します。
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.4</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    最初の依存関係では、maven リポジトリから Data Lake Store SDK (`azure-data-lake-store-sdk`) を使用します。 2 番目の依存関係 (`slf4j-nop`) では、このアプリケーションで使用するログ記録フレームワークを指定します。 Data Lake Store SDK では、[slf4j](http://www.slf4j.org/) ログ ファサードを使用します。slf4j を使用すると、log4j、Java ログ、logback などの多数の一般的なログ記録フレームの中から選択することも、ログを記録しないようにすることもできます。 この例ではログを無効にするため、**slf4j-nop** バインドを使用します。 アプリケーションで他のログ オプションを使用する場合は、[こちら](http://www.slf4j.org/manual.html#projectDep)をご覧ください。

### <a name="add-the-application-code"></a>アプリケーション コードの追加
コードには&3; つの主要部分があります。

1. Azure Active Directory トークンを取得する。
2. トークンを使用して Data Lake Store クライアントを作成する。
3. Data Lake Store クライアントを使用して操作を実行する。

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>手順 1: Azure Active Directory トークンを取得する
Data Lake Store SDK には、Data Lake Store アカウントとの対話に必要なセキュリティ トークンを管理できる便利な方法が用意されています。 ただし、使用する方法はこれらに限定されるわけではありません。 [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) や独自のカスタム コードの使用など、トークンを取得するその他の方法も使用できます。

Data Lake Store SDK を使用して、以前に作成した Active Directory Web アプリケーションのトークンを取得するには、`AccessTokenProvider` のサブクラスの&1; つを使用します (下の例では、`ClientCredsTokenProvider` を使用します)。 トークン プロバイダーは、メモリ内のトークンを取得するために使用する資格情報をキャッシュし、そのトークンの有効期限が切れそうになった場合に自動的に更新します。 トークンがカスタム トークンで取得されるように `AccessTokenProvider` のサブクラスを独自に作成することができますが、ここでは、SDK に用意されているものを使ってみましょう。

**FILL-IN-HERE** を、Azure Active Directory Web アプリケーションの実際の値に置き換えます。

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>手順 2: Azure Data Lake Store クライアント (ADLStoreClient) オブジェクトを作成する
[ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) オブジェクトを作成するには、Data Lake Store アカウント名と、最後の手順で生成されたトークン プロバイダーを指定する必要があります。 Data Lake Store アカウント名は、完全修飾ドメイン名である必要があります。 たとえば、**FILL-IN-HERE** を **mydatalakestore.azuredatalakestore.net** などに置き換えます。

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>手順 3: ADLStoreClient を使用してファイルとディレクトリの操作を実行する
次のコードには、一部の一般的な操作のスニペットの例が含まれています。 他の操作については、**ADLStoreClient** オブジェクトの完全な [Data Lake Store Java SDK API ドキュメント](https://azure.github.io/azure-data-lake-store-java/javadoc/)で確認できます。

ファイルの読み取りと書き込みには、標準の Java ストリームを使用します。 つまり、Data Lake Store ストリームの上の層に Java ストリームを配置することで、標準の Java 機能 (書式設定された出力の印刷ストリームや、追加機能の圧縮または暗号化ストリームなど) のメリットが得られます。

     // create file and write some content
     String filename = "/a/b/c.txt";
     OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
     PrintStream out = new PrintStream(stream);
     for (int i = 1; i <= 10; i++) {
         out.println("This is line #" + i);
         out.format("This is the same line (%d), but using formatted output. %n", i);
     }
     out.close();
    
    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>手順 4: アプリケーションをビルドして実行する
1. IDE 内から実行するには、**[実行]** ボタンを見つけてクリックします。 Maven から実行するには、[exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html) を使用します。
2. コマンド ラインから実行できるスタンドアロン jar を生成するには、[Maven アセンブリ プラグイン](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html)を使用して、すべての依存関係を含めて jar をビルドします。 [GitHub のサンプル ソース コード](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml)の pom.xml に、これを行う方法の例が含まれています。

## <a name="next-steps"></a>次のステップ
* [Java SDK の JavaDoc を確認する](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
* [Data Lake Store で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)



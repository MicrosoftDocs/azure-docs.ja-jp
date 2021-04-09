---
title: Java SDK - Data Lake Storage Gen1 に対するファイルシステム操作 - Azure
description: Azure Data Lake Storage Gen1 用の Java SDK を使用して、Data Lake Storage Gen1 に対して、フォルダーの作成、データ ファイルのアップロードとダウンロードなどのファイルシステム操作を実行します。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: a2c55a2d3277bbb6c3cf72f5ea703780d2a5e9bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87318846"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-java-sdk"></a>Java SDK を使用した Azure Data Lake Storage Gen1 に対するファイルシステム操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Azure Data Lake Storage Gen1 Java SDK を使用して、フォルダーの作成、データ ファイルのアップロードとダウンロードなどの基本操作を実行する方法について説明します。Data Lake Storage Gen1 について詳しくは、[Azure Data Lake Storage Gen1](data-lake-store-overview.md) に関する記事をご覧ください。

[Azure Data Lake Storage Gen1 Java API ドキュメント](https://azure.github.io/azure-data-lake-store-java/javadoc/)で、Data Lake Storage Gen1 に関する Java SDK API ドキュメントにアクセスできます。

## <a name="prerequisites"></a>前提条件
* Java Development Kit (JDK 7 以降、Java バージョン 1.7 以降を使用)。
* Data Lake Storage Gen1 アカウント。 「[Azure portal で Azure Data Lake Storage Gen1 の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。
* [Maven](https://maven.apache.org/install.html)。 このチュートリアルでは、ビルドとプロジェクトの依存関係に Maven を使用します。 Maven や Gradle などのビルド システムを使用しなくてもビルドすることはできますが、これらのシステムを使用すると、依存関係の管理が容易になります。
* (オプション) [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) や [Eclipse](https://www.eclipse.org/downloads/) などの IDE。

## <a name="create-a-java-application"></a>Java アプリケーションの作成
[GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) で入手できるコード サンプルは、ストアでのファイルの作成、ファイルの連結、ファイルのダウンロード、ストアでのファイルの削除のプロセスを示しています。 このセクションでは、コードの主要部分について説明します。

1. コマンド ラインで [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) を使用するか、IDE を使用して、Maven プロジェクトを作成します。 IntelliJ を使用して Java プロジェクトを作成する方法については、[こちら](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html)をご覧ください。 Eclipse を使用してプロジェクトを作成する方法については、[こちら](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm)をご覧ください。 

2. Maven の **pom.xml** ファイルに次の依存関係を追加します。 **\</project>** タグの前に次のスニペットを追加します。
   
    ```xml
    <dependencies>
        <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-data-lake-store-sdk</artifactId>
        <version>2.1.5</version>
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
    import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

    import java.io.*;
    import java.util.Arrays;
    import java.util.List;
    ```

## <a name="authentication"></a>認証

* アプリケーションのエンドユーザー認証については、[Data Lake Storage Gen1 による Java を使用したエンドユーザー認証](data-lake-store-end-user-authenticate-java-sdk.md)に関する記事をご覧ください。
* アプリケーションのサービス間認証については、[Data Lake Storage Gen1 による Java を使用したサービス間認証](data-lake-store-service-to-service-authenticate-java.md)に関する記事をご覧ください。

## <a name="create-a-data-lake-storage-gen1-client"></a>Data Lake Storage Gen1 クライアントの作成
[ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) オブジェクトを作成するには、Data Lake Storage Gen1 アカウント名と、Data Lake Storage Gen1 による認証時に生成したトークン プロバイダーを指定する必要があります (「[認証](#authentication)」セクションを参照)。 Data Lake Storage Gen1 アカウント名は、完全修飾ドメイン名である必要があります。 たとえば、**FILL-IN-HERE** を **mydatalakestoragegen1.azuredatalakestore.net** などに置き換えます。

```java
private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);
```

次のセクションのコード スニペットには、一般的なファイルシステム操作の例が含まれています。 他の操作については、**ADLStoreClient** オブジェクトの完全な [Data Lake Storage Gen1 Java SDK API ドキュメント](https://azure.github.io/azure-data-lake-store-java/javadoc/)で確認できます。

## <a name="create-a-directory"></a>ディレクトリを作成する

次のスニペットを使用して、指定した Data Lake Storage Gen1 アカウントのルートにディレクトリ構造を作成します。

```java
// create directory
client.createDirectory("/a/b/w");
System.out.println("Directory created.");
```

## <a name="create-a-file"></a>ファイルを作成する

次のスニペットを使用して、ディレクトリ構造にファイル (c.txt) を作成し、いくつかのデータをそのファイルに書き込みます。

```java
// create file and write some content
String filename = "/a/b/c.txt";
OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
PrintStream out = new PrintStream(stream);
for (int i = 1; i <= 10; i++) {
    out.println("This is line #" + i);
    out.format("This is the same line (%d), but using formatted output. %n", i);
}
out.close();
System.out.println("File created.");
```

バイト配列を使用してファイル (d.txt) を作成することもできます。

```java
// create file using byte arrays
stream = client.createFile("/a/b/d.txt", IfExists.OVERWRITE);
byte[] buf = getSampleContent();
stream.write(buf);
stream.close();
System.out.println("File created using byte array.");
```

前のスニペットで使用されている `getSampleContent` 関数の定義は、[GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) でサンプルの一部として入手できます。 

## <a name="append-to-a-file"></a>ファイルに追加する

次のスニペットを使用して、既存のファイルに内容を追加します。

```java
// append to file
stream = client.getAppendStream(filename);
stream.write(getSampleContent());
stream.close();
System.out.println("File appended.");
```

前のスニペットで使用されている `getSampleContent` 関数の定義は、[GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) でサンプルの一部として入手できます。

## <a name="read-a-file"></a>ファイルを読み取る

次のスニペットを使用して、Data Lake Storage Gen1 アカウントのファイルから内容を読み取ります。

```java
// Read File
InputStream in = client.getReadStream(filename);
BufferedReader reader = new BufferedReader(new InputStreamReader(in));
String line;
while ( (line = reader.readLine()) != null) {
    System.out.println(line);
}
reader.close();
System.out.println();
System.out.println("File contents read.");
```

## <a name="concatenate-files"></a>ファイルを連結する

次のスニペットを使用して、Data Lake Storage Gen1 アカウントの 2 つのファイルを連結します。 成功した場合、既存の 2 つのファイルは連結されたファイルで置き換えられます。

```java
// concatenate the two files into one
List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
client.concatenateFiles("/a/b/f.txt", fileList);
System.out.println("Two files concatenated into a new file.");
```

## <a name="rename-a-file"></a>ファイル名を変更する

次のスニペットを使用して、Data Lake Storage Gen1 アカウントのファイルの名前を変更します。

```java
//rename the file
client.rename("/a/b/f.txt", "/a/b/g.txt");
System.out.println("New file renamed.");
```

## <a name="get-metadata-for-a-file"></a>ファイルのメタデータを取得する

次のスニペットを使用して、Data Lake Storage Gen1 アカウントのファイルのメタデータを取得します。

```java
// get file metadata
DirectoryEntry ent = client.getDirectoryEntry(filename);
printDirectoryInfo(ent);
System.out.println("File metadata retrieved.");
```

## <a name="set-permissions-on-a-file"></a>ファイルに対するアクセス許可を設定する

次のスニペットを使用して、前のセクションで作成したファイルに対するアクセス許可を設定します。

```java
// set file permission
client.setPermission(filename, "744");
System.out.println("File permission set.");
```

## <a name="list-directory-contents"></a>ディレクトリの内容を一覧表示する

次のスニペットを使用して、ディレクトリの内容を再帰的に一覧表示します。

```java
// list directory contents
List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
System.out.println("Directory listing for directory /a/b:");
for (DirectoryEntry entry : list) {
    printDirectoryInfo(entry);
}
System.out.println("Directory contents listed.");
```

前のスニペットで使用されている `printDirectoryInfo` 関数の定義は、[GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) でサンプルの一部として入手できます。

## <a name="delete-files-and-folders"></a>ファイルとフォルダーを削除する

次のスニペットを使用して、Data Lake Storage Gen1 アカウントの指定したファイルとフォルダーを再帰的に削除します。

```java
// delete directory along with all the subdirectories and files in it
client.deleteRecursive("/a");
System.out.println("All files and folders deleted recursively");
promptEnterKey();
```

## <a name="build-and-run-the-application"></a>アプリケーションの構築と実行
1. IDE 内から実行するには、 **[実行]** ボタンを見つけてクリックします。 Maven から実行するには、[exec:exec](https://www.mojohaus.org/exec-maven-plugin/exec-mojo.html) を使用します。
2. コマンド ラインから実行できるスタンドアロン jar を生成するには、[Maven アセンブリ プラグイン](https://maven.apache.org/plugins/maven-assembly-plugin/usage.html)を使用して、すべての依存関係を含めて jar をビルドします。 [GitHub のサンプル ソース コード](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml)の pom.xml に例が含まれています。

## <a name="next-steps"></a>次のステップ
* [Java SDK の JavaDoc を確認する](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Data Lake Storage Gen1 でのデータのセキュリティ保護](data-lake-store-secure-data.md)



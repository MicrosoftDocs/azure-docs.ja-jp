---
title: "Azure AD Java コマンド ラインの概要 | Microsoft Docs"
description: "API にアクセスするためにユーザーをサインインする Java コマンド ライン アプリを構築する方法。"
services: active-directory
documentationcenter: java
author: xerners
manager: mbaldwin
editor: 
ms.assetid: 51e1a8f9-6ff0-4643-a350-0ba794e26fd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 01/23/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 4e6d859ee621a730e0d2c1062d3a31e7c053e798
ms.lasthandoff: 03/18/2017


---
# <a name="using-java-command-line-app-to-access-an-api-with-azure-ad"></a>Azure AD を使用して API にアクセスするための Java コマンド ライン アプリを使用する
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure AD を使用すると、数行のコードを追加するだけで、Web アプリの ID 管理を外部委託し、シングル サインインおよびサインアウトを提供することが、簡単に実現できます。  Java Web アプリでは、コミュニティ主導型の ADAL4J の Microsoft 実装を使用することで、これを実現できます。

  ここでは、ADAL4J を使用して次のことを行います。

* ID プロバイダーとして Azure AD を使用して、ユーザーをアプリにサインインします。
* ユーザーについての情報を表示します。
* ユーザーをアプリからサインアウトします。

これを行うには、次の手順を実行する必要があります。

1. アプリケーションを Azure AD に登録する
2. ADAL4J ライブラリを使用するようにアプリを設定する
3. ADAL4J ライブラリを使用してサインイン要求とサインアウト要求を Azure AD に発行する
4. ユーザーに関するデータを印刷する

最初に、[アプリのスケルトンをダウンロード](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip)するか、[完全なサンプルをダウンロード](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\\/archive/complete.zip)します。  アプリケーションの登録先となる Azure AD テナントも必要です。  テナントを所有していない場合は、「 [How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](active-directory-howto-tenant.md)」を参照して取得してください。

## <a name="1--register-an-application-with-azure-ad"></a>1.アプリケーションを Azure AD に登録する
アプリケーションがユーザー認証を処理できるようにするには、まず、アプリケーションをテナントに登録する必要があります。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 上部のバーにある自分のアカウントをクリックし、**[ディレクトリ]** の一覧から、アプリケーションを登録する Active Directory テナントを選択します。
3. 左側のナビゲーションで **[More Services (その他のサービス)]**をクリックし、**[Azure Active Directory]** を選択します。
4. **[アプリの登録]** をクリックして、**[追加]** を選択します。
5. 画面の指示に従い、新しい **Web アプリケーションまたは WebAPI**を作成します。
  * アプリケーションの **[名前]** には、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
  * **[サインオン URL]** は、アプリのベース URL です。  スケルトンの既定値は、 `http://localhost:8080/adal4jsample/`です。
6. 登録が完了すると、AAD により、アプリに一意のアプリケーション ID が割り当てられます。  この値は次のセクションで必要になるので、[アプリケーション] タブからコピーします。
7. アプリケーションの **[設定]**  ->  **[プロパティ]** ページで、アプリ ID URI を更新します。 **[アプリケーション ID/URI]** は、アプリケーションの一意識別子です。  形式は、`https://<tenant-domain>/<app-name>` (たとえば、`http://localhost:8080/adal4jsample/`) です。

アプリケーションのポータルで、**[設定]** ページからアプリケーション用の**キー**を作成し、コピーします。  このプロジェクトはすぐに必要になります。

## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisites-using-maven"></a>2.Maven を使用して、ADAL4J ライブラリと前提条件を使用するようにアプリを設定する
ここでは、OpenID Connect 認証プロトコルを使用するように ADAL4J を構成します。  ADAL4J は、サインイン要求とサインアウト要求の発行、ユーザー セッションの管理、ユーザーに関する情報の取得などを行うために使用されます。

* プロジェクトのルート ディレクトリで、`pom.xml` を開くか作成し、`// TODO: provide dependencies for Maven` を探して次のコードに置き換えます。

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>public-client-adal4j-sample</artifactId>
    <packaging>jar</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>public-client-adal4j-sample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.nimbusds</groupId>
            <artifactId>oauth2-oidc-sdk</artifactId>
            <version>4.5</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20090211</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>
</dependencies>
    <build>
        <finalName>public-client-adal4j-sample</finalName>
        <plugins>
                <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install</id>
                        <phase>install</phase>
                        <goals>
                            <goal>sources</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
        </plugins>
    </build>

</project>


```



## <a name="3-create-the-java-publicclient-file"></a>3.Java PublicClient ファイルを作成する
既に説明したとおり、ここでは Graph API を使用して、ログイン ユーザーに関するデータを取得します。 これを簡単に実現するには、**ディレクトリ オブジェクト**を表すファイルと、**ユーザー**を表す個々のファイルの両方を作成し、Java のオブジェクト指向パターンを使用できるようにします。

* `DirectoryObject.java` という名前のファイルを作成します。このファイルは、DirectoryObject に関する基本的なデータを格納するために使用します (後で他のグラフ クエリを実行する際にこのファイルを自由に使用してかまいません)。 次のコードをコピーして貼り付けることができます。

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


## <a name="compile-and-run-the-sample"></a>サンプルをコンパイルして実行する
ルート ディレクトリに戻り、次のコマンドを実行して、 `maven`を使用して作成したサンプルをビルドします。 この場合、依存関係用に作成した `pom.xml` ファイルが使用されます。

`$ mvn package`

これで、`/targets` ディレクトリに `adal4jsample.war` ファイルが作成されます。 そのファイルを Tomcat コンテナーにデプロイし、次の URL にアクセスします。 

`http://localhost:8080/adal4jsample/`

> [!NOTE]
> 最新の Tomcat サーバーで WAR をデプロイするのは非常に簡単です。 `http://localhost:8080/manager/` に移動し、``adal4jsample.war` ファイルのアップロードに関する指示に従うだけです。 ファイルは適切なエンドポイントに自動的にデプロイされます。
> 
> 

## <a name="next-steps"></a>次のステップ
お疲れさまでした。 これで、作業中の Java アプリケーションで、ユーザーの認証、OAuth 2.0 を使用した Web API の安全な呼び出し、ユーザーに関する基本情報の取得が可能になりました。  テナントに一連のユーザーを設定します (設定していない場合)。

参照用に、完成したサンプル (構成値を除く) が[ここに .zip として提供されています](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip)。または、GitHub から複製することもできます。

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```



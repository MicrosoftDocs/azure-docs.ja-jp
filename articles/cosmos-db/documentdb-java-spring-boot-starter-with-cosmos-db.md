---
title: "Azure Cosmos DB DocumentDB API で Spring Boot Starter を使用する方法"
description: "Spring Boot Initializer で作成されたアプリケーションを Azure Cosmos DB DocumentDB API で構成する方法について説明します。"
services: cosmos-db
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
keywords: Spring, Spring Boot Starter, Cosmos DB
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/08/2017
ms.author: robmcm;yungez;kevinzha
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: 273cc750857c5e466882060a38ac0f3475811e98
ms.contentlocale: ja-jp
ms.lasthandoff: 08/10/2017

---

# <a name="how-to-use-the-spring-boot-starter-with-azure-cosmos-db-documentdb-api"></a>Azure Cosmos DB DocumentDB API で Spring Boot Starter を使用する方法

## <a name="overview"></a>概要

**[Spring Framework]** は Java 開発者のエンタープライズ レベルのアプリケーション作成を支援するオープンソース ソリューションです。 このプラットフォームで構築される特に知られたプロジェクトの 1 つが [Spring Boot] です。これによって、スタンドアロンの Java アプリケーションの作成方法が簡略化されます。 Spring Boot を使い始めた開発者を支援するために、<https://github.com/spring-guides/> では、サンプルの Spring Boot パッケージがいくつか用意されています。 基本的な Spring Boot プロジェクトの一覧から選択するだけでなく、**[Spring Initializr]** は、開発者がカスタム Spring Boot アプリケーションの作成を開始できるように支援します。

Azure Cosmos DB は、開発者が DocumentDB、MongoDB、Graph、Table API などのさまざまな標準 API を使用してデータを操作できるようにするグローバル分散型データベース サービスです。 Microsoft の Spring Boot Starter を使用すると、開発者は、DocumentDB API を使用して Azure Cosmos DB と簡単に統合できる Spring Boot アプリケーションを使用できます。

この記事では、Azure Portal を使用して Azure Cosmos DB を作成してから、**Spring Initializr** を使用してカスタム Java アプリケーションを作成し、さらにカスタム アプリケーションに Spring Boot Starter 機能を追加して、DocumentDB API を使用して Azure Cosmos DB にデータを格納またはデータを取得する方法を示します。

## <a name="prerequisites"></a>前提条件

この記事の手順に従うには、次の前提条件が必要です。

* Azure サブスクリプション。Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典]を有効にするか、または[無料の Azure アカウント]にサインアップできます。

* [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/) バージョン 1.7 以降。

* [Apache Maven](http://maven.apache.org/) バージョン 3.0 以降。

## <a name="create-an-azure-cosmos-db-by-using-the-azure-portal"></a>Azure Portal を使用して Azure Cosmos DB を作成する

1. <https://portal.azure.com/> で Azure Portal を参照し、**[+新規]** をクリックします。

   ![Azure ポータル][AZ01]

1. **[データベース]**、**[Azure Cosmos DB]** の順にクリックします。

   ![Azure ポータル][AZ02]

1. **[Azure Cosmos DB]** ページで、次の情報を入力します。

   * データベースの URI として使用する一意の **ID** を入力します。 例: *wingtiptoysdata.documents.azure.com*。
   * API として **[SQL (Document DB)]\(SQL (Document DB)\)** を選択します。
   * データベースに使用する**サブスクリプション**を選択します。
   * データベースに対して新しい**リソース グループ**を作成するか、既存のリソース グループを選択するかを指定します。
   * データベースの**場所**を指定します。
   
   これらのオプションの指定後、**[作成]** をクリックしてデータベースを作成します。

   ![Azure ポータル][AZ03]

1. データベースが作成されると、それが Azure **ダッシュボード**に表示され、**[すべてのリソース]** ページと **[Azure Cosmos DB]** ページにも表示されます。 これらのいずれかの場所でデータベースをクリックすると、キャッシュのプロパティ ページを開くことができます。

   ![Azure ポータル][AZ04]

1. データベースのプロパティ ページが表示されたら、**[アクセス キー]** をクリックし、データベースの URI とアクセス キーをコピーします。これらの値は Spring Boot アプリケーションで使用します。

   ![Azure ポータル][AZ05]

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Spring Initializr でシンプルな Spring Boot アプリケーションを作成する

1. <https://start.spring.io/> に移動します。

1. **Java** で **Maven** プロジェクトを生成することを指定し、アプリケーションの **[グループ]** と **[アーティファクト]** に名前を入力して、**[プロジェクトの生成]** のボタンをクリックします。

   ![基本的な Spring Initializr オプション][SI01]

   > [!NOTE]
   >
   > Spring Initializr では、**[グループ]** と **[アーティファクト]** の名前を使用してパッケージ名を作成します (例: *com.example.wintiptoys*)。
   >

1. メッセージが表示されたら、ローカル コンピューター上のパスにプロジェクトをダウンロードします。

   ![カスタム Spring Boot プロジェクトのダウンロード][SI02]

1. ファイルをローカル システム上に展開したら、シンプルな Spring Boot アプリケーションの編集を開始できます。

   ![カスタム Spring Boot プロジェクト ファイル][SI03]

## <a name="configure-your-spring-boot-app-to-use-the-azure-spring-boot-starter"></a>Azure Spring Boot Starter を使用するように Spring Boot アプリを構成する

1. アプリのディレクトリで *pom.xml* ファイルを探します。次に例を示します。

   `C:\SpringBoot\wingtiptoys\pom.xml`

   または

   `/users/example/home/wingtiptoys/pom.xml`

   ![pom.xml ファイルを探す][PM01]

1. テキスト エディターで *pom.xml* ファイルを開き、`<dependencies>` の一覧に次の行を追加します。

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-documentdb-spring-boot-starter</artifactId>
      <version>0.1.4</version>
   </dependency>
   ```

   ![pom.xml ファイルの編集][PM02]

1. *pom.xml* ファイルを保存して閉じます。

## <a name="configure-your-spring-boot-app-to-use-your-azure-cosmos-db"></a>Azure Cosmos DB を使用するように Spring Boot アプリを構成する

1. アプリの *resources* ディレクトリ内で *application.properties* ファイルを探します。次に例を示します。

   `C:\SpringBoot\wingtiptoys\src\main\resources\application.properties`

   または

   `/users/example/home/wingtiptoys/src/main/resources/application.properties`

   ![application.properties ファイルを探す][RE01]

1. テキスト エディターで *application.properties* ファイルを開き、そのファイルに次の行を追加し、サンプルの値をデータベースの適切なプロパティに置き換えます。

   ```yaml
   # Specify the DNS URI of your Azure Cosmos DB.
   azure.documentdb.uri=https://wingtiptoys.documents.azure.com:443/

   # Specify the access key for your database.
   azure.documentdb.key=57686f6120447564652c20426f6220526f636b73==

   # Specify the name of your database.
   azure.documentdb.database=wingtiptoysdata
   ```

   ![application.properties ファイルの編集][RE02]

1. *application.properties* ファイルを保存して閉じます。

## <a name="add-sample-code-to-implement-basic-database-functionality"></a>基本的なデータベース機能を実装するサンプル コードを追加する

このセクションでは、ユーザー データを格納するための 2 つの Java クラスを作成します。その後、アプリケーションのメイン クラスを変更してユーザー クラスのインスタンスを作成し、それをデータベースに保存します。

### <a name="define-a-basic-class-for-storing-user-data"></a>ユーザー データを格納するための基本クラスを定義する

1. *User.java* という名前の新しいファイルをメイン アプリケーションの Java ファイルと同じディレクトリに作成します。

1. テキスト エディターで *User.java* ファイルを開き、次の行をファイルに追加して、データベースの値を格納および取得する汎用ユーザー クラスを定義します。

   ```java
   package com.example.wingtiptoys;

   public class User {
      private String id;
      private String firstName;
      private String lastName;
 
      public User(String id, String firstName, String lastName) {
         this.id = id;
         this.firstName = firstName;
         this.lastName = lastName;
      }
   
      public String getId() {
         return this.id;
      }

      public void setId(String id) {
         this.id = id;
      }

      public String getFirstName() {
         return firstName;
      }

      public void setFirstName(String firstName) {
         this.firstName = firstName;
      }

      public String getLastName() {
         return lastName;
      }

      public void setLastName(String lastName) {
         this.lastName = lastName;
      }

      @Override
      public String toString() {
         return String.format("User: %s %s", firstName, lastName);
      }
   }
   ```

1. *User.java* ファイルを保存して閉じます。

### <a name="define-a-data-repository-interface"></a>データ リポジトリ インターフェイスを定義する

1. *UserRepository.java* という名前の新しいファイルをメイン アプリケーションの Java ファイルと同じディレクトリに作成します。

1. テキスト エディターで *UserRepository.java* ファイルを開き、既定の DocumentDB リポジトリ インターフェイスを拡張するユーザー リポジトリ インターフェイスを定義する次の行をファイルに追加します。

   ```java
   package com.example.wingtiptoys;

   import com.microsoft.azure.spring.data.documentdb.repository.DocumentDbRepository;
   import org.springframework.stereotype.Repository;

   @Repository
   public interface UserRepository extends DocumentDbRepository<User, String> {}   
   ```

1. *UserRepository.java* ファイルを保存して閉じます。

### <a name="modify-the-main-application-class"></a>アプリケーションのメイン クラスを変更する

1. アプリのパッケージ ディレクトリでメイン アプリケーションの Java ファイルを探します。次に例を示します。

   `C:\SpringBoot\wingtiptoys\src\main\java\com\example\wingtiptoys\WingtiptoysApplication.java`

   または

   `/users/example/home/wingtiptoys/src/main/java/com/example/wingtiptoys/WingtiptoysApplication.java`

   ![アプリケーションの Java ファイルを探す][JV01]

1. テキスト エディターでメイン アプリケーションの Java ファイルを開き、ファイルに次の行を追加します。

   ```java
   package com.example.wingtiptoys;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.CommandLineRunner;

   @SpringBootApplication
   public class WingtiptoysApplication implements CommandLineRunner {

      @Autowired
      private UserRepository repository;
    
      public static void main(String[] args) {
         SpringApplication.run(WingtiptoysApplication.class, args);
      }

      public void run(String... var1) throws Exception {
         final User testUser = new User("testId", "testFirstName", "testLastName");

         repository.deleteAll();
         repository.save(testUser);

         final User result = repository.findOne(testUser.getId());

         System.out.printf("\n\n%s\n\n",result.toString());
      }
   }
   ```

1. メイン アプリケーションの Java ファイルを保存して閉じます。

## <a name="build-and-test-your-app"></a>アプリのビルドとテスト

1. コマンド プロンプトを開き、ディレクトリを *pom.xml* ファイルが置かれているフォルダーに変更します。次に例を示します。

   `cd C:\SpringBoot\wingtiptoys`

   または

   `cd /users/example/home/wingtiptoys`

1. Spring Boot アプリケーションを Maven でビルドし、実行します。次に例を示します。

   ```shell
   mvn package
   java -jar target/wingtiptoys-0.0.1-SNAPSHOT.jar
   ```

1. アプリケーションによっていくつかのランタイム メッセージが表示されます。値が正常に格納され、データベースから取得されたことを示すメッセージ `User: testFirstName testLastName` が表示されます。

   ![アプリケーションからの正常な出力][JV02]

1. 省略可能: Azure ポータルで **[ドキュメント エクスプローラー]** をクリックし、表示されたリストから項目を選んで内容を表示することで、データベースのプロパティ ページから Azure Cosmos DB の内容を表示できます。

   ![ドキュメント エクスプローラーを使用してデータを表示する][JV03]

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB と Java の使用について詳しくは、次の記事をご覧ください。

* [Azure Cosmos DB のドキュメント]。

* [Azure Cosmos DB: Java と Azure Portal による DocumentDB API アプリの構築][Build a DocumentDB API app with Java]

Azure での Spring Boot アプリケーションの使用の詳細については、次の記事を参照してください。

* [Azure の Spring Boot DocumenDB Starter](https://github.com/Microsoft/azure-spring-boot-starters/tree/master/azure-documentdb-spring-boot-starter-sample)

* [Spring Boot アプリケーションを Azure App Service にデプロイする](../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)

* [Running a Spring Boot Application on a Kubernetes Cluster in the Azure Container Service (Azure Container Service での Kubernetes クラスター上の Spring Boot アプリケーションの実行)](../container-service/container-service-deploy-spring-boot-app-on-kubernetes.md)

Java での Azure の使用の詳細については、 [Azure Java デベロッパー センター] と[Java Tools for Visual Studio Team Services] を参照してください。

<!-- URL List -->

[Azure Cosmos DB のドキュメント]: /azure/cosmos-db/
[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[Build a DocumentDB API app with Java]: https://docs.microsoft.com/azure/cosmos-db/create-documentdb-java
[無料の Azure アカウント]: https://azure.microsoft.com/pricing/free-trial/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[MSDN サブスクライバーの特典]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[AZ01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ01.png
[AZ02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ02.png
[AZ03]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ03.png
[AZ04]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ04.png
[AZ05]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ05.png

[SI01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/SI01.png
[SI02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/SI02.png
[SI03]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/SI03.png

[RE01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/RE01.png
[RE02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/RE02.png

[PM01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/PM01.png
[PM02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/PM02.png

[JV01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/JV01.png
[JV02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/JV02.png
[JV03]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/JV03.png


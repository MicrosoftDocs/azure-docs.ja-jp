---
title: Azure Media Services v3 API に接続する - Java
description: この記事では、Java を使って Azure Media Services v3 API に接続する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.author: inhenkel
ms.openlocfilehash: 769b4bc431040ee4d872fa60270196db96978ed2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94748184"
---
# <a name="connect-to-media-services-v3-api---java"></a>Media Services v3 API に接続する - Java

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、サービス プリンシパルによるサインイン方式を使用して Azure Media Services v3 Java SDK に接続する方法を説明します。

この記事では、サンプル アプリの開発に Visual Studio Code が使用されています。

## <a name="prerequisites"></a>前提条件

- 「[Writing Java with Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial)」に従って以下をインストールします。

   - JDK
   - Apache Maven
   - Java Extension Pack
- 環境変数 `JAVA_HOME` と `PATH` を必ず設定します。
- [Media Services アカウントを作成する](./create-account-howto.md) リソース グループ名と Media Services アカウント名を覚えておいてください。
- [API へのアクセス](./access-api-howto.md)に関するトピックの手順を実行します。 後の手順で必要になるので、サブスクリプション ID、アプリケーション ID (クライアント ID)、認証キー (シークレット)、テナント ID を控えておきます。

また、次を確認してください。

- [Java in Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Java Project Management in VS Code](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> [命名規則](media-services-apis-overview.md#naming-conventions)を確認してください。

## <a name="create-a-maven-project"></a>Maven プロジェクトを作成する

コマンドライン ツールを開き、`cd` を実行してプロジェクトを作成するディレクトリに移動します。
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

このコマンドを実行すると、`pom.xml`、`App.java`、およびその他のファイルが作成されます。 

## <a name="add-dependencies"></a>依存関係を追加する

1. Visual Studio Code で、プロジェクトがあるフォルダーを開きます
1. `pom.xml` を見つけて開きます
1. 必要な必要な依存関係を追加します。

   [Video encoding (ビデオ エンコード)](https://github.com/Azure-Samples/media-services-v3-java/blob/master/VideoEncoding/EncodingWithMESCustomPreset/pom.xml) サンプルの `pom.xml` を見てください。

## <a name="connect-to-the-java-client"></a>Java クライアントに接続する

1. `src\main\java\com\azure\ams` の下にある `App.java` ファイルを開き、使用するパッケージが先頭に指定されていることを確認します。

    ```java
    package com.azure.ams;
    ```
1. package ステートメントの下に、次の import ステートメントを追加します。
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. 要求を行うのに必要な Active Directory 資格情報を作成するために、App クラスの main メソッドに次のコードを追加し、[API へのアクセスに関するページ](./access-api-howto.md)で取得した値を設定します。
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. アプリケーションを実行します。

## <a name="see-also"></a>関連項目

- [Media Services の概念](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Java リファレンス](/java/api/overview/azure/mediaservices/management)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>次のステップ

`import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` を挿入して、エンティティの操作を開始できます。

コードの例について詳しくは、[Java SDK サンプル](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) リポジトリをご覧ください。

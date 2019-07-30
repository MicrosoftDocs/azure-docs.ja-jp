---
title: Windows で Azure Service Fabric サービスをコンテナー化する
description: Windows で Service Fabric Reliable Services と Reliable Actors サービスをコンテナー化する方法について説明します。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: anmolah
editor: roroutra
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/23/2018
ms.author: anmola
ms.openlocfilehash: 1210b34590484379ae487ad1b87e76a433e4582a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621815"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Windows で Service Fabric Reliable Services と Reliable Actors をコンテナー化する

Service Fabric は、Service Fabric マイクロサービス (Reliable Services と Reliable Actor ベースのサービス) のコンテナー化をサポートしています。 詳細については、「[Service Fabric とコンテナー](service-fabric-containers-overview.md)」を参照してください。

このドキュメントは、Windows コンテナー内でサービスを実行させるためのガイダンスとなっています。

> [!NOTE]
> 現在、この機能は Windows でのみ機能します。 コンテナーを実行するには、Windows Server 2016 with Containers でクラスターを実行する必要があります。

## <a name="steps-to-containerize-your-service-fabric-application"></a>Service Fabric アプリケーションをコンテナー化する手順

1. Visual Studio で Service Fabric アプリケーションを開きます。

2. クラス [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) をプロジェクトに追加します。 このクラスのコードは、コンテナー内で実行すると、アプリケーション内の Service Fabric ランタイム バイナリを適切に読み込むことができるヘルパーです。

3. コンテナー化するコード パッケージごとに、プログラムのエントリ ポイントのローダーを初期化します。 次のコード スニペットに示されている静的コンストラクターをプログラム エントリ ポイント ファイルに追加します。

   ```csharp
   namespace MyApplication
   {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
   ```

4. プロジェクトをビルドして[パッケージ](service-fabric-package-apps.md#Package-App)を作成します。 ビルドしてパッケージを作成するには、ソリューション エクスプローラーでアプリケーション プロジェクトを右クリックして **[パッケージ]** コマンドを選択します。

5. コンテナー化する必要があるコード パッケージごとに、PowerShell スクリプト [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1) を実行します。 使用方法は次のとおりです。

    完全版の .NET
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $applicationExeName = 'Name of the Code package executable.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
      ```
    .NET Core
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $dotnetCoreDllName = 'Name of the Code package dotnet Core Dll.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -DotnetCoreDllName $dotnetCoreDllName
      ```
      次のスクリプトでは、$dockerPackageOutputDirectoryPath に Docker アーティファクトを含むフォルダーを作成します。 生成された Dockerfile を任意のポートに公開 (`expose`) するよう修正し、 ニーズに基づいて設定スクリプトなどを実行します。

6. 次に、Docker コンテナー パッケージを[ビルド](service-fabric-get-started-containers.md#Build-Containers)してリポジトリに[プッシュ](service-fabric-get-started-containers.md#Push-Containers)します。

7. ApplicationManifest.xml と ServiceManifest.xml を変更し、コンテナー イメージ、リポジトリ情報、レジストリ認証、ポートとホスト間のマッピングを追加します。 マニフェストを変更する方法については、「[Windows で初めての Service Fabric コンテナー アプリケーションを作成する](service-fabric-get-started-containers.md)」を参照してください。 サービス マニフェストのコード パッケージ定義は、対応するコンテナー イメージで置き換える必要があります。 EntryPoint 型を ContainerHost 型に変更します。

   ```xml
   <!-- Code package is your service executable. -->
   <CodePackage Name="Code" Version="1.0.0">
   <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
   </EntryPoint>
   <!-- Pass environment variables to your container: -->
   </CodePackage>
   ```

8. レプリケーターとサービス エンドポイントについてポートとホスト間のマッピングを追加します。 Service Fabric によって実行時に両方のポートが割り当てられているため、割り当て済みポートをマッピングに使用するには、ContainerPort をゼロに設定します。

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. コンテナーの分離モードを構成するには、「[分離モードの構成]( https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-containers#configure-isolation-mode)」を参照してください。 Windows では、コンテナーの 2 つの分離モード (プロセスおよび Hyper-V) がサポートされます。 以下のスニペットは、アプリケーション マニフェスト ファイルで分離モードがどのように指定されるかを示しています。

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```
   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```

> [!NOTE] 
> 既定で、Service Fabric アプリケーションでは、アプリケーション固有の要求を受け入れるエンドポイントの形式で Service Fabric ランタイムにアクセスできます。 アプリケーションで信頼されていないコードがホストされている場合は、このアクセスを無効にすることを検討します。 詳細については、[Service Fabric でのセキュリティのベスト プラクティス](service-fabric-best-practices-security.md#platform-isolation)に関する記事を参照してください。 Service Fabric ランタイムへのアクセスを無効にするには、次のように、インポートしたサービス マニフェストに対応するアプリケーション マニフェストの Policies セクションに次の設定を追加します。
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. このアプリケーションをテストするには、バージョン 5.7 以降を実行しているクラスターにデプロイする必要があります。 ラインタイム バージョンが 6.1 以前の場合は、このプレビュー機能を有効にするようにクラスター設定を編集および更新する必要があります。 この[記事](service-fabric-cluster-fabric-settings.md)の手順に従って、次のように設定を追加します。
    ```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
    ```

11. 次に、編集したアプリケーション パッケージをこのクラスターに[デプロイ](service-fabric-deploy-remove-applications.md)します。

以上の手順で、クラスターを実行しているコンテナー化された Service Fabric アプリケーションが作成されます。

## <a name="next-steps"></a>次の手順
* [Service Fabric でのコンテナー](service-fabric-get-started-containers.md)の実行について確認します。
* Service Fabric の[アプリケーション ライフサイクル](service-fabric-application-lifecycle.md)について確認します。

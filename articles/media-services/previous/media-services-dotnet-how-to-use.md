---
title: .NET を使用して Media Services 開発を行うためにコンピューターをセットアップする方法
description: Media Services SDK for .NET を使用した Media Services の前提条件について説明します。 Visual Studio アプリケーションを作成する方法についても説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a2a9ecbcafa89b4981fe403d116ac0288e60b6b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013892"
---
# <a name="media-services-development-with-net"></a>.NET を使用した Media Services 開発

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](../latest/index.yml) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。

この記事では、.NET を使用して Media Services アプリケーションを開発する方法について説明します。

**Azure Media Services .NET SDK** ライブラリを利用すると、.NET を使用して Media Services に対するプログラミングを行うことができます。 .NET でより簡単に開発できるようにするための **Azure Media Services .NET SDK Extensions** ライブラリも提供されています。 このライブラリには、.NET コードの簡素化に役立つ一連の拡張メソッドとヘルパー関数が含まれています。 どちらのライブラリも、**NuGet** と **GitHub** を通じて利用できます。

## <a name="prerequisites"></a>前提条件
* 新規または既存の Azure サブスクリプションで作成した Media Services アカウント。 「[Media Services アカウントの作成方法](media-services-portal-create-account.md)」をご覧ください。
* オペレーティング システム: Windows 10、Windows 7、Windows 2008 R2、または Windows 8。
* .NET Framework 4.5 以降。
* 見ることができます。

## <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio プロジェクトの作成と構成
このセクションでは、Media Services 開発用に Visual Studio でプロジェクトを作成し、セットアップする方法を説明します。  ここでは C# Windows コンソール アプリケーション プロジェクトを使用していますが、Media Services アプリケーション用に作成できる他の種類のプロジェクト (Windows フォーム アプリケーション、ASP.NET Web アプリケーションなど) についても、同じセットアップ手順を利用できます。

このセクションでは、**NuGet** を使用して、Media Services .NET SDK 拡張機能と、依存するその他のライブラリを追加する方法を説明します。

また、GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) または [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)) から最新の Media Services .NET SDK bits を取得してソリューションをビルドし、クライアント プロジェクトに参照を追加できます。 必要な依存関係はすべて自動的にダウンロードされ抽出されます。

1. Visual Studio で、新しい C# コンソール アプリケーションを作成します。 **[名前]** 、 **[場所]** 、 **[ソリューション名]** を入力し、[OK] をクリックします。
2. ソリューションをビルドします。
3. **NuGet** を使用して、**Azure Media Services .NET SDK Extensions** (**windowsazure.mediaservices.extensions**) をインストールして追加します。 このパッケージをインストールすると、 **Media Services .NET SDK** が一緒にインストールされるほか、必要な依存関係がすべて追加されます。
   
    最新バージョンの NuGet がインストールされていることをご確認ください。 詳しい情報とインストール手順については、 [NuGet](https://nuget.codeplex.com/)をご覧ください。

    1. ソリューション エクスプローラーでプロジェクトの名前を右クリックし、 **[NuGet パッケージの管理]** を選択します。

    2. [NuGet パッケージの管理] ダイアログ ボックスが表示されます。

    3. オンライン ギャラリーで、Azure Media Services Extensions を検索し、 **[Azure Media Services .NET SDK Extensions]** \(**windowsazure.mediaservices.extensions**\) を選択し、 **[インストール]** ボタンをクリックします。
   
    4. プロジェクトが変更され、Media Services .NET SDK Extensions、Media Services .NET SDK、その他の依存アセンブリへの参照が追加されます。
4. よりクリーンな開発環境を実現するために、NuGet パッケージの復元を有効にすることを検討してください。 詳細については、「 [NuGet Package Restore (NuGet パッケージの復元)](https://docs.nuget.org/consume/package-restore)」をご覧ください。
5. **System.Configuration** アセンブリへの参照を追加します。 このアセンブリには、構成ファイル (App.config など) にアクセスするための System.Configuration.**ConfigurationManager** クラスが含まれています。
   
    1. [Manage References (参照の管理)] ダイアログを使用して参照を追加するには、ソリューション エクスプローラーでプロジェクト名を右クリックします。 **[追加]** 、 **[参照]** の順にクリックします。
   
    2. [参照の管理] ダイアログが表示されます。
    3. .NET Framework アセンブリで、System.Configuration アセンブリを探して選択し、 **[OK]** をクリックします。
6. App.config ファイルを開き、**appSettings** セクションをファイルに追加します。 Media Services API に接続するために必要な値を設定します。 詳細については、「[Azure AD Authentication を使用した Azure Media Services API へのアクセス](media-services-use-aad-auth-to-access-ams-api.md)」を参照してください。 

    **サービス プリンシパル** 認証方法を使って接続するために必要な値を設定します。

    ```xml
    <configuration>
    ...
        <appSettings>
            <add key="AMSAADTenantDomain" value="tenant"/>
            <add key="AMSRESTAPIEndpoint" value="endpoint"/>
            <add key="AMSClientId" value="id"/>
            <add key="AMSClientSecret" value="secret"/>
        </appSettings>
    </configuration>
    ```

7. **System.Configuration** の参照をプロジェクトに追加します。
8. Program.cs ファイルの先頭にある既存の **using** ステートメントを次のコードで上書きします。

    ```csharp      
    using System;
    using System.Configuration;
    using System.IO;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Collections.Generic;
    using System.Linq;
    ```

    これで、Media Services アプリケーションの開発準備が整いました。    

## <a name="example"></a>例

この小さい例では、AMS API に接続し、すべての使用可能なメディア プロセッサを一覧表示します。

```csharp
class Program
{
    // Read values from the App.config file.

    private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AMSAADTenantDomain"];
    private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
    private static readonly string _AMSClientId =
        ConfigurationManager.AppSettings["AMSClientId"];
    private static readonly string _AMSClientSecret =
        ConfigurationManager.AppSettings["AMSClientSecret"];
        
    private static CloudMediaContext _context = null;
    static void Main(string[] args)
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
        
        // List all available Media Processors
        foreach (var mp in _context.MediaProcessors)
        {
            Console.WriteLine(mp.Name);
        }
        
    }
 ```

## <a name="next-steps"></a>次のステップ

このあとは、[AMS API に接続](media-services-use-aad-auth-to-access-ams-api.md)したり、[開発](media-services-dotnet-get-started.md)を開始したりできます。


## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

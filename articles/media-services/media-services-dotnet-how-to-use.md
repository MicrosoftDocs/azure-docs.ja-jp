---
title: ".NET を使用して Media Services 開発を行うためにコンピューターをセットアップする方法"
description: "Media Services SDK for .NET を使用した Media Services の前提条件について説明します。 Visual Studio アプリケーションを作成する方法についても説明します。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/07/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 612b58db48e160cb1b4cfef1f8f4c2b203061064
ms.lasthandoff: 03/10/2017


---
# <a name="media-services-development-with-net"></a>.NET を使用した Media Services 開発
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

このトピックでは、.NET を使用して Media Services アプリケーションを開発する方法について説明します。

**Azure Media Services .NET SDK** ライブラリを利用すると、.NET を使用して Media Services に対するプログラミングを行うことができます。 .NET でより簡単に開発できるようにするための **Azure Media Services .NET SDK Extensions** ライブラリも提供されています。 このライブラリには、.NET コードの簡素化に役立つ一連の拡張メソッドとヘルパー関数が含まれています。 どちらのライブラリも、**NuGet** と **GitHub** を通じて利用できます。

## <a name="prerequisites"></a>前提条件
* 新規または既存の Azure サブスクリプションで作成した Media Services アカウント。 「[メディア サービス アカウントの作成方法](media-services-portal-create-account.md)」を参照してください。
* オペレーティング システム: Windows 10、Windows 7、Windows Server 2008 R2、または Windows 8。
* .NET Framework 4.5。
* 見ることができます。

## <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio プロジェクトの作成と構成
このセクションでは、Media Services 開発用に Visual Studio でプロジェクトを作成し、セットアップする方法を説明します。  ここでは C# Windows コンソール アプリケーション プロジェクトを使用していますが、Media Services アプリケーション用に作成できる他の種類のプロジェクト (Windows フォーム アプリケーション、ASP.NET Web アプリケーションなど) についても、同じセットアップ手順を利用できます。

このセクションでは、 **NuGet** を使用して、Media Services .NET SDK と依存するその他のライブラリを追加する方法を説明します。

また、GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) または [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)) から最新の Media Services .NET SDK bits を取得してソリューションをビルドし、クライアント プロジェクトに参照を追加できます。 必要な依存関係はすべて自動的にダウンロードされ抽出されます。

1. Visual Studio で、新しい C# コンソール アプリケーションを作成します。 **[名前]**、**[場所]**、**[ソリューション名]** を入力し、[OK] をクリックします。
2. ソリューションをビルドします。
3. **NuGet** を使用して、**Azure Media Services .NET SDK Extensions** をインストールして追加します。 このパッケージをインストールすると、 **Media Services .NET SDK** が一緒にインストールされるほか、必要な依存関係がすべて追加されます。
   
    最新バージョンの NuGet がインストールされていることをご確認ください。 詳しい情報とインストール手順については、 [NuGet](http://nuget.codeplex.com/)をご覧ください。
4. ソリューション エクスプローラーでプロジェクトの名前を右クリックし、[NuGet パッケージの管理] を選択します。
   
    [NuGet パッケージの管理] ダイアログ ボックスが表示されます。
5. オンライン ギャラリーで、Azure Media Services Extensions を検索し、[Azure Media Services .NET SDK Extensions] を選択し、[インストール] をクリックします。
   
    プロジェクトが変更され、Media Services .NET SDK Extensions、Media Services .NET SDK、その他の依存アセンブリへの参照が追加されます。
6. よりクリーンな開発環境を実現するために、NuGet パッケージの復元を有効にすることを検討してください。 詳細については、「 [NuGet Package Restore (NuGet パッケージの復元)](http://docs.nuget.org/consume/package-restore)」をご覧ください。
7. **System.Configuration** アセンブリへの参照を追加します。 このアセンブリには、構成ファイル (App.config など) にアクセスするための System.Configuration.**ConfigurationManager** クラスが含まれています。
   
    [Manage References (参照の管理)] ダイアログを使用して参照を追加するには、ソリューション エクスプローラーでプロジェクト名を右クリックします。 次に、[追加と参照] を選択します。
   
    [参照の管理] ダイアログが表示されます。
8. .NET framework アセンブリで、System.Configuration アセンブリを探して選択し、[OK] をクリックします。
9. App.config ファイルを開き (既定で追加されていない場合はファイルをプロジェクトに追加してください)、ファイルに *appSettings* セクションを追加します。     
   Azure Media Services のアカウント名とアカウント キーの値を設定します。次の例をご覧ください。
   
    名前とキーの値を検索するには、Azure Portal に移動してアカウントを選択します。 [設定] ウィンドウが右側に表示されます。 [設定] ウィンドウで、[キー] を選択します。 各テキスト ボックスの横にあるアイコンをクリックすると、値がシステム クリップボードにコピーされます。

        <configuration>
        ...
            <appSettings>
              <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
              <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
            </appSettings>

        </configuration>

1. Program.cs ファイルの先頭にある既存の **using** ステートメントを次のコードで上書きします。
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;

これで、Media Services アプリケーションの開発準備が整いました。    

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



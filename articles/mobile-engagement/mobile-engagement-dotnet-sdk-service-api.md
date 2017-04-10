---
title: ".NET SDK を使用して Azure Mobile Engagement サービス API にアクセスする"
description: "Mobile Engagement .NET SDK を使用して Azure Mobile Engagement サービス API にアクセスする方法について説明します"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: c07728aa-43f2-4238-8b4a-c9eddf9d838b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 57b2abc37551a782cb7106b9fc4540ce2ba37732


---
# <a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>.NET SDK を使用して Azure Mobile Engagement サービス API にアクセスする
Azure Mobile Engagement では、デバイス、リーチ/プッシュ キャンペーンなどを管理するために、API のセットが公開されています。これらの API と対話するために、任意の言語の SDK を生成するツールと共に使用できる、[Swagger ファイル](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json)も提供しています。 Swagger ファイルから SDK を生成するには、 [AutoRest](https://github.com/Azure/AutoRest) を使用することをお勧めします。 

C# ラッパーを使用してこれらの API と対話できるようにするのと同様の方法で .NET SDK を作成しましたので、認証トークン ネゴシエーションを行ったり、自分で更新したりする必要はありません。  

このサンプルでは、.NET SDK を使用するために従う一連の手順について説明します。

1. まず、 [こちら](mobile-engagement-api-authentication.md#authentication)に説明されているとおりに、Azure Active Directory を使用して、API の認証をセットアップする必要があります。 これらの手順が完了すると、有効な **SubscriptionId**、**TenantId**、**ApplicationId**、および **Secret** を入手できます。 
2. アナウンス キャンペーンを作成するシナリオを含む .NET SDK の操作を示すために、単純な Windows コンソール アプリを使用します。 そのため、Visual Studio を開き、 **コンソール アプリケーション**を作成します。   
3. 次に、 **こちら** の Nuget ギャラリーで [Microsoft Azure Engagement 管理ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/)として利用可能な .NET SDK をダウンロードする必要があります。
   Visual Studio から Nuget をインストールしている場合は、パッケージを検索するときに、 **[プレリリースを含める]** オプションにチェック マークがあることを確認する必要があります。
   
    ![][1]
4. `Program.cs` ファイルに、次の名前空間を追加します。
   
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;
5. 次に、認証およびアナウンス キャンペーンを作成している Mobile Engagement アプリと対話するために使用する、次の定数を定義する必要があります。
   
        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";
   
        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";
   
        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";
6. Mobile Engagement SDK メソッドを呼び出すために使用する `EngagementManagementClient` 変数を定義します。
   
        static EngagementManagementClient engagementClient; 
7. `Main` メソッドに次を追加します。
   
        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();
   
                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }
8. 最初に認証を行い、次にアナウンス キャンペーンの作成を計画する Mobile Engagement アプリと関連付けて、 `EngagementManagementClient` の初期化を処理する次のメソッドを定義します。
   
        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
   
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;
   
            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }
   
   > [!IMPORTANT]
   > AppName パラメーターの Microsoft Azure 管理ポータルで定義した **アプリ リソース名** を使用する必要があることに注意してください。 
   > 
   > 
9. 最後に、タイトルとメッセージで単純な **AnyTime** と  & **Notification-only** キャンペーンを作成するために、前に初期化された EngagementClient の使用を処理する CreateCampaign メソッドを定義します。 
   
        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/en-us/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );
   
            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/en-us/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }
10. コンソール アプリを実行すると、キャンペーンの作成の成功が次のように表示されます。
    
    **キャンペーン ID '159' は正常に作成され、'ドラフト' 状態にあります**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png



<!--HONumber=Nov16_HO3-->



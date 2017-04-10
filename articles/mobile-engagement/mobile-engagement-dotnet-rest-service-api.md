---
title: "REST API を使用して Azure Mobile Engagement サービス API にアクセスする"
description: "Mobile Engagement REST API を使用して Azure Mobile Engagement サービス API にアクセスする方法について説明します"
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: 
ms.assetid: e8df4897-55ee-45df-b41e-ff187e3d9d12
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/05/2016
ms.author: wesmc;ricksal
translationtype: Human Translation
ms.sourcegitcommit: 555342e88c912a3f43c578a40dc34933996ade4c
ms.openlocfilehash: 512276d151833ab5c65b663d8f2af43153e1d55b


---
# <a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>REST を使用して Azure Mobile Engagement サービス API にアクセスする
Azure Mobile Engagement では、デバイス、リーチ/プッシュ キャンペーンなどを管理するために、[Azure Mobile Engagement REST API](https://msdn.microsoft.com/library/azure/mt683754.aspx) が提供されます。このサンプルでは、直接 REST API を使用して、アナウンス キャンペーンを作成してアクティブ化し、一連のデバイスにプッシュします。 

REST API を直接使用したくない場合は、任意の言語の SDK を生成するツールと共に使用できる、 [Swagger ファイル](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) も提供しています。 Swagger ファイルから SDK を生成するには、 [AutoRest](https://github.com/Azure/AutoRest) を使用することをお勧めします。 C# ラッパーを使用してこれらの API と対話できるようにするのと同様の方法で .NET SDK を作成しましたので、認証トークン ネゴシエーションを行ったり、自分で更新したりする必要はありません。 このラッパーを使用して同様のサンプルについて確認するには、「 [サービス API の .NET SDK のサンプル](mobile-engagement-dotnet-sdk-service-api.md)

このサンプルでは、直接 REST API を使用して、アナウンス キャンペーンを作成およびアクティブ化します。 

## <a name="adding-a-username-appinfo-to-the-mobile-engagement-app"></a>user_name AppInfo を Mobile Engagement アプリに追加する
このサンプルには、Mobile Engagement アプリに追加された app-info タグが必要です。 アプリの Engagement ポータルで、**[設定]** > **[タグ (app-info)]** > **[新しいタグ (app-info)]** の順にクリックして、タグを追加できます。 **user_name** という名前の新しいタグを、**文字列**型として追加します。

![](./media/mobile-engagement-dotnet-rest-service-api/user-name-app-info.png)

「[Windows ユニバーサル アプリの Azure Mobile Engagement の概要](mobile-engagement-windows-store-dotnet-get-started.md)」に従った場合、次のコードのような app-info タグを送信するには、単に `MainPage` クラスの `OnNavigatedTo()` をオーバーライドして、**user_name** タグの送信をテストできます。

    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        base.OnNavigatedTo(e);

        String name = "Wesley"; // Prompt the user to provide this in your client app.

        Dictionary<object, object> info = new Dictionary<object, object>();
        info.Add("user_name", name);
        EngagementAgent.Instance.SendAppInfo(info);
    }



## <a name="creating-the-service-api-app"></a>サービス API アプリを作成する
1. まず、このサンプルを使用するには、4 つの認証パラメーターが必要になります。 そのパラメーターは、**SubscriptionId**、**TenantId**、**ApplicationId**、および **Secret** です。 これらの認証パラメーターを取得するには、 *認証* チュートリアルの [1 回限りのセットアップ (スクリプトを使用)](mobile-engagement-api-authentication.md#authentication) セクションで示される、PowerShell スクリプトのアプローチを使用することをお勧めします。 
2. 単純な Windows コンソール アプリを使用して、新しいアナウンス キャンペーンを作成およびアクティブ化するための REST サービス API の操作を説明します。 そのため、Visual Studio を開き、新しい **コンソール アプリケーション**を作成します。   
3. 次に、 **Newtonsoft.Json** NuGet パッケージをプロジェクトにします。
4. `Program.cs` ファイルで、次の名前空間に次の `using` ステートメントを追加します。
   
        using System.IO;
        using System.Net;
        using Newtonsoft.Json.Linq;
        using Newtonsoft.Json;
5. 次に、 `Program` クラスで次の定数を定義する必要があります。 これらは、認証およびアナウンス キャンペーンを作成している Mobile Engagement アプリと対話するために使用されます。

        // Parameters needed for authentication of API calls.
        // These are returned from the PowerShell script in the authentication tutorial. 
        // https://azure.microsoft.com/documentation/articles/mobile-engagement-api-authentication/
        static String SubscriptionId = "<Your Subscription Id>";
        static String TenantId = "<Your TenantId>";
        static String ApplicationId = "<Your Application Id>";
        static String Secret = "<Your Secret>";

        // The token for authenticating with your Mobile Engagement app.
        static String Token = null;

        // This is the Azure Resource group concept for grouping together resources 
        // See: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        static String ResourceGroup = "MobileEngagement";

        // For Mobile Engagement operations
        // App Collection Name from the Azure portal 
        static String Collection = "<Your App Collection Name>";

        // Application Resource Name - make sure you are using the one as specified in the dashboard of the
        // Azure portal. (This is NOT the App Name)
        static String AppName = "WesmcRestTest-windows";

        // New campaign id returned from creating the new campaign and used to activate and push the campaign
        // a set of devices.
        static String NewCampaignID = null;

        // This list will hold the device Ids we choose to push the campaign to.
        static List<String> deviceList = new List<String>();


1. 次の 2 つのメソッドを `Program` クラスに追加します。 これらは、REST API を実行して、コンソールに応答を表示するために使用されます。
   
        private static async Task<HttpWebResponse> ExecuteREST(string httpMethod, string uri, string authToken, WebHeaderCollection headers = null, string body = null, string contentType = "application/json")
        {
            //=== Execute the request 
            HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(uri);
            HttpWebResponse response = null;
   
            request.Method = httpMethod;
            request.ContentType = contentType;
            request.ContentLength = 0;
   
            if (authToken != null)
                request.Headers.Add("Authorization", authToken);
   
            if (headers != null)
            {
                request.Headers.Add(headers);
            }
   
            if (body != null)
            {
                byte[] bytes = Encoding.UTF8.GetBytes(body);
   
                try
                {
                    request.ContentLength = bytes.Length;
                    Stream requestStream = request.GetRequestStream();
                    requestStream.Write(bytes, 0, bytes.Length);
                    requestStream.Close();
                }
                catch (Exception e)
                {
                    Console.WriteLine(e.Message);
                }
            }
   
            try
            {
                response = (HttpWebResponse)await request.GetResponseAsync();
            }
            catch (WebException we)
            {
                if (we.Response != null)
                {
                    response = (HttpWebResponse)we.Response;
                }
                else
                    Console.WriteLine(we.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
   
            return response;
        }

        private static void DisplayResponse(dynamic data, HttpWebResponse response)
        {
            Console.WriteLine("HTTP Status " + (int)response.StatusCode + " : " + response.StatusDescription);
            Console.WriteLine(data + "\n");
        }

    }

1. 次のコードを `Main` メソッドに追加して、収集した認証パラメーターを使用して認証トークンを生成します。
   
        //***************************************************************************
        //*** Get a valid authorization token with your authentication parameters ***
        //***************************************************************************
   
        String methodUrl = "https://login.microsoftonline.com/" + TenantId + "/oauth2/token";
        String requestBody = "grant_type=client_credentials&client_id=" + ApplicationId +
                            "&client_secret=" + Secret +
                            "&resource=https://management.core.windows.net/";
        Console.WriteLine("Getting authorization token...\n");
        HttpWebResponse response = ExecuteREST("POST", methodUrl, null, null, requestBody, null).Result;
        Stream receiveStream = response.GetResponseStream();
        StreamReader readStream = new StreamReader(receiveStream, Encoding.UTF8);
        dynamic data = JObject.Parse(readStream.ReadToEnd());
        readStream.Close();
        receiveStream.Close();
        DisplayResponse(data, response);
   
        if (response.StatusCode == HttpStatusCode.OK)
        {
            Token = data.token_type + " " + data.access_token;
            Console.WriteLine("Got authorization token...");
            Console.WriteLine("Authorization : " + Token + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to get authorization token. Check your parameters for API calls.\n");
            return;
        }
2. これで、 [キャンペーンの作成](https://msdn.microsoft.com/library/azure/mt683742.aspx) の REST API を使用して新しいリーチ キャンペーンを作成できる、有効な認証トークンができました。 新しいキャンペーンは、タイトルとメッセージを持つ、単純な**時間指定なし**と & **通知のみ**のアナウンス キャンペーンになります。 次のスクリーン ショットで示されるように、手動のプッシュ キャンペーンになります。 つまり、このキャンペーンは、API を使用してのみプッシュされます。

    ![](./media/mobile-engagement-dotnet-rest-service-api/manual-push.png)

    次のコードを `Main` メソッドに追加して、次のアナウンス キャンペーンを作成します。 

        //*****************************************************************************
        //*** Create a campaign to send a notification using the user-name app-info ***
        //*****************************************************************************

        String newCampaignMethodUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
               "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
               Collection + "/apps/" + AppName + "/campaigns/announcements?api-version=2014-12-01";

        String campaignRequestBody = "{ \"name\": \"BirthdayCoupon\", " +
                                        "\"type\": \"only_notif\", " +
                                        "\"deliveryTime\": \"any\", " +
                                        "\"notificationType\": \"popup\", " +
                                        "\"pushMode\":\"manual\", " +
                                        "\"notificationTitle\": \"Happy Birthday ${user_name}\", " +
                                        "\"notificationMessage\": \"Take extra 10% off on your orders today!\"}";

        Console.WriteLine("Creating new campaign...\n");
        HttpWebResponse newCampaignResponse = ExecuteREST("POST", newCampaignMethodUrl, Token, null, campaignRequestBody).Result;
        Stream receiveCampaignStream = newCampaignResponse.GetResponseStream();
        StreamReader readCampaignStream = new StreamReader(receiveCampaignStream, Encoding.UTF8);
        dynamic newCampaignData = JObject.Parse(readCampaignStream.ReadToEnd());
        readCampaignStream.Close();
        receiveCampaignStream.Close();
        DisplayResponse(newCampaignData, newCampaignResponse);

        if (newCampaignResponse.StatusCode == HttpStatusCode.Created)
        {
            NewCampaignID = newCampaignData.id;
            Console.WriteLine("Created new campaign...");
            Console.WriteLine("New Campaign Id    : " + NewCampaignID + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to create birthday campaign.\n");
            return;
        }


1. このキャンペーンを任意のデバイスにプッシュできるようにするには、キャンペーンをアクティブ化する必要があります。 `NewCampaignID` 変数で、新しいキャンペーンの ID を保存しました。 これを URI パス パラメーターとして使用して、 [キャンペーンのアクティブ化](https://msdn.microsoft.com/library/azure/mt683745.aspx) の REST API を使用し、キャンペーンをアクティブ化します。 これは、API を使用して手動でプッシュされるだけにもかかわらず、キャンペーンの状態は **スケジュール済み** に変更されます。
   
    次のコードを `Main` メソッドに追加して、次のアナウンス キャンペーンをアクティブ化します。 
   
        //******************************************
        //*** Activate the new birthday campaign ***
        //******************************************
   
        String activateCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID +
                   "/activate?api-version=2014-12-01";
   
        Console.WriteLine("Activating new campaign (ID : " + NewCampaignID + ")...\n");
        HttpWebResponse activateCampaignResponse = ExecuteREST("POST", activateCampaignUrl, Token).Result;
        Stream activateCampaignStream = activateCampaignResponse.GetResponseStream();
        StreamReader activateCampaignReader = new StreamReader(activateCampaignStream, Encoding.UTF8);
        dynamic activateCampaignData = JObject.Parse(activateCampaignReader.ReadToEnd());
        activateCampaignReader.Close();
        activateCampaignStream.Close();
        DisplayResponse(activateCampaignData, activateCampaignResponse);
   
        if (activateCampaignResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Activated new campaign...");
            Console.WriteLine("New Campaign State : " + activateCampaignData.state + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to activate birthday campaign.\n");
            return;
        }
2. キャンペーンをプッシュするには、通知を受信するユーザーのデバイス ID を提供する必要があります。 [デバイスをクエリする](https://msdn.microsoft.com/library/azure/mt683826.aspx) REST API を使用して、すべてのデバイス ID を取得します。 デバイス ID が **user_name** appInfo と関連付けられている場合は、一覧に各デバイス ID を追加します。
   
   次のコードを `Main` メソッドに追加して、すべてのデバイス ID を取得し、deviceList を入力します。
   
       //************************************************************************
       //*** Now that the manualPush campaign is activated, get the deviceIds ***
       //*** that you want to trigger the push campaign on.                   ***
       //************************************************************************
   
       String getDevicesUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                 "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                  Collection + "/apps/" + AppName + "/devices?api-version=2014-12-01";
   
       Console.WriteLine("Getting device IDs...\n");
       HttpWebResponse devicesResponse = ExecuteREST("GET", getDevicesUrl, Token).Result;
       Stream devicesStream = devicesResponse.GetResponseStream();
       StreamReader devicesReader = new StreamReader(devicesStream, Encoding.UTF8);
       dynamic devicesData = JObject.Parse(devicesReader.ReadToEnd());
       devicesReader.Close();
       devicesStream.Close();
       DisplayResponse(devicesData, devicesResponse);
   
       if (devicesResponse.StatusCode == HttpStatusCode.OK)
       {
           Console.WriteLine("Got devices.");
           foreach (dynamic device in devicesData.value)
           {
               // Just adding all in this example
               if (device.appInfo.user_name != null)
               {
                   Console.WriteLine("Adding device for push campaign...");
                   Console.WriteLine("Device ID    : " + device.deviceId);
                   Console.WriteLine("user_name    : " + device.appInfo.user_name);
                   deviceList.Add((String)device.deviceId);
               }
           }
           Console.WriteLine();
       }
       else
       {
           Console.WriteLine("*** Failed to get devices.\n");
           return;
       }
3. 最後に、 [キャンペーンのプッシュ](https://msdn.microsoft.com/library/azure/mt683734.aspx) の REST API を使用して、一覧にあるすべてのデバイス ID にキャンペーンをプッシュします。 これは **アプリ内** 通知です。 そのため、ユーザーが受信できるように、アプリケーションがデバイス上で実行されている必要があります。
   
   次のコードを `Main` メソッドに追加して、deviceList にあるデバイスにキャンペーンをプッシュします。
   
       //**************************************************************
       //*** Trigger the manualPush campaign on the desired devices ***
       //**************************************************************
   
       String pushCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                 "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                  Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID + 
                  "/push?api-version=2014-12-01";
   
       Console.WriteLine("Triggering push for new campaign (ID : " + NewCampaignID + ")...\n");
       String deviceIds = "{\"deviceIds\":" + JsonConvert.SerializeObject(deviceList) + "}";
       Console.WriteLine("\n" + deviceIds + "\n");
       HttpWebResponse pushDevicesResponse = ExecuteREST("POST", pushCampaignUrl, Token, null, deviceIds).Result;
       Stream pushDevicesStream = pushDevicesResponse.GetResponseStream();
       StreamReader pushDevicesReader = new StreamReader(pushDevicesStream, Encoding.UTF8);
       dynamic pushDevicesData = JObject.Parse(pushDevicesReader.ReadToEnd());
       pushDevicesReader.Close();
       pushDevicesStream.Close();
       DisplayResponse(pushDevicesData, pushDevicesResponse);
   
       if (pushDevicesResponse.StatusCode == HttpStatusCode.OK)
       {
           Console.WriteLine("Triggered push on new campaign.\n");
       }
       else
       {
           Console.WriteLine("*** Failed to push campaign to the device list.\n");
       }
4. コンソール アプリを構築して実行します。 出力は次のようになります。

        C:\Users\Wesley\AzME_Service_API_Rest\test.exe

        Getting authorization token...

        HTTP Status 200 : OK
        {
          "token_type": "Bearer",
          "expires_in": "3599",
          "expires_on": "1458085162",
          "not_before": "1458081262",
          "resource": "https://management.core.windows.net/",
          "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPW
        b3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFh
        NzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0cy53
        MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoiNzJm
        F5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE3OAE
        hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-HASI8
        }

        Got authorization token...
        Authorization : Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNN
        aW5kb3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYt
        Zi1jNzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0
        OGU3MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoi
        iI-oF5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE
        vsf3hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-H

        Creating new campaign...

        HTTP Status 201 : Created
        {
          "id": 24,
          "state": "draft"
        }

        Created new campaign...
        New Campaign Id    : 24

        Activating new campaign (ID : 24)...

        HTTP Status 200 : OK
        {
          "id": 24,
          "state": "scheduled"
        }

        Activated new campaign...
        New Campaign State : scheduled

        Getting device IDs...

        HTTP Status 200 : OK
        {
          "value": [
            {
              "meta": {
                "lastSeen": 1458080534371,
                "firstSeen": 1458080534371,
                "lastLocation": 1458081389617,
                "lastInfo": 1458080571603
              },
              "appInfo": {
                "user_name": "Wesley"
              },
              "deviceId": "1d6208b8f281203ecb49431e2e5ce6b3"
            },
            {
              "meta": {
                "lastSeen": 1457990584698,
                "firstSeen": 1457949384025,
                "lastLocation": 1457990914895,
                "lastInfo": 1457990584597
              },
              "appInfo": {
                "user_name": "Wesley"
              },
              "deviceId": "302486644890e26045884ee5aa0619ec"
            }
          ]
        }

        Got devices.
        Adding device for push campaign...
        Device ID    : 1d6208b8f281203ecb49431e2e5ce6b3
        user_name    : Wesley
        Adding device for push campaign...
        Device ID    : 302486644890e26045884ee5aa0619ec
        user_name    : Wesley

        Triggering push for new campaign (ID : 24)...
        
        {"deviceIds":["1d6208b8f281203ecb49431e2e5ce6b3","302486644890e26045884ee5aa0619ec"]}

        HTTP Status 200 : OK
        {
          "invalidDeviceIds": []
        }

        Triggered push on new campaign.



<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png



<!--HONumber=Nov16_HO3-->



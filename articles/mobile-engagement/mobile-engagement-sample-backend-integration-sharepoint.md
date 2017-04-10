---
title: "Azure Mobile Engagement - バックエンドの統合"
description: "SharePoint バックエンドに Azure Mobile Engagement を接続して SharePoint からキャンペーンを作成する"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 06297b43-579f-46e6-8a58-961a68f9aa09
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: d49f1094f4c3f170f3618f3e19e42266f9ae8858
ms.lasthandoff: 03/01/2017


---
# <a name="azure-mobile-engagement---api-integration"></a>Azure Mobile Engagement - API の統合
自動化されたマーケティング システムでは、マーケティング キャンペーンの作成とアクティブ化も自動で行われます。 この目的のために、Azure Mobile Engagement では API を使用して自動マーケティング キャンペーンを作成することもできます。 

一般に顧客は、マーケティング キャンペーンの一環として、Mobile Engagement のフロント エンド インターフェイスを使用してアナウンスやポーリングなどを作成します。 しかし、マーケティング キャンペーンが成熟するにつれて、完全に自動化されたパイプラインを作成し、バックエンド システム (CRM システムや、SharePoint のような CMS システムなど) から入力されたデータに基づいて Mobile Engagement でキャンペーンを動的に作成できるようにするために、バックエンド システムに封じ込められたデータを活用することが必要になります。 

![][5]

このチュートリアルで扱うシナリオでは、まず SharePoint のビジネス ユーザーが SharePoint リストにマーケティング データを入力します。そして、自動化されたプロセスが利用可能な REST API を使用してこのリストから項目を取得し、Mobile Engagement システムと通信して、SharePoint のデータからマーケティング キャンペーンを作成します。 

> [!IMPORTANT]
> おおまかに言うと、このサンプルでは認証およびパラメーターの受け渡しという、API の呼び出しに関する&2; つの主要な側面を詳細に扱っているため、ここを出発点として、あらゆる Mobile Engagement REST API を呼び出す方法を身に付けることができます。 
> 
> 

## <a name="sharepoint-integration"></a>SharePoint の統合
1. サンプルの SharePoint リストは次のようなものです。 **Title**、**Category**、**NotificationTitle**、**Message**、**URL** はアナウンスの作成に使用されます。 **IsProcessed** という名前の列がありますが、これはコンソール プログラム形式のサンプル自動化プロセスで使用されます。 このコンソール プログラムを Azure WebJob として実行しスケジュールを設定することも、SharePoint ワークフローを直接使用して、この SharePoint リストに項目が挿入された場合にアナウンスが作成およびアクティブ化されるように設定することもできます。 このサンプルで使用するコンソール プログラムは、SharePoint リスト内の項目を調べ、項目ごとに Azure Mobile Engagement でアナウンスを作成してから、最後にアナウンスの作成が成功した場合に **IsProcessed** フラグを true にマークします。
   
    ![][1]
2. *こちら* [Remote Authentication in SharePoint Online Using the Client Object Model](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) (クライアント オブジェクト モデルを使用した SharePoint Online のリモート認証) サンプルのコードを使用して、SharePoint リストの認証を行っています。
3. 認証後に、リスト項目内をループして新規に作成された項目 ( **IsProcessed** が false となっています) を検索します。 
   
         static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);
   
                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();
   
                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;
   
                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;
   
                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";
   
                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Mobile Engagement の統合
1. 処理をする必要がある項目を発見すると、そのリスト項目からアナウンスを作成するのに必要な情報を抽出し、`CreateAzMECampaign` を呼び出してアナウンスを作成してから、`ActivateAzMECampaign` を呼び出してアクティブ化します。 これらは、本質的には Mobile Engagement バックエンドを呼び出す REST API 呼び出しです。 
2. Mobile Engagement REST API では、**基本認証スキームによる HTTP 認証ヘッダー**が必要です。このヘッダーは、`ApplicationId` と、Azure Portal から取得できる `ApiKey` で構成されています。 **[SDK キー]** セクション "*ではなく*"、**[API キー]** セクションのキーを使用していることを確認します。 
   
   ![][2]
   
       static string CreateAuthZHeader()
       {
           string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
           return BasicAuthzHeaderString;
       }
   
       static public string EncodeTo64(string toEncode)
       {
           byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
           string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
           return returnValue;
       }  
3. アナウンス形式のキャンペーンの作成については、 [ドキュメント](https://msdn.microsoft.com/library/azure/mt683750.aspx)を参照してください。 キャンペーンの `kind` を *アナウンス* に設定し、 [ペイロード](https://msdn.microsoft.com/library/azure/mt683751.aspx) を指定して FormUrlEncodedContent として渡すようにする必要があります。 
   
        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";
   
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }
4. お知らせを作成すると、Mobile Engagement ポータルに次のような結果が表示されます (State が Draft であり、Acitivated が N/A であることに注意してください)。
   
    ![][3]
5. `CreateAzMECampaign` はアナウンス キャンペーンを作成し、その ID を呼び出し元に返します。 `ActivateAzMECampaign` はこの ID をキャンペーンのアクティブ化のパラメーターとして要求します。 
   
        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }
6. お知らせを作成すると、Mobile Engagement ポータルに次のような結果が表示されます。
   
    ![][4]
7. キャンペーンがアクティブ化されるとすぐに、キャンペーンの基準を満たすデバイスで通知が表示されるようになります。 
8. アナウンス キャンペーンが作成されると、IsProcessed が false であったリスト項目が true に設定されることにも注目してください。  

このサンプルでは、ほとんどの必須プロパティを指定する単純なアナウンス キャンペーンを作成しました。 このサンプルは、 [こちら](https://msdn.microsoft.com/library/azure/mt683751.aspx)の情報を参考にしてポータルから好きなだけカスタマイズすることができます。 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png





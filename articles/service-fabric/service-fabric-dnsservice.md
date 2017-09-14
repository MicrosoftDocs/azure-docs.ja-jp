---
title: "Azure Service Fabric の DNS サービス | Microsoft Docs"
description: "Service Fabric の DNS サービスを使用して、クラスター内からマイクロサービスを検出します。"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 9871bc5aa4e74ab0faef401d67c4e9558eb5e14b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="dns-service-in-azure-service-fabric"></a>Azure Service Fabric の DNS サービス
オプションのシステム サービスである DNS サービスをクラスターで有効にし、DNS プロトコルを使用して他のサービスを検出できます。

コンテナー化されたサービスなど多くのサービスには既存の URL 名が含まれるため、(Naming Service プロトコルではなく) 標準の DNS プロトコルを使用してこれらを解決できるため、望ましい方法です。特に "リフト アンド シフト" シナリオに適しています。 DNS サービスを使用すると、DNS 名をサービス名にマップして、エンドポイントの IP アドレスを解決できます。 

DNS サービスによって DNS 名はサービス名にマップされ、ネーム サービスによって解決され、サービス エンドポイントに返されます。 サービスの DNS 名は、作成時に提供されます。 

![service endpoints][0]

## <a name="enabling-the-dns-service"></a>DNS サービスを有効にする
最初に、DNS サービスをクラスターで有効にする必要があります。 デプロイするクラスター用テンプレートを用意します。 [サンプル テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)を使用することも、Resource Manager テンプレートを作成することもできます。 次の手順で DNS サービスを有効にできます。

1. まず、`Microsoft.ServiceFabric/clusters` リソースの `apiversion` が `2017-07-01-preview` に設定されていることを確認し、設定されていない場合は次のスニペットのように更新します。

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 次のスニペットのように、`fabricSettings` セクションの後に次の `addonFeatures` セクションを追加して DNS サービスを有効にします。 

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. 上記の変更でクラスター テンプレートを更新したら、その変更を適用してアップグレードを完了します。 完了すると、DNS システム サービスがクラスターで実行されます。このサービスの名前は Service Fabric Explorer のシステム サービス セクションに `fabric:/System/DnsService` と表示されます。 

また、クラスターの作成時にポータルを使用して DNS サービスを有効にすることもできます。 DNS サービスを有効にするには、次のスクリーンショットのように [`Cluster configuration`] メニューの [`Include DNS service`] のボックスをオンにします。

![ポータルで DNS サービスを有効にする][2]


## <a name="setting-the-dns-name-for-your-service"></a>サービスの DNS 名を設定する
DNS サービスがクラスターで実行されるようになったため、`ApplicationManifest.xml` で既定のサービスに対する宣言によって、または Powershell コマンドを使用して、サービスの DNS 名を設定できます。

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>ApplicationManifest.xml で既定のサービスの DNS 名を設定する
Visual Studio またはお好みのエディターでプロジェクトを開いて、`ApplicationManifest.xml` ファイルを開きます。 既定のサービスのセクションに移動し、各サービスに `ServiceDnsName` 属性を追加します。 次の例は、サービスの DNS 名を `service1.application1` に設定する方法を示します。

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
アプリケーションをデプロイすると、Service Fabric Explorer のサービス インスタンスに、このインスタンスの DNS 名が次の図のように表示されます。 

![service endpoints][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Powershell を使用してサービスの DNS 名を設定する
サービスの作成時に、`New-ServiceFabricService` Powershell を使用してサービスの DNS 名を設定できます。 次の例では、DNS 名が `service1.application1` の新しいステートレス サービスを作成します。

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>サービスで DNS を使用する
複数のサービスをデプロイした場合は、DNS 名を使用して、通信する他のサービスのエンドポイントを検索できます。 DNS プロトコルはステートフル サービスと通信できないため、DNS サービスはステートレス サービスにのみ適用されます。 ステートフル サービスの場合は、HTTP 呼び出し用の組み込みのリバース プロキシを使用して、特定のサービス パーティションを呼び出します。

次のコードは、別のサービスを呼び出す方法を示しています。これはごく普通の HTTP 呼び出しであり、ポートと、省略可能なパスを URL の一部として指定します。

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>次のステップ
[サービスとの接続と通信](service-fabric-connect-and-communicate-with-services.md)に関する記事を参照して、クラスター内でのサービスの通信の詳細を確認する

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG
[2]: ./media/service-fabric-dnsservice/DNSService.PNG


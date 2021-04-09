---
title: Azure Service Fabric の DNS サービス
description: Service Fabric の DNS サービスを使用して、クラスター内からマイクロサービスを検出します。
ms.topic: conceptual
ms.date: 7/20/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: f7f06920820cdc73f8d3101ab24ee46625931ee4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91268045"
---
# <a name="dns-service-in-azure-service-fabric"></a>Azure Service Fabric の DNS サービス
オプションのシステム サービスである DNS サービスをクラスターで有効にし、DNS プロトコルを使用して他のサービスを検出できます。 

多くのサービス、特にコンテナー化されたサービスは、既存の URL を使ってアドレス指定することができます。 これらのサービスは、Service Fabric Naming Service プロトコルではなく標準の DNS プロトコルを使って解決できることが望まれます。 DNS サービスを使用すると、DNS 名をサービス名にマップして、エンドポイントの IP アドレスを解決できます。 異なるプラットフォームにまたがるコンテナー化されたサービスの可搬性は、こうした機能により確保されています。Naming Service を利用するようにコードを書き換える必要がなく、既存のサービスの URL を使用できることから、"リフトアンドシフト" のシナリオが実現しやすくなっているのです。 

DNS サービスによって DNS 名はサービス名にマップされ、ネーム サービスによって解決され、サービス エンドポイントに返されます。 サービスの DNS 名は、作成時に提供されます。 次の図は、ステートレス サービスでの DNS サービスの動作を示しています。

![ステートレス サービスの DNS サービスによって DNS 名がサービス名にどのようにマップされるかを示す図。](./media/service-fabric-dnsservice/stateless-dns.png)

Service Fabric バージョン 6.3 以降では、Service Fabric の DNS プロトコルが拡張され、パーティション分割型ステートフル サービスをアドレス指定するためのスキームが追加されています。 これらの拡張機能では、ステートフル サービスの DNS 名とパーティション名とを組み合わせて、特定のパーティションの IP アドレスを解決することができます。 次の 3 つのパーティション分割構成がすべてサポートされています。

- 名前付きパーティション分割
- 範囲パーティション分割
- 単一パーティション分割

次の図は、パーティション分割型ステートフル サービスでの DNS サービスの動作を示しています。

![パーティション分割されたステートレス サービスの DNS サービスによって DNS 名がサービス名にどのようにマップされるかを示す図。](./media/service-fabric-dnsservice/stateful-dns.png)

動的ポートは、DNS サービスではサポートされていません。 動的ポート上で公開されたサービスを解決するには、[リバース プロキシ サービス](./service-fabric-reverseproxy.md)を使用します。

## <a name="enabling-the-dns-service"></a>DNS サービスを有効にする
> [!NOTE]
> Service Fabric サービス用の DNS サービスは UNIX ではまだサポートされていません。

ポータルを使用してクラスターを作成すると、DNS サービスは **[Include DNS service] (DNS サービスを含める)** チェック ボックスと **[クラスター構成]** メニューでは既定で有効になります。

![ポータルで DNS サービスを有効にする](./media/service-fabric-dnsservice/enable-dns-service.png)

ポータルを使用してクラスターを作成していないか、または既存のクラスターを更新している場合は、テンプレートで DNS サービスを有効にする必要があります。

- 新しいクラスターをデプロイするには、[サンプル テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)を使用するか、または独自の Resource Manager テンプレートを作成できます。 
- 既存のクラスターを更新するには、ポータルでそのクラスターのリソース グループに移動し、 **[Automation スクリプト]** をクリックして、グループ内のクラスターやその他のリソースの現在の状態が反映されたテンプレートを操作できます。 詳細については、「[リソース グループからのテンプレートのエクスポート](../azure-resource-manager/templates/export-template-portal.md)」を参照してください。

テンプレートが用意されたら、次の手順で DNS サービスを有効にできます。

1. `Microsoft.ServiceFabric/clusters` リソースの `apiversion` が `2017-07-01-preview` 以降に設定されていることを確認し、設定されていない場合は、それを次の例に示すように更新します。

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. ここで、次のいずれかの方法で DNS サービスを有効にします。

   - 既定の設定で DNS サービスを有効にするには、次の例のように、`properties` セクション内の `addonFeatures` セクションに DNS サービスを追加します。

        ```json
          "properties": {
            ...
            "addonFeatures": [
              "DnsService"
              ],
            ...
          }
        ```

   - 既定以外の設定でサービスを有効にするには、`properties` セクション内の `fabricSettings` セクションに `DnsService` セクションを追加します。 このケースでは、DnsService を `addonFeatures` に追加する必要はありません。 DNS サービスに関して設定できるプロパティの詳細については、[DNS サービスの設定](./service-fabric-cluster-fabric-settings.md#dnsservice)に関するページを参照してください。

       ```json
           "properties": {
             ...  
             "fabricSettings": [
               ...
               {
                 "name": "DnsService",
                 "parameters": [
                   {
                     "name": "IsEnabled",
                     "value": "true"
                   },
                   {
                     "name": "PartitionSuffix",
                     "value": "--"
                   },
                   {
                     "name": "PartitionPrefix",
                     "value": "--"
                   }
                 ]
               },
               ...
              ]
            }
       ```
3. 必要な変更でクラスター テンプレートを更新したら、変更を適用してアップグレードを完了します。 アップグレードが完了すると、DNS システム サービスはクラスター内で実行を開始します。 サービス名は `fabric:/System/DnsService` であり、それは Service Fabric Explorer の **[システム]** サービス セクションで見つかります。 

> [!NOTE]
> DNS を無効から有効にアップグレードしたときに、Service Fabric Explorer に新しい状態が反映されない場合があります。 解決するには、Azure Resource Manager テンプレート内で UpgradePolicy を変更して、ノードを再起動します。 詳細については、[Service Fabric テンプレート リファレンス](/azure/templates/microsoft.servicefabric/2019-03-01/clusters/applications)に関するページを参照してください。

> [!NOTE]
> ローカル コンピューター上で開発するときに DNS サービスを有効にすると、一部の DNS 設定がオーバーライドされます。 インターネットへの接続で問題が発生する場合は、DNS の設定を確認してください。

## <a name="setting-the-dns-name-for-your-service"></a>サービスの DNS 名を設定する
ApplicationManifest.xml ファイルで既定のサービスに対して宣言的に、または PowerShell コマンドを使用してサービスの DNS 名を設定できます。

サービスの DNS 名は、クラスターの全体にわたって解決可能です。したがってクラスター全体で DNS 名の一意性を確保することが重要となります。 

`<ServiceDnsName>.<AppInstanceName>` (`service1.application1` など) の名前付けスキームを使用することを強くお勧めします。 アプリケーションが Docker Compose を使用してデプロイされる場合、サービスには、この名前付けスキームを使用した DNS 名が自動的に割り当てられます。

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>ApplicationManifest.xml で既定のサービスの DNS 名を設定する
Visual Studio またはお好みのエディターでプロジェクトを開いて、ApplicationManifest.xml ファイルを開きます。 既定のサービスのセクションに移動し、各サービスに `ServiceDnsName` 属性を追加します。 次の例は、サービスの DNS 名を `service1.application1` に設定する方法を示します。

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
アプリケーションをデプロイすると、Service Fabric Explorer のサービス インスタンスに、このインスタンスの DNS 名が次の図のように表示されます。 

![サービス エンドポイント](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

次の例では、ステートフル サービスの DNS 名を `statefulsvc.app` に設定しています。 このサービスは、名前付きパーティション構成を使用しています。 パーティション名が小文字になっていることに注目してください。 これは、DNS クエリで対象となるパーティションの要件です。詳細については、「[Making DNS queries on a stateful service partition (ステートフル サービス パーティションに対する DNS クエリの実行)](#preview-making-dns-queries-on-a-stateful-service-partition)」を参照してください。

```xml
    <Service Name="Stateful1" ServiceDnsName="statefulsvc.app" />
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="2" MinReplicaSetSize="2">
        <NamedPartition>
         <Partition Name="partition1" />
         <Partition Name="partition2" />
        </NamedPartition>
      </StatefulService>
    </Service>
```

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Powershell を使用してサービスの DNS 名を設定する
サービスの作成時に、`New-ServiceFabricService` Powershell コマンドを使用してサービスの DNS 名を設定できます。 次の例では、DNS 名が `service1.application1` の新しいステートレス サービスを作成します。

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

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>[プレビュー] ステートフル サービス パーティションに対する DNS クエリの実行
Service Fabric バージョン 6.3 以降では、Service Fabric の DNS サービスがサービス パーティションのクエリをサポートしています。

DNS クエリで使用されるパーティションについては、名前付けに関して次の制限が適用されます。

   - パーティションには、DNS 準拠の名前を使用する必要があります。
   - 複数ラベルの (名前にドット "." を含む) パーティション名は使用しないでください。
   - パーティション名は小文字にする必要があります。

特定のパーティションを対象とした DNS クエリの書式は次のとおりです。

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
各値の説明:

- <*First-Label-Of-Partitioned-Service-DNSName*> は、サービスの DNS 名の最初の部分です。
- <*PartitionPrefix*> は、クラスター マニフェストの DnsService セクションで設定できる値、またはクラスターの Resource Manager テンプレートで設定できる値です。 既定値は "--" です。 詳細については、[DNS サービスの設定](./service-fabric-cluster-fabric-settings.md#dnsservice)に関するページを参照してください。
- <*Target-Partition-Name*> は、パーティションの名前です。 
- <*PartitionSuffix*> は、クラスター マニフェストの DnsService セクションで設定できる値、またはクラスターの Resource Manager テンプレートで設定できる値です。 既定値は空の文字列です。 詳細については、[DNS サービスの設定](./service-fabric-cluster-fabric-settings.md#dnsservice)に関するページを参照してください。
- <*Remaining-Partitioned-Service-DNSName*> は、サービスの DNS 名の残りの部分です。

次の例は、`PartitionPrefix` と `PartitionSuffix` が既定値に設定されたクラスター上で実行されているパーティション分割型サービスの DNS クエリを示しています。 

- 範囲パーティション分割構成を使用した、DNS 名が `backendrangedschemesvc.application` であるサービスのパーティション "0" を解決するには、`backendrangedschemesvc-0.application` を使用します。
- 名前付きパーティション構成を使用した、DNS 名が `backendnamedschemesvc.application` であるサービスのパーティション "first" を解決するには、`backendnamedschemesvc-first.application` を使用します。

DNS サービスからは、パーティションのプライマリ レプリカの IP アドレスが返されます。 パーティションを指定しなかった場合は、ランダムに選択されたパーティションのプライマリ レプリカの IP アドレスが返されます。

## <a name="using-dns-in-your-services"></a>サービスで DNS を使用する
複数のサービスをデプロイした場合は、DNS 名を使用して、通信する他のサービスのエンドポイントを検索できます。 DNS サービスは、ステートレス サービスで機能するほか、Service Fabric バージョン 6.3 以降では、ステートフル サービスでも機能します。 Service Fabric のバージョン 6.3 未満で実行されているステートフル サービスの場合、特定のサービス パーティションを呼び出すための HTTP 呼び出しには、ビルトインの[リバース プロキシ サービス](./service-fabric-reverseproxy.md)を使用してください。 

動的ポートは、DNS サービスではサポートされていません。 動的ポートを使用するサービスを解決するには、リバース プロキシ サービスを使用してください。

次のコードは、DNS を通じてステートレス サービスを呼び出す方法を示しています。 これはごく普通の HTTP 呼び出しであり、DNS 名とポート、省略可能なパスを URL の一部として指定します。

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

次のコードは、ステートフル サービスの特定のパーティションに対する呼び出しを示しています。 この例では、DNS 名にパーティション名 (partition1) が含まれています。 この呼び出しでは、`PartitionPrefix` と `PartitionSuffix` に既定値が使用されているクラスターを想定しています。

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
            Uri uri = new Uri("http://service2-partition1.application1:8080/api/values");
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

## <a name="known-issues"></a>既知の問題
* Service Fabric バージョン 6.3 以降では、DNS 名にハイフンを含むサービス名に対する DNS 参照に関して問題があります。 この問題について詳しくは、こちらの [GitHub の問題](https://github.com/Azure/service-fabric-issues/issues/1197)をご覧ください。 この問題は次の 6.3 更新プログラムで修正される予定です。 

* Service Fabric サービス用の DNS サービスは UNIX ではまだサポートされていません。 DNS サービスは Linux 上のコンテナーに対してサポートされています。 Fabric クライアント/ServicePartitionResolver を使用した手動による解決方法を代わりに使用できます。

## <a name="next-steps"></a>次のステップ
[サービスとの接続と通信](service-fabric-connect-and-communicate-with-services.md)に関する記事を参照して、クラスター内でのサービスの通信の詳細を確認する

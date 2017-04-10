## <a name="azure-dns"></a>Azure DNS
Azure DNS は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。

| プロパティ | 説明 | 値の例 |
| --- | --- | --- |
| **DNSzones** |特定ドメインの DNS レコードをホストするドメイン ゾーンの情報 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com" |

### <a name="dns-record-sets"></a>DNS レコード セット
DNS ゾーンには、レコード セットという名前の子オブジェクトがあります。 レコード セットは、DNS ゾーンに対する種類別のホスト レコードのコレクションです。 レコードの種類は、A、AAAA、CNAME、MX、NS、SOA、SRV、TXT です。

| プロパティ | 説明 | 値の例 |
| --- | --- | --- |
| A |IPv4 レコードの種類 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/A/www |
| AAAA |IPv6 レコードの種類 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/AAAA/hostrecord |
| CNAME |正規名レコードの種類 <sup>1</sup> |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/CNAME/www |
| MX |メール レコードの種類 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/MX/mail |
| NS |ネーム サーバー レコードの種類 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/NS/ |
| SOA |Start of Authority レコードの種類 <sup>2</sup> |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SOA |
| SRV |サービス レコードの種類 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SRV |

<sup>1</sup> レコード セットごとに 1 つの値のみ許可されます。

<sup>2</sup> DNS ゾーンごとに 1 つのレコード種類の SOA のみ許可されます。 

Json 形式での DNS ゾーンの例:

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "newZoneName": {
          "type": "String",
          "metadata": {
              "description": "The name of the DNS zone to be created."
          }
        },
        "newRecordName": {
          "type": "String",
          "defaultValue": "www",
          "metadata": {
              "description": "The name of the DNS record to be created.  The name is relative to the zone, not the FQDN."
          }
        }
      },
      "resources": 
      [
        {
          "type": "microsoft.network/dnszones",
          "name": "[parameters('newZoneName')]",
          "apiVersion": "2015-05-04-preview",
          "location": "global",
          "properties": {
          }
        },
        {
          "type": "microsoft.network/dnszones/a",
          "name": "[concat(parameters('newZoneName'), concat('/', parameters('newRecordName')))]",
          "apiVersion": "2015-05-04-preview",
          "location": "global",
          "properties": 
          {
            "TTL": 3600,
            "ARecords": 
            [
                {
                    "ipv4Address": "1.2.3.4"
                },
                {
                    "ipv4Address": "1.2.3.5"
                }
            ]
          },
          "dependsOn": [
            "[concat('Microsoft.Network/dnszones/', parameters('newZoneName'))]"
          ]
        }
          ]
    }

## <a name="additional-resources"></a>その他のリソース
詳細については、 [DNS ゾーンの REST API ドキュメント ](https://msdn.microsoft.com/library/azure/mt130626.aspx) を参照してください。

詳細については、 [DNS レコード セットの REST API ドキュメント](https://msdn.microsoft.com/library/azure/mt130627.aspx) を参照してください。



<!--HONumber=Nov16_HO3-->



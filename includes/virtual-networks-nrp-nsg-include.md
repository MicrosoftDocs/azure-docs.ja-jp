## <a name="network-security-group"></a>ネットワーク セキュリティ グループ
NSG リソースを使用すると、許可または拒否の規則を実装することで、ワークロードのセキュリティ境界を作成できます。 そのような規則は、VM、NIC、またはサブネットに適用できます。

| プロパティ | 説明 | サンプルの値 |
| --- | --- | --- |
| **サブネット** |NSG が適用されるサブネット ID のリスト。 |/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd |
| **securityRules** |NSG を構成するセキュリティ規則の一覧 |以下の [セキュリティ規則](#Security-rule) をご覧ください。 |
| **defaultSecurityRules** |すべての NSG にある既定セキュリティ規則のリスト |以下の [既定セキュリティ規則](#Default-security-rules) をご覧ください。 |

* **セキュリティ規則** – NSG には複数のセキュリティ規則を定義できます。 各規則で、各種のトラフィックを許可または拒否できます。

### <a name="security-rule"></a>セキュリティ規則
セキュリティ規則は、次のプロパティを含む、NSG の子リソースです。

| プロパティ | 説明 | サンプルの値 |
| --- | --- | --- |
| **description** |規則の説明 |サブネット X 内のすべての VM に対して受信トラフィックを許可します |
| **protocol** |規則に関して一致するプロトコル |TCP、UDP、または * |
| **sourcePortRange** |規則に関して一致するソース ポート範囲 |80, 100-200, * |
| **destinationPortRange** |規則に関して一致する宛先ポート範囲 |80, 100-200, * |
| **sourceAddressPrefix** |規則に関して一致するソース アドレスのプレフィックス |10.10.10.1, 10.10.10.0/24、VirtualNetwork |
| **destinationAddressPrefix** |規則に関して一致する宛先アドレスのプレフィックス |10.10.10.1, 10.10.10.0/24、VirtualNetwork |
| **direction** |規則に関して一致するトラフィックの方向 |受信または送信 |
| **priority** |規則の優先度です。 規則は優先度の順序でチェックされ、規則が適用されると、規則の一致テストは行われなくなります。 |10, 100, 65000 |
| **access** |規則が一致した場合に適用されるアクセスの種類 |許可または拒否 |

JSON 形式の NSG のサンプル

    {
        "name": "NSG-BackEnd",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/networkSecurityGroups",
        "location": "westus",
        "tags": {
            "displayName": "NSG - Front End"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "securityRules": [
                {
                    "name": "rdp-rule",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/rdp-rule",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "description": "Allow RDP",
                        "protocol": "Tcp",
                        "sourcePortRange": "*",
                        "destinationPortRange": "3389",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "access": "Allow",
                        "priority": 100,
                        "direction": "Inbound"
                    }
                }
            ],
            "defaultSecurityRules": [
                { [...],
            "subnets": [
                {
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                }
            ]
        }
    }

### <a name="default-security-rules"></a>既定セキュリティ規則

既定のセキュリティ規則には、セキュリティ規則で使用できるものと同じプロパティがあります。 これらは、NSG を適用するリソース間の基本的な接続を提供するために存在します。 どの[既定セキュリティ規則](../articles/virtual-network/virtual-networks-nsg.md#default-rules)が存在するのかを把握してください。

### <a name="additional-resources"></a>その他のリソース
* [NSG](../articles/virtual-network/virtual-networks-nsg.md)の詳細を確認します。
* NSG については、 [REST API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/mt163615.aspx) を確認してください。
* セキュリティ規則については、 [REST API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/mt163580.aspx) を確認してください。


<!--HONumber=Nov16_HO3-->



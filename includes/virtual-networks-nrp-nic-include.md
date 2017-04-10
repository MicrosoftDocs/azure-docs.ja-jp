## <a name="nic"></a>NIC
ネットワーク インターフェイス カード (NIC) リソースは、VNet リソース内の既存のサブネットへのネットワーク接続を提供します。 NIC をスタンドアロン オブジェクトとして作成できますが、実際に接続を提供するには NIC を別のオブジェクトに関連付ける必要があります。 NIC を使用することで、VM をサブネット、パブリック IP アドレス、またはロード バランサーに接続することができます。  

| プロパティ | 説明 | 値の例 |
| --- | --- | --- |
| **virtualMachine** |NIC が関連付けられるVM。 |/subscriptions/{guid}/../Microsoft.Compute/virtualMachines/vm1 |
| **macAddress** |NIC の MAC アドレス |4 ～ 30 の任意の値 |
| **networkSecurityGroup** |NIC に関連付けられている NSG |/subscriptions/{guid}/../Microsoft.Network/networkSecurityGroups/myNSG1 |
| **dnsSettings** |NIC の DNS 設定 | [PIP](#Public-IP-address) |

ネットワーク インターフェイス カード (NIC) は、仮想マシン (VM) に関連するネットワーク インターフェイスを表します。 VM には、1 つ以上の NIC を含めることができます。

![1 つの VM での NIC](./media/resource-groups-networking/Figure3.png)

### <a name="ip-configurations"></a>IP 構成
NIC には、以下のプロパティを含む **ipConfigurations** という名前の子オブジェクトがあります。

| プロパティ | 説明 | 値の例 |
| --- | --- | --- |
| **subnet** |NIC が接続されているサブネットです。 |/subscriptions/{guid}/../Microsoft.Network/virtualNetworks/myvnet1/subnets/mysub1 |
| **privateIPAddress** |サブネット内の NIC の IP アドレス |10.0.0.8 |
| **privateIPAllocationMethod** |IP の割り当て方法 |動的または静的 |
| **enableIPForwarding** |ルーティングに NIC を使用できるかどうか |true または false |
| **primary** |NIC は VM のプライマリ NIC であるかどうか |true または false |
| **publicIPAddress** |NIC に関連付けられた PIP | [DNS の設定](#DNS-settings) |
| **loadBalancerBackendAddressPools** |NIC が関連付けられたバックエンド アドレス プール | |
| **loadBalancerInboundNatRules** |NIC が関連付けられた受信ロード バランサーの NAT 規則 | |

JSON 形式でのパブリック IP アドレスのサンプル:

    {
        "name": "lb-nic1-be",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/networkInterfaces",
        "location": "eastus",
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "ipConfigurations": [
                {
                    "name": "NIC-config",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/NIC-config",
                    "etag": "W/\"0027f1a2-3ac8-49de-b5d5-fd46550500b1\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "privateIPAddress": "10.0.0.4",
                        "privateIPAllocationMethod": "Dynamic",
                        "subnet": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet"
                        },
                        "loadBalancerBackendAddressPools": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool"
                            }
                        ],
                        "loadBalancerInboundNatRules": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1"
                            }
                        ]
                    }
                }
            ],
            "dnsSettings": { ... },
            "macAddress": "00-0D-3A-10-F1-29",
            "enableIPForwarding": false,
            "primary": true,
            "virtualMachine": {
                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Compute/virtualMachines/web1"
            }
        }
    }

### <a name="additional-resources"></a>その他のリソース
* NIC に関する [REST API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/mt163579.aspx) も参照してください。



<!--HONumber=Nov16_HO3-->



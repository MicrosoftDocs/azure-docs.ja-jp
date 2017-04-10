## <a name="traffic-manager-profile"></a>Traffic Manager プロファイル
Traffic Manager とその子エンドポイントのリソースは、Azure 内と Azure 外部のエンドポイントへ DNS のルーティングをできるようにします。 このようなトラフィック分散は、ルーティング ポリシー メソッドによって管理されます。 Traffic Manager では、エンドポイントの正常性を監視できます。また、エンドポイントの正常性に基づいて、トラフィックが適切に転送できるようにします。 

| プロパティ | 説明 |
| --- | --- |
| **trafficRoutingMethod** |指定できる値は、*Performance*、*Weighted*、および *Priority* です。 |
| **dnsConfig** |プロファイルの FQDN |
| **プロトコル** |監視プロトコル。有効な値は、*HTTP* と *HTTPS* です。 |
| **ポート** |監視ポート |
| **パス** |監視パス |
| **Endpoints** |エンドポイント リソースのコンテナー |

### <a name="endpoint"></a>エンドポイント
エンドポイントは、Traffic Manager プロファイルの子リソースです。 Traffic Manager プロファイル リソースに構成されたポリシーに基づいてユーザー トラフィックが配信されるサービスまたは Web エンドポイントを表します。 

| プロパティ | 説明 |
| --- | --- |
| **型** |エンドポイントの種類。有効な値は *Azure エンドポイント*、*外部エンドポイント*、および*入れ子になったエンドポイント*です。 |
| **targetResourceId** |サービスまたは Web のエンドポイントのパブリック IP アドレスです。 Azure のエンドポイントまたは外部のエンドポイントを指定できます。 |
| **重量** |トラフィック管理に使用されるエンドポイントの重み付けです。 |
| **優先順位** |フェールオーバー アクションを定義するために使用されるエンドポイントの優先度です。 |

Json 形式での Traffic Manager のサンプル: 

        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }


## <a name="additional-resources"></a>その他のリソース
詳細については、 [Traffic Manager の REST API ドキュメント](https://msdn.microsoft.com/library/azure/mt163664.aspx) を参照してください。



<!--HONumber=Nov16_HO3-->



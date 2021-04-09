---
title: Azure VPN Gateway:パケット キャプチャを構成する
description: VPN ゲートウェイで使用でき、問題の原因を絞り込むのに役立つパケット キャプチャ機能について説明します。
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alzam
ms.openlocfilehash: 0983139d1c9af235eba4c9f99da7bc9dea3f231b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726616"
---
# <a name="configure-packet-capture-for-vpn-gateways"></a>VPN ゲートウェイのパケット キャプチャを構成する

接続とパフォーマンスに関連する問題は複雑になることが多く、 問題の原因を絞り込むだけでも、かなりの時間と労力を要する場合があります。 パケット キャプチャは、問題の範囲をネットワークの特定の部分に絞り込むのに役立ちます。 これを使用すれば、問題がネットワークの顧客側にあるのか、ネットワークの Azure 側にあるのか、またはその間のどこかにあるのかを判断できます。 問題を絞り込んだ後は、デバッグと修復アクションがより効率的に行えるようになります。

パケット キャプチャ ツールには、一般に入手可能なものがいくつかあります。 これらのツールで関連するパケット キャプチャを取得することは、大量のトラフィックが発生するシナリオでは特に面倒になることがあります。 Azure VPN Gateway パケット キャプチャで提供されるフィルター処理機能は、主要な差別化要因です。 VPN Gateway パケット キャプチャは、一般に入手可能なパケット キャプチャ ツールと共に使用できます。

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>VPN Gateway パケット キャプチャのフィルター処理機能

VPN Gateway パケット キャプチャは、必要に応じて、ゲートウェイでも特定の接続でも実行できます。 また、複数のトンネルで同時にパケット キャプチャを実行することもできます。 VPN ゲートウェイでフィルター処理を使ってキャプチャできるのは、一方向または双方向のトラフィック、IKE と ESP のトラフィック、および内部パケットです。

大量のトラフィック上の問題を特定するときに、5 タプルのフィルター (ソース サブネット、宛先サブネット、発信元ポート、接続先ポート、プロトコル) と TCP フラグ (SYN、ACK、FIN、URG、PSH、RST) を使用すると便利です。

次の JSON と JSON スキーマの例では、各プロパティについて説明しています。 以下に、パケット キャプチャを実行するときに注意すべき制限事項をいくつか示します。

- ここに示されているスキーマでは、フィルターは配列ですが、現在使用できるフィルターは一度に 1 つのみです。
- ゲートウェイ全体のパケット キャプチャを同時に複数実行することはできません。
- 1 つの接続で複数のパケット キャプチャを同時に実行することはできません。 異なる接続で複数のパケット キャプチャを同時に実行することはできます。
- ゲートウェイごとに最大 5 つのパケット キャプチャを並列実行することができます。 これらのパケット キャプチャは、ゲートウェイ全体のパケット キャプチャと接続ごとのパケット キャプチャを組み合わせたものであってもかまいません。
- MaxPacketBufferSize の単位はバイトで、MaxFileSize はメガバイトです。

### <a name="example-json"></a>JSON の例
```JSON-interactive
{
  "TracingFlags": 11,
  "MaxPacketBufferSize": 120,
  "MaxFileSize": 200,
  "Filters": [
    {
      "SourceSubnets": [
        "20.1.1.0/24"
      ],
      "DestinationSubnets": [
        "10.1.1.0/24"
      ],
      "SourcePort": [
        500
      ],
      "DestinationPort": [
        4500
      ],
      "Protocol": [
        6
      ],
      "TcpFlags": 16,
      "CaptureSingleDirectionTrafficOnly": true
    }
  ]
}
```
### <a name="json-schema"></a>JSON スキーマ
```JSON-interactive
{
    "type": "object",
    "title": "The Root Schema",
    "description": "The root schema input JSON filter for packet capture",
    "default": {},
    "additionalProperties": true,
    "required": [
        "TracingFlags",
        "MaxPacketBufferSize",
        "MaxFileSize",
        "Filters"
    ],
    "properties": {
        "TracingFlags": {
            "$id": "#/properties/TracingFlags",
            "type": "integer",
            "title": "The Tracingflags Schema",
            "description": "Tracing flags that customer can pass to define which packets are to be captured. Supported values are CaptureESP = 1, CaptureIKE = 2, CaptureOVPN = 8. The final value is OR of the bits.",
            "default": 11,
            "examples": [
                11
            ]
        },
        "MaxPacketBufferSize": {
            "$id": "#/properties/MaxPacketBufferSize",
            "type": "integer",
            "title": "The Maxpacketbuffersize Schema",
            "description": "Maximum buffer size of each packet. The capture will only contain contents of each packet truncated to this size.",
            "default": 120,
            "examples": [
                120
            ]
        },
        "MaxFileSize": {
            "$id": "#/properties/MaxFileSize",
            "type": "integer",
            "title": "The Maxfilesize Schema",
            "description": "Maximum file size of the packet capture file. It is a circular buffer.",
            "default": 100,
            "examples": [
                100
            ]
        },
        "Filters": {
            "$id": "#/properties/Filters",
            "type": "array",
            "title": "The Filters Schema",
            "description": "An array of filters that can be passed to filter inner ESP traffic.",
            "default": [],
            "examples": [
                [
                    {
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true,
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ]
                    }
                ]
            ],
            "additionalItems": true,
            "items": {
                "$id": "#/properties/Filters/items",
                "type": "object",
                "title": "The Items Schema",
                "description": "An explanation about the purpose of this instance.",
                "default": {},
                "examples": [
                    {
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ],
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true
                    }
                ],
                "additionalProperties": true,
                "required": [
                    "SourceSubnets",
                    "DestinationSubnets",
                    "SourcePort",
                    "DestinationPort",
                    "Protocol",
                    "TcpFlags",
                    "CaptureSingleDirectionTrafficOnly"
                ],
                "properties": {
                    "SourceSubnets": {
                        "$id": "#/properties/Filters/items/properties/SourceSubnets",
                        "type": "array",
                        "title": "The Sourcesubnets Schema",
                        "description": "An array of source subnets that need to match the Source IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "20.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourceSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "20.1.1.0/24"
                            ]
                        }
                    },
                    "DestinationSubnets": {
                        "$id": "#/properties/Filters/items/properties/DestinationSubnets",
                        "type": "array",
                        "title": "The Destinationsubnets Schema",
                        "description": "An array of destination subnets that need to match the Destination IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "10.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "10.1.1.0/24"
                            ]
                        }
                    },
                    "SourcePort": {
                        "$id": "#/properties/Filters/items/properties/SourcePort",
                        "type": "array",
                        "title": "The Sourceport Schema",
                        "description": "An array of source ports that need to match the Source port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourcePort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                500
                            ]
                        }
                    },
                    "DestinationPort": {
                        "$id": "#/properties/Filters/items/properties/DestinationPort",
                        "type": "array",
                        "title": "The Destinationport Schema",
                        "description": "An array of destination ports that need to match the Destination port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                4500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationPort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                4500
                            ]
                        }
                    },
                    "Protocol": {
                        "$id": "#/properties/Filters/items/properties/Protocol",
                        "type": "array",
                        "title": "The Protocol Schema",
                        "description": "An array of protocols that need to match the Protocol of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                6
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/Protocol/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                6
                            ]
                        }
                    },
                    "TcpFlags": {
                        "$id": "#/properties/Filters/items/properties/TcpFlags",
                        "type": "integer",
                        "title": "The Tcpflags Schema",
                        "description": "A list of TCP flags. The TCP flags set on the packet must match any flag in the list of flags provided. FIN = 0x01,SYN = 0x02,RST = 0x04,PSH = 0x08,ACK = 0x10,URG = 0x20,ECE = 0x40,CWR = 0x80. An OR of flags can be provided.",
                        "default": 0,
                        "examples": [
                            16
                        ]
                    },
                    "CaptureSingleDirectionTrafficOnly": {
                        "$id": "#/properties/Filters/items/properties/CaptureSingleDirectionTrafficOnly",
                        "type": "boolean",
                        "title": "The Capturesingledirectiontrafficonly Schema",
                        "description": "A flags which when set captures reverse traffic also.",
                        "default": false,
                        "examples": [
                            true
                        ]
                    }
                }
            }
        }
    }
}
```

## <a name="packet-capture---portal"></a>パケット キャプチャ - ポータル

Azure portal でパケット キャプチャを設定できます。

:::image type="content" source="./media/packet-capture/portal.jpg" alt-text="ポータルでのパケット キャプチャのスクリーンショット。" lightbox="./media/packet-capture/portal.jpg":::

## <a name="packet-capture---powershell"></a>パケット キャプチャ - PowerShell

次の例は、パケット キャプチャを開始および停止する PowerShell コマンドを示しています。 パラメーター オプションの詳細については、「[AzVirtualnetworkGatewayPacketCapture](/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)」を参照してください。

### <a name="start-packet-capture-for-a-vpn-gateway"></a>VPN ゲートウェイのパケット キャプチャを開始する

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

省略可能なパラメーター `-FilterData` を使用してフィルターを適用できます。

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>VPN ゲートウェイのパケット キャプチャを停止する

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>VPN ゲートウェイ接続のパケット キャプチャを開始する

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

省略可能なパラメーター `-FilterData` を使用してフィルターを適用できます。

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>VPN ゲートウェイ接続のパケット キャプチャを停止する

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>重要な考慮事項

- パケット キャプチャの実行は、パフォーマンスに影響を与える可能性があります。 必要ない場合は、パケット キャプチャを忘れずに停止してください。
- 推奨される最小パケット キャプチャ期間は 600 秒です。 パス上の複数のコンポーネント間で同期の問題が発生するため、パケット キャプチャの時間が短いと、完全なデータを取得できない場合があります。
- パケット キャプチャ データ ファイルは PCAP 形式で生成されます。 PCAP ファイルを開くには、Wireshark または他の一般に利用できるアプリケーションを使用します。
- パケット キャプチャは、ポリシーベースのゲートウェイではサポートされていません。
- `SASurl` パラメーターが正しく構成されていない場合、トレースはストレージ エラーで失敗する可能性があります。 `SASurl` パラメーターを正しく生成する方法の例については、「[Stop-AzVirtualNetworkGatewayPacketCapture](/powershell/module/az.network/stop-azvirtualnetworkgatewaypacketcapture)」を参照してください。



## <a name="next-steps"></a>次のステップ

VPN Gateway の詳細については、「[VPN ゲートウェイとは](vpn-gateway-about-vpngateways.md)」をご覧ください。

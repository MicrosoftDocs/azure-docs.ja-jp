---
title: Azure VPN Gateway:パケット キャプチャの構成
description: VPN ゲートウェイで使用できるパケット キャプチャ機能について説明します。
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 3ba3046367ceece6bf0ddf157451025c79977324
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077207"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>VPN ゲートウェイのパケット キャプチャを構成する

接続とパフォーマンスに関連する問題は、多くの場合複雑であり、問題の原因の絞り込みには非常に多くの時間と労力が必要になります。 パケット キャプチャ機能を使用すれば、問題の範囲をネットワークの特定の部分 (ネットワークの顧客側、ネットワークの Azure 側、またはその間など) に絞り込むための時間を短縮できます。 問題を絞り込めば、デバッグと修復の作業がはるかに効率的になります。

パケット キャプチャのツールには、一般的に入手できるものもいくつかあります。 これらのツールを使用して関連するパケット キャプチャを取得することは、大量のトラフィックが存在する場合には特に面倒です。 VPN ゲートウェイ パケット キャプチャで提供されるフィルター機能は、大きな差別化要因になります。 VPN ゲートウェイ パケット キャプチャは、一般的に入手可能なパケット キャプチャ ツールに加えて使用することもできます。

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>VPN ゲートウェイ パケット キャプチャのフィルター処理機能

VPN ゲートウェイ パケット キャプチャは、お客様のニーズに応じて、ゲートウェイまたは特定の接続に対して実行できます。 また、複数のトンネルに対するパケット キャプチャを同時に実行することもできます。 VPN ゲートウェイでフィルター処理を使ってキャプチャできるのは、単一方向または双方向のトラフィック、IKE と ESP トラフィック、および内部パケットです。

5 タプルのフィルター (ソース サブネット、宛先サブネット、発信元ポート、接続先ポート、プロトコル) と TCP フラグ (SYN、ACK、FIN、URG、PSH、RST) を使用すると、大量のトラフィックについての問題を特定する際に役立ちます。

以下に示した、JSON と、各プロパティの説明が記載されている JSON スキーマの例を参照してください。 また、パケット キャプチャの実行時における下記の制限事項に注意してください。
- スキーマ内では、フィルターは配列として示されていますが、現時点で使用できるのは一度に 1 つのフィルターだけです。
- ゲートウェイ全体でのパケット キャプチャを同時に複数実行することはできません。
- 同じ接続に対するパケット キャプチャを同時に複数実行することはできません。 異なる接続に対しては、パケット キャプチャを同時に実行できます。
- ゲートウェイごとに最大 5 つのパケット キャプチャを並列実行することができます。 これらのパケット キャプチャは、ゲートウェイ全体のパケット キャプチャまたは接続ごとのパケット キャプチャと組み合わせることができます。

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

## <a name="setup-packet-capture-using-powershell"></a>PowerShell を使用してパケッ トキャプチャを設定する

パケット キャプチャを開始および停止する PowerShell コマンドについては、次の例を参照してください。 パラメーター オプションの詳細については、こちらの PowerShell [ドキュメント](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)を参照してください。

### <a name="start-packet-capture-for-a-vpn-gateway"></a>VPN ゲートウェイのパケット キャプチャを開始する

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

省略可能なパラメーター **-FilterData** を使用してフィルターを適用できます。

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>VPN ゲートウェイのパケット キャプチャを停止する

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>VPN ゲートウェイ接続のパケット キャプチャを開始する

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

省略可能なパラメーター **-FilterData** を使用してフィルターを適用できます。

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>VPN ゲートウェイ接続のパケット キャプチャを停止する

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>重要な考慮事項

- パケット キャプチャの実行は、パフォーマンスに影響を与える可能性があります。 不要な場合は、パケット キャプチャを停止してください。
- 推奨される最小パケット キャプチャ期間は 600 秒です。 パケット キャプチャ期間を短くすると、パス上の複数のコンポーネント間で同期の問題が発生し、完全なデータを取得できない場合があります。
- パケット キャプチャ データ ファイルは PCAP 形式で生成されます。 PCAP ファイルを開くには、Wireshark または他の一般に利用できるアプリケーションを使用します。

## <a name="next-steps"></a>次のステップ

VPN Gateway の詳細については、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」をご覧ください

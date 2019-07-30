---
title: OPC Publisher の構成 - Azure | Microsoft Docs
description: OPC Publisher の構成方法
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: bccab4dde5e17ec30a0b8c5e36dd78bdd1bdff93
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605718"
---
# <a name="configure-opc-publisher"></a>OPC Publisher の構成

OPC Publisher を構成して以下を指定することができます。

- 公開する OPC UA ノード データの変更。
- 発行する OPC UA イベント。
- テレメトリ形式。

OPC Publisher は、構成ファイルを使用するか、メソッドの呼び出しを使用して構成できます。

## <a name="use-configuration-files"></a>構成ファイルを使用する

このセクションでは、構成ファイルで OPC UA ノードの公開を構成するためのオプションについて説明します。

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>構成ファイルを使用してデータ変更の公開を構成する

公開する OPC UA ノードを構成するための最も簡単な方法は、構成ファイルを使用することです。 構成ファイルの形式は、リポジトリの [publishednodes.json](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json) に記載されています。

構成ファイルの構文は、時間の経過と共に変更されています。 OPC Publisher は引き続き以前の形式を読み取りますが、構成を維持するときに最新の形式に変換します。

次の例では構成ファイルの形式を示します。

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>構成ファイルを使用してイベントの発行を構成する

OPC UA イベントを発行するには、データ変更の場合と同じ構成ファイルを使用します。

次の例では、[SimpleEvents server](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/SimpleEvents/Server) によって生成されたイベントに対する発行を構成する方法を示しています。 SimpleEvents サーバーは [OPC Foundation リポジトリ](https://github.com/OPCFoundation/UA-.NETStandard)から見つけることができます。

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62563/Quickstarts/SimpleEventsServer",
    "OpcEvents": [
      {
        "Id": "i=2253",
        "DisplayName": "SimpleEventServerEvents",
        "SelectClauses": [
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "EventId"
            ]
          },
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "Message"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CycleId"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CurrentStep"
            ]
          }
        ],
        "WhereClause": [
          {
            "Operator": "OfType",
            "Operands": [
              {
                "Literal": "ns=2;i=235"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

## <a name="use-method-calls"></a>メソッドの呼び出しを使用する

このセクションでは、OPC Publisher を構成するために使用できるメソッドの呼び出しについて説明します。

### <a name="configure-using-opc-ua-method-calls"></a>OPC UA メソッドの呼び出しを使用して構成する

OPC Publisher には OPC UA サーバーが含まれており、ポート 62222 でアクセスできます。 ホスト名が **publisher** の場合、エンドポイント URI は `opc.tcp://publisher:62222/UA/Publisher` です。

このエンドポイントは、次の 4 つのメソッドを公開します。

- PublishNode
- UnpublishNode
- GetPublishedNodes
- IoT HubDirectMethod

### <a name="configure-using-iot-hub-direct-method-calls"></a>IoT Hub ダイレクト メソッド呼び出しを使用して構成する

OPC Publisher は、次の IoT Hub ダイレクト メソッド呼び出しを実装します。

- PublishNodes
- UnpublishNodes
- UnpublishAllNodes
- GetConfiguredEndpoints
- GetConfiguredNodesOnEndpoint
- GetDiagnosticInfo
- GetDiagnosticLog
- GetDiagnosticStartupLog
- ExitApplication
- GetInfo

メソッドの要求と応答の JSON ペイロードの形式は、[opcpublisher/HubMethodModel.cs](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/HubMethodModel.cs) に定義されています。

モジュールで不明なメソッドを呼び出すと、そのメソッドが実装されていないことを示す文字列を返します。 不明なメソッドの呼び出しを、モジュールに ping を実行する手段として使用できます。

### <a name="configure-username-and-password-for-authentication"></a>認証用のユーザー名とパスワードを構成する

認証モードは IoT Hub ダイレクト メソッド呼び出しを通じて設定できます。 ペイロードには、プロパティ **OpcAuthenticationMode** およびユーザー名とパスワードを含める必要があります。

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

パスワードは IoT Hub ワークロード クライアントによって暗号化され、発行元の構成内に格納されています。 認証を元の匿名に変更するには、次のペイロードでメソッドを使用します。

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

**OpcAuthenticationMode** プロパティがペイロードに設定されていない場合、認証設定は構成内で変更されないままになります。

## <a name="configure-telemetry-publishing"></a>テレメトリの公開を構成する

OPC Publisher は、公開されたノードの値の変更の通知を受信すると、IoT Hub に送信される JSON 形式のメッセージを生成します。

この JSON 形式のメッセージの内容を、構成ファイルを使用して構成できます。 `--tc` オプションで構成ファイルが指定されていない場合、[接続済みファクトリ ソリューション アクセラレータ](https://github.com/Azure/azure-iot-connected-factory)と互換性がある既定の構成が使用されます。

OPC Publisher が、メッセージのバッチ処理を行うように構成されている場合、これらは有効な JSON 配列として送信されます。

テレメトリは次のソースから生成されます。

- ノードの OPC Publisher ノード構成
- OPC Publisher が通知を取得する対象の OPC UA スタックの **MonitoredItem** オブジェクト。
- データ値の変更の詳細情報を提供する、この通知に渡される引数。

JSON 形式のメッセージに配置されているテレメトリは、これらのオブジェクトの重要なプロパティの一部です。 その他のプロパティが必要な場合は、OPC Publisher のコード ベースを変更する必要があります。

構成ファイルの構文は次のとおりです。

```json
// The configuration settings file consists of two objects:
// 1) The 'Defaults' object, which defines defaults for the telemetry configuration
// 2) An array 'EndpointSpecific' of endpoint specific configuration
// Both objects are optional and if they are not specified, then publisher uses
// its internal default configuration, which generates telemetry messages compatible
// with the Microsoft Connected factory Preconfigured Solution (https://github.com/Azure/azure-iot-connected-factory).

// A JSON telemetry message for Connected factory looks like:
//  {
//      "NodeId": "i=2058",
//      "ApplicationUri": "urn:myopcserver",
//      "DisplayName": "CurrentTime",
//      "Value": {
//          "Value": "10.11.2017 14:03:17",
//          "SourceTimestamp": "2017-11-10T14:03:17Z"
//      }
//  }

// The 'Defaults' object in the sample below, are similar to what publisher is
// using as its internal default telemetry configuration.
{
    "Defaults": {
        // The first two properties ('EndpointUrl' and 'NodeId' are configuring data
        // taken from the OpcPublisher node configuration.
        "EndpointUrl": {

            // The following three properties can be used to configure the 'EndpointUrl'
            // property in the JSON message send by publisher to IoT Hub.

            // Publish controls if the property should be part of the JSON message at all.
            "Publish": false,

            // Pattern is a regular expression, which is applied to the actual value of the
            // property (here 'EndpointUrl').
            // If this key is ommited (which is the default), then no regex matching is done
            // at all, which improves performance.
            // If the key is used you need to define groups in the regular expression.
            // Publisher applies the regular expression and then concatenates all groups
            // found and use the resulting string as the value in the JSON message to
            //sent to IoT Hub.
            // This example mimics the default behaviour and defines a group,
            // which matches the conplete value:
            "Pattern": "(.*)",
            // Here some more exaples for 'Pattern' values and the generated result:
            // "Pattern": "i=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of '2058'to be sent by publisher
            // "Pattern": "(i)=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of 'i2058' to be sent by publisher

            // Name allows you to use a shorter string as property name in the JSON message
            // sent by publisher. By default the property name is unchanged and will be
            // here 'EndpointUrl'.
            // The 'Name' property can only be set in the 'Defaults' object to ensure
            // all messages from publisher sent to IoT Hub have a similar layout.
            "Name": "EndpointUrl"

        },
        "NodeId": {
            "Publish": true,

            // If you set Defaults.NodeId.Name to "ni", then the "NodeId" key/value pair
            // (from the above example) will change to:
            //      "ni": "i=2058",
            "Name": "NodeId"
        },

        // The MonitoredItem object is configuring the data taken from the MonitoredItem
        // OPC UA object for published nodes.
        "MonitoredItem": {

            // If you set the Defaults.MonitoredItem.Flat to 'false', then a
            // 'MonitoredItem' object will appear, which contains 'ApplicationUri'
            // and 'DisplayNode' proerties:
            //      "NodeId": "i=2058",
            //      "MonitoredItem": {
            //          "ApplicationUri": "urn:myopcserver",
            //          "DisplayName": "CurrentTime",
            //      }
            // The 'Flat' property can only be used in the 'MonitoredItem' and
            // 'Value' objects of the 'Defaults' object and will be used
            // for all JSON messages sent by publisher.
            "Flat": true,

            "ApplicationUri": {
                "Publish": true,
                "Name": "ApplicationUri"
            },
            "DisplayName": {
                "Publish": true,
                "Name": "DisplayName"
            }
        },
        // The Value object is configuring the properties taken from the event object
        // the OPC UA stack provided in the value change notification event.
        "Value": {
            // If you set the Defaults.Value.Flat to 'true', then the 'Value'
            // object will disappear completely and the 'Value' and 'SourceTimestamp'
            // members won't be nested:
            //      "DisplayName": "CurrentTime",
            //      "Value": "10.11.2017 14:03:17",
            //      "SourceTimestamp": "2017-11-10T14:03:17Z"
            // The 'Flat' property can only be used for the 'MonitoredItem' and 'Value'
            // objects of the 'Defaults' object and will be used for all
            // messages sent by publisher.
            "Flat": false,

            "Value": {
                "Publish": true,
                "Name": "Value"
            },
            "SourceTimestamp": {
                "Publish": true,
                "Name": "SourceTimestamp"
            },
            // 'StatusCode' is the 32 bit OPC UA status code
            "StatusCode": {
                "Publish": false,
                "Name": "StatusCode"
                // 'Pattern' is ignored for the 'StatusCode' value
            },
            // 'Status' is the symbolic name of 'StatusCode'
            "Status": {
                "Publish": false,
                "Name": "Status"
            }
        }
    },

    // The next object allows to configure 'Publish' and 'Pattern' for specific
    // endpoint URLs. Those will overwrite the ones specified in the 'Defaults' object
    // or the defaults used by publisher.
    // It is not allowed to specify 'Name' and 'Flat' properties in this object.
    "EndpointSpecific": [
        // The following shows how a endpoint specific configuration can look like:
        {
            // 'ForEndpointUrl' allows to configure for which OPC UA server this
            // object applies and is a required property for all objects in the
            // 'EndpointSpecific' array.
            // The value of 'ForEndpointUrl' must be an 'EndpointUrl' configured in
            // the publishednodes.json confguration file.
            "ForEndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
            "EndpointUrl": {
                // We overwrite the default behaviour and publish the
                // endpoint URL in this case.
                "Publish": true,
                // We are only interested in the URL part following the 'opc.tcp://' prefix
                // and define a group matching this.
                "Pattern": "opc.tcp://(.*)"
            },
            "NodeId": {
                // We are not interested in the configured 'NodeId' value, 
                // so we do not publish it.
                "Publish": false
                // No 'Pattern' key is specified here, so the 'NodeId' value will be
                // taken as specified in the publishednodes configuration file.
            },
            "MonitoredItem": {
                "ApplicationUri": {
                    // We already publish the endpoint URL, so we do not want
                    // the ApplicationUri of the MonitoredItem to be published.
                    "Publish": false
                },
                "DisplayName": {
                    "Publish": true
                }
            },
            "Value": {
                "Value": {
                    // The value of the node is important for us, everything else we
                    // are not interested in to keep the data ingest as small as possible.
                    "Publish": true
                },
                "SourceTimestamp": {
                    "Publish": false
                },
                "StatusCode": {
                    "Publish": false
                },
                "Status": {
                    "Publish": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>次の手順

OPC Publisher を構成する方法を学習しました。推奨される次の手順は [OPC Publisher を実行する](howto-opc-publisher-run.md)方法を学習することです。

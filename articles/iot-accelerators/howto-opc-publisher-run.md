---
title: OPC Publisher の実行 - Azure | Microsoft Docs
description: OPC Publisher の実行
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 3b386171afc7916e5e803c39a9c7b3520752e6fd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603742"
---
# <a name="run-opc-publisher"></a>OPC Publisher の実行

この記事では、OPC Publisher を実行してデバッグする方法について説明します。 パフォーマンスとメモリに関する考慮事項についても説明します。

## <a name="command-line-options"></a>コマンド ライン オプション

`--help` コマンドライン オプションを使用すると、次のようにアプリケーションの使用法が示されます。

```sh/cmd
Current directory is: /appdata
Log file is: <hostname>-publisher.log
Log level is: info

OPC Publisher V2.3.0
Informational version: V2.3.0+Branch.develop_hans_methodlog.Sha.0985e54f01a0b0d7f143b1248936022ea5d749f9

Usage: opcpublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]

OPC Edge Publisher to subscribe to configured OPC UA servers and send telemetry to Azure IoT Hub.
To exit the application, just press CTRL-C while it is running.

applicationname: the OPC UA application name to use, required
                  The application name is also used to register the publisher under this name in the
                  IoT Hub device registry.

IoT Hubconnectionstring: the IoT Hub owner connectionstring, optional

There are a couple of environment variables which can be used to control the application:
_HUB_CS: sets the IoT Hub owner connectionstring
_GW_LOGP: sets the filename of the log file to use
_TPC_SP: sets the path to store certificates of trusted stations
_GW_PNFP: sets the filename of the publishing configuration file

Command line arguments overrule environment variable settings.

Options:
      --pf, --publishfile=VALUE
                              the filename to configure the nodes to publish.
                                Default: '/appdata/publishednodes.json'
      --tc, --telemetryconfigfile=VALUE
                              the filename to configure the ingested telemetry
                                Default: ''
  -s, --site=VALUE           the site OPC Publisher is working in. if specified
                                this domain is appended (delimited by a ':' to
                                the 'ApplicationURI' property when telemetry is
                                sent to IoT Hub.
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --ic, --iotcentral     publisher will send OPC UA data in IoTCentral
                                compatible format (DisplayName of a node is used
                                as key, this key is the Field name in IoTCentral)
                                . you need to ensure that all DisplayName's are
                                unique. (Auto enables fetch display name)
                                Default: False
      --sw, --sessionconnectwait=VALUE
                              specify the wait time in seconds publisher is
                                trying to connect to disconnected endpoints and
                                starts monitoring unmonitored items
                                Min: 10
                                Default: 10
      --mq, --monitoreditemqueuecapacity=VALUE
                              specify how many notifications of monitored items
                                can be stored in the internal queue, if the data
                                can not be sent quick enough to IoT Hub
                                Min: 1024
                                Default: 8192
      --di, --diagnosticsinterval=VALUE
                              shows publisher diagnostic info at the specified
                                interval in seconds (need log level info).
                                -1 disables remote diagnostic log and diagnostic
                                output
                                0 disables diagnostic output
                                Default: 0
      --ns, --noshutdown=VALUE
                              same as runforever.
                                Default: False
      --rf, --runforever     publisher can not be stopped by pressing a key on
                                the console, but will run forever.
                                Default: False
      --lf, --logfile=VALUE  the filename of the logfile to use.
                                Default: './<hostname>-publisher.log'
      --lt, --logflushtimespan=VALUE
                              the timespan in seconds when the logfile should be
                                flushed.
                                Default: 00:00:30 sec
      --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                info, debug, verbose).
                                Default: info
        --ih, --IoT Hubprotocol=VALUE
                              the protocol to use for communication with IoT Hub (
                                allowed values: Amqp, Http1, Amqp_WebSocket_Only,
                                  Amqp_Tcp_Only, Mqtt, Mqtt_WebSocket_Only, Mqtt_
                                Tcp_Only) or IoT EdgeHub (allowed values: Mqtt_
                                Tcp_Only, Amqp_Tcp_Only).
                                Default for IoT Hub: Mqtt_WebSocket_Only
                                Default for IoT EdgeHub: Amqp_Tcp_Only
      --ms, --IoT Hubmessagesize=VALUE
                              the max size of a message which can be send to
                                IoT Hub. when telemetry of this size is available
                                it will be sent.
                                0 will enforce immediate send when telemetry is
                                available
                                Min: 0
                                Max: 262144
                                Default: 262144
      --si, --IoT Hubsendinterval=VALUE
                              the interval in seconds when telemetry should be
                                send to IoT Hub. If 0, then only the
                                IoT Hubmessagesize parameter controls when
                                telemetry is sent.
                                Default: '10'
      --dc, --deviceconnectionstring=VALUE
                              if publisher is not able to register itself with
                                IoT Hub, you can create a device with name <
                                applicationname> manually and pass in the
                                connectionstring of this device.
                                Default: none
  -c, --connectionstring=VALUE
                              the IoT Hub owner connectionstring.
                                Default: none
      --hb, --heartbeatinterval=VALUE
                              the publisher is using this as default value in
                                seconds for the heartbeat interval setting of
                                nodes without
                                a heartbeat interval setting.
                                Default: 0
      --sf, --skipfirstevent=VALUE
                              the publisher is using this as default value for
                                the skip first event setting of nodes without
                                a skip first event setting.
                                Default: False
      --pn, --portnum=VALUE  the server port of the publisher OPC server
                                endpoint.
                                Default: 62222
      --pa, --path=VALUE     the enpoint URL path part of the publisher OPC
                                server endpoint.
                                Default: '/UA/Publisher'
      --lr, --ldsreginterval=VALUE
                              the LDS(-ME) registration interval in ms. If 0,
                                then the registration is disabled.
                                Default: 0
      --ol, --opcmaxstringlen=VALUE
                              the max length of a string opc can transmit/
                                receive.
                                Default: 131072
      --ot, --operationtimeout=VALUE
                              the operation timeout of the publisher OPC UA
                                client in ms.
                                Default: 120000
      --oi, --opcsamplinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds to request the servers to sample
                                the nodes with this interval
                                this value might be revised by the OPC UA
                                servers to a supported sampling interval.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a negative value will set the sampling interval
                                to the publishing interval of the subscription
                                this node is on.
                                0 will configure the OPC UA server to sample in
                                the highest possible resolution and should be
                                taken with care.
                                Default: 1000
      --op, --opcpublishinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds for the publishing interval setting
                                of the subscriptions established to the OPC UA
                                servers.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a value less than or equal zero will let the
                                server revise the publishing interval.
                                Default: 0
      --ct, --createsessiontimeout=VALUE
                              specify the timeout in seconds used when creating
                                a session to an endpoint. On unsuccessful
                                connection attemps a backoff up to 5 times the
                                specified timeout value is used.
                                Min: 1
                                Default: 10
      --ki, --keepaliveinterval=VALUE
                              specify the interval in seconds the publisher is
                                sending keep alive messages to the OPC servers
                                on the endpoints it is connected to.
                                Min: 2
                                Default: 2
      --kt, --keepalivethreshold=VALUE
                              specify the number of keep alive packets a server
                                can miss, before the session is disconneced
                                Min: 1
                                Default: 5
      --aa, --autoaccept     the publisher trusts all servers it is
                                establishing a connection to.
                                Default: False
      --tm, --trustmyself=VALUE
                              same as trustowncert.
                                Default: False
      --to, --trustowncert   the publisher certificate is put into the trusted
                                certificate store automatically.
                                Default: False
      --fd, --fetchdisplayname=VALUE
                              same as fetchname.
                                Default: False
      --fn, --fetchname      enable to read the display name of a published
                                node from the server. this will increase the
                                runtime.
                                Default: False
      --ss, --suppressedopcstatuscodes=VALUE
                              specifies the OPC UA status codes for which no
                                events should be generated.
                                Default: BadNoCommunication,
                                BadWaitingForInitialData
      --at, --appcertstoretype=VALUE
                              the own application cert store type.
                                (allowed values: Directory, X509Store)
                                Default: 'Directory'
      --ap, --appcertstorepath=VALUE
                              the path where the own application cert should be
                                stored
                                Default (depends on store type):
                                X509Store: 'CurrentUser\UA_MachineDefault'
                                Directory: 'pki/own'
      --tp, --trustedcertstorepath=VALUE
                              the path of the trusted cert store
                                Default: 'pki/trusted'
      --rp, --rejectedcertstorepath=VALUE
                              the path of the rejected cert store
                                Default 'pki/rejected'
      --ip, --issuercertstorepath=VALUE
                              the path of the trusted issuer cert store
                                Default 'pki/issuer'
      --csr                  show data to create a certificate signing request
                                Default 'False'
      --ab, --applicationcertbase64=VALUE
                              update/set this applications certificate with the
                                certificate passed in as bas64 string
      --af, --applicationcertfile=VALUE
                              update/set this applications certificate with the
                                certificate file specified
      --pb, --privatekeybase64=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as base64 string
      --pk, --privatekeyfile=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as file
      --cp, --certpassword=VALUE
                              the optional password for the PEM or PFX or the
                                installed application certificate
      --tb, --addtrustedcertbase64=VALUE
                              adds the certificate to the applications trusted
                                cert store passed in as base64 string (multiple
                                strings supported)
      --tf, --addtrustedcertfile=VALUE
                              adds the certificate file(s) to the applications
                                trusted cert store passed in as base64 string (
                                multiple filenames supported)
      --ib, --addissuercertbase64=VALUE
                              adds the specified issuer certificate to the
                                applications trusted issuer cert store passed in
                                as base64 string (multiple strings supported)
      --if, --addissuercertfile=VALUE
                              adds the specified issuer certificate file(s) to
                                the applications trusted issuer cert store (
                                multiple filenames supported)
      --rb, --updatecrlbase64=VALUE
                              update the CRL passed in as base64 string to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --uc, --updatecrlfile=VALUE
                              update the CRL passed in as file to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --rc, --removecert=VALUE
                              remove cert(s) with the given thumbprint(s) (
                                multiple thumbprints supported)
      --dt, --devicecertstoretype=VALUE
                              the IoT Hub device cert store type.
                                (allowed values: Directory, X509Store)
                                Default: X509Store
      --dp, --devicecertstorepath=VALUE
                              the path of the iot device cert store
                                Default Default (depends on store type):
                                X509Store: 'My'
                                Directory: 'CertificateStores/IoT Hub'
  -i, --install              register OPC Publisher with IoT Hub and then exits.
                                Default:  False
  -h, --help                 show this message and exit
      --st, --opcstacktracemask=VALUE
                              ignored, only supported for backward comaptibility.
      --sd, --shopfloordomain=VALUE
                              same as site option, only there for backward
                                compatibility
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --vc, --verboseconsole=VALUE
                              ignored, only supported for backward comaptibility.
      --as, --autotrustservercerts=VALUE
                              same as autoaccept, only supported for backward
                                cmpatibility.
                                Default: False
      --tt, --trustedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted cert store will always reside in a
                                directory.
      --rt, --rejectedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the rejected cert store will always reside in a
                                directory.
      --it, --issuercertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted issuer cert store will always
                                reside in a directory.
```

通常、アプリケーションを最初に実行するときにのみ、IoT Hub 所有者の接続文字列を指定します。 この接続文字列は暗号化され、プラットフォーム証明書ストアに格納されます。 それ以降の実行では、アプリケーションは、証明書ストアから接続文字列を読み取ります。 実行ごとに接続文字列を指定する場合、IoT Hub デバイス レジストリ内にアプリケーション用に作成されたデバイスは削除され、再作成されます。

## <a name="run-natively-on-windows"></a>Windows 上でのネイティブな実行

Visual Studio で **opcpublisher.sln** プロジェクトを開き、ソリューションをビルドし、公開します。 次のようにして、公開先の**ターゲット ディレクトリ**でアプリケーションを開始できます。

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>自作コンテナーの使用

自分用のコンテナーを構築し、次のように起動します。

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Microsoft Container Registry のコンテナーの使用

Microsoft Container Registry には、あらかじめ構築されたコンテナーが用意されています。 次のように起動します。

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

サポートされているオペレーティング システムとプロセッサ アーキテクチャについては、[Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) を確認してください。 お使いの OS および CPU アーキテクチャがサポートされている場合は、適切なコンテナーが Docker によって自動的に選択されます。

## <a name="run-as-an-azure-iot-edge-module"></a>Azure IoT Edge モジュールとしての実行

OPC Publisher は、[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge) モジュールとして使用する準備が整っています。 OPC Publisher を IoT Edge モジュールとして使用する場合、サポートされるトランスポート プロトコルは **Amqp_Tcp_Only** と **Mqtt_Tcp_Only** のみです。

OPC Publisher をモジュールとして自分の IoT Edge のデプロイに追加するには、Azure portal で対象の IoT Hub の設定に移動し、次の手順を実行します。

1. **[IoT Edge]** に移動し、IoT Edge デバイスを作成するか、お使いの IoT Edge デバイスを選択します。
1. **[Set Modules] \(モジュールの設定)** を選択します。
1. **[デプロイ モジュール]** の下の **[追加]** を選択し、次に **[IoT Edge モジュール]** を選択します。
1. **[名前]** フィールドに、「**publisher**」と入力します。
1. **[イメージの URI]** フィールドに、「`mcr.microsoft.com/iotedge/opc-publisher:<tag>`」と入力します
1. 使用可能なタグは [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) で確認できます
1. **[コンテナーの作成オプション]** フィールドに、次の JSON テンプレートを貼り付けます。

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    この構成では、OPC Publisher イメージを使用して **publisher** という名前のコンテナーを起動するように IoT Edge を構成しています。 コンテナーのシステムのホスト名は **publisher** に設定されています。 OPC Publisher は、`--aa` コマンドライン引数で呼び出されます。 このオプションにより、OPC Publisher は、接続先の OPC UA サーバーの証明書を信頼します。 OPC Publisher の任意のコマンドライン オプションを使用できます。 唯一の制限は、IoT Edge でサポートされる "**コンテナーの作成オプション**" のサイズです。

1. 他の設定はそのままにして、 **[保存]** を選択します。
1. OPC Publisher の出力を別の IoT Edge モジュールでローカルに処理する場合は、 **[モジュールの設定]** ページに戻ります。 次に、 **[ルートの指定]** タブに移動し、次の JSON のような新しいルートを追加します。

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. **[モジュールの設定]** ページに戻り、構成の最後のページに到達するまで **[次へ]** を選択します。
1. **[送信]** を選択して、自分の構成を IoT Edge に送信します。
1. お使いのエッジ デバイス上で IoT Edge を起動し、Docker コンテナー **publisher** が実行状態になったら、`docker logs -f publisher` を使用するかログファイルを確認することで、OPC Publisher のログ出力を確認できます。 前の例では、ログ ファイルは上記の `d:\iiotegde\publisher-publisher.log` です。 [iot-edge-opc-publisher-diagnostics ツール](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics)を使用することもできます。

### <a name="make-the-configuration-files-accessible-on-the-host"></a>構成ファイルをホスト上でアクセス可能にする

IoT Edge モジュールの構成ファイルをホスト ファイル システム上でアクセス可能にするには、次の "**コンテナーの作成オプション**" を使用します。 次の例は、Windows 用の Linux コンテナーを使用したデプロイの例です。

```json
{
    "Hostname": "publisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "d:/iiotedge:/appdata"
        ]
    }
}
```

これらのオプションにより、OPC Publisher によって公開する必要があるノードがファイル `./pn.json` から読み取られ、起動時にコンテナーの作業ディレクトリが `/appdata` に設定されます。 これらの設定により、OPC Publisher によってファイル `/appdata/pn.json` がコンテナーから読み取られ、その構成が取得されます。 `--pf` オプションを指定しない場合、OPC Publisher は、既定の構成ファイル `./publishednodes.json` の読み取りを試行します。

ログ ファイルは、既定の名前 `publisher-publisher.log` を使用して `/appdata` に書き込まれます。このディレクトリには、`CertificateStores` ディレクトリも作成されます。

これらすべてのファイルをホスト ファイル システム上で使用できるようにするには、コンテナー構成にバインド マウント ボリュームが必要です。 `d://iiotedge:/appdata` バインドにより、コンテナー起動時の現在の作業ディレクトリである `/appdata` ディレクトリが `d://iiotedge` ホスト ディレクトリにマップされます。 このオプションを指定しないと、次にコンテナーを起動したときにファイル データは保持されません。

Windows コンテナーを実行している場合、`Binds` パラメーターの構文は異なります。 コンテナーの起動時の作業ディレクトリは `c:\appdata` です。 ホストの `d:\iiotedge` ディレクトリの構成ファイルを含めるには、`HostConfig` セクションに次のマッピングを指定します。

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Linux 上で Linux コンテナーを実行している場合も、`Binds` パラメーターの構文が異なります。 コンテナーの起動時の作業ディレクトリは `/appdata` です。 ホスト上の `/iiotedge` ディレクトリの構成ファイルを含めるには、`HostConfig` セクションに次のマッピングを指定します。

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>コンテナーを使用する場合の考慮事項

以降のセクションでは、コンテナーを使用する場合の注意事項について説明します。

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>OPC Publisher OPC UA サーバーへのアクセス

OPC Publisher OPC UA サーバーは、既定でポート 62222 でリッスンします。 コンテナーのこの受信ポートを公開するには、次のコマンドを使用します。

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>コンテナー間の名前解決の有効化

コンテナー内から他のコンテナーへの名前解決を有効にするには、ユーザー定義の Docker ブリッジ ネットワークを作成し、`--network` オプションを使用してコンテナーをこのネットワークに接続します。 さらに、次のように `--name` オプションを使用して、コンテナーに名前を割り当てます。

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

これで、同じネットワーク上の他のコンテナーが名前 `publisher` を使用してこのコンテナーに到達できるようになりました。

### <a name="access-other-systems-from-within-the-container"></a>コンテナー内からの他のシステムへのアクセス

他のコンテナーには、前のセクションで説明したパラメーターを使用して到達できます。 Docker がホストされているオペレーティング システムが DNS に対応している場合、DNS に認識されているすべてのシステムへのアクセスが可能になります。

NetBIOS 名前解決を使用するネットワークでは、対象のコンテナーを `--add-host` オプションで起動して、他のシステムへのアクセスを有効にします。 このオプションは、コンテナーのホスト ファイルにエントリを効果的に追加します。

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>ホスト名の割り当て

OPC Publisher は、証明書とエンドポイントを生成するために、実行中のマシンのホスト名を使用します。 `-h` オプションを使用してホスト名が設定されていない場合、Docker はランダムなホスト名を選択します。 次の例は、コンテナーの内部ホスト名を `publisher` に設定する方法を示しています。

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>バインド マウント (共有ファイルシステム) の使用

コンテナー ファイル システムを使用する代わりに、構成情報とログ ファイルを格納するホスト ファイル システムを選択することができます。 このオプションを構成するには、バインド マウント モードで `docker run` の `-v` オプションを使用します。

## <a name="opc-ua-x509-certificates"></a>OPC UA X.509 証明書

OPC UA は、X.509 証明書を使用して、接続を確立するときに OPC UA クライアントとサーバーを認証し、それらの間の通信を暗号化します。 OPC Publisher は、OPC UA スタックによって管理される証明書ストアを使用して、すべての証明書を管理します。 起動時に、OPC Publisher は、自身の証明書があるかどうかをチェックします。 証明書ストアに証明書がなく、コマンドラインで証明書が渡されない場合、OPC Publisher は、自己署名証明書を作成します。 詳細については、`OpcApplicationConfigurationSecurity.cs` 内の **InitApplicationSecurityAsync** メソッドを参照してください。

自己署名証明書は、信頼された CA によって署名されていないため、セキュリティをまったく提供しません。

OPC Publisher には、次の操作を行うためのコマンドライン オプションが用意されています。

- OPC Publisher によって使用されている現在のアプリケーション証明書の CSR 情報を取得する。
- CA 署名付き証明書を使用して OPC Publisher をプロビジョニングする。
- 新しいキー ペアと一致する CA 署名付き証明書を使用して OPC Publisher をプロビジョニングする。
- 信頼されたピアまたは信頼された発行者の証明書ストアに証明書を追加する。
- CRL を追加する。
- 信頼されたピアまたは信頼された発行者の証明書ストアから証明書を削除する。

これらのすべてのオプションで、ファイルまたは base64 でエンコードされた文字列を使用してパラメーターを渡すことができます。

すべての証明書ストアの既定のストアの種類は、ファイル システムです。これは、コマンドライン オプションを使用して変更できます。 コンテナーはそのファイル システムに永続的なストレージを提供しないため、別のストアの種類を選択する必要があります。 証明書ストアをホスト ファイル システムまたは Docker ボリューム上に保持するには、Docker `-v` オプションを使用します。 Docker ボリュームを使用している場合は、base64 でエンコードされた文字列を使用して証明書を渡すことができます。

ランタイム環境は、証明書の保存方法に影響します。 アプリケーションを実行するたびに新しい証明書ストアを作成することは避けてください。

- アプリケーションを Windows 上でネイティブに実行する場合は、秘密キーへのアクセスが失敗するため、種類が `Directory` のアプリケーション証明書ストアを使用することはできません。 この場合は、`--at X509Store` オプションを使用します。
- アプリケーションを Linux Docker コンテナーとして実行する場合は、Docker run オプション `-v <hostdirectory>:/appdata` を使用して、証明書ストアをホスト ファイル システムにマップできます。 このオプションは、アプリケーションの実行間で証明書を永続的に保持します。
- アプリケーションを Linux Docker コンテナーとして実行し、アプリケーション証明書用に X509 ストアを使用する場合は、Docker run オプション `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` とアプリケーション オプション `--at X509Store` を使用します

## <a name="performance-and-memory-considerations"></a>パフォーマンスとメモリに関する考慮事項

このセクションでは、メモリとパフォーマンスを管理するためのオプションについて説明します。

### <a name="command-line-parameters-to-control-performance-and-memory"></a>パフォーマンスとメモリを制御するためのコマンドライン パラメーター

OPC Publisher を実行するときは、自分のパフォーマンス要件と、ホスト上で使用可能なメモリ リソースを意識する必要があります。

メモリとパフォーマンスは相互に依存し、どちらも、構成する公開ノードの数の構成に依存します。 次のパラメーターが自分の要件を満たしていることを確認します。

- IoT Hub 送信間隔: `--si`
- IoT Hub メッセージ サイズ (既定値 `1`): `--ms`
- 監視項目のキュー容量: `--mq`

`--mq` パラメーターは、すべての OPC ノード値変更通知をバッファー処理する内部キューの容量の上限を制御します。 OPC Publisher が IoT Hub に十分な速さでメッセージを送信できない場合、このキューは通知をバッファー処理します。 このパラメーターを使用して、バッファー処理できる通知の数を設定します。 テスト実行中にこのキュー内の項目数が増加していることがわかった場合は、メッセージが失われないようにするために、次の操作を行う必要があります。

- IoT Hub 送信間隔を小さくする
- IoT Hub メッセージ サイズを大きくする

`--si` パラメーターを使用すると、OPC Publisher が指定された間隔で IoT Hub にメッセージを送信するように強制できます。 OPC Publisher は、`--ms` パラメーターで指定されたメッセージ サイズに達するか、`--si` パラメーターで指定された間隔に達すると、すぐにメッセージを送信します。 メッセージ サイズのオプションを無効にするには、`--ms 0` を使用します。 この場合、OPC Publisher は、データをバッチ処理するために、IoT Hub メッセージ サイズとして最大の 256 KB を使用します。

`--ms` パラメーターを使用すると、IoT Hub に送信されたメッセージをバッチ処理できます。 使用しているプロトコルによって、IoT Hub へのメッセージ送信のオーバーヘッドが実際のペイロード送信時間と比べて大きいかどうかが決まります。 シナリオでデータが IoT Hub によって取り込まれるときの待ち時間を考慮している場合は、256 KB の最大メッセージ サイズを使用するように OPC Publisher を構成します。

運用シナリオで OPC Publisher を使用する前に、運用条件下でパフォーマンスとメモリ使用量をテストします。 `--di` パラメーターを使用すると、OPC Publisher が診断情報を書き込む間隔を秒単位で指定できます。

### <a name="test-measurements"></a>テスト測定

次の診断例は、1 秒の OPC 公開間隔で 500 個のノードを公開しているときに `--si` および `--ms` パラメーターに異なる値を指定した場合の測定値を示しています。  このテストでは、Windows 10 上で 120 秒間ネイティブに OPC Publisher デバッグ ビルドを使用しました。 IoT Hub プロトコルは、既定の MQTT プロトコルでした。

#### <a name="default-configuration---si-10---ms-262144"></a>既定の構成 (--si 10 --ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:33:05 (started @ 26.10.2017 15:31:09)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54363
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54363
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:33:04
bytes sent to IoT Hub: 12709429
avg msg size: 116600
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 10
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

既定の構成では、10 秒ごとに、または IoT Hub によって取り込まれる 256 KB のデータの準備ができたときに、データが IoT Hub に送信されます。 この構成では、約 10 秒の中程度の待ち時間が追加されますが、メッセージ サイズが大きいため、データが失われる可能性が最も低くなります。 `monitored item notifications enqueue failure: 0` 診断出力は、失われた OPC ノード更新がないことを示しています。

#### <a name="constant-send-interval---si-1---ms-0"></a>一定の送信間隔 (--si 1 --ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:35:59 (started @ 26.10.2017 15:34:03)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54243
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54243
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:35:59
bytes sent to IoT Hub: 12683836
avg msg size: 116365
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 1
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

メッセージ サイズを 0 に設定した場合、OPC Publisher は、サポートされている最大の IoT Hub メッセージ サイズ (256 KB) を使用して、データを内部的にバッチ処理します。 この診断出力は、平均メッセージ サイズが 115,019 バイトであることを示しています。 この構成では、OPC Publisher は、OPC ノード値の更新を失うことはなく、既定値と比較して待ち時間が短くなります。

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>各 OPC ノード値の更新の送信 (--si 0 --ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:39:33 (started @ 26.10.2017 15:37:37)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 8184
monitored item notifications enqueued: 54232
monitored item notifications enqueue failure: 44624
monitored item notifications dequeued: 1424
---------------------------------
messages sent to IoT Hub: 1423
last successful msg sent @: 26.10.2017 15:39:33
bytes sent to IoT Hub: 333046
avg msg size: 234
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 96
--si setting: 0
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

この構成では、各 OPC ノード値が変更されるたびにメッセージが IoT Hub に送信されます。 診断から、平均メッセージ サイズが、小さな 234 バイトであることがわかります。 この構成の利点は、OPC Publisher の処理による待ち時間が発生しないことです。 失われた OPC ノード値の更新数 (`monitored item notifications enqueue failure: 44624`) が多いため、この構成は、大量のテレメトリが公開されるシナリオには不適切です。

### <a name="maximum-batching---si-0---ms-262144"></a>最大バッチ処理 (--si 0 --ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:42:55 (started @ 26.10.2017 15:41:00)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54137
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54137
---------------------------------
messages sent to IoT Hub: 48
last successful msg sent @: 26.10.2017 15:42:55
bytes sent to IoT Hub: 12565544
avg msg size: 261782
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 0
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

この構成では、できるだけ多くの OPC ノード値の更新がバッチ処理されます。 最大 IoT Hub メッセージ サイズは、ここで構成した 256 KB です。 送信間隔は要求されていません。つまり、IoT Hub によって取り込まれるデータの量によって待ち時間が決まります。 この構成は、OPC ノード値を失う可能性が最も低く、多数のノードを公開するのに適しています。 この構成を使用するときは、メッセージ サイズが 256 KB に達しない場合に対象のシナリオで高遅延が発生するような状況にならないようにします。

## <a name="debug-the-application"></a>アプリケーションのデバッグ

このアプリケーションをデバッグするには、Visual Studio で **opcpublisher.sln** ソリューション ファイルを開き、Visual Studio のデバッグ ツールを使用します。

OPC Publisher の OPC UA サーバーにアクセスする必要がある場合は、サーバーがリッスンするポートへのアクセスがお使いのファイアウォールによって許可されていることを確認してください。 既定のポートは次のとおりです: 62222。

## <a name="control-the-application-remotely"></a>アプリケーションのリモート制御

ノードを公開するように構成するには、IoT Hub ダイレクト メソッドを使用します。

OPC Publisher は、いくつかの追加の IoT Hub ダイレクト メソッド呼び出しを実装して、次の情報を読み取ります。

- 一般情報。
- OPC セッション、サブスクリプション、および監視項目の診断情報。
- IoT Hub のメッセージおよびイベントの診断情報。
- 起動ログ。
- ログの最後の 100 行。
- アプリケーションをシャットダウンします。

次の GitHub リポジトリには、[公開するノードを構成する](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration)ためのツールと[診断情報を読み取る](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics)ためのツールが含まれています。 どちらのツールも Docker Hub でコンテナーとして入手できます。

## <a name="use-a-sample-opc-ua-server"></a>サンプル OPC UA サーバーの使用

実際の OPC UA サーバーがない場合は、[サンプル OPC UA PLC](https://github.com/Azure-Samples/iot-edge-opc-plc) を使用して作業を開始できます。 このサンプル PLC も、Docker Hub 上で入手できます。

これは、ランダムなデータと異常のあるタグを生成する多数のタグを実装します。 追加のタグ値をシミュレートする必要がある場合は、サンプルを拡張できます。

## <a name="next-steps"></a>次の手順

ここでは、OPC Publisher の実行方法について学習しました。次のステップとして、[OPC Twin](overview-opc-twin.md) と [OPC Vault](overview-opc-vault.md) について学習することをお勧めします。

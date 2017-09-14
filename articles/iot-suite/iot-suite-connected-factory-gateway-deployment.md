---
title: "Azure IoT Suite コネクテッド ファクトリのゲートウェイをデプロイする | Microsoft Docs"
description: "Windows または Linux 上にゲートウェイをデプロイして、構成済みのコネクテッド ファクトリ ソリューションに接続できるようにする方法。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: caa12f4ef55006cd3edbe2d9606397d34fed3a3e
ms.contentlocale: ja-jp
ms.lasthandoff: 07/24/2017

---

# <a name="deploy-a-gateway-on-windows-or-linux-for-the-connected-factory-preconfigured-solution"></a>構成済みのコネクテッド ファクトリ ソリューション用のゲートウェイを Windows または Linux 上にデプロイする

構成済みのコネクテッド ファクトリ ソリューション用のゲートウェイをデプロイするために必要なソフトウェアには、次の 2 つのコンポーネントがあります。

* *OPC Proxy* は、IoT Hub との接続を確立し、 コネクテッド ファクトリ ソリューション ポータルで動作する統合された *OPC* ブラウザーからのコマンドと制御のメッセージを待機します。
* *OPC Publisher* は、既存のオンプレミス OPC UA サーバーに接続し、そこから IoT Hub にテレメトリ メッセージを転送します。

どちらのコンポーネントもオープン ソースであり、GitHub 上のソースとして、または Docker コンテナーとして利用することができます。

| GitHub | DockerHub |
| ------ | --------- |
| [OPC Publisher][lnk-publisher-github] | [OPC Publisher][lnk-publisher-docker] |
| [OPC Proxy][lnk-proxy-github] | [OPC Proxy][lnk-proxy-docker] |

どちらのコンポーネントも、公開 IP アドレスまたはゲートウェイ ファイアウォールの開放ポートは必要ありません。 OPC Proxy と OPC Publisher で使用されるのは、送信ポート 443、5671、8883 だけです。

この記事では、Docker を使用して [Windows](#windows-deployment) または [Linux](#linux-deployment) 上にゲートウェイをデプロイする手順について説明します。 ゲートウェイにより、構成済みのコネクテッド ファクトリ ソリューションに接続できるようになります。

> [!NOTE]
> Docker コンテナーで実行されるゲートウェイ ソフトウェアは、[Azure IoT Edge]です。

## <a name="windows-deployment"></a>Windows デプロイ

> [!NOTE]
> ゲートウェイ デバイスがまだない場合は、Microsoft のパートナーのいずれかから商用ゲートウェイを購入することをお勧めします。 コネクテッド ファクトリ ソリューションと互換性のあるゲートウェイ デバイスの一覧については、[Azure IoT デバイス カタログ]をご覧ください。 デバイスに付属する手順書に従って、ゲートウェイを設定します。 また、以下の手順に従って、既存のゲートウェイのいずれかを手動で設定することもできます。

### <a name="install-docker"></a>Docker をインストールする

Windows ベースのゲートウェイ デバイスに [Docker for Windows] をインストールします。 Windows Docker のセットアップ時に、Docker と共有するホスト コンピューターのドライブを選択します。 次のスクリーンショットは、Windows システムの D ドライブの共有を示しています。

![Docker をインストールする][img-install-docker]

次に、共有ドライブのルートに **docker** というフォルダーを作成します。
この手順は、Docker のインストール後に **[設定]** メニューから実行することもできます。

### <a name="configure-the-gateway"></a>ゲートウェイの構成

1. ゲートウェイのデプロイを実行するには、Azure IoT Suite コネクテッド ファクトリ デプロイの **iothubowner** 接続文字列が必要です。 [Azure Portal] で、コネクテッド ファクトリ ソリューションをデプロイしたときに作成したリソース グループの IoT Hub に移動します。 **[共有アクセス ポリシー]** をクリックして、**iothubowner** 接続文字列にアクセスします。

    ![IoT Hub 接続文字列を見つける][img-hub-connection]

    **[接続文字列 -- 主キー]** の値をコピーします。

1. コマンド プロンプトで次のコマンドを使用して、2 つのゲートウェイ モジュールを **1 回**実行し、IoT Hub のゲートウェイを構成します。

    `docker run -it --rm -h <ApplicationName> -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;** は、OPC UA Publisher に付ける名前です。**publisher.&lt;完全修飾ドメイン名&gt;** の形式になります。 たとえば、ファクトリ ネットワークの名称が **myfactorynetwork.com** である場合、**ApplicationName** の値は **publisher.myfactorynetwork.com** になります。
    * **&lt;IoTHubOwnerConnectionString&gt;** は、前の手順でコピーした **iothubowner** 接続文字列です。 この接続文字列はこの手順でのみ使用します。以降の手順では不要です。

    マップされた D:\\docker フォルダー (`-v` 引数) は、ゲートウェイ モジュールで使用する 2 つの X.509 証明書を保持するために後で使用します。

### <a name="run-the-gateway"></a>ゲートウェイを実行する

1. 次のコマンドを使用して、ゲートウェイを再起動します。

    `docker run -it --rm -h <ApplicationName> --expose 62222 -p 62222:62222 -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/shared -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. セキュリティ上の理由から、D:\\docker フォルダーに保持される 2 つの X.509 証明書には秘密キーが含まれます。 このフォルダーへのアクセスは、Docker コンテナーを実行する際に使用する資格情報 (通常は **Administrators**) に制限してください。 D:\\docker フォルダーを右クリックし、**[プロパティ]**、**[セキュリティ]**、**[編集]** の順に選択します。 **Administrators** にフル コントロールを付与し、他のすべてのユーザーを削除します。

    ![Docker 共有にアクセス許可を付与する][img-docker-share]

1. ネットワーク接続を確認します。 コマンド プロンプトで `ping publisher.<your fully qualified domain name>` コマンドを入力し、ゲートウェイに ping を送信します。 接続先に到達できない場合は、ゲートウェイの hosts ファイルにゲートウェイの IP アドレスと名前を追加します。 hosts ファイルは、**Windows\\System32\\drivers\\etc** フォルダーにあります。

1. 次に、ゲートウェイで実行されているローカル OPC UA クライアントを使用して、パブリッシャーに接続してみます。 OPC UA エンドポイントの URL は、`opc.tcp://publisher.<your fully qualified domain name>:62222` です。 OPC UA クライアントがない場合は、[オープンソースの OPC UA クライアント]をダウンロードして使用できます。

1. これらのローカル テストが正常に完了したら、コネクテッド ファクトリ ソリューション ポータルの **[Connect your own OPC UA Server (独自の OPC UA サーバーの接続)]** ページを参照します。 パブリッシャー エンドポイントの URL (`tcp://publisher.<your fully qualified domain name>:62222`) を入力し、**[接続]** をクリックします。 証明書の警告が表示されたら、**[続行]** をクリックします。 次に、パブリッシャーが UA Web クライアントを信頼していないことを示すエラーが表示されます。 このエラーを解決するには、**UA Web クライアント**の証明書を、**D:\\docker\\Rejected Certificates\\certs** フォルダーからゲートウェイの **D:\\docker\\UA Applications\\certs** フォルダーにコピーします。 ゲートウェイを再起動する必要はありません。 この手順を繰り返します。 これでクラウドからゲートウェイに接続できるようになりました。OPC UA サーバーをソリューションに追加する準備ができました。

### <a name="add-your-opc-ua-servers"></a>OPC UA サーバーを追加する

1. コネクテッド ファクトリ ソリューション ポータルの **[Connect your own OPC UA Server (独自の OPC UA サーバーの接続)]** ページを参照します。 前のセクションの手順に従って、コネクテッド ファクトリ ポータルと OPC UA サーバー間の信頼を確立します。 この手順により、コネクテッド ファクトリ ポータルと OPC UA サーバーから証明書の相互の信頼が確立され、接続が作成されます。

1. OPC UA サーバーの OPC UA ノード ツリーを参照し、OPC ノードを右クリックして **[発行]** をクリックします。 発行をこのように実行するには、OPC UA サーバーとパブリッシャーが同じネットワーク上に存在する必要があります。 つまり、パブリッシャーの完全修飾ドメイン名が **publisher.mydomain.com** の場合、OPC UA サーバーの完全修飾ドメイン名は、たとえば **myopcuaserver.mydomain.com** である必要があります。 現在のセットアップが異なる場合は、**D:\\docker** フォルダーにある publishesnodes.json ファイルにノードを手動で追加できます。 publishesnodes.json ファイルは、OPC ノードが最初に正常に発行されたときに自動的に生成されます。

1. これでテレメトリがゲートウェイ デバイスから送られてくるようになります。 テレメトリは、コネクテッド ファクトリ ポータルの **[Factory Locations (ファクトリの場所)]** ビューにある **[New Factory (新しいファクトリ)]** で確認できます。

## <a name="linux-deployment"></a>Linux デプロイ

> [!NOTE]
> ゲートウェイ デバイスがまだない場合は、Microsoft のパートナーのいずれかから商用ゲートウェイを購入することをお勧めします。 コネクテッド ファクトリ ソリューションと互換性のあるゲートウェイ デバイスの一覧については、[Azure IoT デバイス カタログ]をご覧ください。 デバイスに付属する手順書に従って、ゲートウェイを設定します。 また、以下の手順に従って、既存のゲートウェイのいずれかを手動で設定することもできます。

Linux ゲートウェイ デバイスに [Docker をインストール]します。

### <a name="configure-the-gateway"></a>ゲートウェイの構成

1. ゲートウェイのデプロイを実行するには、Azure IoT Suite コネクテッド ファクトリ デプロイの **iothubowner** 接続文字列が必要です。 [Azure Portal] で、コネクテッド ファクトリ ソリューションをデプロイしたときに作成したリソース グループの IoT Hub に移動します。 **[共有アクセス ポリシー]** をクリックして、**iothubowner** 接続文字列にアクセスします。

    ![IoT Hub 接続文字列を見つける][img-hub-connection]

    **[接続文字列 -- 主キー]** の値をコピーします。

1. シェルで次のコマンドを使用して、2 つのゲートウェイ モジュールを **1 回**実行し、IoT Hub のゲートウェイを構成します。

    `sudo docker run -it --rm -h <ApplicationName> -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/ -v /shared:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `sudo docker run --rm -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;** は、ゲートウェイによって作成された OPC UA アプリケーションの名前です。**publisher.&lt;完全修飾ドメイン名&gt;** の形式になります  (例: **publisher.microsoft.com**)。
    * **&lt;IoTHubOwnerConnectionString&gt;** は、前の手順でコピーした **iothubowner** 接続文字列です。 この接続文字列はこの手順でのみ使用します。以降の手順では不要です。

    **/shared** フォルダー (`-v` 引数) は、ゲートウェイ モジュールで使用する 2 つの X.509 証明書を保持するために後で使用します。

### <a name="run-the-gateway"></a>ゲートウェイを実行する

1. 次のコマンドを使用して、ゲートウェイを再起動します。

    `sudo docker run -it -h <ApplicationName> --expose 62222 -p 62222:62222 –-rm -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v /shared:/shared -v /shared:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `sudo docker run -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. セキュリティ上の理由から、**/shared** フォルダーに保持される 2 つの X.509 証明書には秘密キーが含まれます。 このフォルダーへのアクセスは、Docker コンテナーを実行する際に使用する資格情報に制限してください。 **root** のアクセス許可だけを設定するには、フォルダーに対して `chmod` シェル コマンドを使用します。

1. ネットワーク接続を確認します。 シェルで `ping publisher.<your fully qualified domain name>` コマンドを入力し、ゲートウェイに ping を送信します。 接続先に到達できない場合は、ゲートウェイの hosts ファイルにゲートウェイの IP アドレスと名前を追加します。 hosts ファイルは **/etc** フォルダーにあります。

1. 次に、ゲートウェイで実行されているローカル OPC UA クライアントを使用して、パブリッシャーに接続してみます。 OPC UA エンドポイントの URL は、`opc.tcp://publisher.<your fully qualified domain name>:62222` です。 OPC UA クライアントがない場合は、[オープンソースの OPC UA クライアント]をダウンロードして使用できます。

1. これらのローカル テストが正常に完了したら、コネクテッド ファクトリ ソリューション ポータルの **[Connect your own OPC UA Server (独自の OPC UA サーバーの接続)]** ページを参照します。 パブリッシャー エンドポイントの URL (`tcp://publisher.<your fully qualified domain name>:62222`) を入力し、**[接続]** をクリックします。 証明書の警告が表示されたら、**[続行]** をクリックします。 次に、パブリッシャーが UA Web クライアントを信頼していないことを示すエラーが表示されます。 このエラーを解決するには、**UA Web クライアント**の証明書を、**/shared/Rejected Certificates/certs** フォルダーからゲートウェイの **/shared/UA Applications/certs** フォルダーにコピーします。 ゲートウェイを再起動する必要はありません。 この手順を繰り返します。 これでクラウドからゲートウェイに接続できるようになりました。OPC UA サーバーをソリューションに追加する準備ができました。

### <a name="add-your-opc-ua-servers"></a>OPC UA サーバーを追加する

1. コネクテッド ファクトリ ソリューション ポータルの **[Connect your own OPC UA Server (独自の OPC UA サーバーの接続)]** ページを参照します。 前のセクションの手順に従って、コネクテッド ファクトリ ポータルと OPC UA サーバー間の信頼を確立します。 この手順により、コネクテッド ファクトリ ポータルと OPC UA サーバーから証明書の相互の信頼が確立され、接続が作成されます。

1. OPC UA サーバーの OPC UA ノード ツリーを参照し、OPC ノードを右クリックして **[発行]** をクリックします。 発行をこのように実行するには、OPC UA サーバーとパブリッシャーが同じネットワーク上に存在する必要があります。 つまり、パブリッシャーの完全修飾ドメイン名が **publisher.mydomain.com** の場合、OPC UA サーバーの完全修飾ドメイン名は、たとえば **myopcuaserver.mydomain.com** である必要があります。 現在のセットアップが異なる場合は、**/shared** フォルダーにある publishesnodes.json ファイルにノードを手動で追加できます。 publishesnodes.json は、OPC ノードが最初に正常に発行されたときに自動的に生成されます。

1. これでテレメトリがゲートウェイ デバイスから送られてくるようになります。 テレメトリは、コネクテッド ファクトリ ポータルの **[Factory Locations (ファクトリの場所)]** ビューにある **[New Factory (新しいファクトリ)]** で確認できます。

## <a name="next-steps"></a>次のステップ

構成済みの コネクテッド ファクトリ ソリューションのアーキテクチャの詳細については、[構成済みのコネクテッド ファクトリ ソリューションのチュートリアル][lnk-walkthrough]をご覧ください。

[img-install-docker]: ./media/iot-suite-connected-factory-gateway-deployment/image1.png
[img-hub-connection]: ./media/iot-suite-connected-factory-gateway-deployment/image2.png
[img-docker-share]: ./media/iot-suite-connected-factory-gateway-deployment/image3.png

[Docker for Windows]: https://www.docker.com/docker-windows
[Azure IoT デバイス カタログ]: https://catalog.azureiotsuite.com/?q=opc
[Azure Portal]: http://portal.azure.com/
[オープンソースの OPC UA クライアント]: https://github.com/OPCFoundation/UA-.NETStandardLibrary/tree/master/SampleApplications/Samples/Client.Net4
[Docker をインストール]: https://www.docker.com/community-edition#/download
[lnk-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[Azure IoT Edge]: https://github.com/Azure/iot-edge

[lnk-publisher-github]: https://github.com/Azure/iot-edge-opc-publisher
[lnk-publisher-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua
[lnk-proxy-github]: https://github.com/Azure/iot-edge-opc-proxy
[lnk-proxy-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua-proxy

---
title: お使いの接続済みファクトリ ゲートウェイをデプロイする - Azure | Microsoft Docs
description: Windows または Linux 上にゲートウェイをデプロイして、接続済みファクトリ ソリューション アクセラレータに接続できるようにする方法。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 99953b486fbd1daa9800aa850684447465eead9e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182100"
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-solution-accelerator-on-windows-or-linux"></a>接続済みファクトリ ソリューション アクセラレータ用のエッジ ゲートウェイを Windows または Linux にデプロイする

"*接続済みファクトリ*" ソリューション アクセラレータ用のエッジ ゲートウェイをデプロイするには、次の 2 つのソフトウェア コンポーネントが必要です。

-  *OPC Proxy*  は、接続済みファクトリとの接続を確立します。 OPC Proxy は、接続済みファクトリ ソリューション ポータルで動作する統合された OPC ブラウザーからのコマンドと制御メッセージを待機します。

-  *OPC Publisher*  は、既存のオンプレミス OPC UA サーバーに接続し、そこから接続済みファクトリにテレメトリ メッセージを転送します。 [OPC UA 用の OPC クラシック アダプター](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md)を使用して、OPC クラシック デバイスに接続できます。

どちらのコンポーネントもオープン ソースであり、GitHub 上のソースとして、または DockerHub 上の Docker コンテナーとして利用できます。

| GitHub | DockerHub |
| ------ | --------- |
| [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) | [OPC Publisher](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy)         | [OPC Proxy](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

どちらのコンポーネントも、公開 IP アドレスまたはゲートウェイ ファイアウォールの受信ポートの開放は必要ありません。 OPC Proxy コンポーネントと OPC Publisher コンポーネントでは、送信ポート 443 のみが使用されます。

この記事では、Docker を使用して Windows または Linux 上にエッジ ゲートウェイをデプロイする手順について説明します。 ゲートウェイにより、接続済みファクトリ ソリューション アクセラレータに接続できるようになります。 コンポーネントは、接続済みファクトリなしで使用することもできます。

> [!NOTE]
> どちらのコンポーネントも、 [Azure IoT Edge](https://github.com/Azure/iot-edge) 内でモジュールとして使用できます。

## <a name="choose-a-gateway-device"></a>ゲートウェイ デバイスを選択する

ゲートウェイ デバイスがまだない場合は、Microsoft のパートナーのいずれかから商用ゲートウェイを購入することをお勧めします。 接続済みファクトリ ソリューションと互換性のあるゲートウェイ デバイスの一覧については、 [Azure IoT デバイス カタログ](https://catalog.azureiotsolutions.com/?q=opc)をご覧ください。 デバイスに付属する手順書に従って、ゲートウェイを設定します。

既存のゲートウェイ デバイスを手動で設定する必要がある場合は、以下の手順に従ってください。

## <a name="install-and-configure-docker"></a>Docker をインストールして構成する

 [Docker for Windows](https://www.docker.com/docker-windows)  を Windows ベースのゲートウェイ デバイスにインストールするか、パッケージ マネージャーを使用して Linux ベースのゲートウェイ デバイスに Docker をインストールします。

Docker for Windows のセットアップ時に、Docker と共有するホスト コンピューターのドライブを選択します。 次のスクリーンショットは、Windows システムの **D** ドライブの共有を示しています。 ドライブを共有すると、Docker コンテナーの内部からホスト ドライブにアクセスすることができます。

![Docker for Windows のインストール](./media/iot-accelerators-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> この手順は、Docker のインストール後に  **[設定]** ダイアログから実行することもできます。 Windows システム トレイの **[Docker]** アイコンを右クリックし、**[設定]** を選択します。 Windows のメジャー アップデート (Windows Fall Creators アップデートなど) がシステムに展開された場合は、ドライブをいったん共有解除してから再び共有して、アクセス権を更新します。

Linux を使用している場合は、ファイル システムへのアクセスを有効にするために必要な追加構成はありません。

Windows では Docker と共有したドライブにフォルダーを作成し、Linux ではルート ファイル システムの下にフォルダーを作成します。 このチュートリアルでは、このフォルダーを `<SharedFolder>` と呼びます。

Docker コマンドで `<SharedFolder>` を参照するときは、必ずオペレーティング システムの正しい構文を使用してください。 Windows 用と Linux 用の 2 つの例を次に示します。

- Windows で `D:\shared` フォルダーを `<SharedFolder>` として使用する場合、Docker コマンドの構文は `d:/shared` になります。

- Linux で `/shared` フォルダーを `<SharedFolder>` として使用する場合、Docker コマンドの構文は `/shared` になります。

詳細については、Docker エンジン リファレンスの[ボリュームの使用](https://docs.docker.com/engine/admin/volumes/volumes/)に関する記事を参照してください。

## <a name="configure-the-opc-components"></a>OPC コンポーネントを構成する

OPC コンポーネントをインストールする前に、次の手順を完了して環境を準備してください。

1. ゲートウェイのデプロイを完了するには、接続済みファクトリのデプロイに IoT ハブの  **iothubowner** 接続文字列が必要です。  [Azure portal](https://portal.azure.com/) で、接続済みファクトリ ソリューションのデプロイ時に作成したリソース グループの IoT ハブに移動します。  **[共有アクセス ポリシー]** をクリックして  **iothubowner** 接続文字列にアクセスします。

    ![IoT Hub 接続文字列を見つける](./media/iot-accelerators-connected-factory-gateway-deployment/image2.png)

     **[接続文字列 -- 主キー]** の値をコピーします。

1. Docker コンテナー間の通信を許可するのには、ユーザー定義のブリッジ ネットワークが必要です。 コンテナー用のブリッジ ネットワークを作成するには、コマンド プロンプトで次のコマンドを実行します。

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    **iot_edge** ブリッジ ネットワークが作成されたことを確認するには、次のコマンドを実行します。

    ```cmd/sh
    docker network ls
    ```

    ネットワークの一覧に **Iot_edge** ブリッジ ネットワークが含まれます。

OPC Publisher を実行するには、コマンド プロンプトで次のコマンドを実行します。

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.4 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- [OPC Publisher GitHub](https://github.com/Azure/iot-edge-opc-publisher) と [Docker 実行リファレンス](https://docs.docker.com/engine/reference/run/)で、以下の項目の詳細を参照できます。

  - コンテナー名の前に指定される Docker コマンド ライン オプション (`microsoft/iot-edge-opc-publisher:2.1.4`)。
  - コンテナー名の後ろに指定される OPC Publisher コマンド ライン パラメーターの意味 (`microsoft/iot-edge-opc-publisher:2.1.4`)。

- `<IoTHubOwnerConnectionString>` は、Azure Portal の **iothubowner** 共有アクセス ポリシーの接続文字列です。 この接続文字列は、前の手順でコピーしています。 この接続文字列は、OPC Publisher の初回の実行時にのみ必要です。 セキュリティ リスクがあるため、以降の実行時は省略する必要があります。

- 使用する `<SharedFolder>`とその構文については、「[Docker をインストールして構成する](#install-and-configure-docker)」セクションで説明されています。 OPC Publisher は、`<SharedFolder>` を使用して次のことを行います。

    - OPC Publisher 構成ファイルを読み書きする。
    - ログ ファイルにログを書き込む。
    - これら両方のファイルを、コンテナーの外部から使用できるようにする。

- OPC Publisher は、その構成を **publishednodes.json** ファイルから読み取ります。このファイルは、`<SharedFolder>/docker` フォルダーで読み書きされます。 この構成ファイルは、OPC Publisher がサブスクライブする必要がある OPC UA サーバー上の OPC UA ノード データを定義します。 **publishednodes.json** ファイルの完全な構文については、GitHub の [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) ページで説明されています。 ゲートウェイを追加するときは、空の **publishednodes.json** をフォルダーに置きます。

    ```json
    [
    ]
    ```

- OPC UA サーバーが OPC Publisher にデータの変更を通知するたびに、新しい値が IoT Hub に送信されます。 バッチ処理の設定によっては、OPC Publisher は、データを蓄積した後で IoT Hub にデータをバッチ送信する場合があります。

- Docker は、NetBIOS 名の解決はサポートしません。DNS 名の解決のみをサポートします。 ネットワークに DNS サーバーがない場合は、前のコマンド ラインの例に示されている回避策を使用できます。 前のコマンド ラインの例では、`--add-host` パラメーターを使用して、コンテナーのホスト ファイルにエントリを追加します。 このエントリによって、特定の `<OpcServerHostname>` のホスト名検索が可能になり、IP アドレス `<IpAddressOfOpcServerHostname>` に解決されます。

- OPC UA は、認証と暗号化で X.509 証明書を使用します。 OPC Publisher 証明書を接続先の OPC UA サーバーに配置して、OPC Publisher を信頼できるようにしてください。 OPC Publisher 証明書ストアは、`<SharedFolder>/CertificateStores` フォルダーに配置されます。 OPC Publisher 証明書は、`CertificateStores` フォルダー内の `trusted/certs` フォルダーで見つけることができます。

  OPC UA サーバーを構成する手順は、使用しているデバイスによって異なります。 これらの手順は、通常は OPC UA サーバーのユーザー マニュアルに記載されています。

OPC Proxy をインストールするには、コマンド プロンプトで次のコマンドを実行します。

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

インストールは、システムで 1 回のみ実行する必要があります。

次のコマンドを使用して OPC Proxy を実行します。

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -D /mapped/cs.db
```

OPC Proxy は、インストール中に接続文字列を保存します。 セキュリティ リスクがあるため、以降の実行では接続文字列を省略する必要があります。

## <a name="enable-your-gateway"></a>ゲートウェイを有効にする

次の手順を完了して、接続済みファクトリ ソリューション アクセラレータで、ゲートウェイを有効にします。

1. 両方のコンポーネントが実行されているときに、接続済みファクトリ ソリューション ポータルの  **[独自の OPC UA サーバーを接続する]** ページに移動します。 このページは、ソリューションの管理者のみが使用できます。 パブリッシャー エンドポイント URL (opc.tcp://publisher:62222) を入力し、 **[接続]** をクリックします。

1. 接続済みファクトリ ポータルと OPC Publisher の間に信頼関係を確立します。 証明書の警告が表示されたら、 **[続行]** をクリックします。 次に、OPC Publisher が UA Web クライアントを信頼していないことを示すエラーが表示されます。 このエラーを解決するには、 **UA Web クライアント** 証明書を `<SharedFolder>/CertificateStores/rejected/certs` フォルダーからゲートウェイの `<SharedFolder>/CertificateStores/trusted/certs` フォルダーにコピーします。 ゲートウェイを再起動する必要はありません。

これでクラウドからゲートウェイに接続できるようになりました。OPC UA サーバーをソリューションに追加する準備ができました。

## <a name="add-your-own-opc-ua-servers"></a>独自の OPC UA サーバーを追加する

独自の OPC UA サーバーを接続済みファクトリ ソリューション アクセラレータに追加するには:

1. 接続済みファクトリ ソリューション ポータルの  **[独自の OPC UA サーバーを接続する]** ページに移動します。

    1. 接続する OPC UA サーバーを起動します。 コンテナーで実行している OPC Publisher および OPC Proxy から OPC UA サーバーに到達できることを確認します。 名前解決に関する前記のコメントを参照してください。
    1. OPC UA サーバーのエンドポイント URL (`opc.tcp://<host>:<port>`) を入力して、**[接続]** をクリックします。
    1. 接続のセットアップ プロセスでは、接続済みファクトリ ポータル (OPC UA クライアント) と接続しようとしている OPC UA サーバーの間に、信頼関係が確立されます。 接続済みファクトリ ダッシュボードで、"**接続先のサーバーの証明書を確認できません**" という警告が表示されます。 証明書の警告が表示されたら、**[続行]** をクリックします。
    1. 接続しようとしている OPC UA サーバーの証明書の構成は、セットアップがさらに困難です。 PC ベースの OPC UA サーバーでは、ダッシュボードの警告ダイアログに対して確認するだけで済む場合があります。 埋め込まれた OPC UA サーバー システムの場合は、お使いの OPC UA サーバーのドキュメントを参照して、このタスクの実行方法を確認してください。 このタスクを完了するには、接続済みファクトリ ポータルの OPC UA クライアントの証明書が必要になる場合があります。 管理者は、この証明書を **[独自の OPC UA サーバーを接続する]** ページでダウンロードできます。

        ![ソリューション ポータル](./media/iot-accelerators-connected-factory-gateway-deployment/image4.png)

1. OPC UA サーバーの OPC UA ノード ツリーに移動します。接続済みファクトリに値を送信する OPC ノードを右クリックし、 **[発行]** を選びます。

1. これでテレメトリがゲートウェイ デバイスから送られてくるようになります。 テレメトリは、接続済みファクトリ ポータルの  **[ファクトリの場所]** ビューにある  **[新しいファクトリ]** で確認できます。

## <a name="next-steps"></a>次の手順

接続済みファクトリ ソリューション アクセラレータのアーキテクチャの詳細については、 [接続済みファクトリ ソリューション アクセラレータのチュートリアル](iot-accelerators-connected-factory-sample-walkthrough.md)をご覧ください。

 [OPC Publisher のリファレンス実装](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher)をご覧ください。
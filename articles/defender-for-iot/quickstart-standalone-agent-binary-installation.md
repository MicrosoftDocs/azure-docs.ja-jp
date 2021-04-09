---
title: Defender for IoT マイクロ エージェントをインストールする (プレビュー)
description: Defender マイクロ エージェントをインストールして認証する方法について説明します。
ms.date: 3/9/2021
ms.topic: quickstart
ms.openlocfilehash: ecde9f42acc5cf4a924a657f4f0cdc545c4a668e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782726"
---
# <a name="install-defender-for-iot-micro-agent-preview"></a>Defender for IoT マイクロ エージェントをインストールする (プレビュー)

この記事では、Defender マイクロ エージェントをインストールして認証する方法について説明します。

## <a name="prerequisites"></a>前提条件

Defender for IoT モジュールをインストールする前に、IoT Hub にモジュール ID を作成する必要があります。 モジュール ID の作成方法の詳細については、[Defender for IoT マイクロ エージェントのモジュール ツインを作成する方法 (プレビュー)](quickstart-create-micro-agent-module-twin.md) に関するページを参照してください。

## <a name="install-the-package"></a>パッケージをインストールする

[これらの手順](/windows-server/administration/linux-package-repository-for-microsoft-software)に従って、Microsoft パッケージ リポジトリをインストールし、構成します。 

Debian 9 の場合は、追加する必要があるリポジトリが手順に含まれていません。以下のコマンドを使用して、リポジトリを追加してください。 

```azurecli
curl -sSL https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add - 

sudo apt-get install software-properties-common

sudo apt-add-repository https://packages.microsoft.com/debian/9/multiarch/prod

sudo apt-get update
```

Debian および Ubuntu ベースの Linux ディストリビューションに Defender マイクロ エージェント パッケージをインストールするには、次のコマンドを使用します。

```azurecli
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>マイクロ エージェントの認証方法 

Defender for IoT マイクロ エージェントを認証するために使用される 2 つのオプションは、次のとおりです。 

- モジュール ID の接続文字列。 

- 証明書。

### <a name="authenticate-using-a-module-identity-connection-string"></a>モジュール ID の接続文字列を使用した認証

この記事の「[前提条件](#prerequisites)」が満たされていることを確認し、これらの手順を開始する前にモジュール ID を作成してください。 

#### <a name="get-the-module-identity-connection-string"></a>モジュール ID の接続文字列を取得する

IoT Hub からモジュール ID の接続文字列を取得するには、次のようにします。 

1. IoT Hub に移動し、お使いのハブを選択します。

1. 左側のメニューの **[エクスプローラー]** セクションで、 **[IoT デバイス]** を選択します。

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/iot-devices.png" alt-text="左側のメニューから [IoT デバイス] を選択する。":::

1. デバイス ID の一覧からデバイスを選択すると、 **[デバイスの詳細]** ページが表示されます。

1.  **[モジュール ID]**   タブを選択し、デバイスに関連付けられているモジュール ID の一覧から  **DefenderIotMicroAgent**  モジュールを選択します。

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/module-identities.png" alt-text="[モジュール ID] タブを選択する。":::

1. **[モジュール ID の詳細]** ページで、 **[コピー]** ボタンを選択して主キーをコピーします。

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/copy-button.png" alt-text="[コピー] ボタンを選択して主キーをコピーする。":::

#### <a name="configure-authentication-using-a-module-identity-connection-string"></a>モジュール ID の接続文字列を使用して認証を構成する

モジュール ID の接続文字列を使用して認証するようにエージェントを構成するには、次のようにします。

1. 次のコマンドを入力して、utf-8 でエンコードされた接続文字列を含む `connection_string.txt` という名前のファイルを defender エージェント ディレクトリの `/var/defender_iot_micro_agent` パスに配置します。

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    `connection_string.txt` は、`/var/defender_iot_micro_agent/connection_string.txt` というパスの場所に配置する必要があります。

1. このコマンドを使用して、サービスを再起動します。  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>証明書を使用して認証する

証明書を使用して認証するには、次の手順を実行します。

1. [これらの手順](../iot-hub/iot-hub-security-x509-get-started.md)に従って、証明書を入手します。

1. PEM でエンコードされた証明書の公開部分と秘密キーを、Defender エージェント ディレクトリの `certificate_public.pem` および `certificate_private.pem` という名前のファイル内に配置します。 

1. 適切な接続文字列を `connection_string.txt` ファイル内に配置します。 接続文字列は次のようになります。 

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true` 

    この文字列は、認証用に証明書が指定されることを想定するように、defender エージェントに警告します。 

1. 次のコマンドを使用して、サービスを再起動します。  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>インストールを検証する

インストールを検証するには、次の手順を実行します。

1. 次のコマンドを使用して、マイクロ エージェントが正常に実行されていることを確認します。  

    ```azurecli
    systemctl status defender-iot-micro-agent.service
    ```
1. サービスが安定していることを確認するには、サービスが `active` であり、プロセスの稼働時間が適切であることを確認します

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="サービスが安定していてアクティブであることを確認します。":::
 
## <a name="testing-the-system-end-to-end"></a>システムをエンド ツー エンドでテストする 

デバイスにトリガー ファイルを作成することによって、システムをエンド ツー エンドでテストできます。 トリガー ファイルによってエージェントでベースライン スキャンが行われ、ファイルがベースライン違反として検出されます。 

次のコマンドを使用して、ファイル システムにファイルを作成します。

```azurecli
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```
ハブでベースライン検証エラーの推奨が発生します (CIS-debian-9-DEFENDER_FOR_IOT_TEST_CHECKS-0.0 の `CceId`)。 

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="ハブで発生するベースライン検証エラーの推奨。" lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

ハブに推奨が表示されるまでに最大 1 時間かかります。 

## <a name="micro-agent-versioning"></a>マイクロ エージェントのバージョン管理 

特定のバージョンの Defender IoT マイクロ エージェントをインストールするには、次のコマンドを実行します。 

```azurecli
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>次の手順

[ソース コードから Defender マイクロ エージェントをビルドする](quickstart-building-the-defender-micro-agent-from-source.md)
---
title: チュートリアル - IoT Edge デバイスの階層を作成する - Azure IoT Edge
description: このチュートリアルでは、ゲートウェイを使用する IoT Edge デバイスの階層構造を作成する方法について説明します。
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: c1b30a1eafe9af92c1ef3f81773d213ccf96555c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462033"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>チュートリアル:IoT Edge デバイスの階層を作成する (プレビュー)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

階層レイヤーに編成された複数のネットワークに Azure IoT Edge ノードをデプロイします。 階層内の各レイヤーは、その下のレイヤーにあるデバイスからのメッセージと要求を処理するゲートウェイ デバイスです。

>[!NOTE]
>この機能には、Linux コンテナーを実行している IoT Edge バージョン 1.2 (パブリック プレビュー段階) が必要です。

最上位レイヤーだけがクラウドに接続できるように、デバイスの階層構造を構築できます。下位レイヤーは、その上下に隣接するレイヤーとだけ通信できます。 このネットワーク レイヤー化は、ほとんどの産業用ネットワークの基礎であり、[ISA-95 標準](https://en.wikipedia.org/wiki/ANSI/ISA-95)に準拠しています。

このチュートリアルの目的は、運用環境をシミュレートする IoT Edge デバイスの階層を作成することです。 最後には、階層を通じてコンテナー イメージをダウンロードし、[シミュレートされた温度センサー モジュール](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor)を、インターネットにアクセスできない下位レイヤー デバイスにデプロイします。

この目標を達成するために、このチュートリアルでは、IoT Edge デバイスの階層を作成し、IoT Edge ランタイム コンテナーをデバイスにデプロイして、デバイスをローカルで構成する手順について説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * IoT Edge デバイスの階層でリレーションシップを作成して定義します。
> * 階層内のデバイスで IoT Edge ランタイムを構成します。
> * デバイス階層全体で一貫した証明書をインストールします。
> * 階層内のデバイスにワークロードを追加します。
> * [IoT Edge API プロキシ モジュール](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview)を使用して、下位レイヤー デバイスから単一のポートを介して HTTP トラフィックを安全にルーティングします。

このチュートリアルでは、以下のネットワーク レイヤーを定義します。

* **最上位レイヤー**: このレイヤーの IoT Edge デバイスは、クラウドに直接接続できます。

* **下位レイヤー**: 最上位レイヤーより下にあるレイヤーの IoT Edge デバイスは、クラウドに直接接続できません。 データを送受信するには、1 つまたは複数の中間 IoT Edge デバイスを経由する必要があります。

このチュートリアルでは、簡潔にするため 2 つのデバイスから成る階層を使用します (下図参照)。 1 つのデバイス (**最上位レイヤー デバイス**) は、階層の最上位レイヤーにあるデバイスを表します。これは、クラウドに直接接続できます。 このデバイスは、**親デバイス** とも呼ばれます。 もう 1 つのデバイス (**下位レイヤー デバイス**) は、階層の下位レイヤーにあるデバイスを表します。これは、クラウドに直接接続できません。 必要に応じて、さらに下位レイヤー デバイスを追加して運用環境を表すことができます。 下位レイヤーのデバイスは、**子デバイス** とも呼ばれます。 追加の下位レイヤー デバイスの構成は、**下位レイヤー デバイス** の構成に従います。

![2 つのデバイス (最上位レイヤー デバイスと下位レイヤー デバイス) が含まれるチュートリアルの階層の構造](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>前提条件

IoT Edge デバイスの階層を作成するには、以下のものが必要です。

* インターネットに接続できるコンピューター (Windows または Linux)。
* 有効なサブスクリプションがある Azure アカウント。 [Azure サブスクリプション](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)をお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。
* Azure IoT 拡張機能 v0.10.6 以降がインストールされている Azure CLI v2.3.1。 このチュートリアルでは、[Azure Cloud Shell](../cloud-shell/overview.md) を使用します。 Azure Cloud Shell に馴染みのない方は、[クイックスタートで詳細を確認](./quickstart-linux.md#prerequisites)してください。
  * Azure CLI モジュールと拡張機能の現在のバージョンを確認するには、[az version](/cli/azure/reference-index?#az_version) を実行します。
* 階層内の各デバイスの IoT Edge デバイスとして構成する Linux デバイス。 このチュートリアルでは 2 つのデバイスを使用します。 使用できるデバイスがない場合は、次のコマンドのプレースホルダー テキストを置き換えて実行することにより、階層内の各デバイス用に Azure 仮想マシンを作成できます。

   ```azurecli-interactive
   az vm create \
    --resource-group <REPLACE_WITH_RESOURCE_GROUP> \
    --name <REPLACE_WITH_UNIQUE_NAMES_FOR_EACH_VM> \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password <REPLACE_WITH_PASSWORD>
   ```

* 8000、443、5671、8883 の各インバウンド ポートが開放されていること。
  * 8000: API プロキシを介して Docker コンテナー イメージをプルする際に使用されます。
  * 443:親エッジ ハブと子エッジ ハブ間で REST API 呼び出しに使用されます。
  * 5671、8883: AMQP と MQTT に使用されます。

  詳細については、「[Azure portal を使用して仮想マシンへのポートを開く方法](../virtual-machines/windows/nsg-quickstart-portal.md)」を参照してください。

>[!TIP]
>IoT Edge デバイスの階層が自動的に設定される様子を確認したい場合は、スクリプト化された[工業用 IoT 向け Azure IoT Edge サンプル](https://aka.ms/iotedge-nested-sample)に従ってください。 このスクリプト化されたシナリオでは、工場環境をシミュレートするための構成済みのデバイスとして Azure 仮想マシンがデプロイされます。
>
>サンプル階層の作成手順を進めたい場合は、以下のチュートリアルの手順を続けてください。

## <a name="configure-your-iot-edge-device-hierarchy"></a>IoT Edge デバイス階層を構成する

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>IoT Edge デバイスの階層を作成する

IoT Edge デバイスにより、階層のレイヤーが構成されます。 このチュートリアルでは、2 つの IoT Edge デバイス (**最上位レイヤー デバイス** とその子の **下位レイヤー デバイス**) の階層を作成します。 必要に応じて、追加の子デバイスを作成できます。

IoT Edge デバイスを作成するには、Azure portal または Azure CLI を使用できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://ms.portal.azure.com/) で、お使いの IoT Hub に移動します。

1. 左側のペインのメニューで、 **[デバイスの自動管理]** の下にある **[IoT Edge]** を選択します。

1. **[+ IoT Edge デバイスの追加]** を選択します。 このデバイスは最上位レイヤーのデバイスであるため、それに適した一意のデバイス ID を入力してください。 **[保存]** を選択します。

1. もう一度、 **[+ IoT Edge デバイスの追加]** を選択します。 このデバイスは下位レイヤー デバイスなので、それに適した一意のデバイス ID を入力します。

1. **[親デバイスの設定]** を選択し、デバイスの一覧で最上位レイヤーのデバイスを選択して、 **[OK]** を選択します。 **[保存]** を選択します。

   ![下位レイヤー デバイスの親を設定する](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/) で次のコマンドを入力して、ハブに IoT Edge デバイスを作成します。 このデバイスは最上位レイヤー デバイスであるため、それに適した一意のデバイス ID を入力してください。

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

   デバイスが正常に作成されると、デバイスの JSON 構成が出力されます。

   ![成功したデバイス作成の JSON 出力](./media/tutorial-nested-iot-edge/json-success-output.png)

1. 次のコマンドを入力して、下位レイヤーの IoT Edge デバイスを作成します。 階層にさらに多くのレイヤーが必要な場合は、複数の下位レイヤー デバイスを作成できます。 それぞれに一意のデバイス ID を指定してください。

   ```azurecli-interactive
   az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. 次のコマンドを入力して、**最上位レイヤー デバイス** と **下位レイヤー デバイス** の間の親子関係を定義します。 このコマンドを、階層内の下位レイヤー デバイスごとに実行してください。

   ```azurecli-interactive
   az iot hub device-identity parent set --device-id {lower_layer_device_id} --parent-device-id {top_layer_device_id} --hub-name {hub_name}
   ```

   このコマンドには明示的な出力はありません。

---

次に、各 IoT Edge デバイスの接続文字列を記録しておきます。 これらは後で使用します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://ms.portal.azure.com/) で、IoT Hub の **[IoT Edge]** セクションに移動します。

1. デバイスの一覧で、いずれかのデバイスのデバイス ID をクリックします。

1. **[プライマリ接続文字列]** フィールドの **[コピー]** を選択し、任意の場所に記録しておきます。

1. 他のすべてのデバイスに対して、手順を繰り返します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/) で、各デバイスについて次のコマンドを入力してデバイスの接続文字列を取得し、任意の場所に記録しておきます。

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id {device_id} --hub-name {hub_name}
   ```

---

上記の手順を正しく完了している場合は、次の手順を使用して、Azure portal または Azure CLI で親子関係が正しいことを確認できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://ms.portal.azure.com/) で、IoT Hub の **[IoT Edge]** セクションに移動します。

1. デバイスの一覧で、**下位レイヤー デバイス** のデバイス ID をクリックします。

1. デバイスの詳細ページで、 **[親デバイス]** フィールドの横に **最上位レイヤー デバイス** の ID が表示されるはずです。

   [子デバイスによって認識された親デバイス](./media/tutorial-nested-iot-edge/lower-layer-device-parent.png)

**最上位レイヤー デバイス** から階層関係の子を指定することもできます。

1. デバイスの一覧で、**最上位レイヤー デバイス** のデバイス ID をクリックします。

1. 上部にある **[デバイスの管理]** タブを選択します。

1. デバイスの一覧に、**下位レイヤー デバイス** が表示されます。

   [親デバイスによって認識された子デバイス](./media/tutorial-nested-iot-edge/top-layer-device-child.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/) では、子デバイスで認識されている親デバイスの ID を取得することにより、子デバイスと親デバイスの関係が正常に確立されていることを確認できます。 以下からは、上で示した親デバイスの JSON 構成が出力されます。

   ```azurecli-interactive
   az iot hub device-identity parent show --device-id {lower_layer_device_id} --hub-name {hub_name}
   ```

**最上位レイヤー デバイス** のクエリを実行して、階層関係の子を確認することもできます。

1. 親デバイスで認識されている子デバイスの一覧を表示します。

    ```azurecli-interactive
    az iot hub device-identity children list --device-id {top_layer_device_id} --hub-name {hub_name}
    ```

---

階層が正しく構成されたら、先に進むことができます。

### <a name="create-certificates"></a>証明書の作成

[ゲートウェイのシナリオ](iot-edge-as-gateway.md)のデバイスにはいずれも、それらの間にセキュリティで保護された接続を設定するための共通の証明書が必要です。 次の手順に従って、このシナリオの両方のデバイスにデモ証明書を作成します。

Linux デバイスでデモ証明書を作成するには、生成スクリプトを複製し、bash でローカルに実行されるようにスクリプトを設定する必要があります。

1. デモ用の証明書を生成するスクリプトが含まれている IoT Edge git リポジトリを複製します。

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

1. 作業するディレクトリに移動します。 すべての証明書ファイルとキー ファイルがこのディレクトリに作成されます。

1. 構成ファイルとスクリプト ファイルを、複製した IoT Edge リポジトリから作業ディレクトリにコピーします。

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

1. ルート CA 証明書と 1 つの中間証明書を作成します。

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   このスクリプト コマンドではいくつかの証明書ファイルとキー ファイルが作成されますが、ここでは次のファイルを **ゲートウェイ階層のルート CA 証明書** として使用しています。

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

1. 次のコマンドを使用して、2 セットの IoT Edge デバイス CA 証明書と秘密キーを作成します。1 つのセットは最上位レイヤー デバイス用で、もう 1 つのセットは下位レイヤー デバイス用です。 それぞれを区別するために、CA 証明書に覚えやすい名前を指定します。

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "{top_layer_certificate_name}"
   ./certGen.sh create_edge_device_ca_certificate "{lower_layer_certificate_name}"
   ```

   このスクリプト コマンドによりいくつかの証明書ファイルとキー ファイルが作成されますが、ここでは次の証明書とキーのペアを各 IoT Edge デバイスで使用し、構成ファイルで参照します。

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

各デバイスには、ルート CA 証明書のコピーと、独自のデバイス CA 証明書および秘密キーのコピーが必要です。 証明書を各デバイスに移動するには、USB ドライブか、[セキュア ファイル コピー](https://www.ssh.com/ssh/scp/)を使用できます。

1. 証明書を転送した後、各デバイスのルート CA をインストールします。

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   このコマンドを実行すると、`/etc/ssl/certs` に証明書が 1 つ追加されたことが出力されます。

   [証明書インストール成功のメッセージ](./media/tutorial-nested-iot-edge/certificates-success-output.png)

上記の手順を正しく完了した場合は、`/etc/ssl/certs` に移動し、インストールされている証明書を検索することにより、証明書がそのディレクトリにインストールされていることを確認できます。

1. 次のように `/etc/ssl/certs` に移動します。

   ```bash
   cd /etc/ssl/certs
   ```

1. インストールされている証明書の一覧を取得し、`azure` で `grep` します。

   ```bash
   ll | grep azure
   ```

   ルート CA 証明書にリンクされている証明書ハッシュと、`usr/local/share` ディレクトリ内のコピーにリンクされているルート CA 証明書が表示されます。

   [Azure 証明書の検索結果](./media/tutorial-nested-iot-edge/certificate-list-results.png)

各デバイスへの証明書のインストールに問題がなければ、先に進むことができます。

### <a name="install-iot-edge-on-the-devices"></a>デバイスに IoT Edge をインストールする

IoT Edge バージョン 1.2 のランタイム イメージをインストールすると、デバイスの階層として動作するために必要な機能に、デバイスからアクセスできます。

IoT Edge をインストールするには、適切なリポジトリ構成をインストールし、前提条件をインストールして、必要なリリース アセットをインストールする必要があります。

1. デバイスのオペレーティング システムに対応するリポジトリ構成をインストールします。

   * **Ubuntu Server 18.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspberry Pi OS Stretch**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

1. 生成されたリストを sources.list.d ディレクトリにコピーします。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Microsoft GPG 公開キーをインストールします。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

1. デバイスのパッケージ リストを更新します。

   ```bash
   sudo apt-get update
   ```

1. Moby エンジンをインストールします。

   ```bash
   sudo apt-get install moby-engine
   ```

1. IoT Edge と IoT ID サービスをインストールします。

   ```bash
   sudo apt-get install aziot-edge
   ```

上記の手順を正しく完了した場合は、階層内の各デバイスで Azure IoT Edge 構成ファイル `/etc/aziot/config.toml` を更新するよう要求する Azure IoT Edge のバナー メッセージが表示されます。 その場合は、先に進むことができます。

### <a name="configure-the-iot-edge-runtime"></a>IoT Edge ランタイムを構成する

構成手順では、デバイスのプロビジョニングに加えて、前に作成した証明書を使用して、階層内のデバイス間に信頼された通信を確立します。 この手順では、階層のネットワーク構造の確立も開始します。 最上位レイヤー デバイスはインターネットに接続することができ、クラウドからランタイム用のイメージをプルできます。一方、下位レイヤー デバイスは、最上位レイヤー デバイス経由でルーティングされて、これらのイメージにアクセスします。

IoT Edge ランタイムを構成するには、構成ファイルのいくつかのコンポーネントを変更する必要があります。 **最上位レイヤー デバイス** と **下位レイヤー デバイス** では構成が若干異なるため、各ステップで編集している構成ファイルがどのデバイスのものかに注意してください。 デバイスの IoT Edge ランタイムの構成は次の 4 つの手順から成り、すべてを IoT Edge 構成ファイルの編集することによって行います。

* デバイスの接続文字列を構成ファイルに追加して、各デバイスを手動でプロビジョニングします。

* デバイス CA 証明書、デバイス CA 秘密キー、およびルート CA 証明書を参照するように構成ファイルを更新して、デバイスの証明書の設定を完了します。

* IoT Edge エージェントを使用してシステムのブートストラップを実行します。

* **最上位レイヤー** デバイスの **hostname** パラメーターを更新し、**下位レイヤー** デバイスの **hostname** パラメーターと **parent_hostname** パラメーターの両方を更新します。

これらの手順を完了し、IoT Edge サービスを再起動して、デバイスを構成します。

>[!TIP]
>Nano で構成ファイル内を移動するときは、**Ctrl + W** キーを使用して、ファイル内のキーワードを検索できます。

1. 各デバイスで、含まれているテンプレートに基づいて構成ファイルを作成します。

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml

1. On each device, open the IoT Edge configuration file.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. **最上位レイヤー** デバイスの場合は、**ホスト名** セクションを見つけます。 `hostname` パラメーターが含まれる行をコメント解除し、IoT Edge デバイスの完全修飾ドメイン名 (FQDN) または IP アドレスになるように、値を更新します。 いずれかの値を選択して、階層内のデバイス全体で一貫して使用します。

   ```toml
   hostname = "<device fqdn or IP>"
   ```

   >[!TIP]
   >クリップボードの内容を Nano に貼り付けるには、`Shift+Right Click` キーまたは `Shift+Insert` キーを押します。

1. **下位レイヤー** の IoT Edge デバイスの場合は、**親ホスト名** セクションを見つけます。 `parent_hostname` パラメーターが含まれる行をコメント解除し、親デバイスの FQDN または IP を指すように値を更新します。 親デバイスの **hostname** フィールドに入力した値と正確に同じ値を使用します。 **最上位レイヤー** の IoT Edge デバイスの場合は、このパラメーターをコメントのままにしておきます。

   ```toml
   parent_hostname = "<parent device fqdn or IP>"
   ```

   > [!NOTE]
   > 階層に下位レイヤーが複数ある場合は、*parent_hostname* フィールドをすぐ上のレイヤーにあるデバイスの FQDN に更新します。

1. ファイルの **信頼バンドル証明書** セクションを見つけます。 `trust_bundle_cert` パラメーターが含まれる行をコメント解除し、ゲートウェイ階層内のすべてのデバイスで共有されるルート CA 証明書へのファイル URI パスで値を更新します。

   ```toml
   trust_bundle_cert = "<root CA certificate>"
   ```

1. ファイルの **プロビジョニング** セクションを見つけます。 **Manual provisioning with connection string** (接続文字列を使用した手動プロビジョニング) の行をコメント解除します。 階層内のデバイスごとに、**connection_string** の値を自分の IoT Edge デバイスの接続文字列で更新します。

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string: "<Device connection string>"
   ```

1. **既定のエッジ エージェント** セクションを見つけます。

   * **最上位レイヤー** デバイスの場合は、edgeAgent イメージの値を、Azure Container Registry 内のモジュールのパブリック プレビュー バージョンに更新します。
   
     ```toml
     [agent.config]
     image = "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4"
     ```

   * **下位レイヤー** の各 IoT Edge デバイスについては、edgeAgent イメージを、親デバイスの後に API プロキシがリッスンしているポートを付加したものを指すように更新します。 次のセクションでは、API プロキシ モジュールを親デバイスにデプロイします。
   
     ```toml
     [agent.config]
     image = "<parent hostname value>:8000/azureiotedge-agent:1.2.0-rc4"
     ```

1. **エッジ CA 証明書** セクションを見つけます。 このセクションの最初の 3 行をコメント解除します。 次に、2 つのパラメーターを、前のセクションで作成して IoT Edge デバイスに移動したデバイス CA 証明書とデバイス CA 秘密キーのファイルを指すように更新します。 ファイルの URI パスを `file:///<path>/<filename>` という形式で指定します (例: `file:///certs/iot-edge-device-ca-top-layer-device.key.pem`)。

   ```yml
   [edge_ca]
   cert = "<File URI path to the device full chain CA certificate unique to this device.>"
   pk = "<File URI path to the device CA private key unique to this device.>"
   ```

   >[!NOTE]
   >`device_ca_cert` フィールドを設定するには、**完全なチェーン** の CA 証明書パスとファイル名を必ず使用してください。

1. ファイルを保存して閉じます。

   `CTRL + X`, `Y`, `Enter`

1. 構成ファイルにプロビジョニング情報を入力した後、変更を適用します。

   ```bash
   sudo iotedge config apply
   ```

続ける前に、階層内の各デバイスの構成ファイルを更新したことを確認します。 階層の構造に応じて、1 つの **最上位レイヤー デバイス** と、1 つ以上の **下位レイヤー デバイス** を構成しました。

上記の手順を正しく完了した場合は、デバイスが正しく構成されていることを確認できます。

1. デバイスで構成と接続の確認を実行します。

   ```bash
   sudo iotedge check
   ```

**最上位レイヤー デバイス** の場合、出力には複数の合格した評価と、少なくとも 1 つの警告が表示されることが予想されます。 `latest security daemon` のチェックでは、IoT Edge バージョン 1.2 はパブリック プレビューであるため、IoT Edge の別のバージョンが最新の安定したバージョンであることが警告されます。 ログ ポリシーと、ネットワークによっては DNS ポリシーに関する、他の警告が表示される場合もあります。

**下位レイヤー デバイス** の場合、最上位レイヤー デバイスと似た出力が表示されることが予想されますが、EdgeAgent モジュールを上流からプルできないことを示す追加の警告が表示されます。 下位レイヤー デバイスによるイメージのプルに使用される IoT Edge API プロキシ モジュールと Docker コンテナー レジストリ モジュールは、**最上位レイヤー デバイス** にまだデプロイされていないため、これは許容されます。

`iotedge check` のサンプル出力は次のようになります。

[構成と接続の結果の例](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png)

各デバイスの構成が正しいことを確認したら、続行することができます。

## <a name="deploy-modules-to-the-top-layer-device"></a>最上位レイヤーのデバイスにモジュールをデプロイする

モジュールにより、デプロイが完了され、デバイスに IoT Edge ランタイムが提供されて、階層の構造がさらに定義されます。 IoT Edge API プロキシ モジュールにより、下位レイヤー デバイスから単一のポートを介して HTTP トラフィックが安全にルーティングされます。 Docker レジストリ モジュールは、イメージ プルを最上位レイヤー デバイスにルーティングすることで下位レイヤー デバイスがアクセスできる Docker イメージのリポジトリになります。

最上位レイヤー デバイスにモジュールをデプロイするには、Azure portal または Azure CLI を使用できます。

>[!NOTE]
>IoT Edge ランタイムの構成を完了し、ワークロードをデプロイする残りの手順は、IoT Edge デバイスでは行いません。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

[Azure ポータル](https://ms.portal.azure.com/)で次の操作を行います。

1. IoT Hub に移動します。

1. 左側のペインのメニューで、 **[デバイスの自動管理]** の下にある **[IoT Edge]** を選択します。

1. デバイスの一覧で、**最上位レイヤー** のエッジ デバイスのデバイス ID をクリックします。

1. 上部のバーで **[モジュールの設定]** を選択します。

1. 歯車アイコンの横の **[ランタイムの設定]** を選択します。

1. **[Edge Hub]** の下の [イメージ] フィールドに、「`mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4`」と入力します。

   ![Edge Hub のイメージを編集する](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Edge Hub モジュールに、以下の環境変数を追加します。

    | 名前 | [値] |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Edge Hub の環境変数を編集する](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. **[Edge エージェント]** の下の [イメージ] フィールドに、「`mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4`」と入力します。 **[保存]** を選択します。

1. 最上位レイヤー デバイスに Docker レジストリ モジュールを追加します。 **[+ 追加]** を選択し、ドロップダウンで **[IoT Edge モジュール]** を選択します。 Docker レジストリ モジュールに名前 `registry` を指定し、イメージ URI に「`registry:latest`」と入力します。 次に、環境変数を追加し、Microsoft コンテナー レジストリのローカル レジストリ モジュールを参照するオプションを作成します。そこからコンテナー イメージがダウンロードされ、それらのイメージが registry:5000 で提供されます。

1. 環境変数のタブで、次の環境変数の名前と値のペアを入力します。

    | 名前 | [値] |
    | - | - |
    | `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

1. コンテナーの作成オプションのタブで、次の JSON を入力します。

   ```json
   {
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
         }
      }
   }
   ```

1. 次に、最上位レイヤー デバイスに API プロキシ モジュールを追加します。 **[+ 追加]** を選択し、ドロップダウンで **[Marketplace モジュール]** を選択します。 `IoT Edge API Proxy` を検索し、モジュールを選択します。 IoT Edge API プロキシはポート 8000 を使用し、既定ではポート 5000 で `registry` という名前のレジストリ モジュールを使用するように構成されています。

1. **[確認および作成]** 、 **[作成]** の順に選択して、デプロイを完了します。 インターネットにアクセスできる最上位レイヤー デバイスの IoT Edge ランタイムは、IoT Edge ハブおよび IoT Edge エージェント用の **パブリック プレビュー** 構成をプルして実行します。

   ![エッジ ハブ、エッジ エージェント、レジストリ モジュール、および API プロキシ モジュールを含むデプロイを完了する](./media/tutorial-nested-iot-edge/complete-top-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/) で次のコマンドを入力して、deployment.json ファイルを作成します。

   ```azurecli-interactive
   code deploymentTopLayer.json
   ```

1. 以下の JSON の内容を deployment.json にコピーし、保存して閉じます。

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "registry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
                               } 
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\":\"8000\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "8000"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               },
                               "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                   "value": "AzureBlobStorageonIoTEdge:11002"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. 次のコマンドを入力して、最上位レイヤーのエッジ デバイスへのデプロイを作成します。

   ```azurecli-interactive
   az iot edge set-modules --device-id <top_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentTopLayer.json
   ```

---

上記の手順を正しく完了した場合、**最上位レイヤー デバイス** からは、IoT Edge API プロキシ モジュール、Docker コンテナー レジストリ モジュール、および **[デプロイで指定]** のシステム モジュールの、4 つのモジュールが報告されるはずです。 デバイスが新しいデプロイを受け取り、モジュールが開始されるまで、数分かかる場合があります。 **[デバイス別に報告]** として温度センサー モジュールが表示されるまで、ページを更新します。 デバイスによってモジュールが報告されたら、続行できる状態になります。

## <a name="deploy-modules-to-the-lower-layer-device"></a>下位レイヤーのデバイスにモジュールをデプロイする

モジュールは、下位レイヤー デバイスのワークロードとしても機能します。 シミュレートされた温度センサー モジュールにより、デバイスの階層を通過するデータの機能フローを提供するためのテレメトリ データのサンプルが作成されます。

下位レイヤー デバイスにモジュールをデプロイするには、Azure portal または Azure CLI を使用できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

[Azure ポータル](https://ms.portal.azure.com/)で次の操作を行います。

1. IoT Hub に移動します。

1. 左側のペインのメニューで、 **[デバイスの自動管理]** の下にある **[IoT Edge]** を選択します。

1. IoT Edge デバイスの一覧で、下位レイヤーのデバイスのデバイス ID をクリックします。

1. 上部のバーで **[モジュールの設定]** を選択します。

1. 歯車アイコンの横の **[ランタイムの設定]** を選択します。

1. **[Edge Hub]** の下の [イメージ] フィールドに、「`$upstream:8000/azureiotedge-hub:1.2.0-rc4`」と入力します。

1. Edge Hub モジュールに、以下の環境変数を追加します。

    | 名前 | [値] |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. **[Edge エージェント]** の下の [イメージ] フィールドに、「`$upstream:8000/azureiotedge-agent:1.2.0-rc4`」と入力します。 **[保存]** を選択します。

1. 温度センサー モジュールを追加します。 **[+ 追加]** を選択し、ドロップダウンで **[Marketplace モジュール]** を選択します。 `Simulated Temperature Sensor` を検索し、モジュールを選択します。

1. **[IoT Edge モジュール]** で、先ほど追加した `Simulated Temperature Sensor` モジュールを選択し、そのイメージ URI を、`$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0` を参照するように更新します。

1. **[保存]** 、 **[確認および作成]** 、 **[作成]** の順に選択し、デプロイを完了します。

   ![エッジ ハブ、エッジ エージェント、およびシミュレートされた温度センサーを含むデプロイを完了する](./media/tutorial-nested-iot-edge/complete-lower-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/) で次のコマンドを入力して、deployment.json ファイルを作成します。

   ```azurecli-interactive
   code deploymentLowerLayer.json
   ```

1. 以下の JSON の内容を deployment.json にコピーし、保存して閉じます。

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "simulatedTemperatureSensor": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0",
                               "createOptions": ""
                           },
                           "type": "docker",
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc4",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. 次のコマンドを入力して、下位レイヤーのエッジ デバイスへのモジュールの設定デプロイを作成します。

   ```azurecli-interactive
   az iot edge set-modules --device-id <lower_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentLowerLayer.json

---

Notice that the image URI that we used for the simulated temperature sensor module pointed to `$upstream:8000` instead of to a container registry. We configured this device to not have direct connections to the cloud, because it's in a lower layer. To pull container images, this device requests the image from its parent device instead. At the top layer, the API proxy module routes this container request to the registry module, which handles the image pull.

If you completed the above steps correctly, your **lower layer device** should report three modules: the temperature sensor module and the system modules, as **Specified in Deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by Device**. Once the modules are reported by the device, you are ready to continue.

## Troubleshooting

Run the `iotedge check` command to verify the configuration and to troubleshoot errors.

You can run `iotedge check` in a nested hierarchy, even if the child machines don't have direct internet access.

When you run `iotedge check` from the lower layer, the program tries to pull the image from the parent through port 443.

In this tutorial, we use port 8000, so we need to specify it:

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2.0-rc4
```

`azureiotedge-diagnostics` の値は、レジストリ モジュールにリンクされたコンテナー レジストリからプルされます。 このチュートリアルでは、既定値の https://mcr.microsoft.com: に設定しています。

| 名前 | [値] |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

プライベート コンテナー レジストリを使用する場合は、コンテナー レジストリにすべてのイメージ (IoTEdgeAPIProxy、edgeAgent、edgeHub、diagnostics など) が存在することを確認してください。

## <a name="view-generated-data"></a>生成されたデータを表示する

プッシュした **Simulated Temperature Sensor** モジュールは、サンプル環境データを生成します。 送信されるメッセージには、周囲の温度と湿度、機械の温度と圧力、タイムスタンプが含まれます。

[Visual Studio Code 用の Azure IoT Hub の拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)を使用して、IoT ハブに到着したメッセージを監視することができます。

それらのメッセージを [Azure Cloud Shell](https://shell.azure.com/) から確認することもできます。

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除できます。

リソースを削除するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインし、 **[リソース グループ]** を選択します。

2. IoT Edge のテスト リソースを含んだリソース グループの名前を選択します。 

3. リソース グループに含まれるリソースの一覧を確認します。 それらすべてを削除する場合は、**[リソース グループの削除]** を選択します。 一部だけを削除する場合は、削除する各リソースをクリックして個別に削除してください。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、2 つの IoT Edge デバイスをゲートウェイとして構成し、一方をもう一方の親デバイスとして設定しました。 次に、ゲートウェイを介してコンテナー イメージを子デバイスにプルする方法を示しました。

Azure IoT Edge がビジネス ソリューションを作成する方法をさらに確認するには、他のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [モジュールとして Azure Machine Learning モデルをデプロイする](tutorial-deploy-machine-learning.md)

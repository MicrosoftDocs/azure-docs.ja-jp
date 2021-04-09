---
title: 新しいデバイスを登録する - Azure IoT Edge | Microsoft Docs
description: IoT Hub に 1 つの IoT Edge デバイスを登録して、対称キーまたは X.509 証明書を使用して手動でプロビジョニングする
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: d75f184a324a9d418b0af2e3cf5790205af0fa42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200715"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>IoT Edge デバイスを IoT Hubに登録する

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

この記事では、IoT Hub に新しい IoT Edge デバイスを登録する手順について説明します。

IoT ハブに接続するすべてのデバイスには、cloud-to-device または device-to-cloud の通信の追跡に使用されるデバイス ID があります。 デバイスをその接続情報で構成します。これには、IoT ハブ ホスト名、デバイス ID、IoT Hub に対して認証を行うためにデバイスで使用される情報が含まれます。

この記事の手順では、手動プロビジョニングと呼ばれるプロセスについて説明します。ここでは、1 つのデバイスを IoT ハブに接続します。 手動プロビジョニングの場合、IoT Edge デバイスの認証には 2 つのオプションがあります。

* **対称キー**: IoT Hub で新しいデバイス ID を作成すると、サービスによって 2 つのキーが作成されます。 いずれかのキーをデバイスに配置すると、認証時にそのキーが IoT Hub に提供されます。

  この認証方法は比較的すばやく開始できますが、それほど安全ではありません。

* **X.509 自己署名**: 2 つの X.509 ID 証明書を作成し、デバイスに配置します。 IoT Hub で新しいデバイス ID を作成するときは、両方の証明書の拇印を指定します。 デバイスでは IoT Hub に対して認証を行うときに、1 つの証明書が提示され、その証明書がその拇印と一致することが IoT によって検証されます。

  この認証方法はより安全であり、運用環境のシナリオの場合に推奨されます。

この記事では、両方の認証方法について説明します。

設定するデバイスが多数あり、それぞれを手動でプロビジョニングしたくない場合は、次の記事のいずれかを使用して、IoT Edge が IoT Hub Device Provisioning Service でどのように動作するかを確認してください。

* [X.509 証明書を使用して IoT Edge デバイスを作成およびプロビジョニングする](how-to-auto-provision-x509-certs.md)
* [TPM を使用して IoT Edge デバイスを作成およびプロビジョニングする](how-to-auto-provision-simulated-device-linux.md)
* [対称キーを使用して IoT Edge デバイスを作成およびプロビジョニングする](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>前提条件

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure サブスクリプション内の無料または標準の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Azure サブスクリプション内の無料または標準の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code 用 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Azure サブスクリプション内の無料または標準の [IoT ハブ](../iot-hub/iot-hub-create-using-cli.md)。
* ご使用の環境内の [Azure CLI](/cli/azure/install-azure-cli)。 Azure CLI のバージョンは、少なくとも 2.0.70 以降である必要があります。 検証するには、`az --version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。

---

## <a name="option-1-register-with-symmetric-keys"></a>オプション 1: 対称キーを使用した登録

IoT Hub に新しい IoT Edge デバイスを登録し、その接続文字列を取得するために、好みに応じていくつかのツールを使用できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal の IoT Hubで、IoT Edge デバイスは、Edge 対応ではない IoT デバイスとは別に作成および管理されます。

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。

1. 左側のペインで、メニューから **[IoT Edge]** を選択し、 **[IoT Edge デバイスを追加する]** を選択します。

   ![Azure portal から IoT Edge デバイスを追加する](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. **[デバイスの作成]** ページで、次の情報を指定します。

   * わかりやすいデバイス ID を作成します。
   * 認証の種類として **[対称キー]** を選択します。
   * 認証キーを自動生成して、新しいデバイスをハブに接続するために、既定の設定を使用します。

1. **[保存]** を選択します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>サインインして IoT ハブにアクセスする

Visual Studio Code 用の Azure IoT 拡張機能を使用して、IoT ハブで操作を実行できます。 これらの操作を機能させるには、Azure アカウントにサインインし、ハブを選択する必要があります。

1. Visual Studio Code で **エクスプローラー** ビューを開きます。
1. エクスプローラーの下部で、 **[Azure IoT Hub]** セクションを展開します。

   ![[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\) セクションを展開する](./media/how-to-register-device/azure-iot-hub-devices.png)

1. **[Azure IoT Hub]** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをクリックするかポイントします。
1. **[Select IoT Hub]\(IoT ハブの選択\)** を選択します。
1. Azure アカウントにサインインしていない場合は、プロンプトに従ってサインインします。
1. Azure サブスクリプションを選択します。
1. IoT ハブを選択します。

### <a name="register-a-new-device-with-visual-studio-code"></a>Visual Studio Code を使用して新しいデバイスを登録する

1. Visual Studio Code エクスプローラーで、 **[Azure IoT Hub]** セクションを展開します。
1. **[Azure IoT Hub]** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをクリックするかポイントします。
1. **[Create IoT Edge Device]\(IoT Edge デバイスの作成\)** を選択します。
1. 表示されたテキスト ボックスにデバイス ID を指定します。

コマンドの結果が出力画面に表示されます。 デバイスの情報が出力されます。この情報には、指定した **deviceId** および IoT ハブに物理デバイスを接続するために使用できる **connectionString** が含まれます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) コマンドを使用して、IoT ハブに新しいデバイス ID を作成します。 次に例を示します。

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

このコマンドには、3 つのパラメーターが含まれます。

* `--device-id` または `-d`: IoT ハブ内で一意のわかりやすい名前を指定します。
* `--hub-name` または `-n`: IoT ハブの名前を指定します。
* `--edge-enabled` または `--ee`:デバイスが IoT Edge デバイスであることを宣言します。

   ![az iot hub device-identity create output](./media/how-to-register-device/create-edge-device-cli.png)

---

デバイスが IoT Hub に登録されたので、IoT Edge ランタイムのインストールとプロビジョニングを完了するために使用する接続文字列を取得します。 この記事で後述する手順に従って、[登録されたデバイスを表示し、接続文字列を取得](#view-registered-devices-and-retrieve-connection-strings)します。

## <a name="option-2-register-with-x509-certificates"></a>オプション 2:X.509 証明書を使用した登録

X.509 証明書を使用した手動プロビジョニングには、バージョン 1.0.10 以降の IoT Edge が必要です。

X.509 証明書認証の場合、各デバイスの認証情報は、デバイス ID 証明書から取得した "*拇印*" の形式で提供されます。 これらの拇印は、サービスが接続時にデバイスを認識できるように、デバイスの登録時に IoT Hub に付与されます。

### <a name="create-certificates-and-thumbprints"></a>証明書と拇印を作成する

X.509 証明書を使用して IoT Edge デバイスをプロビジョニングする場合は、"*デバイス ID 証明書*" と呼ばれるものを使用します。 この証明書は、IoT Edge デバイスのプロビジョニングと、Azure IoT Hub でのデバイス認証を行うためにのみ使用されます。 これは、他の証明書には署名しないリーフ証明書です。 デバイス ID 証明書は、IoT Edge デバイスによって検証のためにモジュールやダウンストリーム デバイスに提示される証明機関 (CA) 証明書とは別のものです。 IoT Edge デバイスでの CA 証明書の使用方法については、「[Azure IoT Edge での証明書の使用方法について理解する](iot-edge-certs.md)」を参照してください。

X.509 を使用して手動でプロビジョニングするには、次のファイルが必要です。

* 2 つのデバイス ID 証明書と、それに対応する .cer または pem 形式の秘密キー証明書。

  IoT Edge ランタイムには、証明書/キー ファイルのセットが 1 つ用意されています。 デバイス ID 証明書を作成するとき、IoT ハブ内のデバイスで使用するデバイス ID を使って証明書の共通名 (CN) を設定します。

* 両方のデバイス ID 証明書から取得された拇印。

  拇印の値は SHA-1 ハッシュの場合は 40 桁の 16 進数、SHA-256 ハッシュの場合は 64 桁の 16 進数です。 両方の拇印は、デバイスの登録時に IoT Hub に提供されます。

使用できる証明書がない場合は、[デモ証明書を作成して IoT Edge デバイスの機能をテスト](how-to-create-test-certificates.md)できます。 この記事に記載されている手順に従って、証明書作成スクリプトを設定し、ルート CA 証明書を作成してから、2 つの IoT Edge デバイス ID 証明書を作成します。

証明書から拇印を取得する方法の 1 つは、次の openssl コマンドを使用することです。

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>新しいデバイスを登録する

複数のツールを使用して IoT Hub に新しい IoT Edge デバイスを登録し、その証明書の拇印をアップロードできます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal の IoT Hubで、IoT Edge デバイスは、Edge 対応ではない IoT デバイスとは別に作成および管理されます。

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。

1. 左側のペインで、メニューから **[IoT Edge]** を選択し、 **[IoT Edge デバイスを追加する]** を選択します。

   ![Azure portal から IoT Edge デバイスを追加する](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. **[デバイスの作成]** ページで、次の情報を指定します。

   * わかりやすいデバイス ID を作成します。 このデバイス ID は、次のセクションで使用するのでメモしておいてください。
   * 認証の種類として **[X.509 自己署名済み]** を選択します。
   * プライマリとセカンダリの ID 証明書の拇印を指定します。 拇印の値は SHA-1 ハッシュの場合は 40 桁の 16 進数、SHA-256 ハッシュの場合は 64 桁の 16 進数です。

1. **[保存]** を選択します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

現在、Visual Studio Code 用の Azure IoT 拡張機能では、X.509 証明書を使用したデバイス登録はサポートされていません。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) コマンドを使用して、IoT ハブに新しいデバイス ID を作成します。 次に例を示します。

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

このコマンドには、いくつかのパラメーターが含まれています。

* `--device-id` または `-d`: IoT ハブに対して一意のわかりやすい名前を指定します。 このデバイス ID は、次のセクションで使用するのでメモしておいてください。
* `hub-name` または `-n`: IoT ハブの名前を指定します。
* `--edge-enabled` または `--ee`: デバイスが IoT Edge デバイスであることを宣言します。
* `--auth-method` または `--am`: デバイスが使用する承認の種類を宣言します。 この例では、X.509 証明書の拇印を使用しています。
* `--primary-thumbprint` または `--ptp`: 主キーとして使用する X.509 証明書の拇印を指定します。
* `--secondary-thumbprint` または `--stp`: セカンダリ キーとして使用する X.509 証明書の拇印を指定します。

---

これで IoT Hub にデバイス ID が登録されたので、IoT Edge ランタイムをデバイスにインストールしてプロビジョニングできます。 X.509 証明書を使用して認証する IoT Edge デバイスでは接続文字列は使用されないため、次の手順に進むことができます。

* [Azure IoT Edge for Linux をインストールまたはアンインストールする](how-to-install-iot-edge.md)
* [Azure IoT Edge for Windows をインストールまたはアンインストールする](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>登録済みデバイスを表示し、接続文字列を取得する

対称キー認証を使用するデバイスでは、IoT Edge ランタイムのインストールとプロビジョニングを完了するために接続文字列が必要です。

X.509 証明書認証を使用するデバイスでは、接続文字列は必要ありません。 代わりに、これらのデバイスでは、IoT Edge ランタイムのインストールとプロビジョニングを完了するために、IoT Hub 名、デバイス名、および証明書ファイルが必要です。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

IoT ハブに接続するすべてのエッジ対応デバイスが、**IoT Edge** ページに一覧表示されます。

![Azure portal を使用して IoT ハブ内の IoT Edge デバイスをすべて表示する](./media/how-to-register-device/portal-view-devices.png)

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。

対称キーを使用して認証を行うデバイスでは、接続文字列をポータルでコピーできます。

1. ポータルの **IoT Edge** ページで、IoT Edge デバイスの一覧からデバイス ID をクリックします。
2. **[プライマリ接続文字列]** または **[セカンダリ接続文字列]** のどちらかの値をコピーします。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Visual Studio Code を使用して IoT Edge デバイスを表示する

IoT ハブに接続するすべてのデバイスは、Visual Studio Code エクスプローラーの **[Azure IoT Hub]** セクションに表示されます。 IoT Edge デバイスとそれ以外のデバイスはアイコンで区別できるほか、各 IoT Edge デバイスには **$edgeAgent** モジュールと **$edgeHub** モジュールがデプロイされています。

![VS Code を使用して IoT ハブ内の IoT Edge デバイスをすべて表示する](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Visual Studio Code を使用して接続文字列を取得する

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。

1. **[Azure IoT Hub]** セクションでデバイスの ID を右クリックします。
1. **[Copy Device Connection String]\(デバイス接続文字列のコピー\)** を選択します。

   接続文字列がクリップボードにコピーされます。

右クリック メニューから **[Get Device Info]\(デバイス情報の取得\)** を選択して、接続文字列を含むすべてのデバイス情報を出力ウィンドウに表示することもできます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Azure CLI を使用して IoT Edge デバイスを表示する

IoT ハブのすべてのデバイスを表示するには、[az iot hub device-identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) コマンドを使用します。 次に例を示します。

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

IoT Edge デバイスとして登録されたすべてのデバイスのプロパティ **capabilities.iotEdge** は **true** に設定されます。

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Azure CLI を使用して接続文字列を取得する

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。 [az iot hub device-identity connection-string show](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) コマンドを使用すると、単一のデバイスの接続文字列が返されます。

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

>[!TIP]
>`connection-string show` コマンドは、Azure IoT 拡張機能のバージョン 0.9.8 で導入され、これによって、非推奨の `show-connection-string` コマンドが置き換えられました。 このコマンドの実行中にエラーが発生した場合は、拡張機能のバージョンが 0.9.8 以降に更新されていることを確認してください。 詳細情報と最新の更新プログラムについては、[Azure CLI 用の Microsoft Azure IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)に関するページを参照してください。

`device-id` パラメーターの値は、大文字と小文字が区別されます。

デバイスで使用する接続文字列をコピーする場合は、接続文字列を囲んでいる引用符を含めないでください。

---

## <a name="next-steps"></a>次のステップ

これで IoT Hub にデバイス ID が登録されたので、IoT Edge ランタイムをデバイスにインストールしてプロビジョニングできます。

* [Azure IoT Edge for Linux をインストールまたはアンインストールする](how-to-install-iot-edge.md)
* [Azure IoT Edge for Windows をインストールまたはアンインストールする](how-to-install-iot-edge-windows-on-windows.md)
---
title: Azure IoT Edge モジュールの技術アセットの準備 - Azure Marketplace
description: Azure Marketplace に発行する前に、モノのインターネット (IoT) Edge モジュールの技術アセットが満たす必要のある技術的要件と構成要件について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/03/2020
ms.openlocfilehash: aca27b89a3b92b410fa560c8b4bd7eb3d4e0a935
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93346776"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>IoT Edge モジュールの技術アセットの準備

この記事では、Azure Marketplace で公開する前にモノのインターネット (IoT) Edge モジュールの技術アセットが満たす必要のある要件について説明します。

## <a name="get-started"></a>はじめに

IoT Edge モジュールは、IoT Edge デバイスで実行する Docker 互換コンテナーです。

- IoT Edge モジュールの詳細については、[Azure IoT Edge モジュールを理解する](../../iot-edge/iot-edge-modules.md)を参照してください。
- IoT Edge モジュールの開発を開始するには、[独自の IoT Edge モジュールを開発する](../../iot-edge/module-development.md)を参照してください。

## <a name="technical-requirements"></a>技術的な要件

IoT Edge モジュールが認定され Azure Marketplace で公開できるようにするには、次の技術的な要件を満たす必要があります。

### <a name="platform-support"></a>プラットフォームのサポート

IoT Edge モジュールは、次のプラットフォーム オプションのいずれかをサポートする必要があります。

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>IoT Edge によってサポートされるレベル 1 プラットフォーム

IoT Edge でサポートされているすべてのレベル 1 プラットフォーム ([Azure IoT Edge サポート](../../iot-edge/support.md)に記録されているもの) をサポートしなくてはなりません。 より良いカスタマー エクスペリエンスを提供するため、このオプションをお勧めします。 この基準を満たすモジュールが紹介されます。 このプラットフォーム オプションを使用するモジュールは、以下のことが必要です。

- [GitHub Manifest-tool](https://github.com/estesp/manifest-tool) でビルドされたマニフェスト タグである最新のタグとバージョン タグ (1.0.1 など) を指定します。

- [パートナー センター](https://partner.microsoft.com/dashboard/commercial-marketplace)でオファーの一覧タブを使用し、「**有用なリンク**」セクションのリンクを [Azure IoT Edge 認定デバイス カタログ](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/)に追加します。

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>IoT Edge によってサポートされるレベル 1 プラットフォームのサブセット

お使いになっているモジュールは、IoT Edge でサポートされているレベル 1 のプラットフォーム ([Azure IoT Edge サポート](../../iot-edge/support.md)に記録されているもの) のサブセットを少なくとも 1 つサポートする必要があります。 このプラットフォーム オプションを使用するモジュールは、以下のことが必要です。

- 複数のプラットフォームがサポートされている場合は、GitHub の [manifest-tool](https://github.com/estesp/manifest-tool) でビルドされたマニフェスト タグである最新のタグとバージョン タグ (1.0.1 など) を指定します。 マニフェスト タグは、1 つのプラットフォームのみがサポートされている場合は省略できます。
- [パートナー センター](https://partner.microsoft.com/dashboard/commercial-marketplace)でオファーの一覧タブを使用し、「**有用なリンク**」セクションのリンクを [Azure IoT Edge 認定デバイス カタログ](https://catalog.azureiotsolutions.com/)から少なくとも 1 つの IoT Edge デバイスに追加します。

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="これは、パートナー センター内の「オファーの一覧」セクションのイメージです":::

### <a name="device-dimensions"></a>デバイスのサイズ

対象の IoT Edge デバイス上の IoT Edge モジュールのサイズ (CPU、RAM、ストレージ、GPU など) は、次の要件を満たす必要があります。

- モジュールは、[Azure IoT Edge 認定デバイス カタログ](https://catalog.azureiotsolutions.com/)から少なくとも 1 つの IoT Edge デバイスで動作する必要があります。

- ハードウェアの最低要件をオファーの説明の最後の段落に記載する必要があります ([パートナー センター](https://partner.microsoft.com/dashboard/commercial-marketplace)のオファーの一覧タブ)。 必要に応じて、推奨されるハードウェア要件が大幅に異なる場合は、要件を一覧にすることもできます。 たとえば、プランの説明の最後に次のセクションを追加します。

この HTML テキストをコピーするか、該当するリッチ テキスト関数を編集ウィンドウで使用します。

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>構成

モジュールには、IoT Edge デバイスへのデプロイをできるだけ簡単にするための既定の構成設定を含める必要があります。 この情報は、[パートナー センター](https://partner.microsoft.com/dashboard/commercial-marketplace)のプランの「**技術的構成**」ページで指定できます。 コンテナーには、edgeHub や IoT Hub と通信できるようにするため、IoT Edge モジュール SDK が含まれることもあります。

#### <a name="default-configuration"></a>既定の構成

IoT Edge モジュールは、[パートナー センター](https://partner.microsoft.com/dashboard/commercial-marketplace)の計画の「**技術的構成**」ページで指定された既定の設定で開始できなくてはなりません。 次の既定の設定を使用できます。

- 既定の **ルート**
- 既定の **モジュール ツインの必要なプロパティ**
- 既定の **環境変数**
- 既定の **コンテナー作成オプション**

既定値に必要なパラメーターでは意味をなさないシナリオでは (たとえば、顧客のサーバーの IP アドレスなど)、既定値としてパラメーターを追加します。 この値は大文字で表記し、角かっこで囲みます。 この例では、次の既定の環境変数を設定します。

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>構成に関するドキュメント

IoT Edge モジュールのすべての構成設定を明確に文書化する必要があります。 たとえば、ルート、ツインが必要なプロパティ、環境変数、createOptions などを使用する方法を文書化する必要があります。 ドキュメントへのリンクを指定するか、ドキュメントをオファーまたはプランの説明の一部に含める必要があります。 この情報は、[パートナー センター](https://partner.microsoft.com/dashboard/commercial-marketplace)の **オファーの一覧** と **計画の一覧** ページに記載できます。

#### <a name="tags-and-versioning"></a>タグとバージョン管理

お客様はモジュールを簡単にデプロイし、マーケットプレースから (開発者シナリオで) 更新プログラムを自動的に取得できなくてはなりません。 また、(運用環境シナリオで) テスト済みのバージョンを使用して凍結できる必要もあります。

お客様のこれらの期待に応え、マーケットプレースに公開できるようにするために、IoT Edge モジュールは次の要件を満たす必要があります

- サポートされているすべてのプラットフォームの最新バージョンを参照するマニフェストの最新タグを含めます。
- バージョン タグを X.Y.Z 形式にします (X、Y、Z は整数)。
- サポートされているすべてのプラットフォームで特定のバージョンを参照する「バージョン」タグ (1.0.1 など) を含めます。
- 「バージョン」タグ (1.0.1 など) は変更する必要がないため、更新しないでください。

> [!NOTE]
> 必要に応じて、2.0 や 1.0 といった「ローリング バージョン」タグを含めることができます。 これは、複数のメジャー バージョンの並列管理をサポートします。

### <a name="telemetry"></a>テレメトリ

IoT Module SDK を使用するモジュールは、テレメトリ目的で一意のモジュール識別子を PublisherId.OfferId.SkuId に設定する必要があります。 一意の識別子により、Azure Marketplace では、実行されているモジュール インスタンスの数を識別できます。

IoT Module SDK から次の方法のいずれかを使用して、ProductInfo をこの識別子に設定します。

- [C#](/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](/java/api/com.microsoft.azure.sdk.iot.device.productinfo)

IoT Module SDK を使用していないモジュールでは、ダウンロード数などパートナー センターを通して入手できる分析情報の精度が低くなります。

### <a name="security"></a>Security

IoT Edge モジュールは、[特権モジュール](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)を避ける必要があります。 その代わり、ホストに対する特権アクセスは最小限にするよう要請します。

### <a name="module-iot-sdk"></a>Module IoT SDK

Module IoT SDK の同梱は、認定の前提条件ではありません。 ただし、IoT モジュールの SDK を含めると、優れたユーザー エクスペリエンスを提供できる場合があります。 たとえば、ルーティングやクラウドへのメッセージ送信などをサポートできます。

IoT Module SDK は、実行中のモジュール インスタンスの数に関するテレメトリ データを取得するために必要です。

## <a name="recertification-process"></a>再認定プロセス

以下のようなモジュールに影響する破壊的変更があったときはパートナーに通知されます。

- IoT Edge でサポートされるレベル 1 の OS/arch サポート マトリックス
- IoT Module SDK
- IoT Edge ランタイム
- IoT Edge モジュール認定ガイドライン

パートナーは、[パートナー センター](https://partner.microsoft.com/dashboard/commercial-marketplace)で再公開することによって、オファーを更新して再認定する必要があります。

また、オファーを更新すると再認定されます (新しいイメージ タグの追加など)。

## <a name="host-module-in-azure-container-registry"></a>Azure Container Registry でモジュールをホストする

IoT Edge モジュールを Azure Marketplace にアップロードするには、まず、これを [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR) でホストする必要があります。 モジュールには、マニフェスト タグによって参照されるイメージ タグなど、発行するすべてのタグを含める必要があります。 詳細については、「[Azure コンテナー レジストリを作成してコンテナー イメージをプッシュする](../../container-instances/container-instances-tutorial-prepare-acr.md)」チュートリアルを参照してください。

## <a name="next-steps"></a>次のステップ

- [IoT Edge モジュール プランの作成](azure-iot-edge-module-creation.md)
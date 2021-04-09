---
title: Azure IoT device SDK プラットフォームのサポート | Microsoft Docs
description: C、.NET (C#)、Java、Node.js、Python のオープンソース デバイス SDK が GitHub で入手可能であり、デバイスを Azure IoT Hub や Device Provisioning Service (DPS) に接続するのに利用できます。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: b8f8403617a5fc8b442e8a3f74ab9226fe1479fe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97588762"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Azure IoT device SDK プラットフォームのサポート

Microsoft では、Azure IoT Hub 対応のデバイスを継続的に拡張することに努めています。 Microsoft は、デバイスを Azure IoT Hub と Device Provisioning Service に接続できるように、GitHub でオープンソースのデバイス SDK を発行しています。 C、.NET (C#)、Java、Node.js、および Python のサービス SDK を用意しています。 Microsoft は、各 SDK をテストして、これがサポートされている構成で実行されることを保証します。詳細は「[Microsoft SDK とデバイス プラットフォームのサポート](#microsoft-sdks-and-device-platform-support)」のセクションで説明されています。

デバイス SDK に加えて、Microsoft では、お客様と開発者が自分のデバイスを Azure IoT に接続するためのさまざまな手段を提供しています。

* 複数のパートナー企業と協力して、Azure IoT C SDK をベースにした開発キットを自社のハードウェア プラットフォームに公開できるよう支援しています。

* Microsoft の信頼できるパートナーと協力して、Azure IoT のテストと認定を受けた、拡張を続ける一連のデバイスを提供しています。 これらのデバイスの現在の一覧については、[Azure Certified for IoT デバイス カタログ](https://catalog.azureiotsolutions.com/)を参照してください。

* 開発者が SDK をプラットフォームに簡単に移植できるように、Azure IoT Hub デバイス C SDK にプラットフォーム アブストラクション レイヤー (PAL) を提供しています。 詳細については、「[C SDK 移植ガイダンス](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)」を参照してください。

このトピックでは、Microsoft SDK とそれがサポートするプラットフォーム構成、および上記のその他のオプションについて説明します。

## <a name="microsoft-sdks-and-device-platform-support"></a>Microsoft SDK とデバイス プラットフォームのサポート

Microsoft では、GitHub で次の言語のオープンソース SDK を公開しています:C、.NET (C#)、Node.js、Java、Python。 このセクションでは、これらの SDK とその依存関係を一覧表示します。 これらの SDK は、これらの依存関係を満たすすべてのデバイス プラットフォームでサポートされています。

一覧表示されている各 SDK について、Microsoft では次のことを行っています。

* いくつかの一般的なプラットフォーム上で、GitHub 内の関連する SDK のメイン ブランチに対するエンドツーエンド テストを継続的に構築して実行しています。  異なるコンパイラ バージョン間でのテスト カバレッジを提供するために、最新の LTS バージョンと、最も一般的なバージョンに対してテストを行っています。

* インストール ガイダンスまたはインストール パッケージを提供しています (該当する場合)。

* オープンソース コード、顧客貢献のためのパス、GitHub の問題に対する製品チームの取り組みにより、GitHub 上の SDK をフルにサポートしています。

### <a name="c-sdk"></a>C SDK

[Azure IoT Hub C デバイス SDK](https://github.com/Azure/azure-iot-sdk-c) は次の構成でテストされ、これらの構成をサポートしています。

| OS                  | TLS ライブラリ                  | その他の要件                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL、WolfSSL、または BearSSL | Berkeley ソケット</br></br>ポータブル オペレーティング システム インターフェイス (POSIX)                       |
| iOS 12.2            | OpenSSL                      | OSX 10.13.4 でエミュレートされる XCode                                                               |
| Windows 10 ファミリ   | SChannel                     |                                                                                             |
| Mbed OS 5.4         | Mbed TLS 2                   | [MXChip IoT 開発キット](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure Sphere OS     | WolfSSL                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | BearSSL                      | [ESP32 または ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Python SDK

[Azure IoT Hub Python デバイス SDK](https://github.com/Azure/azure-iot-sdk-python) は次の構成でテストされ、これらの構成をサポートしています。

| OS                  | コンパイラ                          |
|---------------------|-----------------------------------|
| Linux               | Python 2.7.*、3.5 以降 |
| macOS High Sierra   | Python 2.7.*、3.5 以降 |
| Windows 10 ファミリ   | Python 2.7.*、3.5 以降 |

非同期 API は Python バージョン 3.5.3 以降でのみサポートされます。バージョン 3.7 以降を使用することをお勧めします。

### <a name="net-sdk"></a>.NET SDK

[Azure IoT Hub .NET (C#) デバイス SDK](https://github.com/Azure/azure-iot-sdk-csharp) は、次の構成でテストされ、これらの構成をサポートしています。

| OS                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Windows 10 デスクトップおよびサーバー SKU   | .NET Core 2.1、.NET Framework 4.5.1、または .NET Framework 4.7 |

.NET SDK は、[Azure Device Agent](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) を含む Windows IoT Core、または [RPC を使用して UWP アプリケーションと通信できるカスタム NTService](/samples/microsoft/windows-iotcore-samples/ntservice-rpc/) でも使用できます。

### <a name="nodejs-sdk"></a>Node.js SDK

[Azure IoT Hub Node.js デバイス SDK](https://github.com/Azure/azure-iot-sdk-node) は、次の構成でテストされ、これらの構成をサポートしています。

| OS                  | Node バージョン    |
|---------------------|-----------------|
| Linux               | LTS と最新 |
| Windows 10 ファミリ   | LTS と最新 |

### <a name="java-sdk"></a>Java SDK

[Azure IoT Hub Java デバイス SDK](https://github.com/Azure/azure-iot-sdk-java)は、次の構成でテストされ、これらの構成をサポートしています。

| OS                     | Java バージョン |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Windows 10 ファミリ x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>パートナーがサポートする開発キット

Microsoft ではさまざまなパートナーと連携して、複数のマイクロプロセッサ アーキテクチャ向けの開発キットを提供しています。 これらのパートナーは、Azure IoT C SDK を自社のプラットフォームに移植しています。 パートナーは、SDK のプラットフォーム アブストラクション レイヤー (PAL) を作成および管理しています。 Microsoft では、これらのパートナーと連携して延長サポートを提供しています。

| Partner             | デバイス                            | Link                     | サポート |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT モデム     | [Qualcomm LTE for IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [フォーラム](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | STM32L4 シリーズ <br/> STM32F4 シリーズ <br/>  STM32F7 シリーズ <br/>  STM32L4 Discovery Kit for IoT ノード    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [サポート](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF LaunchPad </br> CC3220S LaunchPad </br> CC3235SF LaunchPad </br> CC3235S LaunchPad </br> MSP432E4 LaunchPad | [Azure IoT Plugin for SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E フォーラム](https://e2e.ti.com) <br/> [CC3220 の TI E2E フォーラム](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [MSP432E4 の TI E2E フォーラム](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>Microsoft Azure IoT C SDK の移植

デバイス プラットフォームが前のセクションのいずれにも該当しない場合は、Azure IoT C SDK の移植を検討してください。 C SDK の移植には、主に SDK のプラットフォーム アブストラクション レイヤー (PAL) の実装が含まれます。 PAL により、デバイスと SDK の上位レベルの関数との接続を提供するプリミティブが定義されます。 詳細については、[移植のガイダンス](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)を参照してください。

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Microsoft パートナーと認定済みの Azure IoT デバイス

Microsoft では数多くのパートナーと連携して、Azure IoT のテストと認定を受けたデバイスで Azure IoT ユニバースを継続的に拡張しています。

* Azure IoT の認定デバイスを確認するには、[Microsoft Azure Certified for IoT デバイス カタログ](https://catalog.azureiotsolutions.com/)を参照してください。

* Azure Certified for IoT エコシステムの詳細については、「[Certified for IoT エコシステムに参加](https://catalog.azureiotsolutions.com/register)」を参照してください。

## <a name="connecting-to-iot-hub-without-an-sdk"></a>SDK を使用せずに IoT Hub に接続する

IoT Hub デバイス SDK のいずれかを使用できない場合は、サポートされている通信プロトコルを使用して IoT Hub に直接接続できます。 詳しくは、「[Azure IoT SDK を使用せずに開発する](iot-hub-devguide-no-sdk.md)」を参照してください。

## <a name="support-and-other-resources"></a>サポートとその他のリソース

Azure IoT device SDK の使用中に問題が発生した場合は、以下にまとめてあるいくつかの方法でサポートを求めることができます。 すべてのサポート オプションの詳細については、[Azure IoT のサポートとヘルプのオプション](../iot-fundamentals/iot-support-help.md)を参照してください。 

**バグの報告** – デバイス SDK のバグは、関連する GitHub プロジェクトの問題ページで報告できます。 修正プログラムはプロジェクトが製品の更新プログラムになるまでの時間を早めます。

* [Azure IoT Hub C SDK の問題](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Azure IoT Hub .NET (C#) SDK の問題](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Azure IoT Hub Java SDK の問題](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Azure IoT Hub Node.js SDK の問題](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Azure IoT Hub Python SDK の問題](https://github.com/Azure/azure-iot-sdk-python/issues)

**技術的な質問** – [Microsoft Q&A](/answers/topics/azure-iot-sdk.html) と [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-sdk) の両方で、*azure-iot-sdk* を利用して技術的な質問をすることができます。

**Microsoft カスタマー サポート チーム** – [サポート プラン](https://azure.microsoft.com/support/plans/)に加入しているユーザーは、[Azure portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) から新しいサポート要求を直接作成することで、Microsoft カスタマー サポート チームとやり取りすることができます。

**機能の要望** – Azure IoT 機能の要望は、製品の [ユーザーの声のページ](https://feedback.azure.com/forums/321918-azure-iot)を介して追跡されます。

## <a name="next-steps"></a>次のステップ

* [デバイス SDK とサービス SDK](iot-hub-devguide-sdks.md)
* [移植ガイダンス](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
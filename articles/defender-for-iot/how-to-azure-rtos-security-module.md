---
title: Azure RTOS 用の Defender-IoT-micro-agent を構成およびカスタマイズする
description: Azure RTOS 用の Defender-IoT-micro-agent を構成およびカスタマイズする方法を説明します。
ms.topic: how-to
ms.date: 03/07/2021
ms.openlocfilehash: afab823b6bb187c9a7b7529f52efc37b20e8c66f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778986"
---
# <a name="configure-and-customize-defender-iot-micro-agent-for-azure-rtos-preview"></a>Azure RTOS 用の Defender-IoT-micro-agent を構成およびカスタマイズする (プレビュー)

この記事では、お使いのネットワーク、帯域幅、およびメモリの要件を満たすために、Azure RTOS デバイス用の Defender-IoT-micro-agent を構成する方法について説明します。

`netxduo/addons/azure_iot/azure_iot_security_module/configs` ディレクトリから、`*.dist` 拡張子を持つターゲット ディストリビューション ファイルを選択する必要があります。  

CMake コンパイル環境を使用する場合は、選択した値のコマンドライン パラメーターを `IOT_SECURITY_MODULE_DIST_TARGET` に設定する必要があります。 たとえば、「 `-DIOT_SECURITY_MODULE_DIST_TARGET=RTOS_BASE` 」のように入力します。

IAR またはその他の非 CMake コンパイル環境では、既知の含まれているパスに `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/` パスを追加する必要があります。 たとえば、「 `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/RTOS_BASE` 」のように入力します。

デバイスの動作を構成するには、次のファイルを使用します。

**netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/\<target distribution>/asc_config.h**

CMake コンパイル環境では、`netxduo/addons/azure_iot/azure_iot_security_module/configs/<target distribution>.dist` ファイルを編集して既定の構成を変更する必要があります。 次の CMake 形式 `set(ASC_XXX ON)`、または他のすべての環境では次のファイル `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/asc_config.h` を使用します。 たとえば、「 `#define ASC_XXX` 」のように入力します。

各構成の既定の動作を次の表に示します。 

## <a name="general"></a>全般

| 名前 | Type | Default | 詳細 |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | String | defender-iot-micro-agent | デバイスの一意識別子。  |
| SECURITY_MODULE_VERSION_(MAJOR)(MINOR)(PATCH)  | Number | 3.2.1 | バージョン。 |
| ASC_SECURITY_MODULE_SEND_MESSAGE_RETRY_TIME  | Number  | 3 | 失敗後に Defender-IoT-micro-agent でセキュリティ メッセージを送信するのにかかる時間の長さ。 (秒単位) |
| ASC_SECURITY_MODULE_PENDING_TIME  | 番号 | 300 | Defender-IoT-micro-agent の保留時間 (秒)。 時間が経過すると、状態が保留に変わります。 |

## <a name="collection"></a>コレクション

| 名前 | Type | Default | 詳細 |
| - | - | - | - |
| ASC_FIRST_COLLECTION_INTERVAL | 番号  | 30  | コレクターの開始コレクション間隔のオフセット。 メッセージが複数のデバイスから同時に送信されないようにするために、開始時にこの値がシステムのコレクションに追加されます。  |
| ASC_HIGH_PRIORITY_INTERVAL | 番号 | 10 | コレクターの優先度が高いグループ間隔 (秒)。 |
| ASC_MEDIUM_PRIORITY_INTERVAL | 番号 | 30 | コレクターの優先度が中程度のグループ間隔 (秒)。 |
| ASC_LOW_PRIORITY_INTERVAL | 番号 | 145,440  | コレクターの優先度が低いグループ間隔 (秒)。 |

#### <a name="collector-network-activity"></a>コレクターのネットワーク アクティビティ

コレクターのネットワーク アクティビティ構成をカスタマイズするには、以下を使用します。

| 名前 | Type | Default | 詳細 |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolean | false | `TCP` ネットワーク アクティビティをフィルター処理します。 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolean | false | `UDP` ネットワーク アクティビティ イベントをフィルター処理します。 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolean | false | `ICMP` ネットワーク アクティビティ イベントをフィルター処理します。 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Boolean | true | ユニキャスト着信パケットのみをキャプチャします。 false に設定すると、ブロードキャストとマルチキャストの両方もキャプチャされます。 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_SEND_EMPTY_EVENTS  | Boolean  | false  | コレクターの空のイベントを送信します。 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | 番号 | 64 | メモリに格納する IPv4 ネットワーク イベントの最大数。 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | 番号 | 64  | メモリに格納する IPv6 ネットワーク イベントの最大数。 |

### <a name="collectors"></a>Collectors
| 名前 | Type | Default | 詳細 |
| - | - | - | - |
| ASC_COLLECTOR_HEARTBEAT_ENABLED | Boolean | ON | ハートビート コレクターを有効にします。 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ENABLED  | Boolean | ON | ネットワーク アクティビティ コレクターを有効にします。 |
| ASC_COLLECTOR_SYSTEM_INFORMATION_ENABLED | Boolean | ON | システム情報コレクターを有効にします。  |

その他の構成フラグは高度であり、サポートされていない機能があります。 これを変更するには、または詳細については、サポートにお問い合わせください。
 
## <a name="supported-security-alerts-and-recommendations"></a>サポートされているセキュリティ アラートと推奨事項

Azure RTOS 用の Defender-IoT-micro-agent では、特定のセキュリティ アラートと推奨事項がサポートされています。 サービスに対する[関連するアラートと推奨事項の値を確認してカスタマイズ](concept-rtos-security-alerts-recommendations.md)します。

## <a name="log-analytics-optional"></a>Log Analytics (省略可能)

Log Analytics を有効にして構成し、デバイスのイベントとアクティビティを調査することができます。 [Defender for IoT サービスと Log Analytics](how-to-security-data-access.md#log-analytics) を設定して使用する方法の詳細を参照してください。 

## <a name="next-steps"></a>次のステップ


- Azure RTOS 用の Defender-IoT-micro-agent の [セキュリティ アラートと推奨事項](concept-rtos-security-alerts-recommendations.md) をレビューしカスタマイズする
- 必要に応じて Azure RTOS 用の [Defender-IoT-micro-agent のリファレンス API](azure-rtos-security-module-api.md) を参照してください。

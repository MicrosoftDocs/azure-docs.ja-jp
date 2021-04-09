---
title: Device Update for Azure IoT Hub 用に Microsoft 接続キャッシュを構成する | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Device Update for Azure IoT Hub 用の Microsoft 接続キャッシュの概要
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 2903407f88b57a7be948cdeb0610e6d65df975b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101660625"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>Device Update for Azure IoT Hub 用に Microsoft 接続キャッシュを構成する

Microsoft 接続キャッシュは、Azure IoT Edge モジュールとして Azure IoT Edge ゲートウェイにデプロイされます。 他の Azure IoT Edge モジュールと同様に、Microsoft 接続キャッシュ モジュールの構成には、MCC モジュール デプロイ環境変数およびコンテナー作成オプションが使用されます。  このセクションでは、Device Update for Azure IoT Hub で使用される Microsoft 接続キャッシュ モジュールを顧客が正しくデプロイするために必要な環境変数とコンテナー作成オプションを定義します。

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Microsoft 接続キャッシュ Azure IoT Edge モジュールのデプロイの詳細

Microsoft 接続キャッシュ モジュールの名前は、管理者の裁量で付けられます。 通信にモジュールの名前を使用する他のモジュールまたはサービス操作はありません。 また、Microsoft 接続キャッシュ サーバーの親子関係は、このモジュール名に依存しませんが、前述のように構成した Azure IoT Edge ゲートウェイの FQDN または IP アドレスには依存します。

Microsoft 接続キャッシュ Azure IoT Edge モジュール環境変数は、基本的なモジュール ID 情報と機能モジュール設定をコンテナーに渡すために使用されます。

| 変数名                 | 値の形式                           | 必須/省略可能 | 機能                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | Azure サブスクリプション ID GUID             | 必須          | これは顧客のキーであり、配信の最適化サービスに、キャッシュ ノードのセキュリティで保護された認証を提供します<br>配信の最適化サービスに、キャッシュ ノードのセキュリティで保護された認証を<br>承認。 モジュールが機能するために必要です。 |
| CACHE_NODE_ID                 | キャッシュ ノード ID GUID                     | 必須          | 配信の最適化サービスへの Microsoft 接続キャッシュ ノードを一意に識別します。<br>一意に識別します。 モジュールが機能するために必要です。<br> 必要です。 |
| CUSTOMER_KEY                  | カスタマー キー GUID                     | 必須          | これは顧客のキーであり、配信の最適化サービスにキャッシュ ノードのセキュリティで保護された認証を提供します。<br>配信の最適化サービスにキャッシュ ノードのセキュリティで保護された認証を提供します。<br>モジュールが機能するために必要です。|
| STORAGE_ *N* _SIZE_GB           | ここで、N は必要な GB 数です   | 必須          | コンテンツをキャッシュする最大 9 個のドライブを指定し、各キャッシュ ドライブ上でコンテンツに割り当てる最大容量を GB で指定します。<br>各キャッシュ ドライブ上でコンテンツに割り当てる最大容量を GB で指定します。 例 :<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>ドライブの番号は、コンテナー作成オプションの MicrosoftConnectedCacheN 値で指定されたキャッシュ ドライブのバインド値と一致している必要があります<br>MicrosoftConnectedCache *N* 値で指定されたキャッシュ ドライブのバインド値と一致している必要があります|
| UPSTREAM_HOST                 | FQDN/IP                                | 省略可能          | この値では、接続キャッシュ ノードがインターネットから切断された場合に、プロキシとして機能するアップストリーム Microsoft 接続キャッシュ ノードを指定できます。<br>プロキシとして機能するアップストリーム Microsoft 接続キャッシュ ノードを<br> 指定できます。 この設定は、入れ子になった IoT シナリオをサポートするために使用されます。<br> サポートするために使用されます。<br>**注:** Microsoft 接続キャッシュは、http の既定のポート 80 でリッスンします。|
| UPSTREAM_PROXY                | FQDN/IP:PORT                           | 省略可能          | 送信インターネット プロキシ。<br>これは、ISA 95 ネットワークの場合、OT DMZ プロキシであることもあります。 |
| CACHEABLE_CUSTOM_ *N* _HOST     | HOST/IP<br>FQDN                        | 省略可能          | カスタム パッケージ リポジトリをサポートするために必要です。<br>リポジトリはローカルでもインターネット上でもホストできます。<br>構成できるカスタム ホストの数に制限はありません。<br><br>例 :<br>名前 = CACHEABLE_CUSTOM_1_HOST 値 = packages.foo.com<br> 名前 = CACHEABLE_CUSTOM_2_HOST 値 = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| エイリアス                                  | 省略可能          | カスタム パッケージ リポジトリをサポートするために必要です。<br>この値は、エイリアスとして使用でき、さまざまな DNS 名を参照するためにキャッシュ サーバーで使用されます。<br>キャッシュ サーバーで使用されます。 たとえば、リポジトリ コンテンツのホスト名は packages.foo.com にすることができますが、各地域に応じて、westuscdn.packages.foo.com や eastuscdn.packages.foo.com のように、追加プレフィックスをホスト名に付加できます。<br>各地域に応じて、<br>westuscdn.packages.foo.com や eastuscdn.packages.foo.com のように、追加プレフィックスをホスト名に付加できます。<br>標準の別名を設定することによって、コンテンツが同じホストからではなく、異なる CDN ソースからのコンテンツに対して重複されるようになります。<br>異なる CDN ソースからのコンテンツに対して重複されるようになります。<br>標準の値の形式は重要ではありませんが、ホストに対して一意である必要があります。<br>ホストの値と一致するように値を設定することが最も簡単な場合があります。<br><br>上記のカスタム ホスト例に基づく例:<br>名前 = CACHEABLE_CUSTOM_1_CANONICAL 値 = foopackages<br> 名前 = CACHEABLE_CUSTOM_2_CANONICAL 値 = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | True または False                          | 省略可能          | ローカル ネットワークまたはインターネットでの概要レポートの表示を有効にします。<br>True に設定した場合、概要レポートを表示するために API キー (後述) を使用する必要があります。 |
| IS_SUMMARY_ACCESS_UNRESTRICTED| True または False                          | 省略可能          | ネットワーク内のデバイスから API キーを使用せずに、ローカル ネットワークまたはインターネットで概要レポートを表示できるようにします。<br>概要レポートを表示できるようにします。 ブラウザーを介して、キャッシュ サーバーの概要データの表示へのアクセスをロック ダウンしない場合に使用します。<br>ロック ダウンしない場合に使用します。 |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Microsoft 接続キャッシュ Azure IoT Edge モジュール コンテナー作成オプション

MCC モジュール デプロイのコンテナー作成オプションは、MCC モジュールで使用されるストレージとポートに関連する設定を制御します。 これは、MCC のデプロイに使用される、コンテナーによって作成された必要な変数の一覧です。

### <a name="container-to-host-os-drive-mappings"></a>OS ドライブ マッピングをホストするコンテナー

コンテナーの保存場所を、ディスク上の保存場所にマップするために必要です。最大 9 つの場所を指定できます。

>[!Note]
>ドライブの番号は、環境変数 STORAGE_ *N* _SIZE_GB 値で指定されたキャッシュ ドライブのバインド値と一致している必要があります (```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```)

### <a name="container-to-host-tcp-port-mappings"></a>TCP ポート マッピングをホストするコンテナー

このオプションは、MCC がコンテンツ要求をリッスンしている外部コンピューターの HTTP ポートを指定します。 現在、ADU クライアントはポート 80 で要求を行っているので、既定の HostPort はポート 80 であり、現時点では他のポートはサポートされていません。 TCP ポート 8081 は、MCC がリッスンしている内部コンテナー ポートであり、変更することはできません。

```markdown
8081/tcp": [
   {
       "HostPort": "80"
   }
]
```

### <a name="container-service-tcp-port-mappings"></a>コンテナー サービスの TCP ポート マッピング

Microsoft 接続キャッシュ モジュールには、.NET Core サービスがあり、これは、さまざまな機能のためにキャッシュ エンジンによって使用されます。

>[!Note]
>Azure IoT の入れ子になったエッジをサポートするには、レジストリ プロキシ モジュールが既にホスト ポート 5000 でリッスンしているため、HostPort を 5000 に設定することはできません。

```markdown
5000/tcp": [
   {
       "HostPort": "5001"
   }
]
```

## <a name="microsoft-connected-cache-summary-report"></a>Microsoft 接続キャッシュの概要レポート

現在、概要レポートは、Azure IoT Edge ゲートウェイにデプロイされている Microsoft 接続キャッシュ インスタンスのキャッシュ データを顧客が表示する唯一の方法です。 レポートは 15 秒間隔で生成され、その期間の平均統計と、モジュールの有効期間の集計された統計が含まれます。 顧客が関心を持つ主要な統計情報は次のとおりです。

* **hitBytes** - これはキャッシュから直接取得した、配信されたバイト数の合計です。
* **missBytes** - これは、Microsoft 接続キャッシュがキャッシュを表示するために CDN からダウンロードする必要のあった、配信されたバイト数の合計です。
* **eggressBytes** - これは、hitBytes と missBytes の合計であり、クライアントに配信された合計バイト数です。
* **hitRatioBytes** - これは、hitBytes と egressBytes の比率です。  たとえば、ある期間に配信された eggressBytes の 100% が hitBytes と同じであれば、これは 1 になります。

概要レポートは `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` で入手できます (このレポートの表示については、以下の環境変数の詳細を参照してください)。

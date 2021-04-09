---
title: Azure Stack Edge Pro R デバイスを配置するための配置前チェックリスト | Microsoft Docs
description: この記事では、Azure Stack Edge Pro R デバイスを配置する前に収集できる情報について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/24/2021
ms.author: alkohli
ms.openlocfilehash: eca26934605ff70ecb26c10604fc9b493e88f2cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727446"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-pro-r-device"></a>Azure Stack Edge Pro R デバイスの配置チェックリスト  

この記事では、Azure Stack Edge Pro R デバイスを実際に配置する前に収集できる情報について説明します。 

次のチェックリストを使用して、Azure Stack Edge Pro R デバイスを注文した後、デバイスを受け取る前にこの情報を確実に入手してください。 

## <a name="deployment-checklist"></a>展開のチェックリスト 

| 段階                             | パラメーター                                                                                                                                                                                                                           | 詳細                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| デバイスの管理               | <li>Azure サブスクリプション</li><li>登録済みのリソース プロバイダー</li><li>Azure ストレージ アカウント</li>|<li>Azure Stack Edge Pro および Data Box Gateway に対して所有者または共同作成者のアクセスが有効になっています。</li><li>Azure portal で、 **[ホーム] > [サブスクリプション] > 自分のサブスクリプション > [リソース プロバイダー]** に移動します。 `Microsoft.DataBoxEdge` を検索して、登録します。 IoT ワークロードをデプロイする場合は、`Microsoft.Devices` に対しても繰り返します。</li><li>アクセスの資格情報が必要</li> |
| デバイスのインストール               | パッケージ内の電源ケーブル。 <br>米国の場合、定格 125 V、15 アンペアの SVE 18/3 ケーブルと NEMA 5-15P to C13 (入力から出力) コネクタが付属しています。 | 詳細については、[国別のサポートされている電源コード](azure-stack-edge-technical-specifications-power-cords-regional.md)の一覧を参照してください。  |
|                                   | <li>ポート 1 用の少なくとも 1 本の 1 GbE RJ-45 ネットワーク ケーブル  </li><li> ポート 3、ポート 4 用の少なくとも 1 本の 25 GbE SFP+ 銅線ケーブル</li>| これらのケーブルはお客様が調達する必要があります。<br>デバイス ネットワーク カードでサポートされているネットワーク ケーブル、スイッチ、トランシーバーの詳細な一覧については、[Cavium FastlinQ 41000 シリーズの相互運用性マトリックス](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)および [Mellanox デュアル ポート 25G ConnectX-4 チャネル ネットワーク アダプター互換製品](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)に関するドキュメントを参照してください。| 
| 初回のデバイス接続      | <li>IPv4 設定を変更できるラップトップ。 このラップトップは、スイッチまたは USB イーサネット アダプターを介してポート 1 に接続します。  </li><!--<li> A minimum of 1 GbE switch must be used for the device once the initial setup is complete. The local web UI will not be accessible if the connected switch is not at least 1 Gbe.</li>-->|   |
| デバイスへのサインイン                      | デバイス管理者のパスワード。8 文字から 16 文字で、大文字、小文字、数字、特殊文字のうち、3 種類を含みます。                                            | 既定のパスワードは *Password1* であり、最初のサインイン時に有効期限が切れます。                                                     |
| ネットワーク設定                  | デバイスには、2 x 1-GbE、4 x 25-GbE ネットワーク ポートが付属しています。 <li>ポート 1 は、管理設定の構成にのみ使用されます。 1 つ以上のデータ ポートを接続して構成できます。 </li><li> 少なくとも 1 つのデータ ネットワーク インターフェイス (ポート 2 からポート 6) がインターネットに接続されている必要があります (Azure への接続が可能なもの)。</li><li> DHCP と静的 IPv4 構成がサポートされています。 | 静的 IPv4 構成には、IP、DNS サーバーと既定のゲートウェイが必要です。   |
| ネットワーク設定を計算する     | <li>Kubernetes ノード用に 2 つの連続した静的空き IP と、IoT Edge サービス用に 1 つの静的 IP が必要です。</li><li>デプロイする追加のサービスまたはモジュールごとに、追加の IP が 1 つ必要です。</li>| 静的 IPv4 構成のみがサポートされています。|
| (省略可能) Web プロキシ設定     | <li>Web プロキシ サーバーの IP または FQDN、ポート </li><li>Web プロキシのユーザー名、パスワード</li> |  |
| ファイアウォールとポートの設定        | ファイアウォールを使用している場合は、[一覧にある URL パターンとポート](azure-stack-edge-system-requirements.md#networking-port-requirements)がデバイスの IP で許可されるようにします。 |  |
| (推奨) 時間設定       | タイム ゾーン、プライマリ NTP サーバー、セカンダリ NTP サーバーを構成します。 | ローカル ネットワークでプライマリおよびセカンダリ NTP サーバーを構成します。<br>ローカル サーバーを使用できない場合は、パブリック NTP サーバーを構成できます。                                                    |
| (省略可能) サーバー設定を更新する | <li>ローカル ネットワーク上の更新サーバーの IP アドレス、WSUS サーバーへのパスが必要です。 </li> | 既定では、パブリック Windows 更新サーバーが使用されます。|
| デバイスの設定 | <li>デバイスの完全修飾ドメイン名 (FQDN) </li><li>DNS ドメイン</li> | |
| (省略可能) 証明書  | 署名チェーンを含む独自の証明書を持ち込む場合は、適切な形式の[証明書を追加](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#bring-your-own-certificates)します。| デバイス名または DNS ドメインを変更する場合にのみ、証明書を構成します。 |
| VPN  | <!--Need VPN certificate, VPN gateway, firewall setup in Azure,  passphrase and region info VPN scripts. -->   | |
| 保存時の暗号化  | 自動生成された暗号化キーを使用することをお勧めします。   |独自のキーを使用する場合は、32 文字の Base-64 でエンコードされたキーが必要です。  |
| アクティベーション  | Azure Stack Edge Pro および Data Box Gateway リソースのアクティブ化キーが必要です。    | 生成されたキーは 3 日後に有効期限が切れます。 |

<!--
| (Optional) MAC Address            | If MAC address needs to be approved, get the address of the connected port from local UI of the device. |                                                                                                                   |
| (Optional) Network switch port    | Device hosts Hyper-V VMs for compute. Some network switch port configurations don’t accommodate these setups by default.                                                                                                        |                                                                                                                   |-->


## <a name="next-steps"></a>次のステップ

[Azure Stack Edge Pro デバイス](azure-stack-edge-pro-r-deploy-prep.md)のデプロイを準備します。

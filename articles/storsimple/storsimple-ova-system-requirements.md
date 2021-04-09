---
title: Microsoft Azure StorSimple Virtual Array のシステム要件
description: StorSimple Virtual Array のソフトウェア要件とネットワーク要件について説明します。
author: alkohli
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 5970e82619667a47ba160c84df2cdeb145b0dab8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966175"
---
# <a name="storsimple-virtual-array-system-requirements"></a>StorSimple Virtual Array のシステム要件

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>概要

この記事では、Microsoft Azure StorSimple Virtual Array とその配列にアクセスするストレージ クライアントの重要なシステム要件について説明します。 StorSimple システムをデプロイする前に以下の情報を慎重に確認します。さらにデプロイと後続の操作中にも必要に応じて以下の情報を参照することをお勧めします。

システム要件は次のとおりです。

* **ストレージ クライアントのソフトウェア要件** - サポートされている仮想化プラットフォーム、Web ブラウザー、iSCSI イニシエーター、SMB クライアント、仮想デバイスの最小要件、オペレーティング システムの追加要件について説明します。
* **、StorSimple デバイスのネットワーク要件** - iSCSI、クラウド、または管理トラフィックを使用できるようにするため、ファイアウォールで開いておく必要があるポートについての情報を提供します。

この記事に記載されている StorSimple のシステム要件の情報は、StorSimple Virtual Array にのみ適用されます。

* 8000 シリーズ デバイスについては、「 [StorSimple ソフトウェア、高可用性、ネットワークの要件](./storsimple-8000-system-requirements.md)」をご覧ください。
* 7000 シリーズ デバイスについては、「 [StorSimple System Requirements](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements)」をご覧ください。

## <a name="software-requirements"></a>ソフトウェア要件
ソフトウェア要件には、サポートされている Web ブラウザー、SMB のバージョン、仮想化プラットフォーム、仮想デバイスの最小要件に関する情報が含まれています。

### <a name="supported-virtualization-platforms"></a>サポートされている仮想化プラットフォーム
| **ハイパーバイザー** | **Version** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 以降 |
| VMware ESXi |5.0、5.5、6.0、および 6.5。 |

> [!IMPORTANT]
> VMware ツールを StorSimple Virtual Array にインストールしないでください。インストールすると、サポートされない構成になります。

### <a name="virtual-device-requirements"></a>仮想デバイスの要件
| **コンポーネント** | **要件** |
| --- | --- |
| 最小仮想プロセッサ (コア) 数 |4 |
| 最小メモリ (RAM) |8 GB <br> ファイル サーバーの場合、処理するファイル数が 200 万個未満の場合は 8 GB、200 ～ 400 万個の場合は 16 GB|
| ディスク領域<sup>1</sup> |OS ディスク - 80 GB  <br></br>データ ディスク - 500 GB ～ 8 TB |
| 最小ネットワーク インターフェイス数 |1 |
| インターネット帯域幅 <sup>2</sup> |必要な最小帯域幅: 5 Mbps <br> 推奨する帯域幅: 100 Mbps <br> データ転送速度は、インターネットの帯域幅に対応します。 たとえば、100 GB のデータは、5 Mbps で転送すると 2 日かかります。するとバックアップが 1 日で完了しないため、バックアップ エラーを招く可能性があります。 100 Mbps の帯域幅では、100 GB のデータが 2.5 時間で転送できます。   |

<sup>1</sup> - 仮想プロビジョニング対応

<sup>2</sup> - ネットワーク要件は、日次データ変化率によって異なる場合があります。 たとえば、デバイスで 1 日に 10 GB 以上の変更をバックアップする必要がある場合、5 Mbps 接続での日次バックアップには最大 4.25 時間かかる可能性があります (データの圧縮も重複除去もできなかった場合)。

### <a name="supported-web-browsers"></a>サポートされている Web ブラウザー
| **コンポーネント** | **Version** | **その他の要件/注意事項** |
| --- | --- | --- |
| Microsoft Edge |最新バージョン | |
| Internet Explorer |最新バージョン |Internet Explorer 11 でテスト済み |
| Google Chrome |最新バージョン |Chrome 46 でテスト済み |

### <a name="supported-storage-clients"></a>サポートされているストレージ クライアント
次のソフトウェア要件は、StorSimple Virtual Array (iSCSI サーバーとして構成されている) にアクセスする iSCSI イニシエーターに適用されます。

| **サポートされているオペレーティング システム** | **必須のバージョン** | **その他の要件/注意事項** |
| --- | --- | --- |
| Windows Server |2008R2 SP1、2012、2012R2 |StorSimple は、シン プロビジョニングされたボリュームと完全にプロビジョニングされたボリュームを作成できます。 部分的にプロビジョニングされたボリュームは作成できません。 StorSimple iSCSI ボリュームは以下でのみサポートされます。 <ul><li>Windows ベーシック ディスク上のシンプル ボリューム。</li><li>ボリュームをフォーマットするための Windows NTFS。</li> |

次のソフトウェア要件は、StorSimple Virtual Array (ファイル サーバーとして構成されている) にアクセスする SMB クライアントに適用されます。

| **SMB バージョン** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

> [!IMPORTANT]
> StorSimple Virtual Array のファイル サーバーに Windows 暗号化ファイル システム (EFS) で保護されているファイルを保存またはコピーしないでください。サポートされていない構成の原因になります。


### <a name="supported-storage-format"></a>サポートされているストレージ形式
Azure ブロック BLOB ストレージのみがサポートされています。 ページ BLOB はサポートされていません。 [ブロック BLOB とページ BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)に関する詳細情報。

## <a name="networking-requirements"></a>ネットワーク要件
iSCSI、SMB、クラウド、または管理トラフィックを許可するためにファイアウォールで開く必要があるポートを次の表に示します。 この表では、*イン* または *受信* はデバイスにアクセスするクライアント要求が入ってくる方向を意味します。 *アウト* または *送信* は StorSimple デバイスがデプロイを超えて外部に (たとえば、インターネットに) データを送信する方向を意味します。

| **ポート番号 <sup>1</sup>** | **インまたはアウト** | **ポート範囲** | **必須** | **メモ** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |アウト |WAN |いいえ |送信ポートは、更新プログラムを取得するためのインターネット アクセスに使用します。 <br></br>送信 Web プロキシは、ユーザーが構成できます。 |
| TCP 443 (HTTPS) |アウト |WAN |はい |送信ポートは、クラウドのデータへのアクセスに使用します。 <br></br>送信 Web プロキシは、ユーザーが構成できます。 |
| UDP 53 (DNS) |アウト |WAN |場合によっては、メモを参照してください。 |このポートは、インターネット ベースの DNS サーバーを使用する場合にのみ必要です。 <br></br> ファイル サーバーをデプロイする場合は、ローカル DNS サーバーを使用することをお勧めします。 |
| UDP 123 (NTP) |アウト |WAN |場合によっては、メモを参照してください。 |このポートは、インターネット ベースの NTP サーバーを使用する場合にのみ必要です。<br></br> ファイル サーバーをデプロイする場合は、Active Directory ドメイン コントローラーと時刻を同期することをお勧めします。 |
| TCP 80 (HTTP) |場所 |LAN |はい |これは、ローカル管理に使用する StorSimple デバイスのローカル UI の受信ポートです。 <br></br> HTTP 経由でのローカル UI へのアクセスは、自動的に HTTPS にリダイレクトされます。 |
| TCP 443 (HTTPS) |場所 |LAN |はい |これは、ローカル管理に使用する StorSimple デバイスのローカル UI の受信ポートです。 |
| TCP 3260 (iSCSI) |場所 |LAN |いいえ |このポートは、iSCSI を介してデータにアクセスするために使用されます。 |

<sup>1</sup> 受信ポートがパブリック インターネットで開かれている必要はありません。

> [!IMPORTANT]
> StorSimple デバイスと Azure 間でファイアウォールが TLS トラフィックの変更や暗号化解除を行っていないことを確認します。
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>ファイアウォール ルールの URL パターン
多くの場合、ネットワーク管理者は、受信トラフィックと送信トラフィックをフィルターする URL パターンに基づいて、高度なファイアウォール ルールを構成できます。 Virtual Array と StorSimple デバイス マネージャー サービスは、Azure Service Bus、Azure Active Directory Access Control、ストレージ アカウント、Microsoft Update サーバーなどの他の Microsoft アプリケーションに依存しています。 その Microsoft アプリケーションと関連付けられた URL パターンを使用してファイアウォール ルールを構成できます。 Microsoft アプリケーションに関連付けられた URL パターンは変化する可能性がある点を理解することが重要です。 これにより、ネットワーク管理者は必要に応じて StorSimple のファイアウォール ルールを監視し更新する必要があります。 

ほとんどの場合、StorSimple 固定 IP アドレスに基づき、送信トラフィックのファイアウォール ルールを設定することが推奨されます。 ただし、次の情報を使用して、セキュリティで保護された環境を作成するのにために必要な高度なファイアウォール ルールを設定することもできます。

> [!NOTE]
> 
> * デバイスの (送信元) IP は、常にすべてのクラウド対応ネットワーク インターフェイスに合わせて設定します。 
> * 宛先 IP は、[Azure データセンターの IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)に合わせて設定するようにします。
> 
> 

| URL パターン | コンポーネント/機能 |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|StorSimple デバイス マネージャー サービス<br>Access Control Service<br>Azure Service Bus<br>認証サービス|
| `http://*.backup.windowsazure.com` |デバイス登録 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |証明書の失効 |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure ストレージ アカウントと監視 |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update サーバー<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |サポート パッケージ |
| `https://*.data.microsoft.com` |Windows の Telemetry Service (「[顧客満足度及び診断テレメトリのための更新プログラム](https://support.microsoft.com/en-us/kb/3068708)」を参照) |

## <a name="next-steps"></a>次のステップ
* [StorSimple Virtual Array をデプロイするためにポータルを準備します。](storsimple-virtual-array-deploy1-portal-prep.md)
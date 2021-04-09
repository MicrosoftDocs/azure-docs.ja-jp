---
title: Defender for IoT のインストール
description: Azure Defender for IoT のセンサーとオンプレミス管理コンソールをインストールする方法について説明します。
ms.date: 12/2/2020
ms.topic: how-to
ms.openlocfilehash: 5bdb292750ea041be68a22519583511f58b3b517
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782250"
---
# <a name="defender-for-iot-installation"></a>Defender for IoT のインストール

この記事では、Azure Defender for IoT の次の要素をインストールする方法について説明します。

- **センサー**:Defender for IoT センサーは、パッシブ (エージェントレス) 監視を使用して、ICS ネットワーク トラフィックを収集します。 このセンサーは、パッシブで非侵入型であるため、OT と IoT のネットワークとデバイスに影響しません。 このセンサーは、SPAN ポートまたはネットワーク TAP に接続し、ネットワークの監視を直ちに開始します。 検出はセンサー コンソールに表示されます。 そこでは、ネットワーク マップ、デバイス インベントリ、広範なレポートによって、それらを表示、調査、分析できます。 例としては、リスク評価レポート、データ マイニング クエリ、攻撃ベクトルなどがあります。 センサー機能の詳細については、[Defender for IoT センサーのユーザー ガイド (直接ダウンロード)](./getting-started.md) を参照してください。

- **オンプレミス管理コンソール**:オンプレミス管理コンソールを使用すると、デバイス管理、リスク管理、脆弱性管理を行うことができます。 また、これを使用すると、企業全体の脅威の監視とインシデント対応も行うことができます。 センサーが配置された施設で検出された、すべてのネットワーク デバイス、主要な IoT、OT のリスク インジケーターとアラートの統一されたビューが提供されます。 オンプレミス管理コンソールを使用して、エアギャップ ネットワークのセンサーを表示および管理します。

この記事では、次のインストール情報について説明します。

  - **ハードウェア:** Dell と HPE の物理アプライアンスの詳細。

  - **ソフトウェア:** センサーとオンプレミス管理コンソール ソフトウェアのインストール。

  - **仮想アプライアンス:** 仮想マシンの詳細とソフトウェアのインストール。

インストール後、センサーをご自身のネットワークに接続してください。

## <a name="about-defender-for-iot-appliances"></a>Defender for IoT アプライアンスについて 

次のセクションでは、Defender for IoT センサー アプライアンスと、Defender for IoT オンプレミス管理コンソールのアプライアンスに関する情報を提供します。

### <a name="physical-appliances"></a>物理アプライアンス

Defender for IoT アプライアンス センサーは、SPAN ポートまたはネットワーク TAP に接続し、パッシブ (エージェントレス) 監視を使用して ICS ネットワーク トラフィックの収集を直ちに開始します。 このプロセスはデータ パスに配置されず、OT デバイスを積極的にスキャンしないため、OT ネットワークとデバイスに影響しません。

次のラック マウント アプライアンスを使用できます。

| **デプロイの種類** | **企業** | **エンタープライズ** | **SMB** | **Line** |
|--|--|--|--|--|
| **Model** | HPE ProLiant DL360 | Dell PowerEdge R340 XL | HPE ProLiant DL20 | HPE ProLiant DL20 |
| **監視ポート** | 最大 15 個の RJ45 または 8 個の OPT | 最大 9 個の RJ45 または 6 個の OPT | 最大 8 個の RJ45 または 6 個の OPT | 4 個の RJ45 |
| **最大帯域幅\*** | 3 GB/秒 | 1 GB/秒 | 1 GB/秒 | 100 MB/秒 |
| **保護される最大デバイス数** | 30,000 | 10,000 | 15,000 | 1,000 |

*最大帯域幅の容量は、プロトコルの配布によって異なる場合があります。

### <a name="virtual-appliances"></a>仮想アプライアンス: 

次の仮想アプライアンスを使用できます。

| **デプロイの種類** | **エンタープライズ** | **SMB** | **Line** |
|--|--|--|--|
| **説明** | Enterprise デプロイ用の仮想アプライアンス | SMB デプロイ用の仮想アプライアンス | Line デプロイ用の仮想アプライアンス |
| **最大帯域幅\*** | 150 MB/秒 | 15 MB/秒 | 3 MB/秒 |
| **保護される最大デバイス数** | 3,000 | 300 | 100 |
| **デプロイの種類** | Enterprise | SMB | 行 |
| **説明** | Enterprise デプロイ用の仮想アプライアンス | SMB デプロイ用の仮想アプライアンス | Line デプロイ用の仮想アプライアンス |

*最大帯域幅の容量は、プロトコルの配布によって異なる場合があります。

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>オンプレミス管理コンソールのハードウェア仕様

 | 項目 | 説明 |
 |----|--|
 **説明** | 多層アーキテクチャでは、オンプレミス管理コンソールにより、地理的に分散したサイト全体の可視性と制御を実現できます。 これは、SIEM、チケット システム、次世代ファイアウォール、セキュリティで保護されたリモート アクセス プラットフォーム、Defender for IoT ICS マルウェア サンドボックスなどの SOC セキュリティ スタックと統合されます。 |
 **デプロイの種類** | Enterprise |
 **アプライアンスの種類**  | Dell R340、VM |
 **管理対象センサーの数** | 無制限 |

## <a name="prepare-for-the-installation"></a>インストールの準備

### <a name="access-the-iso-installation-image"></a>ISO インストール イメージにアクセスする

インストール イメージには、Defender for IoT ポータルからアクセスできます。

ファイルにアクセスするには:

1. Defender for IoT アカウントにサインインします。

1. **[Network sensor]\(ネットワーク センサー\)** または **[On-premises management console]\(オンプレミス管理コンソール\)** ページに移動し、ダウンロードするバージョンを選択します。

### <a name="install-from-dvd"></a>DVD からインストールする

インストールする前に、次のものがあることを確認してください。

- USB コネクタを備えたポータブル DVD ドライブ。

- ISO インストーラーイメージ。

インストールするには、次のようにします。

1. イメージを DVD に書き込むか、またはキーのディスクを準備します。 ポータブル DVD ドライブをご自身のコンピューターに接続し、ISO イメージを右クリックして、 **[Burn to disk]\(ディスクへの書き込み\)** を選択します。

1. キーの DVD またはディスクを接続し、キーの DVD またはディスクから起動するようにアプライアンスを構成します。

### <a name="install-from-disk-on-a-key"></a>キーのディスクからインストールする

インストールする前に、次のものがあることを確認してください。

  - インストールされた Rufus。
  
  - USB バージョン 3.0 以降のキーのディスク 最小サイズは 4 GB です。

  - ISO インストーラー イメージ ファイル。

キーのディスクは、このプロセスで消去されます。

キーのディスクを準備するには:

1. Rufus を実行し、 **[SENSOR ISO]\(センサー ISO\)** を選択します。

1. キーのディスクを前面パネルに接続します。

1. USB から起動するようにサーバーの BIOS を設定します。

## <a name="dell-poweredger340xl-installation"></a>Dell PowerEdgeR340XL のインストール

Dell アプライアンスにソフトウェアをインストールする前に、アプライアンスの BIOS 構成を調整する必要があります。

  - 「[Dell PowerEdge R340 の前面パネル](#dell-poweredge-r340-front-panel)」と「[Dell PowerEdge R340 の背面パネル](#dell-poweredge-r340-back-panel)」には、前面と背面のパネルの説明、およびドライバーやポートなど、インストールに必要な情報があります。

  - 「[Dell BIOS 構成](#dell-bios-configuration)」では、Dell アプライアンス管理インターフェイスに接続して BIOS を構成する方法について説明します。

  - 「[ソフトウェア インストール (Dell R340)](#software-installation-dell-r340)」では、Defender for IoT センサー ソフトウェアをインストールするために必要な手順について説明します。

### <a name="dell-poweredge-r340xl-requirements"></a>Dell PowerEdge R340XL の要件 

Dell PowerEdge R340XL アプライアンスをインストールするには、次のものが必要です。

- Dell Remote Access Controller (iDrac) の Enterprise ライセンス

- BIOS 構成 XML

- サーバー ファームウェアのバージョン:

  - BIOS バージョン 2.1.6

  - iDrac バージョン 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Dell PowerEdge R340 の前面パネル

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Dell PowerEdge R340 の前面パネル。":::

 1. 左側のコントロール パネル 
 1. 光学式ドライブ (省略可能) 
 1. 右コントロール パネル 
 1. 情報タグ 
 1. ドライブ  

### <a name="dell-poweredge-r340-back-panel"></a>Dell PowerEdge R340 の背面パネル

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Dell PowerEdge R340 の背面パネル。":::

1. シリアル ポート 
1. NIC ポート (Gb 1) 
1. NIC ポート (Gb 1) 
1. ハーフハイトの PCIe 
1. フルハイトの PCIe 拡張カード スロット 
1. 電源装置ユニット 1 
1. 電源装置ユニット 2 
1. システム ID 
1. システム状態インジケーター ケーブル ポート (CMA) ボタン 
1. USB 3.0 ポート (2) 
1. iDRAC9 専用ネットワーク ポート 
1. VGA ポート 

### <a name="dell-bios-configuration"></a>Dell BIOS 構成

Dell アプライアンスを調整してソフトウェアで動作するようにするために、Dell BIOS 構成が必要です。

BIOS 構成は、定義済みの構成を使用して実行します。 このファイルには、[ヘルプ センター](https://help.cyberx-labs.com/)からアクセスできます。

構成ファイルを Dell アプライアンスにインポートします。 構成ファイルを使用する前に、Dell アプライアンスと管理コンピューター間の通信を確立する必要があります。

Dell アプライアンスは、ライフサイクル コントローラー (LC) を備えた統合 iDRAC によって管理されます。 LC は、すべての Dell PowerEdge サーバーに組み込まれており、Dell PowerEdge アプライアンスのデプロイ、更新、監視、保守に役立つ機能を提供します。

Dell アプライアンスと管理コンピューター間の通信を確立するには、同じサブネットで iDRAC の IP アドレスと管理コンピューターの IP アドレスを定義する必要があります。

接続が確立されると、BIOS が構成可能になります。

Dell BIOS を構成するには:

1. [iDRAC の IP アドレスを構成する](#configure-idrac-ip-address)

1. [BIOS 構成ファイルをインポートする](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>iDRAC の IP アドレスを構成する

1. センサーの電源を入れます。

1. OS が既にインストールされている場合は、F2 キーを選択して BIOS 構成に入ります。

1. **[iDRAC 設定]** を選択します。

1. **[ネットワーク]** を選択します。

   > [!NOTE]
   > インストール中に、次の手順で言及されている既定の iDRAC IP アドレスとパスワードを構成する必要があります。 インストール後に、これらの定義を変更します。

1. 静的 IPv4 アドレスを **10.100.100.250** に変更します。

1. 静的サブネット マスクを **255.255.255.0** に変更します。

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="静的サブネット マスクを示すスクリーンショット。":::

1. **[戻る]**  >  **[終了]** を選択します。

#### <a name="import-the-bios-configuration-file"></a>BIOS 構成ファイルをインポートする

この記事では、構成ファイルを使用して BIOS を構成する方法について説明します。

1. 静的 IP アドレス **10.100.100.200** が事前に構成された PC を **iDRAC** ポートに接続します。

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="事前に構成された IP アドレス ポートのスクリーンショット。":::

1. ブラウザーを開き、「**10.100.100.250**」と入力して iDRAC Web インターフェイスに接続します。

1. Dell の既定の管理者特権でサインインします。

   - ユーザー名: **root**

   - パスワード: **calvin**

1. アプライアンスの資格情報は次のとおりです。

   - ユーザー名:**XXX**

   - Password (パスワード):**XXX**

     サーバー プロファイルのインポート操作が開始されます。

     > [!NOTE]
     > ファイルをインポートする前に、次のことを確認します。
     > - 自分が現在 iDRAC に接続している唯一のユーザーである。
     > - システムが BIOS メニューにない。

1. **[構成]**  >  **[Server Configuration Profile]\(サーバー構成プロファイル\)** に移動します。 次のパラメーターを設定します。

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="サーバー プロファイルの構成を示すスクリーンショット。":::

   | パラメーター | 構成 |
   |--|--|
   | 場所の種類 | **[ローカル]** を選択します。 |
   | ファイル パス | **[ファイルの選択]** を選択し、構成 XML ファイルを追加します。 |
   | [Import Components]\(インポート コンポーネント\) | **[BIOS]、[NIC]、[RAID]** を選択します。 |
   | 最大待機時間 | **[20 分]** を選択します。 |

1. **[インポート]** を選択します。

1. プロセスを監視するには、 **[メンテナンス]**  >  **[ジョブ キュー]** に移動します。

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="[ジョブ キュー] を示すスクリーンショット。":::

#### <a name="manually-configuring-bios"></a>BIOS の手動構成 

次の場合、アプライアンスの BIOS を手動で構成する必要があります。

- Arrow からアプライアンスを購入しなかった。

- アプライアンスはあるが、XML 構成ファイルへのアクセス権がない。

BIOS にアクセスした後、 **[デバイス設定]** に移動します。

手動で構成するには:

1. キーボードとスクリーンを使用してアプライアンス BIOS に直接アクセスするか、iDRAC を使用します。

   - アプライアンスが Defender for IoT アプライアンスではない場合は、ブラウザーを開き、前に構成した IP アドレスに移動します。 Dell の既定の管理者特権でサインインします。 ユーザー名に **root**、パスワードに **calvin** を使用します。

   - アプライアンスが Defender for IoT アプライアンスの場合は、ユーザー名に **XXX**、パスワードに **XXX** を使用してサインインします。

1. BIOS にアクセスした後、 **[デバイス設定]** に移動します。

1. **[Integrated RAID controller 1:Dell PERC\<PERC H330 Adapter\> Configuration Utility]** を選択して、RAID 制御構成を選択します。

1. **[構成管理]** を選択します。

1. **[Create Virtual Disk]\(仮想ディスクの作成\)** を選択します。

1. **[Select RAID Level]\(RAID レベルの選択\)** フィールドで **[RAID5]** を選択します。 **[Virtual Disk Name]\(仮想ディスク名\)** フィールドに「**ROOT**」と入力し、 **[Physical Disks]\(物理ディスク\)** を選択します。

1. **[すべて選択]** を選択し、 **[変更の適用]** を選択します。

1. **[OK]** を選択します。

1. 下にスクロールし、 **[Create Virtual Disk]\(仮想ディスクの作成\)** を選択します。

1. **[確認]** チェック ボックスをオンにし、 **[はい]** を選択します。

1. **[OK]** を選択します。

1. メイン画面に戻り、 **[System BIOS]\(システム BIOS\)** を選択します。

1. **[Boot Settings]\(ブート設定\)** を選択します。

1. **[Boot Mode]\(ブート モード\)** オプションで **[BIOS]** を選択します。

1. **[戻る]** を選択し、 **[完了]** を選択して BIOS 設定を終了します。

### <a name="software-installation-dell-r340"></a>ソフトウェア インストール (Dell R340)

インストール プロセスは約 20 分かかります。 インストール後、システムが数回再起動されます。

インストールするには、次のようにします。

1. 次のいずれかの方法で、バージョン メディアがアプライアンスにマウントされていることを確認します。

   - リリースを含む外部 CD またはキーのディスクを接続します。

   - iDRAC を使用して ISO イメージをマウントします。 iDRAC にサインインした後、仮想コンソールを選択し、次に **[Virtual Media]\(仮想メディア\)** を選択します。

1. **[Map CD/DVD]\(CD/DVD のマップ\)** セクションで、 **[ファイルの選択]** を選択します。

1. 表示されるダイアログ ボックスから、このバージョンのバージョン ISO イメージ ファイルを選択します。

1. **[Map Device]\(デバイスのマップ\)** ボタンを選択します。

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="マップされたデバイスを示すスクリーンショット。":::

1. メディアがマウントされます。 **[閉じる]** を選択します。

1. アプライアンスを起動します。 iDRAC を使用する場合、 **[Consul Control]\(Consul の制御\)** ボタンを選択してサーバーを再起動できます。 次に、 **[Keyboard Macros]\(キーボード マクロ\)** で、 **[適用]** ボタンを選択します。これにより、Ctrl + Alt + Delete シーケンスが開始されます。

1. **[English]\(英語\)** を選択します。

1. **[SENSOR-RELEASE-\<version\> Enterprise]** を選択します。

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="バージョン選択を示すスクリーンショット。":::   

1. アプライアンス プロファイルとネットワークのプロパティを定義します。

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="アプライアンス プロファイルを示すスクリーンショット。":::   

   | パラメーター | 構成 |
   |--|--|
   | **ハードウェア プロファイル** | **enterprise** |
   | **管理インターフェイス** | **eno1** |
   | **ネットワーク パラメーター (お客様が指定)** | - |
   |**管理ネットワークの IP アドレス:** | - |
   | **サブネット マスク:** | - |
   | **アプライアンスのホスト名:** | - |
   | **DNS:** | - |
   | **既定のゲートウェイ IP アドレス:** | - |
   | **入力インターフェイス:** |  入力インターフェイスの一覧がシステムによって生成されます。 入力インターフェイスをミラー化するには、一覧に表示されているすべての項目をコンマ区切り記号を使用してコピーします。 ブリッジ インターフェイスを構成する必要はありません。 このオプションは、特別なユース ケースにのみ使用されます。 |

1. 約 10 分後に、2 つの資格情報のセットが表示されます。 1 つは **CyberX** ユーザーに関するもので、もう 1 つは **Support** ユーザーに関するものです。  

1. アプライアンス ID とパスワードを保存します。 これらの資格情報は、初めてプラットフォームを使用するときにアクセスするために必要です。

1. **Enter** キーを選択して続行します。

## <a name="hpe-proliant-dl20-installation"></a>HPE ProLiant DL20 のインストール

この記事では、HPE ProLiant DL20 のインストール プロセスについて説明します。これには次の手順が含まれます。

  - リモート アクセスを有効にし、既定の管理者パスワードを更新する。
  - BIOS と RAID の設定を構成する。
  - ソフトウェアをインストールする。

### <a name="about-the-installation"></a>インストールについて

  - Enterprise および SMB アプライアンスをインストールできます。 インストール プロセスは、アレイ構成を除き、どちらのアプライアンスの種類でも同じです。
  - 既定の管理ユーザーが提供されます。 ネットワーク構成プロセス中にパスワードを変更することをお勧めします。
  - ネットワーク構成プロセス中に、ネットワーク ポート 1 で iLO ポートを構成します。
  - インストール プロセスは約 20 分かかります。 インストール後、システムが数回再起動されます。

### <a name="hpe-proliant-dl20-front-panel"></a>HPE ProLiant DL20 の前面パネル

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="HPE ProLiant DL20 の前面パネル。":::

### <a name="hpe-proliant-dl20-back-panel"></a>HPE ProLiant DL20 の背面パネル

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="HPE ProLiant DL20 の背面パネル。":::

### <a name="enable-remote-access-and-update-the-password"></a>リモート アクセスを有効にし、パスワードを更新する

次の手順に使用して、ネットワーク オプションを設定し、既定のパスワードを更新します。

パスワードを有効にして更新するには:

1. スクリーンとキーボードを HP アプライアンスに接続し、アプライアンスの電源を入れ、**F9** キーを押します。

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="HPE ProLiant のウィンドウを示すスクリーンショット。":::

1. **[システム ユーティリティ]**  >  **[システム構成]**  >  **[iLO 5 Configuration Utility]\(iLO 5 構成ユーティリティ\)**  >  **[ネットワーク オプション]** に移動します。

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="[システム構成] ウィンドウを示すスクリーンショット。":::

    1.  **[Network Interface Adapter]\(ネットワーク インターフェイス アダプター\)** フィールドから **[Shared Network Port-LOM]\(共有ネットワーク ポート - LOM\)** を選択します。
    
    1.  DHCP を無効にします。
    
    1.  IP アドレス、サブネット マスク、ゲートウェイ IP アドレスを入力します。

1. **F10(保存)** キーを選択します。

1. **Esc** キーを選択して **[iLO 5 Configuration Utility]\(iLO 5 構成ユーティリティ\)** に戻り、 **[ユーザー管理]** を選択します。

1. **[Edit/Remove User]\(ユーザーの編集/削除\)** を選択します。 管理者は、定義されている唯一の既定ユーザーです。 

1. 既定のパスワードを変更し、**F10 (保存)** キーを選択します。

### <a name="configure-the-hpe-bios"></a>HPE BIOS を構成する

次の手順では、Enterprise および SMB アプライアンスの HPE BIOS を構成する方法について説明します。

HPE BIOS を構成するには:

1. **[システム ユーティリティ]**  >  **[システム構成]**  >  **[BIOS/Platform Configuration (RBSU)]\(BIOS/プラットフォーム構成 (RBSU)\)** を選択します。

1. **[BIOS/Platform Configuration (RBSU)]\(BIOS/プラットフォーム構成 (RBSU)\)** フォームで **[Boot Options]\(ブート オプション\)** を選択します。

1. **[Boot Mode]\(ブート モード\)** を **[Legacy BIOS Mode]\(レガシ BIOS モード\)** に変更し、**F10(保存)** キーを選択します。

1. **Esc** キーを 2 回選択して、 **[システム構成]** フォームを閉じます。

#### <a name="for-the-enterprise-appliance"></a>Enterprise アプライアンスの場合

1. **[Embedded RAID 1:HPE Smart Array P408i-a SR Gen 10]\(内蔵型 RAID 1: HPE スマート アレイ P408i-a SR Gen 10\)**  >  **[Array Configuration]\(アレイ構成\)**  >  **[Create Array]\(アレイの作成\)** を選択します。

1. **[Create Array]\(アレイの作成\)** フォームで、すべてのオプションを選択します。 **Enterprise** アプライアンスでは、3 つのオプションを使用できます。

#### <a name="for-the-smb-appliance"></a>SMB アプライアンスの場合

1. **[Embedded RAID 1:HPE Smart Array P208i-a SR Gen 10]\(内臓型 RAID 1: HPE スマート アレイ P208i-a SR Gen 10\)**  >  **[Array Configuration]\(アレイ構成\)**  >  **[Create Array]\(アレイの作成\)** を選択します。

1. **[Proceed to Next Form]\(次のフォームに進む\)** を選択します。

1. **[Set RAID Level]\(RAID レベルの設定\)** フォームで、Enterprise デプロイの場合はレベルを **[RAID 5]** に、SMB デプロイの場合は **[RAID 1]** に設定します。

1. **[Proceed to Next Form]\(次のフォームに進む\)** を選択します。

1. **[Logical Drive Label]\(論理ドライブのラベル\)** フォームで「**Logical Drive 1**」と入力します。

1. **[Submit Changes]\(変更内容の送信\)** を選択します。

1. **[送信]** フォームで **[Back to Main Menu]\(メイン メニューに戻る\)** を選択します。

1. **F10(保存)** キーを選択し、**Esc** キーを 2 回押します。

1. **[システム ユーティリティ]** ウィンドウで **[One-Time Boot Menu]\(ワンタイム ブート メニュー\)** を選択します。

1. **[One-Time Boot Menu]\(ワンタイム ブート メニュー\)** フォームで **[Legacy BIOS One-Time Boot Menu]\(レガシ BIOS ワンタイム ブート メニュー\)** を選択します。

1. **[Booting in Legacy]\(レガシでブート\)** および **[Boot Override]\(ブート オーバーライド\)** ウィンドウが表示されます。 ブート オーバーライド オプションを選択します。たとえば、CD-ROM、USB、HDD、UEFI シェルなどです。

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="最初の [Boot Override]\(ブート オーバーライド\) ウィンドウを示すスクリーンショット。":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="2 番目の [Boot Override]\(ブート オーバーライド\) ウィンドウを示すスクリーンショット。":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>ソフトウェアのインストール (HPE ProLiant DL20 アプライアンス)

インストール プロセスは約 20 分かかります。 インストール後、システムが数回再起動されます。

ソフトウェアをインストールするには:

1. スクリーンとキーボードをアプライアンスに接続し、次に CLI に接続します。

1. Defender for IoT ポータルの **[更新]** ページからダウンロードした ISO イメージを含む外部 CD またはキーのディスクを接続します。

1. アプライアンスを起動します。

1. **[English]\(英語\)** を選択します。

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="CLI ウィンドウで [English]\(英語\) を選択。":::

1. **[SENSOR-RELEASE-<version> Enterprise]** を選択します。

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="バージョンを選択するための画面のスクリーンショット。":::

1. インストール ウィザードで、アプライアンス プロファイルとネットワークのプロパティを定義します。

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="インストール ウィザードを示すスクリーンショット。":::

    | パラメーター | 構成 |
    | ----------| ------------- |
    | **ハードウェア プロファイル** | SMB デプロイの場合、 **[Enterprise]** または **[Office]** を選択します。 |
    | **管理インターフェイス** | **eno2** |
    | **既定のネットワーク パラメーター (通常、パラメーターはお客様が指定します)** | **管理ネットワークの IP アドレス:** <br/> <br/>**アプライアンスのホスト名:** <br/>**DNS:** <br/>**既定のゲートウェイ IP アドレス:**|
    | **入力インターフェイス:** | 入力インターフェイスの一覧がシステムによって生成されます。<br/><br/>入力インターフェイスをミラー化するには、一覧に表示されているすべての項目をコンマ区切り記号を使用してコピーします: **eno5, eno3, eno1, eno6, eno4**<br/><br/>**HPE DL20 の場合:eno1、enp1s0f4u4 (iLo インターフェイス) は一覧に含めないでください**<br/><br/>**ブリッジ**:ブリッジ インターフェイスを構成する必要はありません。 このオプションは、特別なユース ケースにのみ使用されます。 **Enter** キーを押して続行します。 |

1. 約 10 分後に、2 つの資格情報のセットが表示されます。 1 つは **CyberX** ユーザーに関するもので、もう 1 つは **Support** ユーザーに関するものです。

1. アプライアンスの ID とパスワードを保存します。 プラットフォームに初めてアクセスするときに、この資格情報が必要です。

1. **Enter** キーを選択して続行します。

## <a name="hpe-proliant-dl360-installation"></a>HPE ProLiant DL360 のインストール

  - 既定の管理ユーザーが提供されます。 ネットワーク構成中にパスワードを変更することをお勧めします。

  - ネットワーク構成中に、iLO ポートを構成します。

  - インストール プロセスは約 20 分かかります。 インストール後、システムが数回再起動されます。

### <a name="hpe-proliant-dl360-front-panel"></a>HPE ProLiant DL360 の前面パネル

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="HPE ProLiant DL360 の前面パネル。":::

### <a name="hpe-proliant-dl360-back-panel"></a>HPE ProLiant DL360 の背面パネル

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="HPE ProLiant DL360 の背面パネル。":::

### <a name="enable-remote-access-and-update-the-password"></a>リモート アクセスを有効にし、パスワードを更新する

HPE ProLiant DL20 のインストールについては、前のセクションを参照してください。

  - 「リモート アクセスを有効にし、パスワードを更新する」

  - 「HPE BIOS を構成する」

Enterprise 構成は同じです。

> [!Note]
> アレイ フォームで、すべてのオプションが選択されていることを確認します。

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>iLO リモート インストール (仮想ドライブから)

この手順では、仮想ドライブからの iLO のインストールについて説明します。

インストールするには、次のようにします。

1. iLO コンソールにサインインし、サーバーの画面を右クリックします。

1. **[HTML5 Console]\(HTML5 コンソール\)** を選択します。

1. コンソールで、[CD] アイコンを選択し、[CD/DVD] オプションを選択します。

1. **[Local ISO file]\(ローカル ISO ファイル\)** を選択します。

1. ダイアログ ボックスで、関連する ISO ファイルを選択します。

1. 左側のアイコンに移動し、 **[Power]\(電源\)** を選択し、 **[リセット]** を選択します。

1. アプライアンスが再起動し、センサーのインストール プロセスが実行されます。

### <a name="software-installation-hpe-dl360"></a>ソフトウェア インストール (HPE DL360)

インストール プロセスは約 20 分かかります。 インストール後、システムが数回再起動されます。

インストールするには、次のようにします。

1. スクリーンとキーボードをアプライアンスに接続し、次に CLI に接続します。

1. Defender for IoT ポータルの **[更新]** ページからダウンロードした ISO イメージを含む外部 CD またはキーのディスクを接続します。

1. アプライアンスを起動します。

1. **[English]\(英語\)** を選択します。

1. **[SENSOR-RELEASE-<version> Enterprise]** を選択します。

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="バージョンの選択を示すスクリーンショット。":::

1. インストール ウィザードで、アプライアンス プロファイルとネットワークのプロパティを定義します。

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="インストール ウィザードを示すスクリーンショット。":::

    | パラメーター | 構成 |
    | ----------| ------------- |
    | **ハードウェア プロファイル** | **[corporate]** を選択します。 |
    | **管理インターフェイス** | **eno2** |
    | **既定のネットワーク パラメーター (お客様が指定)** | **管理ネットワークの IP アドレス:** <br/>**サブネット マスク:** <br/>**アプライアンスのホスト名:** <br/>**DNS:** <br/>**既定のゲートウェイ IP アドレス:**|
    | **入力インターフェイス:**  | 入力インターフェイスの一覧がシステムによって生成されます。<br/><br/>入力インターフェイスをミラー化するには、一覧に表示されているすべての項目をコンマ区切り記号を使用してコピーします。<br/><br/> ブリッジ インターフェイスを構成する必要はありません。 このオプションは、特別なユース ケースにのみ使用されます。 |

1. 約 10 分後に、2 つの資格情報のセットが表示されます。 1 つは **CyberX** ユーザーに関するもので、もう 1 つは **support** ユーザーに関するものです。

1. アプライアンスの ID とパスワードを保存します。 プラットフォームに初めてアクセスするときに、これらの資格情報が必要です。

1. **Enter** キーを選択して続行します。

## <a name="sensor-installation-for-the-virtual-appliance"></a>仮想アプライアンスのセンサーのインストール

次のアーキテクチャで、Defender for IoT センサー用の仮想マシンをデプロイできます。


| アーキテクチャ | 仕様 | 使用方法 | 説明 |
|---|---|---|---|
| **エンタープライズ** | CPU: 8<br/>メモリ:32G RAM<br/>HDD:1800 GB | 運用環境 | 既定かつ最も一般的 |
| **小規模企業** | CPU: 4 <br/>メモリ:8G RAM<br/>HDD:500 GB | テストまたは小規模な運用環境 | -  |
| **Office** | CPU: 4<br/>メモリ:8G RAM<br/>HDD:100 GB | 小規模なテスト環境 | -  |

### <a name="prerequisites"></a>前提条件

オンプレミス管理コンソールでは、VMware と Hyper-V の両方のデプロイ オプションがサポートされます。 インストールを開始する前に、次の項目が満たされていることを確認してください。

  - VMware (ESXi 5.5 以降) または Hyper-V ハイパーバイザー (Windows 10 Pro または Enterprise) がインストールされ、動作している

  - 仮想マシンに使用できるハードウェア リソース

  - Azure Defender for IoT センサーの ISO インストール ファイル

ハイパーバイザーが実行されていることを確認します。

### <a name="create-the-virtual-machine-esxi"></a>仮想マシンを作成する (ESXi)

1. ESXi にサインインし、関連する **データストア** を選択して、 **[Datastore Browser]\(データストア ブラウザー\)** を選択します。

1. イメージを **アップロード** し、 **[閉じる]** を選択します。

1. **[仮想マシン]** に移動してから、 **[VM の作成/登録]** を選択します。

1. **[新しい仮想マシンの作成]** を選択してから、 **[次へ]** を選択します。

1. センサー名を追加し、次のものを選択します。

   - 互換性: **&lt;最新の ESXi バージョン&gt;**

   - ゲスト OS ファミリ:**Linux**

   - ゲスト OS バージョン:**Ubuntu Linux (64 ビット)**

1. **[次へ]** を選択します。

1. 関連するデータストアを選択し、 **[次へ]** を選択します。

1. 必要なアーキテクチャに応じて、仮想ハードウェアのパラメーターを変更します。

1. **[CD/DVD Drive 1]\(CD/DVD ドライブ 1\)** については、 **[Datastore ISO file]\(データストア ISO ファイル\)** を選択し、前にアップロードした ISO ファイルを選択します。

1. **[次へ]**  >  **[完了]** の順に選択します。

### <a name="create-the-virtual-machine-hyper-v"></a>仮想マシンを作成する (Hyper-V)

この手順では、Hyper-V を使用して仮想マシンを作成する方法について説明します。

仮想マシンを作成するには:

1. Hyper-V マネージャーで仮想ディスクを作成します。

1. **[format = VHDX]\(形式 = VHDX\)** を選択します。

1. **[type = Dynamic Expanding]\(種類 = 容量可変\)** を選択します。

1. VHD の名前と場所を入力します。

1. (アーキテクチャに応じて) 必要なサイズを入力します。   

1. 概要を確認し、 **[完了]** を選択します。

1. **[アクション]** メニューで、新しい仮想マシンを作成します。

1. 仮想マシンの名前を入力します。

1. **[世代の指定]**  >  **[第 1 世代]** を選択します。

1. (アーキテクチャに応じて) メモリ割り当てを指定し、動的メモリのチェック ボックスをオンにします。

1. サーバーのネットワーク トポロジに応じて、ネットワーク アダプターを構成します。

1. 以前に作成した VHDX を仮想マシンに接続します。

1. 概要を確認し、 **[完了]** を選択します。

1. 新しい仮想マシンを右クリックし、 **[設定]** を選択します。

1. **[ハードウェアの追加]** 選択し、新しいネットワーク アダプターを追加します。

1. センサー管理ネットワークに接続する仮想スイッチを選択します。

1. (アーキテクチャに応じて) CPU リソースを割り当てます。

1. 管理コンソールの ISO イメージを仮想 DVD ドライブに接続します。

1. 仮想マシンを開始します。

2. **[アクション]** メニューで、 **[接続]** を選択してソフトウェアのインストールを続行します。

### <a name="software-installation-esxi-and-hyper-v"></a>ソフトウェア インストール (ESXi および Hyper-V)

このセクションでは、ESXi および Hyper-V ソフトウェアのインストールについて説明します。

インストールするには、次のようにします。

1. 仮想マシン コンソールを開きます

1. VM が ISO イメージから起動し、[言語の選択] 画面が表示されます。 **[English]\(英語\)** を選択します。

1. 必要なアーキテクチャを選択します。

1. アプライアンス プロファイルとネットワークのプロパティを定義します。

    | パラメーター | 構成 |
    | ----------| ------------- |
    | **ハードウェア プロファイル** | &lt;必要なアーキテクチャ&gt; |
    | **管理インターフェイス** | **ens192** |
    | **ネットワーク パラメーター (お客様が指定)** | **管理ネットワークの IP アドレス:** <br/>**サブネット マスク:** <br/>**アプライアンスのホスト名:** <br/>**DNS:** <br/>**既定のゲートウェイ:** <br/>**入力インターフェイス:**|
    | **ブリッジ インターフェイス:** | ブリッジ インターフェイスを構成する必要はありません。 このオプションは、特別なユース ケース専用です。 |

1. **Y** を入力して設定を受け入れます。

1. サインイン資格情報が自動的に生成され、表示されます。 ユーザー名とパスワードは、サインインと管理に必要であるため、安全な場所にコピーしてください。

      - **Support**:ユーザー管理のための管理ユーザー。

      - **CyberX**:アプライアンスにアクセスするための root に相当します。

1. アプライアンスが再起動されます。

1. 以前に構成した IP アドレス `https://ip_address` を使用して管理コンソールにアクセスします。

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="管理コンソールへのアクセスを示すスクリーンショット。":::

## <a name="virtual-appliance-on-premises-management-console-installation"></a>仮想アプライアンス:オンプレミス管理コンソールのインストール

オンプレミス管理コンソール VM では、次のアーキテクチャがサポートされます。

| アーキテクチャ | 仕様 | 使用方法 | 
|--|--|--|
| Enterprise <br/>(既定かつ最も一般的) | CPU: 8 <br/>メモリ:32G RAM<br/> HDD:1.8 TB | 大規模な運用環境 | 
| Enterprise | CPU: 4 <br/> メモリ:8G RAM<br/> HDD:500 GB | 大規模な運用環境 |
| Enterprise | CPU: 4 <br/>メモリ:8G RAM <br/> HDD:100 GB | 小規模なテスト環境 | 
   
### <a name="prerequisites"></a>前提条件

オンプレミス管理コンソールでは、VMware と Hyper-V の両方のデプロイ オプションがサポートされます。 インストールを開始する前に、次のことを確認します。

- VMware (ESXi 5.5 以降) または Hyper-V ハイパーバイザー (Windows 10 Pro または Enterprise) がインストールされていて、動作している。

- 仮想マシンに対してハードウェア リソースを使用できる。

- オンプレミス管理コンソールの ISO インストール ファイルがある。
    
- ハイパーバイザーが実行されている。

### <a name="create-the-virtual-machine-esxi"></a>仮想マシンを作成する (ESXi)

仮想マシンを作成するには (ESXi):

1. ESXi にサインインし、関連する **データストア** を選択して、 **[Datastore Browser]\(データストア ブラウザー\)** を選択します。

1. イメージをアップロードし、 **[閉じる]** を選択します。

1. **[仮想マシン]** に移動します。

1. **[VM の作成/登録]** を選択します。

1. **[新しい仮想マシンの作成]** を選択し、 **[次へ]** を選択します。

1. センサー名を追加し、次のものを選択します。

   - 互換性: \<latest ESXi version>

   - ゲスト OS ファミリ:Linux

   - ゲスト OS バージョン:Ubuntu Linux (64 ビット)

1. **[次へ]** を選択します。

1. 関連するデータストアを選択し、 **[次へ]** を選択します。

1. 必要なアーキテクチャに応じて、仮想ハードウェアのパラメーターを変更します。

1. **[CD/DVD Drive 1]\(CD/DVD ドライブ 1\)** については、 **[Datastore ISO file]\(データストア ISO ファイル\)** を選択し、前にアップロードした ISO ファイルを選択します。

1. **[次へ]**  >  **[完了]** の順に選択します。

### <a name="create-the-virtual-machine-hyper-v"></a>仮想マシンを作成する (Hyper-V)

Hyper-V を使用して仮想マシンを作成するには:

1. Hyper-V マネージャーで仮想ディスクを作成します。

1. 形式として **[VHDX]** を選択します。

1. **[次へ]** を選択します。

1. 種類として **[容量可変]** を選択します。

1. **[次へ]** を選択します。

1. VHD の名前と場所を入力します。

1. **[次へ]** を選択します。

1. (アーキテクチャに応じて) 必要なサイズを入力します。

1. **[次へ]** を選択します。

1. 概要を確認し、 **[完了]** を選択します。

1. **[アクション]** メニューで、新しい仮想マシンを作成します。

1. **[次へ]** を選択します。

1. 仮想マシンの名前を入力します。

1. **[次へ]** を選択します。

1. **[世代]** を選択し、 **[第 1 世代]** に設定します。

1. **[次へ]** を選択します。

1. (アーキテクチャに応じて) メモリ割り当てを指定し、動的メモリのチェック ボックスをオンにします。

1. **[次へ]** を選択します。

1. サーバーのネットワーク トポロジに応じて、ネットワーク アダプターを構成します。

1. **[次へ]** を選択します。

1. 以前に作成した VHDX を仮想マシンに接続します。

1. **[次へ]** を選択します。

1. 概要を確認し、 **[完了]** を選択します。

1. 新しい仮想マシンを右クリックし、 **[設定]** を選択します。

1. **[ハードウェアの追加]** を選択し、 **[ネットワーク アダプター]** で新しいアダプターを追加します。

1. **[仮想スイッチ]** で、センサー管理ネットワークに接続するスイッチを選択します。

1. (アーキテクチャに応じて) CPU リソースを割り当てます。

1. 管理コンソールの ISO イメージを仮想 DVD ドライブに接続します。

1. 仮想マシンを開始します。

1. **[アクション]** メニューで、 **[接続]** を選択してソフトウェアのインストールを続行します。

### <a name="software-installation-esxi-and-hyper-v"></a>ソフトウェア インストール (ESXi および Hyper-V)

仮想マシンを起動すると、ISO イメージからインストール プロセスが開始されます。 セキュリティを強化するために、オンプレミスの管理コンソールに 2 つ目のネットワーク インターフェイスを作成できます。 1 つのネットワーク インターフェイスはユーザー専用で、ルーティングされたネットワークのゲートウェイの構成をサポートできます。 2 つ目のネットワーク インターフェイスは、IP アドレス範囲内のすべての接続されたセンサー専用です。

どちらのネットワーク インターフェイスでもユーザー インターフェイス (UI) が有効になっており、UI でサポートされているすべての機能は、ルーティングが不要であればセカンダリ ネットワーク インターフェイスで使用できるようになります。 高可用性は、セカンダリ ネットワーク インターフェイスで実行されます。

セカンダリ ネットワーク インターフェイスをデプロイしないことを選択した場合は、すべての機能がプライマリ ネットワーク インターフェイスを介して使用できるようになります。 

ソフトウェアをインストールするには:

1. **[English]\(英語\)** を選択します。

1. デプロイに必要なアーキテクチャを選択します。

1. センサー管理ネットワークのネットワーク インターフェイスを定義します (インターフェイス、IP、サブネット、DNS サーバー、既定のゲートウェイ)。

1. (省略可能) オンプレミスの管理コンソールに 2 つ目のネットワーク インターフェイスを追加します。

    1. `Please type sensor monitoring interface (Optional. Applicable when sensors are on a different network segment. For more information see the Installation instructions): <name of interface>`
    
    1. `Please type an IP address for the sensor monitoring interface (accessible by the sensors): <ip address>`
    
    1. `Please type a subnet mask for the sensor monitoring interface (accessible by the sensors): <subnet>`

1. サインイン資格情報が自動的に生成され、表示されます。 これらの資格情報は、サインインと管理に必要であるため、安全な場所に保管してください。

    | ユーザー名 | 説明 |
    |--|--|
    | サポート | ユーザー管理のための管理ユーザー。 |
    | CyberX | アプライアンスにアクセスするための root に相当します。 |

1. アプライアンスが再起動されます。

1. 以前に構成した IP アドレス `<https://ip_address>` を使用して管理コンソールにアクセスします。

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="管理コンソールのサインイン画面を示すスクリーンショット。":::

## <a name="post-installation-validation"></a>インストール後の検証

物理アプライアンスのインストールを検証するには、多くのテストを実行する必要があります。 すべての種類のアプライアンスに同じ検証プロセスが適用されます。

GUI または CLI を使用して検証を実行します。 検証は、ユーザー **Support** とユーザー **CyberX** が使用できます。

インストール後の検証には、次のテストを含める必要があります。

  - **サニティ テスト**:システムが実行されていることを確認します。

  - **バージョン**:バージョンが正しいことを確認します。

  - **ifconfig**:インストール プロセス中に構成されたすべての入力インターフェイスが実行されていることを確認します。

### <a name="checking-system-health-by-using-the-gui"></a>GUI を使用してシステムの正常性を確認する

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="システム正常性チェックを示すスクリーンショット。":::

#### <a name="sanity"></a>サニティ

- **アプライアンス**:アプライアンスのサニティ チェックを実行します。 CLI コマンド `system-sanity` を使用して、同じチェックを実行できます。

- **バージョン**:アプライアンスのバージョンが表示されます。

- **ネットワーク プロパティ**:センサー ネットワークのパラメーターが表示されます。

#### <a name="redis"></a>Redis

- **メモリ**:使用されたメモリの量や残りの量など、メモリ使用量の全体像が示されます。

- **[Longest Key]\(最長キー\)** :過度なメモリ使用の原因となる可能性のある最長キーが表示されます。

#### <a name="system"></a>システム

- **[Core Log]\(コア ログ\)** :コア ログの最後の 500 行が表示されます。これにより、システム ログ全体をエクスポートすることなく、最近のログ行を確認できます。

- **タスク マネージャー**:プロセスのテーブルに表示されるタスクを次のレイヤーに変換します。 
  
  - 永続レイヤー (Redis) 
  - キャッシュ レイヤー (SQL)

- **[Network Statistics]\(ネットワークの統計\)** :ネットワークの統計情報が表示されます。

- **TOP**:プロセスの表が表示されます。 これは、実行中のシステムを動的にリアルタイムに表示する Linux コマンドです。

- **[Backup Memory Check]\(バックアップ メモリ チェック\)** :バックアップ メモリの状態が示されます。次のことを確認します。
  - バックアップ フォルダーの場所 
  - バックアップ フォルダーのサイズ
  - バックアップ フォルダーの制限事項
  - 前回のバックアップがいつ実行されたか
  - 追加のバックアップ ファイル用の空き領域の容量

- **ifconfig**:アプライアンスの物理インターフェイスのパラメーターが表示されます。

- **CyberX nload**:6 秒のテストを使用して、ネットワーク トラフィックと帯域幅が表示されます。

- **[Errors from Core, log]\(コア ログからのエラー\)** :コア ログ ファイルからのエラーが表示されます。

ツールにアクセスするには:

1. **Support** ユーザーの資格情報を使用してセンサーにサインインします。

1. **[システム設定]** ウィンドウで **[System Statistics]\(システムの統計\)** を選択します。

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>CLI を使用してシステムの正常性を確認する

**テスト 1:サニティ**

システムが稼働していることを確認します。

1. Linux ターミナル (たとえば、PuTTY) とユーザー **Support** を使用して CLI に接続します。

1. 「`system sanity`」と入力します。

1. すべてのサービスが緑色 (実行中) であることを確認します。

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="実行中のサービスを示すスクリーンショット。":::

1. **[System is UP! (prod)]\(システムは稼働中です (prod)\)** が下部に表示されることを確認します。

**テスト 2:バージョン チェック**

正しいバージョンが使用されていることを確認します。

1. Linux ターミナル (たとえば、PuTTY) とユーザー **Support** を使用して CLI に接続します。

1. 「`system version`」と入力します。

1. 正しいバージョンが表示されることを確認します。

**テスト 3:ネットワークの検証**

インストール プロセス中に構成されたすべての入力インターフェイスが実行されていることを確認します。

1. Linux ターミナル (たとえば、PuTTY) とユーザー **Support** を使用して CLI に接続します。

1. `network list` (Linux コマンド `ifconfig` と同等) を入力します。

1. 必要な入力インターフェイスが表示されることを確認します。 たとえば、2 つの Quad Copper NIC がインストールされている場合、一覧に 10 個のインターフェイスが表示されるはずです。

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="インターフェイスの一覧を示すスクリーンショット。":::

**テスト 4:UI への管理アクセス**

コンソール Web GUI にアクセスできることを確認します。

1. イーサネット ケーブルを使用して、ラップトップを管理ポート (**Gb1**) に接続します。

1. ラップトップの NIC のアドレスがアプライアンスと同じ範囲内になるように定義します。

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="UI への管理アクセスを示すスクリーンショット。":::

1. ラップトップからアプライアンスの IP アドレスに対して ping を実行し、接続を確認します (既定:10.100.10.1)。

1. ラップトップで Chrome ブラウザーを開き、アプライアンスの IP アドレスを入力します。

1. **[この接続ではプライバシーが保護されません]** ウィンドウで、 **[詳細設定]** を選択し、続行します。

1. Defender for IoT のサインイン画面が表示されたら、テストは成功です。

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="管理コンソールへのアクセスを示すスクリーンショット。":::

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="you-cant-connect-by-using-a-web-interface"></a>Web インターフェイスを使用して接続できない

1. 接続しようとしているコンピューターが、アプライアンスと同じネットワークにあることを確認します。

1. GUI ネットワークが管理ポートに接続されていることを確認します。

1. アプライアンスの IP アドレスに対して ping を実行します。 ping がない場合:

   1. モニターとキーボードをアプライアンスに接続します。

   1. **Support** ユーザーとパスワードを使用してサインインします。

   1. コマンド `network list` を使用して、現在の IP アドレスを確認します。

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="ネットワークの一覧を示すスクリーンショット。":::

1. ネットワーク パラメーターが正しく構成されていない場合は、次の手順を使用して変更します。

   1. コマンド `network edit-settings` を使用します。

   1. 管理ネットワークの IP アドレスを変更するには、 **[Y]** を選択します。

   1. サブネット マスクを変更するには、 **[Y]** を選択します。

   1. DNS を変更するには、 **[Y]** を選択します。

   1. 既定のゲートウェイ IP アドレスを変更するには、 **[Y]** を選択します。

   1. 入力インターフェイスの変更の場合は (センサーのみ)、 **[N]** を選択します。

   1. 設定を適用するには、 **[Y]** を選択します。

1. 再起動後、support ユーザーの資格情報を使用して接続し、`network list` コマンドを使用して、パラメーターが変更されたことを確認します。

1. もう一度 ping を実行し、GUI からの接続を試みます。

### <a name="the-appliance-isnt-responding"></a>アプライアンスが応答しない

1. モニターとキーボードをアプライアンスに接続するか、PuTTY を使用して CLI にリモート接続します。

1. **Support** ユーザーの資格情報を使用してサインインします。

1. `system sanity` コマンドを使用して、すべてのプロセスが実行されていることを確認します。

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="system sanity コマンドを示すスクリーンショット。":::

その他の問題については、[Microsoft サポート](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)にお問い合わせください。

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>付録 A: vSwitch のミラーリング ポート (ESXi)

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>既存の vSwitch で SPAN ポートを構成する

vSwitch にはミラーリング機能がありませんが、対処法を使用して SPAN ポートを実装できます。

SPAN ポートを構成するには:

1. vSwitch のプロパティを開きます。

1. **[追加]** を選択します。

1. **[仮想マシン]**  >  **[次へ]** を選択します。

1. ネットワーク ラベル「**SPAN Network**」を挿入し、 **[VLAN ID]**  >  **[すべて]** を選択し、 **[次へ]** を選択します。

1. **[完了]** を選択します。

1. **[SPAN Network]** > * *[編集]* を選択します。

1. **[セキュリティ]** を選択し、 **[Promiscuous Mode]\(無作為検出モード\)** ポリシーが **[Accept]\(承認\)** モードに設定されていることを確認します。

1. **[OK]** を選択し、次に **[閉じる]** を選択して vSwitch のプロパティを閉じます。

1. **[XSense VM]** プロパティを開きます。

1. **[Network Adapter 2]\(ネットワーク アダプター 2\)** で **[SPAN]** ネットワークを選択します。

1. **[OK]** を選択します。

1. センサーに接続し、ミラーリングが動作していることを確認します。

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>付録 B: オンプレミス管理コンソールからセンサーにアクセスする

センサーへの直接ユーザー アクセスを防止することで、システムのセキュリティを強化できます。 代わりに、プロキシ トンネリングを使用して、ユーザーが 1 つのファイアウォール規則でオンプレミスの管理コンソールからセンサーにアクセスできるようにします。 この手法により、センサーを介してネットワーク環境に不正にアクセスされる可能性が減少します。 センサーへのサインイン時のユーザー エクスペリエンスは変わりません。

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="センサーへのアクセスを示すスクリーンショット。":::

トンネリングを有効にするには:

1. **CyberX** または **Support** ユーザーの資格情報を使用して、オンプレミス管理コンソールの CLI にサインインします。

1. 「`sudo cyberx-management-tunnel-enable`」と入力します。

1. **[Enter]** を選択します。

1. 「`--port 10000`」と入力します。

### <a name="next-steps"></a>次のステップ

[ネットワークをセットアップする](how-to-set-up-your-network.md)
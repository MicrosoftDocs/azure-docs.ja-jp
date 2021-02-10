---
title: 必要なアプライアンスを特定する
description: 認定済みの Defender for IoT センサーとオンプレミス管理コンソールのハードウェアおよび仮想アプライアンスについて説明します。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/13/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 92bf066c9769cc4b2525923b9e18ed3c0e9c577a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937131"
---
# <a name="identify-required-appliances"></a>必要なアプライアンスを特定する

この記事では、認定済みの Defender for IoT センサー アプライアンスについて説明します。 Defender for IoT は、物理および仮想アプライアンス上にデプロイできます。

これには、ソフトウェアが既にインストールされている "*事前構成済み*" の認定アプライアンスと、必要なソフトウェアをダウンロードしてインストールできる未構成の認定アプライアンスが含まれます。

この記事では、オンプレミス管理コンソール アプライアンスの仕様についても説明します。 オンプレミス管理コンソールは、事前構成済みのアプライアンスとしては提供されていません。

- 事前構成済みのセンサーを購入する場合は、「[センサー アプライアンス](#sensor-appliances)」セクションで入手可能なモデルを確認してから、購入を進めてください。

- 独自のアプライアンスを購入する場合は、「[センサー アプライアンス](#sensor-appliances)」セクションと「[その他の認定アプライアンス](#additional-certified-appliances)」セクションで入手可能なモデルを確認してください。 アプライアンスを入手した後、ソフトウェアをダウンロードしてインストールできます。

- オンプレミス管理コンソールを購入する場合は、「[オンプレミス管理コンソール アプライアンス](#on-premises-management-console-appliance)」セクションの情報を確認してください。 デバイスを入手した後、ソフトウェアをダウンロードしてインストールできます。

ここで紹介したタスクを完了したら、ソフトウェアをインストールし、ネットワークをセットアップできます。

## <a name="sensor-appliances"></a>センサー アプライアンス

Defender for IoT では、物理および仮想デプロイの両方をサポートしています。

### <a name="physical-sensors"></a>物理センサー

ここでは、入手可能な物理センサー モデルの概要を説明します。 事前構成済みのソフトウェアを備えたセンサーを購入することも、事前構成されていないセンサーを購入することもできます。

| デプロイの種類 | 企業 | Enterprise | SMB |
|--|--|--|--|
| Image | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="企業レベルのモデル。"::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="エンタープライズ レベルのモデル。"::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="SMB レベルのモデル。"::: |
| モデル | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 |
| 監視ポート | 最大 15 個の RJ45 または 8 個の OPT | 最大 8 個の RJ45 または 6 個の OPT | 4 個の RJ45 |
| 最大帯域幅 [1](#anchortext) | 3 Gbps | 1 Gbps | 200 Mbps |
| 保護される最大デバイス数 | 30,000 | 15,000 | 1,000 |

ベンダーの詳細については、「[アプライアンスの仕様](#appliance-specifications)」をご覧ください。

事前構成済みのセンサーについて: Microsoft は Arrow と提携して、事前構成済みのセンサーを提供しています。 事前構成済みのセンサーを購入するには、Arrow (<hardware.sales@arrow.com>) にお問い合わせください。

独自のアプライアンスの導入について: ここで説明するサポートされているモデルを確認してください。 アプライアンスを入手したら、 **[Defender for IoT ]**  >  **[ネットワーク センサーの ISO]**  >  **[インストール]** の順にアクセスして、ソフトウェアをダウンロードします。

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="ネットワークセンサーの ISO。":::

<a id="anchortext">1</a> 帯域幅の容量は、プロトコルの分布によって異なる場合があります。

### <a name="virtual-sensors"></a>仮想センサー

ここでは、入手可能な仮想センサーの概要を説明します。

| デプロイの種類 | 企業 | Enterprise | SMB |
|--|--|--|--|
| 最大帯域幅 | 2.5 Gbps | 800 Mbps | 160 Mbps |
| 保護される最大デバイス数 | 30,000 | 10,000 | 2,500 |

## <a name="on-premises-management-console-appliance"></a>オンプレミス管理コンソール アプライアンス

管理コンソールは、仮想デプロイとして提供されています。

| デプロイの種類 | Enterprise |
|--|--|
| アプライアンスの種類 | HPE DL20、VM |
| 管理対象センサーの数 | 最大 300 |

オンプレミス管理コンソールを入手したら、 **[Defender for IoT]**  >  **[オンプレミス管理コンソール]**  >  **[ISO インストール]** の順にアクセスして ISO をダウンロードします。

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="オンプレミス管理コンソール。":::

## <a name="appliance-specifications"></a>アプライアンスの仕様

ここでは、次のアプライアンスのハードウェア仕様について説明します。

- 企業でのデプロイ: HPE ProLiant DL360

- エンタープライズでのデプロイ: HPE ProLiant DL20

- SMB でのデプロイ: HPE ProLiant DL20

- 仮想アプライアンスの仕様

## <a name="corporate-deployment-hpe-proliant-dl360"></a>企業でのデプロイ: HPE ProLiant DL360

| コンポーネント | 技術仕様 |
|--|--|
| シャーシ | 1U ラック サーバー |
| Dimensions | 42.9 x 43.46 x 70.7 (cm)/1.69 x 17.11 x 27.83 (インチ) |
| Weight | 最大 16.27 kg (35.86 ポンド) |
| プロセッサ | Intel Xeon Silver 4215 R 3.2 GHz、11 M キャッシュ、8c/16T、130 W |
| チップセット | Intel C621 |
| メモリ | 32 GB = 2 x 16GB 2666MT/秒 DDR4 ECC UDIMM |
| ストレージ | 6 x 1.2TB SAS 12G Enterprise 10K SFF (2.5 インチ) ホットプラグ対応ハード ドライブ - RAID 5 |
| ネットワーク コントローラー | オンボード: 2 x 1Gb Broadcom BCM5720<br>オンボード LOM: iDRAC ポート カード 1Gb Broadcom BCM5720<br><br>外部: 1 x Intel Ethernet i350 QP 1Gb サーバー アダプター、ロー プロファイル |
| 管理 | HPE iLO Advanced |
| デバイス アクセス | 2 つの背面 USB 3.0<br>1 つの前面 USB 2.0<br>1 つの内蔵 USB 3.0 |
| Power | 2 x HPE 500 W フレキシブル スロット Platinum ホット プラグ対応低ハロゲン電源キット |
| ラック サポート | HPE 1U Gen10 SFF Easy Install Rail Kit |

### <a name="appliance-bom"></a>アプライアンスの BOM

| PN | 説明 | Quantity |
|--|--|--|
| P19766-B21 | HPE DL360 Gen10 8SFF NC CTO Server | 1 |
| P19766-B21 | Europe - Multilingual Localization | 1 |
| P24479-L21 | Intel Xeon-S 4215 R FIO Kit for DL360 G10 | 1 |
| P24479-B21 | Intel Xeon-S 4215 R Kit for DL360 Gen10 | 1 |
| P00922-B21 | HPE 16-GB 2Rx8 PC4-2933Y-R Smart Kit | 2 |
| 872479-B21 | HPE 1.2-TB SAS 10K SFF SC DS HDD | 6 |
| 811546-B21 | HPE 1-GbE 4-p BASE-T I350 アダプター | 1 |
| P02377-B21 | HPE Smart ハイブリッド コンデンサー (145 mm ケーブル付属) | 1 |
| 804331-B21 | HPE Smart アレイ P408i-a SR Gen10 コントローラー | 1 |
| 665240-B21 | HPE 1-GbE 4-p FLR-T I350 アダプター | 1 |
| 871244-B21 | HPE DL360 Gen10 High Performance Fan Kit | 1 |
| 865408-B21 | HPE 500W FS Plat ホット プラグ対応 LH 電源キット | 2 |
| 512485-B21 | HPE iLO Adv 1-Server License (1 年間のサポート付き) | 1 |
| 874543-B21 | HPE 1U Gen10 SFF Easy Install Rail Kit | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>エンタープライズでのデプロイ: HPE ProLiant DL20

| コンポーネント | 技術仕様 |
|--|--|
| シャーシ | 1U ラック サーバー |
| 寸法 (高さ x 幅 x 奥行) | 4.32 x 43.46 x 38.22 cm/1.70 x 17.11 x 15.05 インチ |
| Weight | 7.9 kg (17.41 ポンド) |
| プロセッサ | Intel Xeon E-2234、3.6 GHz、4C/8T、71 W |
| チップセット | Intel C242 |
| メモリ | 2 x 16GB デュアル ランク x 8 DDR4-2666 |
| ストレージ | 3 x 1TB SATA 6G Midline 7.2 K SFF (2.5 インチ) – Smart アレイ P408i-SR コントローラーを使用した RAID 5 |
| ネットワーク コントローラー | オンボード: 2 x 1 Gb <br>オンボード: iLO ポート カード 1 Gb <br>外部: 1 x HPE Ethernet 1Gb 4 ポート 366FLR アダプター |
| 管理 | HPE iLO Advanced |
| デバイス アクセス | 前面: 1 x USB 3.0、1 x USB iLO サービス ポート <br>背面: 2 x USB 3.0 <br>内部:1 x USB 3.0 |
| Power | デュアル 500 W ホット プラグ対応電源装置 |
| ラック サポート | HPE 1U Short Friction Rail Kit |

### <a name="appliance-bom"></a>アプライアンスの BOM

| PN | 説明: ハイエンド | Quantity |
|--|--|--|
| P06963-B21 | HPE DL20 Gen10 4SFF CTO サーバー | 1 |
| P06963-B21 | HPE DL20 Gen10 4SFF CTO サーバー | 1 |
| P17104-L21 | HPE DL20 Gen10 E-2234 FIO Kit | 1 |
| 879507-B21 | HPE 16GB 2Rx8 PC4-2666V-E STND Kit | 2 |
| 655710-B21 | HPE 1TB SATA 7.2 K SFF SC DS HDD | 3 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM Riser Kit | 1 |
| 665240-B21 | HPE Ethernet 1 Gb 4 ポート 366FLR アダプター | 1 |
| 782961-B21 | HPE 12W Smart ストレージ バッテリ | 1 |
| 869081-B21 | HPE Smart アレイ P408i-a SR G10 LH コントローラー | 1 |
| 865408-B21 | HPE 500W FS Plat ホット プラグ対応 LH 電源キット | 2 |
| 512485-B21 | HPE iLO Adv 1-Server License (1 年間のサポート付き) | 1 |
| P06722-B21 | HPE DL20 Gen10 RPS Enablement FIO Kit | 1 |
| 775612-B21 | HPE 1U Short Friction Rail Kit | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>SMB でのデプロイ: HPE ProLiant DL20

| コンポーネント | 技術仕様 |
|--|--|
| シャーシ | 1U ラック サーバー |
| 寸法 (高さ x 幅 x 奥行) | 4.32 x 43.46 x 38.22 cm/1.70 x 17.11 x 15.05 インチ |
| Weight | 7.88 kg (17.37 ポンド) |
| プロセッサ | Intel Xeon E-2224、3.4 GHz、4C、71 W |
| チップセット | Intel C242 |
| メモリ | 1 x 8GB デュアル ランク x 8 DDR4-2666 |
| ストレージ | 2 x 1TB SATA 6G Midline 7.2 K SFF (2.5 インチ) – Smart アレイ P208i を使用した RAID 1 |
| ネットワーク コントローラー | オンボード: 2 x 1 Gb <br>オンボード: iLO ポート カード 1 Gb <br>外部: 1 x HPE Ethernet 1Gb 4 ポート 366FLR アダプター |
| 管理 | HPE iLO Advanced |
| デバイス アクセス | 前面: 1 x USB 3.0、1 x USB iLO サービス ポート <br>背面: 2 x USB 3.0 <br>内部:1 x USB 3.0 |
| Power | 290 W ホット プラグ対応電源装置 |
| ラック サポート | HPE 1U Short Friction Rail Kit |

### <a name="appliance-bom"></a>アプライアンスの BOM

| PN | 説明 | Quantity |
|--|--|--|
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO サーバー | 1 |
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO サーバー | 1 |
| P17102-L21 | HPE DL20 Gen10 E-2224 FIO Kit | 1 |
| 879505-B21 | HPE 8GB 1Rx8 PC4-2666V-E STND Kit | 1 |
| 801882-B21 | HPE 1TB SATA 7.2 K LFF RW HDD | 2 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM Riser Kit | 1 |
| 665240-B21 | HPE Ethernet 1 Gb 4 ポート 366FLR アダプター | 1 |
| 869079-B21 | HPE Smart アレイ E208i-a SR G10 LH コントローラー | 1 |
| P21649-B21 | HPE DL20 Gen10 Plat 290 W FIO 電源キット | 1 |
| P06683-B21 | HPE DL20 Gen10 M.2 SATA/LFF AROC ケーブル キット | 1 |
| 512485-B21 | HPE iLO Adv 1-Server License (1 年間のサポート付き) | 1 |
| 775612-B21 | HPE 1U Short Friction Rail Kit | 1 |

## <a name="virtual-appliance-specifications"></a>仮想アプライアンスの仕様

### <a name="sensors"></a>Sensors

| Type | 企業 | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| メモリ | 32 GB | 32 GB | 8 GB |
| ストレージ | 5.6 TB | 1.8 TB | 500 GB |

### <a name="on-premises-management-console-appliance"></a>オンプレミス管理コンソール アプライアンス

| Type | Enterprise |
|--|--|
| 説明 | エンタープライズ デプロイ タイプ用の仮想アプライアンス |
| vCPU | 8 |
| メモリ | 32 GB |
| ストレージ | 1.8 TB |

サポートされているハイパーバイザー: VMware ESXi バージョン 5.0 以降、Hyper-V

## <a name="additional-certified-appliances"></a>その他の認定アプライアンス

ここでは、Microsoft によって認定済みであるが、事前構成済みのアプライアンスとして提供されていない、その他のアプライアンスについて詳しく説明します。

| デプロイの種類 | Enterprise |
|--|--|
| Image | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="エンタープライズ デプロイ タイプ。"::: |
| モデル | Dell PowerEdge R340 XL |
| 監視ポート | 最大 9 個の RJ45 または 6 個の OPT |
| 最大帯域幅 [1](#anchortext2)| 1 GB/秒 |
| 保護される最大デバイス数 | 10,000 |

<a id="anchortext2">1</a> 帯域幅の容量は、プロトコルの分布によって異なる場合があります。

アプライアンスを購入したら、 **[Defender for IoT ]**  >  **[ネットワーク センサーの ISO]**  >  **[インストール]** の順にアクセスして、ソフトウェアをダウンロードします。

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="ネットワークセンサーの ISO。":::

## <a name="enterprise-deployment-dell-poweredge-r340-xl"></a>エンタープライズでのデプロイ: Dell PowerEdge R340 XL

| コンポーネント | 技術仕様 |
|--|--|
| シャーシ | 1U ラック サーバー |
| Dimensions | 42.8 x 434.0 x 596 (mm)/1.67 x 17.09 x 23.5 (インチ) |
| Weight | 最大 29.98 ポンド/13.6 kg |
| プロセッサ | Intel Xeon E-2144G 3.6 GHz、8 M キャッシュ、4C/8T、ターボ (71 W) |
| チップセット | Intel C246 |
| メモリ | 32 GB = 2 x 16GB 2666MT/秒 DDR4 ECC UDIMM |
| ストレージ | 3 x 2TB 7.2 K RPM SATA 6 Gbps 512n 3.5 インチ ホット プラグ対応ハード ドライブ - RAID 5 |
| ネットワーク コントローラー | オンボード: 2 x 1Gb Broadcom BCM5720<br>オンボード LOM: iDRAC ポート カード 1Gb Broadcom BCM5720 <br><br>外部: 1 x Intel Ethernet i350 QP 1Gb サーバー アダプター、ロー プロファイル |
| 管理 | iDRAC 9 Enterprise |
| デバイス アクセス | 2 つの背面 USB 3.0 <br> 1 つの前面 USB 3.0 |
| Power | デュアル 350 W ホット プラグ対応電源装置 |
| ラック サポート | 角穴またはねじ溝なしの丸穴付き 4 ポスト ラックへの工具不要の取り付け、または 4 ポストねじ式ラックへの工具を使用した取り付け用の ReadyRails II スライド式レール。オプションの工具不要ケーブル管理アームがサポートされています。 |

## <a name="dell-r340-bom"></a>Dell R340 の BOM

:::image type="content" source="media/how-to-prepare-your-network/enterprise-deployment-for-azure-defender-for-iot-dell-r340-bom.png" alt-text="Dell R340 の BOM。":::

## <a name="next-steps"></a>次のステップ

[Azure Defender for IoT のインストールについて](how-to-install-software.md)

[Azure Defender for IoT のネットワーク設定について](how-to-set-up-your-network.md)

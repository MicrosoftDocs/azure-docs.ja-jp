---
title: Azure で利用可能な Red Hat Enterprise Linux イメージ
description: Microsoft Azure の Red Hat Enterprise Linux イメージについて説明します
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: f4ebf433237193c0db7859b029bb224c72c92eae
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291020"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Azure で利用可能な Red Hat Enterprise Linux (RHEL) イメージ
Azure では、さまざまなユース ケースに対応する多様な RHEL イメージを提供しています。

> [!NOTE]
> すべての RHEL イメージは、Azure パブリック クラウドと Azure Government クラウドで利用できます。 これらは Azure China クラウドでは利用できません。

## <a name="list-of-rhel-images"></a>RHEL イメージの一覧
これは、Azure で利用可能な RHEL イメージの一覧です。 特に明記されていない限り、すべてのイメージは LVM パーティションに分割され、(EUS、E4S ではなく) 標準の RHEL リポジトリに接続されています。 現在、次のイメージが一般に利用できます。

> [!NOTE]
> LVM パーティション分割されたイメージを優先するために、未加工のイメージが生成されなくなりました。 LVM では、以前の未加工の (LVM でない) パーティション分割構成と比べて、より柔軟なパーティション サイズ変更オプションを含めていくつかの利点があります。

プラン| SKU | パーティション分割 | プロビジョニング | Notes
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Linux エージェント |
|             | 6.8      | RAW    | Linux エージェント |
|             | 6.9      | RAW    | Linux エージェント |
|             | 6.10     | RAW    | Linux エージェント |
|             | 7-RAW    | RAW    | Linux エージェント | RHEL 7.x イメージ ファミリ。 <br> 既定で (EUS ではなく) 標準リポジトリに接続されています。
|             | 7-LVM    | LVM    | Linux エージェント | RHEL 7.x イメージ ファミリ。 <br> 既定で (EUS ではなく) 標準リポジトリに接続されています。 デプロイする標準の RHEL イメージを探している場合は、このイメージのセット、またはそれに対応する第 2 世代のイメージを使用します。
|             | 7lvm-gen2| LVM    | Linux エージェント | 第 2 世代、RHEL 7.x イメージ ファミリ。 <br> 既定で (EUS ではなく) 標準リポジトリに接続されています。 デプロイする標準の RHEL イメージを探している場合は、このイメージのセット、またはそれに対応する第 1 世代のイメージを使用します。
|             | 7-RAW-CI | RAW-CI | cloud-init  | RHEL 7.x イメージ ファミリ。 <br> 既定で (EUS ではなく) 標準リポジトリに接続されています。
|             | 7.2      | RAW    | Linux エージェント |
|             | 7.3      | RAW    | Linux エージェント |
|             | 7.4      | RAW    | Linux エージェント | 2019 年 4 月現在、既定で EUS リポジトリに接続されています。
|             | 74-gen2  | RAW    | Linux エージェント | 既定では EUS リポジトリに接続されています。
|             | 7.5      | RAW    | Linux エージェント | 2019 年 6 月現在、既定で EUS リポジトリに接続されています。
|             | 75-gen2  | RAW    | Linux エージェント | 既定では EUS リポジトリに接続されています。
|             | 7.6      | RAW    | Linux エージェント | 2019 年 5 月現在、既定で EUS リポジトリに接続されています。
|             | 76-gen2  | RAW    | Linux エージェント | 既定では EUS リポジトリに接続されています。
|             | 7.7      | LVM    | Linux エージェント | 既定では EUS リポジトリに接続されています。
|             | 77-gen2  | LVM    | Linux エージェント | 既定では EUS リポジトリに接続されています。
|             | 7.8      | LVM    | Linux エージェント | 標準リポジトリに接続されています (RHEL 7.8 では EUS は使用できません)。
|             | 78-gen2  | LVM    | Linux エージェント | 標準リポジトリに接続されています (RHEL 7.8 では EUS は使用できません)。
|             | 8-LVM    | LVM    | Linux エージェント | RHEL 8.x イメージ ファミリ。 通常のリポジトリに接続されています。
|             | 8-lvm-gen2| LVM    | Linux エージェント | Hyper-V Generation 2 - RHEL 8.x イメージ ファミリ。 通常のリポジトリに接続されています。
|             | 8        | LVM    | Linux エージェント | RHEL 8.0 イメージ。
|             | 8-gen2   | LVM    | Linux エージェント | Hyper-V 第 2 世代 - RHEL 8.0 イメージ。
|             | 8.1      | LVM    | Linux エージェント | RHEL 8.2 イメージ。 現在、通常のリポジトリに接続されています。
|             | 81gen2   | LVM    | Linux エージェント | Hyper-V 第 2 世代 - RHEL 8.1 イメージ。 現在、通常のリポジトリに接続されています。
|             | 8.1-ci   | LVM    | Linux エージェント | プロビジョニング エージェントとして cloud-init を使用する RHEL 8.1 イメージ。 現在、通常のリポジトリに接続されています。
|             | 81-ci-gen2| LVM    | Linux エージェント | Hyper-V 第 2 世代 - プロビジョニング エージェントとして cloud-init を使用する RHEL 8.1 イメージ。 現在、通常のリポジトリに接続されています。
|             | 8.2      | LVM    | Linux エージェント | RHEL 8.2 イメージ。 現在、通常のリポジトリに接続されています。
|             | 82gen2   | LVM    | Linux エージェント | Hyper-V 第 2 世代 - RHEL 8.1 イメージ。 現在、通常のリポジトリに接続されています。
RHEL-SAP      | 7.4      | LVM    | Linux エージェント | RHEL 7.4 for SAP HANA および Business Apps。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
|             | 74sap-gen2| LVM    | Linux エージェント | RHEL 7.4 for SAP HANA および Business Apps。 第 2 世代イメージ。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
|             | 7.5       | LVM    | Linux エージェント | RHEL 7.5 for SAP HANA および Business Apps。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
|             | 75sap-gen2| LVM    | Linux エージェント | RHEL 7.5 for SAP HANA および Business Apps。 第 2 世代イメージ。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
|             | 7.6       | LVM    | Linux エージェント | RHEL 7.6 for SAP HANA および Business Apps。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
|             | 76sap-gen2| LVM    | Linux エージェント | RHEL 7.6 for SAP HANA および Business Apps。 第 2 世代イメージ。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
|             | 7.7       | LVM    | Linux エージェント | RHEL 7.7 for SAP HANA および Business Apps。 E4S リポジトリに接続されており、SAP および RHEL の割増料金と基本コンピューティング料金が請求されます。
RHEL-SAP-HANA | 6.7       | RAW    | Linux エージェント | RHEL 6.7 for SAP HANA。 RHEL-SAP イメージを優先して古くなりました。
|             | 7.2       | LVM    | Linux エージェント | RHEL 7.2 for SAP HANA。 RHEL-SAP イメージを優先して古くなりました。
|             | 7.3       | LVM    | Linux エージェント | RHEL 7.3 for SAP HANA。 RHEL-SAP イメージを優先して古くなりました。
RHEL-SAP-APPS | 6.8       | RAW    | Linux エージェント | RHEL 6.8 for SAP Business Applications。 RHEL-SAP イメージを優先して古くなりました。
|             | 7.3       | LVM    | Linux エージェント | RHEL 7.3 for SAP Business Applications。 RHEL-SAP イメージを優先して古くなりました。
RHEL-HA       | 7.4       | LVM    | Linux エージェント | HA アドオンが追加された RHEL 7.4。 基本コンピューティング料金に加えて、HA および RHEL の割増料金が請求されます。
|             | 7.5       | LVM    | Linux エージェント | HA アドオンが追加された RHEL 7.5。 基本コンピューティング料金に加えて、HA および RHEL の割増料金が請求されます。
|             | 7.6       | LVM    | Linux エージェント | HA アドオンが追加された RHEL 7.6。 基本コンピューティング料金に加えて、HA および RHEL の割増料金が請求されます。
RHEL-SAP-HA   | 7.4          | LVM    | Linux エージェント | HA および更新サービスを備えた RHEL 7.4 for SAP。 E4S リポジトリに接続されています。 基本コンピューティング料金に加えて、SAP および HA リポジトリと RHEL の割増料金が請求されます。
|             | 74sapha-gen2 | LVM    | Linux エージェント | HA および更新サービスを備えた RHEL 7.4 for SAP。 第 2 世代イメージ。 E4S リポジトリに接続されています。 基本コンピューティング料金に加えて、SAP および HA リポジトリと RHEL の割増料金が請求されます。
|             | 7.5          | LVM    | Linux エージェント | HA および更新サービスを備えた RHEL 7.5 for SAP。 E4S リポジトリに接続されています。 基本コンピューティング料金に加えて、SAP および HA リポジトリと RHEL の割増料金が請求されます。
|             | 7.6          | LVM    | Linux エージェント | HA および更新サービスを備えた RHEL 7.6 for SAP。 E4S リポジトリに接続されています。 基本コンピューティング料金に加えて、SAP および HA リポジトリと RHEL の割増料金が請求されます。
|             | 76sapha-gen2 | LVM    | Linux エージェント | HA および更新サービスを備えた RHEL 7.6 for SAP。 第 2 世代イメージ。 E4S リポジトリに接続されています。 基本コンピューティング料金に加えて、SAP および HA リポジトリと RHEL の割増料金が請求されます。
|             | 7.7          | LVM    | Linux エージェント | HA および更新サービスを備えた RHEL 7.7 for SAP。 E4S リポジトリに接続されています。 基本コンピューティング料金に加えて、SAP および HA リポジトリと RHEL の割増料金が請求されます。
|             | 77sapha-gen2 | LVM    | Linux エージェント | HA および更新サービスを備えた RHEL 7.7 for SAP。 第 2 世代イメージ。 E4S リポジトリに接続されています。 基本コンピューティング料金に加えて、SAP および HA リポジトリと RHEL の割増料金が請求されます。
rhel-byos     |rhel-lvm74| LVM    | Linux エージェント | RHEL 7.4 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm75| LVM    | Linux エージェント | RHEL 7.5 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm76| LVM    | Linux エージェント | RHEL 7.6 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm76-gen2| LVM    | Linux エージェント | RHEL 7.6 第 2 世代 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm77| LVM    | Linux エージェント | RHEL 7.7 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm77-gen2| LVM    | Linux エージェント | RHEL 7.7 第 2 世代 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm78| LVM    | Linux エージェント | RHEL 7.8 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm78-gen2| LVM    | Linux エージェント | RHEL 7.8 第 2 世代 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm8 | LVM    | Linux エージェント | RHEL 8.0 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm8-gen2 | LVM    | Linux エージェント | RHEL 8.0 第 2 世代 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm81 | LVM    | Linux エージェント | RHEL 8.1 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm81-gen2 | LVM    | Linux エージェント | RHEL 8.1 第 2 世代 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm82 | LVM    | Linux エージェント | RHEL 8.2 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。
|             |rhel-lvm82-gen2 | LVM    | Linux エージェント | RHEL 8.2 第 2 世代 BYOS イメージ。どの更新ソースにも接続されておらず、RHEL の割増料金は請求されません。

> [!NOTE]
> RHEL-SAP-HANA 製品は、Red Hat では有効期限が終了したものと見なされています。 既存のデプロイは引き続き正常に動作しますが、Red Hat ではお客様が RHEL-SAP-HANA イメージから、SAP HANA リポジトリと HA アドオンを含む RHEL-SAP-HA イメージに移行することを推奨しています。 Red Hat の SAP クラウド サービスの詳細については[こちら](https://access.redhat.com/articles/3751271)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure の Red Hat イメージ](./redhat-images.md)の詳細を確認します。
* [Red Hat Update Infrastructure](./redhat-rhui.md) の詳細を確認します。
* [RHEL BYOS プラン](./byos.md)の詳細を確認します。
* すべてのバージョンの RHEL に対する Red Hat のサポート ポリシーに関する情報は、「[Red Hat Enterprise Linux Life Cycle \(Red Hat Enterprise Linux のライフ サイクル\)](https://access.redhat.com/support/policy/updates/errata)」ページに記載されています。

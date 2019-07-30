---
title: Azure Automation で Azure 仮想マシンを垂直方向にスケーリングする| Microsoft Docs
description: Azure Automation で監視アラートに応じて Linux 仮想マシンを垂直方向にスケーリングする方法
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: dcee199e-fa25-44d5-9b25-df564cee9b45
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e0317344fd8ee1eb415b61d4f5035219e649b18d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695478"
---
# <a name="vertically-scale-azure-linux-virtual-machine-with-azure-automation"></a>Azure Automation で Azure Linux 仮想マシンを垂直方向にスケーリングする
垂直方向のスケーリングは、ワークロードに応じてコンピューターのリソースを増減するプロセスです。 Azure では、仮想マシンのサイズを変更することで実行できます。 これは、次のようなシナリオで役立ちます。

* 仮想マシンが頻繁に使用されていない場合、より小さなサイズに変更して、毎月のコストを削減することができます
* 仮想マシンでピーク時の負荷が表れている場合、より大きなサイズに変更して、容量を増やすことができます

これを実現するための手順の概要を、以下に示します

1. Azure Automation をセットアップして、Virtual Machines にアクセスする
2. サブスクリプションに Azure Automation の垂直スケールの Runbook をインポートする
3. Webhook を Runbook に追加する
4. 仮想マシンにアラートを追加する

## <a name="scale-limitations"></a>スケールの制限

最初の仮想マシンのサイズによっては、スケーリングできるサイズが制限される場合があります。これは、その仮想マシンがデプロイされているクラスターの空き容量によるものです。 この記事で使用される公開済みの Automation Runbook では、このケースのみを扱い、VM のサイズ ペアを超えない範囲でのみスケーリングします。 つまり、Standard_D1v2 仮想マシンが急に Standard_G5 にスケールアップしたり、Basic_A0 にスケールダウンしたりすることはありません。 また、制約付きの仮想マシンのサイズのスケールアップ/スケールダウンはサポートされていません。 

次のようなサイズのペアの間でスケールの設定を選択できます。

* [A シリーズ](#a-series)
* [B シリーズ](#b-series)
* [D シリーズ](#d-series)
* [E シリーズ](#e-series)
* [F シリーズ](#f-series)
* [G シリーズ](#g-series)
* [H シリーズ](#h-series)
* [L シリーズ](#l-series)
* [M シリーズ](#m-series)
* [N シリーズ](#n-series)

### <a name="a-series"></a>A シリーズ

| 初期サイズ | サイズのスケールアップ | 
| --- | --- |
| Basic_A0 | Basic_A1 |
| Basic_A1 | Basic_A2 |
| Basic_A2 | Basic_A3 |
| Basic_A3 | Basic_A4 |
| Standard_A0 | Standard_A1 |
| Standard_A1 | Standard_A2 |
| Standard_A2 | Standard_A3 |
| Standard_A3 | Standard_A4 |
| Standard_A5 | Standard_A6 |
| Standard_A6 | Standard_A7 |
| Standard_A8 | Standard_A9 |
| Standard_A10 | Standard_A11 |
| Standard_A1_v2 | Standard_A2_v2 |
| Standard_A2_v2 | Standard_A4_v2 |
| Standard_A4_v2 | Standard_A8_v2 |
| Standard_A2m_v2 | Standard_A4m_v2 |
| Standard_A4m_v2 | Standard_A8m_v2 |

### <a name="b-series"></a>B シリーズ

| 初期サイズ | サイズのスケールアップ | 
| --- | --- |
| Standard_B1s | Standard_B2s |
| Standard_B1ms | Standard_B2ms |
| Standard_B2ms | Standard_B4ms |
| Standard_B4ms | Standard_B8ms |

### <a name="d-series"></a>D シリーズ

| 初期サイズ | サイズのスケールアップ | 
| --- | --- |
| Standard_D1 | Standard_D2 |
| Standard_D2 | Standard_D3 |
| Standard_D3 | Standard_D4 |
| Standard_D11 | Standard_D12 |
| Standard_D12 | Standard_D13 |
| Standard_D13 | Standard_D14 |
| Standard_DS1 | Standard_DS2 |
| Standard_DS2 | Standard_DS3 |
| Standard_DS3 | Standard_DS4 |
| Standard_DS11 | Standard_DS12 |
| Standard_DS12 | Standard_DS13 |
| Standard_DS13 | Standard_DS14 |
| Standard_D1_v2 | Standard_D2_v2 |
| Standard_D2_v2 | Standard_D3_v2 |
| Standard_D3_v2 | Standard_D4_v2 |
| Standard_D4_v2 | Standard_D5_v2 |
| Standard_D11_v2 | Standard_D12_v2 |
| Standard_D12_v2 | Standard_D13_v2 |
| Standard_D13_v2 | Standard_D14_v2 |
| Standard_DS1_v2 | Standard_DS2_v2 |
| Standard_DS2_v2 | Standard_DS3_v2 |
| Standard_DS3_v2 | Standard_DS4_v2 |
| Standard_DS4_v2 | Standard_DS5_v2 |
| Standard_DS11_v2 | Standard_DS12_v2 |
| Standard_DS12_v2 | Standard_DS13_v2 |
| Standard_DS13_v2 | Standard_DS14_v2 |
| Standard_D2_v3 | Standard_D4_v3 |
| Standard_D4_v3 | Standard_D8_v3 |
| Standard_D8_v3 | Standard_D16_v3 |
| Standard_D16_v3 | Standard_D32_v3 |
| Standard_D32_v3 | Standard_D64_v3 |
| Standard_D2s_v3 | Standard_D4s_v3 |
| Standard_D4s_v3 | Standard_D8s_v3 |
| Standard_D8s_v3 | Standard_D16s_v3 |
| Standard_D16s_v3 | Standard_D32s_v3 |
| Standard_D32s_v3 | Standard_D64s_v3 |
| Standard_DC2s | Standard_DC4s |

### <a name="e-series"></a>E シリーズ

| 初期サイズ | サイズのスケールアップ | 
| --- | --- |
| Standard_E2_v3 | Standard_E4_v3 |
| Standard_E4_v3 | Standard_E8_v3 |
| Standard_E8_v3 | Standard_E16_v3 |
| Standard_E16_v3 | Standard_E20_v3 |
| Standard_E20_v3 | Standard_E32_v3 |
| Standard_E32_v3 | Standard_E64_v3 |
| Standard_E2s_v3 | Standard_E4s_v3 |
| Standard_E4s_v3 | Standard_E8s_v3 |
| Standard_E8s_v3 | Standard_E16s_v3 |
| Standard_E16s_v3 | Standard_E20s_v3 |
| Standard_E20s_v3 | Standard_E32s_v3 |
| Standard_E32s_v3 | Standard_E64s_v3 |

### <a name="f-series"></a>F シリーズ

| 初期サイズ | サイズのスケールアップ | 
| --- | --- |
| Standard_F1 | Standard_F2 |
| Standard_F2 | Standard_F4 |
| Standard_F4 | Standard_F8 |
| Standard_F8 | Standard_F16 |
| Standard_F1s | Standard_F2s |
| Standard_F2s | Standard_F4s |
| Standard_F4s | Standard_F8s |
| Standard_F8s | Standard_F16s |
| Standard_F2s_v2 | Standard_F4s_v2 |
| Standard_F4s_v2 | Standard_F8s_v2 |
| Standard_F8s_v2 | Standard_F16s_v2 |
| Standard_F16s_v2 | Standard_F32s_v2 |
| Standard_F32s_v2 | Standard_F64s_v2 |
| Standard_F64s_v2 | Standard_F7s_v2 |

### <a name="g-series"></a>G シリーズ

| 初期サイズ | サイズのスケールアップ | 
| --- | --- |
| Standard_G1 | Standard_G2 |
| Standard_G2 | Standard_G3 |
| Standard_G3 | Standard_G4 |
| Standard_G4 | Standard_G5 |
| Standard_GS1 | Standard_GS2 |
| Standard_GS2 | Standard_GS3 |
| Standard_GS3 | Standard_GS4 |
| Standard_GS4 | Standard_GS5 |

### <a name="h-series"></a>H シリーズ

| 初期サイズ | サイズのスケールアップ | 
| --- | --- |
| Standard_H8 | Standard_H16 |
| Standard_H8m | Standard_H16m |

### <a name="l-series"></a>L シリーズ

| 初期サイズ | サイズのスケールアップ | 
| --- | --- |
| Standard_L4s | Standard_L8s |
| Standard_L8s | Standard_L16s |
| Standard_L16s | Standard_L32s |
| Standard_L8s_v2 | Standard_L16s_v2 |
| Standard_L16s_v2 | Standard_L32s_v2 |
| Standard_L32s_v2 | Standard_L64s_v2 |
| Standard_L64s_v2 | Standard_L80s_v2 |

### <a name="m-series"></a>M シリーズ

| 初期サイズ | サイズのスケールアップ | 
| --- | --- |
| Standard_M8ms | Standard_M16ms |
| Standard_M16ms | Standard_M32ms |
| Standard_M32ms | Standard_M64ms |
| Standard_M64ms | Standard_M128ms |
| Standard_M32ls | Standard_M64ls |
| Standard_M64s | Standard_M128s |
| Standard_M64 | Standard_M128 |
| Standard_M64m | Standard_M128m |

### <a name="n-series"></a>N シリーズ

| 初期サイズ | サイズのスケールアップ | 
| --- | --- |
| Standard_NC6 | Standard_NC12 |
| Standard_NC12 | Standard_NC24 |
| Standard_NC6s_v2 | Standard_NC12s_v2 |
| Standard_NC12s_v2 | Standard_NC24s_v2 |
| Standard_NC6s_v3 | Standard_NC12s_v3 |
| Standard_NC12s_v3 | Standard_NC24s_v3 |
| Standard_ND6 | Standard_ND12 |
| Standard_ND12 | Standard_ND24 |
| Standard_NV6 | Standard_NV12 |
| Standard_NV12 | Standard_NV24 |
| Standard_NV6s_v2 | Standard_NV12s_v2 |
| Standard_NV12s_v2 | Standard_NV24s_v2 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Azure Automation をセットアップして、Virtual Machines にアクセスする
最初に、VM スケール セットのインスタンスをスケーリングするために使用する runbook をホストする、Azure Automation アカウントを作成する必要があります。 最近、Automation サービスでは、ユーザーの代わりに非常に簡単に Runbook を自動的に実行するためのサービス プリンシパルをセットアップする "アカウントとして実行" 機能が導入されました。 詳しくは、次の記事を参照してください。

* [Azure 実行アカウントを使用した Runbook の認証](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>サブスクリプションに Azure Automation の垂直スケールの Runbook をインポートする
仮想マシンの垂直方向へのスケーリングに必要な Runbook は、Azure Automation Runbook ギャラリーに既に公開されています。 これを、サブスクリプションにインポートする必要があります。 Runbook をインポートする方法は、次の記事を参照してください。

* [Azure Automation 用の Runbook ギャラリーとモジュール ギャラリー](../../automation/automation-runbook-gallery.md)

インポートする必要がある Runbook を次の図に示します

![Runbook のインポート](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Webhook を Runbook に追加する
Runbook をインポートしたら、仮想マシンからのアラートによって Webhook がトリガーされるように、Runbook に追加する必要があります。 Runbook で Webhook を作成する方法の詳細は、次の記事を参照してください。

* [Azure Automation Webhook](../../automation/automation-webhooks.md)

Webhook のダイアログを閉じる前に、Webhook をコピーしてください。これは次のセクションで必要になります。

## <a name="add-an-alert-to-your-virtual-machine"></a>仮想マシンにアラートを追加する
1. 仮想マシンの設定を選択する
2. "アラート ルール" を作成する
3. "アラートの追加" を選択する
4. アラートを発生させるメトリックを選択する
5. アラートを発生させるための条件を選択する
6. 手順 5 の条件で満たす必要がある しきい値を選択する
7. 手順 5 と 6 で選択した条件としきい値を監視サービスが確認する期間を選択する
8. 前のセクションからコピーした Webhook を貼り付ける

![アラートを仮想マシン 1 に追加](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![アラートを仮想マシン 2 に追加](./media/vertical-scaling-automation/add-alert-webhook-2.png)


---
title: Azure Automation を使用して Windows 仮想マシンを垂直方向にスケーリングする | Microsoft Docs
description: Azure Automation による監視アラートに応じて Windows 仮想マシンを垂直方向にスケーリングします
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4f964713-fb67-4bcc-8246-3431452ddf7d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 44243f97b6c431578185fcdeb1ddcabc548d927f
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011743"
---
# <a name="vertically-scale-windows-vms-with-azure-automation"></a>Azure Automation を使用して Windows VM を垂直方向にスケーリングする

垂直方向のスケーリングは、ワークロードに応じてコンピューターのリソースを増減するプロセスです。 Azure では、仮想マシンのサイズを変更することで実行できます。 これは、次のようなシナリオで役立ちます。

* 仮想マシンが頻繁に使用されていない場合、より小さなサイズに変更して、毎月のコストを削減することができます
* 仮想マシンでピーク時の負荷が表れている場合、より大きなサイズに変更して、容量を増やすことができます

これを実現するための手順の概要を、以下に示します

1. Azure Automation をセットアップして、Virtual Machines にアクセスする
2. サブスクリプションに Azure Automation の垂直スケールの Runbook をインポートする
3. Webhook を Runbook に追加する
4. 仮想マシンにアラートを追加する

> [!NOTE]
> 最初の仮想マシンのサイズによっては、スケーリングできるサイズが制限される場合があります。これは、その仮想マシンがデプロイされているクラスターの空き容量によるものです。 この記事で使用される公開済みの Automation Runbook では、このケースのみを扱い、VM のサイズ ペアを超えない範囲でのみスケーリングします。 つまり、Standard_D1v2 仮想マシンが急に Standard_G5 にスケールアップしたり、Basic_A0 にスケールダウンしたりすることはありません。 また、制約付きの仮想マシンのサイズのスケールアップ/スケールダウンはサポートされていません。 次のようなサイズのペアの間でスケールの設定を選択できます。
> 
> | VM サイズのスケーリングのペア |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2v3 |Standard_E64v3 |
> | Standard_E2sv3 |Standard_E64sv3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> 
> 

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Azure Automation をセットアップして、Virtual Machines にアクセスする
最初に、仮想マシンをスケーリングするために使用する runbook をホストする、Azure Automation アカウントを作成する必要があります。 最近、Automation サービスでは、ユーザーの代わりに非常に簡単に Runbook を自動的に実行するためのサービス プリンシパルをセットアップする "アカウントとして実行" 機能が導入されました。 詳しくは、次の記事を参照してください。

* [Azure 実行アカウントを使用した Runbook の認証](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>サブスクリプションに Azure Automation の垂直スケールの Runbook をインポートする
垂直方向へのスケーリングに必要な Runbook は、Azure Automation Runbook ギャラリーに既に公開されています。 これを、サブスクリプションにインポートする必要があります。 Runbook をインポートする方法は、次の記事を参照してください。

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


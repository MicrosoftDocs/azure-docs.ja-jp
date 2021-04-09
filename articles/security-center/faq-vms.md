---
title: Azure Security Center FAQ - 仮想マシンに関する質問
description: 脅威の防御、検出、対応を可能にする製品である Azure Security Center での仮想マシンに関してよく寄せられる質問
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 32023fca15de9d3f9258bc05166c3cff4184ea15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96499851"
---
# <a name="faq---questions-about-virtual-machines"></a>FAQ - 仮想マシンに関する質問


## <a name="what-types-of-virtual-machines-are-supported"></a>サポートされる仮想マシンのタイプは何ですか。

監視と推奨事項は、[クラシック デプロイ モデルと Resource Manager デプロイ モデル](../azure-resource-manager/management/deployment-models.md)のどちらで作成された仮想マシン (VM) でも利用できます。

サポートされるプラットフォームの一覧については、「[Azure Security Center でサポートされているプラットフォーム](security-center-os-coverage.md)」を参照してください。


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Azure VM で実行しているマルウェア対策ソリューションが、Azure Security Center で認識されないのはなぜですか。

Azure Security Center では、Azure 拡張機能によりインストールされたマルウェア対策ソリューションを可視化できます。 たとえば、Azure Security Center では、お客様のイメージにプレインストールされていたマルウェア対策ソリューションや、お客様が独自のプロセス (構成管理システムなど) で仮想マシンにインストールしたマルウェア対策ソリューションは検出できません。


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>VM で "スキャン データがありません" というメッセージが表示されるのはなぜですか。

このメッセージが表示されるのは、VM のスキャン データがない場合です。 Azure Security Center でデータ収集が有効になると、スキャン データが取り込まれるまでにある程度の時間 (1 時間未満) がかかる場合があります。 スキャン データの初回作成以降でも、スキャン データが存在しなかったり、最近のスキャン データがなかったりする場合にはこのメッセージが表示される可能性があります。 停止状態の VM のスキャン データは取り込まれません。 スキャン データが最近取り込まれていない場合 (Windows エージェントの保持ポリシーに従い、既定値は 30 日間) にも、このメッセージが表示されることがあります。


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Security Center は、オペレーティング システムの脆弱性、システムの更新、およびエンドポイントの保護の問題をどのくらいの頻度でスキャンしますか。

脆弱性、更新プログラム、その他の問題を Security Center がスキャンするときの待ち時間は次の通りです。

- オペレーティング システムのセキュリティ構成 - データは 48 時間以内に更新されます
- システムの更新プログラム - 24 時間以内にデータを更新
- Endpoint Protection の問題 - 8 時間以内にデータを更新

Security Center は通常、1 時間ごとに新しいデータをスキャンし、それに応じて推奨事項を更新します。 

> [!NOTE]
> Security Center では、データの収集と格納に Log Analytics エージェントを使用します。 詳細については、「[Azure Security Center のプラットフォームの移行](./security-center-enable-data-collection.md)」を参照してください。


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>"VM Agent is Missing? (VM エージェントが見つかりません)" というメッセージが表示されるのはなぜですか。

データ収集を有効にするには、VM エージェントが VM にインストールされている必要があります。 既定では、Azure Marketplace からデプロイされた VM に VM エージェントがインストールされます。 他の VM への VM エージェントのインストール方法については、 [VM エージェントと拡張機能](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)に関するブログ投稿を参照してください。
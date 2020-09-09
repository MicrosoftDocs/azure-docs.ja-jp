---
title: セキュリティ ソリューションを構成して CEF データを Azure Sentinel Preview に接続する | Microsoft Docs
description: セキュリティ ソリューションを構成して CEF データを Azure Sentinel に接続する方法についての説明。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 1c25e48bd46f0d37330f693cb4d6538e7bc29c4b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367242"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>手順 2: CEF メッセージを送信するように、セキュリティ ソリューションを構成する

この手順では、CEF エージェントにログを送信するために、セキュリティ ソリューション自体に必要な構成変更を実行します。

## <a name="configure-a-solution-with-a-connector"></a>コネクタを使用してソリューションを構成する

セキュリティ ソリューションに既存のコネクタが既にある場合は、コネクタ固有の手順を次のように使用します。

- [AI Vectra 検出](connect-ai-vectra-detect.md)
- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>その他のソリューションを構成する

特定のセキュリティ ソリューションにコネクタが存在しない場合は、次の一般的な手順に従って、CEF エージェントにログを転送します。

1. ソリューションを構成して CEF メッセージを送信する方法の手順については、特定の構成に関する記事を確認してください。 ご利用のソリューションが一覧に含まれていない場合は、アプライアンスでこれらの値を設定して、このアプライアンスが、Log Analytics エージェントに基づいて、必要な形式で必要なログを Azure Sentinel Syslog エージェントに送信するようにします。 Azure Sentinel エージェントの Syslog デーモンでこれらのパラメーターを変更できれば、ご利用のアプライアンスでもこれらのパラメーターを変更することができます。
    - プロトコル = TCP
    - ポート = 514
    - 形式 = CEF
    - IP アドレス - CEF メッセージを、この目的専用の仮想マシンの IP アドレスに送信していることを確認します。

   > [!NOTE]
   > このソリューションは、Syslog RFC 3164 または RFC 5424 をサポートしています。

1. Log Analytics で CEF イベントに関連するスキーマを使用するために、`CommonSecurityLog` を検索します。

1. 「手順 3: [接続を検証する](connect-cef-verify.md)」に進みます。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、CEF アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。

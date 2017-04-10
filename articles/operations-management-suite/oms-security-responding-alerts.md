---
title: "Operations Management Suite のセキュリティと監査ソリューションでのセキュリティの警告に対する監視と応答 | Microsoft Docs"
description: "このドキュメントでは、OMS のセキュリティと監査で提供される脅威インテリジェンス オプションを使用した、セキュリティ警告に対する監視と対応に役立つ情報を提供します。"
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 7d45a32b-1341-4bb5-a436-1f42a8a2590a
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7161cacfd371aa73974e635a343793bbec76d858
ms.lasthandoff: 11/17/2016


---
# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>Operations Management Suite のセキュリティと監査ソリューションでのセキュリティの警告に対する監視と対応
このドキュメントでは、OMS のセキュリティと監査で提供される脅威インテリジェンス オプションを使用した、セキュリティ警告に対する監視と対応に役立つ情報を提供します。

## <a name="what-is-oms"></a>OMS とは
Microsoft Operations Management Suite (OMS) は、Microsoft のクラウド ベースの IT 管理ソリューションです。OMS を使用して、オンプレミスとクラウドのインフラストラクチャを管理し、保護することができます。 OMS の詳細については、[Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx) に関する記事をご覧ください。

## <a name="threat-intelligence"></a>脅威インテリジェンス
ユーザーがネットワークへの広範なアクセス手段を持ち、さまざまなデバイスを使用して企業のデータに接続するエンタープライズ環境では、リソースを能動的に監視し、セキュリティ インシデントにすばやく対応できることが必要になります。 一部のサイバーセキュリティの脅威は、従来型のセキュリティの技術的制御で特定できる警告や疑わしいアクティビティを引き起こさないこともあるため、セキュリティ ライフサイクルの観点から考えた場合、これは特に重要です。 

IT 管理者は、OMS のセキュリティと監査で利用できる **[Threat Intelligence]** (脅威インテリジェンス) オプションを利用することで、たとえば特定のコンピューターが [ボットネット](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection)の一部であるかどうかを確認するなど、環境に対するセキュリティの脅威を特定することができます。 コンピューターをひそかにコマンド アンド コントロール サーバーに接続するマルウェアが攻撃者によって不正にインストールされた場合、そのコンピューターはボットネットのノードになる可能性があります。 また、 [ダークネット](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents)のようなアンダーグラウンド通信チャネルに由来する潜在的な脅威を特定することもできます。 

この脅威インテリジェンスを構築するために、OMS のセキュリティと監査では Microsoft 内部の複数のソースからのデータを使用しています。 OMS のセキュリティと監査では、このデータを活用して環境に対する潜在的な脅威を特定します。

[Threat Intelligence] \(脅威インテリジェンス\) ウィンドウは、次に示す&3; つの主要なオプションで構成されています。

* Servers with outbound malicious traffic (悪意のある送信トラフィックを持つサーバー)
* Detected threats types (検出された脅威の種類)
* 脅威インテリジェンス マップ

> [!NOTE]
> これらのオプションすべての概要については、「 [Operations Management Suite のセキュリティと監査ソリューションの概要](oms-security-getting-started.md)」を参照してください。
> 
> 

### <a name="responding-to-security-alerts"></a>セキュリティの警告への対応
[セキュリティ インシデント対応](https://technet.microsoft.com/library/cc512623.aspx) プロセスの手順の&1; つは、システムの侵害の重要度を特定することです。 このフェーズでは、次のタスクを実行する必要があります。

* 攻撃の性質を特定する。
* 攻撃の発生ポイントを特定する。
* 攻撃の目的を特定する。 その攻撃が、特定の情報を取得するために自分の組織に向けられたものか、それともランダムなものかを特定します。
* 侵害されているシステムを特定する。
* アクセスされているファイルを特定してそのファイルの機密度を判定する。

OMS のセキュリティと監査ソリューションの **[Threat Intelligence]** (脅威インテリジェンス) の情報を、これらのタスクに活用できます。 この **[Threat Intelligence]** (脅威インテリジェンス) オプションにアクセスするには、次の手順に従います。

1. **Microsoft Operations Management Suite** のメイン ダッシュボードで、**[セキュリティおよび監査]** タイルをクリックします。
   
    ![[Security and Audit]](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. **[Security and Audit]** (セキュリティおよび監査) ダッシュボードの右側に、次のような **[Threat Intelligence]** (脅威インテリジェンス) オプションが表示されます。
   
    ![Threat intel](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

これら&3; つのタイルには、現在の脅威についての概要が表示されます。 **[Servers with outbound malicious traffic]** (悪意のある送信トラフィックを持つサーバー) では、監視しているコンピューター (ネットワークの内部または外部) の中に、インターネットに対して悪意のあるトラフィックを送信しているコンピューターがないかどうかを確認できます。 

**[Detected threat types]** (検出された脅威の種類) タイルには、現在 "そのままの状態" の脅威についての概要が表示されます。このタイルをクリックすると、次のようにこれらの脅威についての詳細が表示されます。

![Detected threat types](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

それぞれの脅威をクリックすることで、さらに詳しい情報を抽出することができます。 次の例では、ボットネットの詳細が表示されています。

![more details about a threat](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

このセクションの最初に説明したとおり、この情報はインシデント対応の際に非常に役に立ちます。 また、攻撃元、侵害されたシステム、タイムラインを特定する必要があるフォレンジック調査においても重要な情報となります。 このレポートでは、攻撃元、侵害されたローカル IP、現在の接続のセッション状態など、攻撃に関する重要な詳細の一部を簡単に特定できます。 

**脅威インテリジェンス マップ** では、世界中の悪意のあるトラフィックの現在の場所を特定することができます。 このマップには、トラフィックの方向を示すオレンジ (受信側) と赤 (送信側) の矢印があり、これらの矢印の&1; つをクリックすると、次のように脅威の種類とトラフィックの方向が表示されます。

![threat intel map](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

> [!NOTE]
> インシデントへの対応プロセスでこの機能を使用する方法を示すデモンストレーションについては、Microsoft Ignite のプレゼンテーション「[Mitigate datacenter security threats with guided investigation using Operations Management Suite (Operations Management Suite を使ってガイド付き調査によってデータセンターのセキュリティの脅威を軽減する)](https://myignite.microsoft.com/videos/5000)」をご覧ください。
> 
> 

## <a name="see-also"></a>関連項目
このドキュメントでは、OMS のセキュリティと監査ソリューションの **[Threat Intelligence]** (脅威インテリジェンス) オプションを使用してセキュリティの警告に対応する方法について説明しました。 OMS セキュリティの詳細については、次の記事を参照してください。

* [Operations Management Suite (OMS) overview (Operations Management Suite (OMS) の概要)](operations-management-suite-overview.md)
* [Operations Management Suite のセキュリティと監査ソリューションの概要](oms-security-getting-started.md)
* [Operations Management Suite のセキュリティと監査ソリューションでのリソースの監視](oms-security-monitoring-resources.md)



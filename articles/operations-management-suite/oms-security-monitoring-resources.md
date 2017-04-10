---
title: "Operations Management Suite のセキュリティと監査ソリューションでのリソースの監視 | Microsoft Docs"
description: "このドキュメントでは、OMS のセキュリティと監査機能を使用した、リソースの監視とセキュリティの問題の特定に役立つ情報を提供します。"
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: d6752120-821f-4aa7-a049-25bf5a653b95
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
ms.openlocfilehash: 81d7d09f48d08943623b24e7e7a26847a0d129fe
ms.lasthandoff: 11/17/2016


---
# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>Operations Management Suite のセキュリティと監査ソリューションでのリソースの監視
このドキュメントでは、OMS のセキュリティと監査機能を使用した、リソースの監視とセキュリティの問題の特定に役立つ情報を提供します。

## <a name="what-is-oms"></a>OMS とは
Microsoft Operations Management Suite (OMS) は、Microsoft のクラウド ベースの IT 管理ソリューションです。OMS を使用して、オンプレミスとクラウドのインフラストラクチャを管理し、保護することができます。 OMS の詳細については、[Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx) に関する記事をご覧ください。

## <a name="monitoring-resources"></a>リソースの監視
セキュリティ インシデントは、可能なら常に最初の段階で発生前に防止することが大切です。 しかし、すべてのセキュリティ インシデントを防止することはできません。 セキュリティ インシデントが発生した場合は、その影響を最小限に抑える必要があります。  次に示す&3; つの重要な推奨事項に従うことで、セキュリティ インシデントの数と影響を最小限に抑えることができます。

* 環境内の脆弱性を定期的に評価する。
* すべてのコンピューター システムとネットワーク デバイスに対して、最新の修正プログラムがすべてインストールされていることを定期的にチェックする。
* オペレーティング システムのイベント ログ、アプリケーション固有のログ、侵入検知システムのログを含むすべてのログとログ記録メカニズムを定期的にチェックする。

OMS のセキュリティと監査ソリューションによって、IT 管理者はすべてのリソースを能動的に監視できるようになるため、セキュリティ インシデントの影響を最小化することができます。 OMS のセキュリティと監査には、リソースを監視するために利用できるセキュリティ ドメインがあります。 セキュリティ ドメインによって各オプションへの速やかなアクセスが可能になるほか、セキュリティを監視するうえで、次に示すドメインの監視対象がさらに詳細になります。

* マルウェアの評価
* 更新の評価
* ID とアクセス

> [!NOTE]
> これらのオプションすべての概要については、「 [Operations Management Suite のセキュリティと監査ソリューションの概要](oms-security-getting-started.md)」を参照してください。
> 
> 

### <a name="monitoring-system-protection"></a>システム保護の監視
多層防御の手法では、資産全体でのセキュリティの状態にとって、保護を構成するすべての層が重要です。 脅威が検出されたコンピューターや十分に保護されていないコンピューターは、[Security Domains] \(セキュリティ ドメイン) の下にある [Malware Assessment] \(マルウェアの評価) タイルに表示されます。 [Malware Assessment] \(マルウェアの評価) に表示される情報を利用して、保護を必要とするサーバーに対してその保護を適用する計画を立てることができます。 このオプションにアクセスするには、次の手順に従います。

1. **Microsoft Operations Management Suite** のメイン ダッシュボードで、**[セキュリティおよび監査]** タイルをクリックします。
   
    ![[Security and Audit]](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. **[Security and Audit]** \(セキュリティと監査) ダッシュボードで、**[Security Domains]** (セキュリティ ドメイン) の下にある **[Antimalware Assessment]** \(マルウェアの評価) をクリックします。 次のように、**[Antimalware Assessment]** \(マルウェアの評価) ダッシュボードが表示されます。

![マルウェアの評価](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

**[Malware Assessment]** (マルウェアの評価) ダッシュボードを使用して、次のようなセキュリティの問題を特定できます。

* **アクティブな脅威**: 侵害され、システム内にアクティブな脅威が存在しているコンピューター。
* **修復済みの脅威**: 侵害されたものの、脅威は修復されているコンピューター。
* **署名が最新ではない**: マルウェア対策は有効になっているが、署名が最新ではないコンピューター。
* **リアルタイム保護なし**: マルウェア対策がインストールされていないコンピューター。

### <a name="monitoring-updates"></a>更新プログラムの監視
セキュリティのベスト プラクティスとは、最新のセキュリティ更新プログラムを適用することであり、これは更新プログラムの管理戦略に組み込む必要があります。 Microsoft Monitoring Agent サービス (HealthService.exe) は、監視対象のコンピューターから更新プログラムの情報を読み取り、その情報を処理するためにクラウド上の OMS サービスに送信します。 Microsoft Monitoring Agent サービスは自動サービスとして構成されるため、対象のコンピューター上で常に動作している必要があります。

![更新プログラムの監視](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

更新データにはロジックが適用され、クラウド サービスによってそのデータが記録されます。 不足している更新プログラムが見つかった場合、 **[更新プログラム]** ダッシュボードに表示されます。 **[更新プログラム]** ダッシュボードを使用して、更新プログラムの適用漏れに対処したり、更新プログラムを必要とするサーバーに、それらを適用するためのプランを作成したりすることができます。 **[Updates]** (更新プログラム) ダッシュボードにアクセスするには、次の手順に従います。

1. **Microsoft Operations Management Suite** のメイン ダッシュボードで、**[セキュリティおよび監査]** タイルをクリックします。
2. **[Security and Audit]** (セキュリティと監査) ダッシュボードで、**[Security Domains]** (セキュリティ ドメイン) の下にある **[Update Assessment]** (更新の評価) をクリックします。 次のように、[Updates] \(更新プログラム) ダッシュボードが表示されます。

![更新の評価](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

このダッシュボードでは、更新の評価を実行し、コンピューターの現在の状態を把握して最も重大な脅威に対処することができます。 **[Critical or Security Updates]** (重要な更新プログラムまたはセキュリティ更新プログラム) タイルを使用すると、IT 管理者は次のような不足している更新プログラムについての詳細情報にアクセスできるようになります。

![search result](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

このレポートには、このシステムが影響を受けやすい脅威の種類を特定するために利用できる、重要な情報が表示されます。これには、セキュリティ更新プログラムに関連する Microsoft サポート技術情報の記事や、脆弱性についての詳細が記載されている Microsoft セキュリティ情報が含まれます。

### <a name="monitoring-identity-and-access"></a>ID とアクセスの監視
さまざまなデバイスを使用し、膨大な量のクラウドおよびオンプレミスのアプリにアクセスしながら、ユーザーがどこからでも作業を行うような場合、そのユーザーの資格情報を保護することが必要です。 資格情報の盗難を目的とした攻撃では、攻撃者は最初に通常ユーザーの資格情報へのアクセスを取得し、ネットワーク内のシステムにアクセスします。 多くの場合、この最初の攻撃はネットワークへのアクセスを取得するための手段にすぎず、最終的な目標は、特権を持つアカウントを見つけることです。 

攻撃者はネットワーク内に留まり、自由に利用できるツールを使って、ログオンしている他のアカウントのセッションから資格情報を抽出します。 システム構成によっては、このような資格情報をハッシュ、チケット、またはプレーンテキスト パスワードの形式で抽出することができます。  

> [!NOTE]
> インターネットに直接公開されているコンピューターでは、あらゆる種類の既知のユーザー名 (Administrator など) を使用したログイン試行が数多く失敗したことがわかります。 ほとんどのケースでは、既知のユーザー名を使用していない場合や、パスワードが十分強力である場合には問題ありません。
> 
> 

このような侵入者を、特権を持つアカウントが侵害される前に特定することは可能です。 **OMS のセキュリティと監査ソリューション** を活用すると、ID とアクセスを監視することができます。 **[Identity and Access]** (ID とアクセス) ダッシュボードにアクセスするには、次の手順に従います。

1. **Microsoft Operations Management Suite** のメイン ダッシュボードで、[Security and Audit] \(セキュリティと監査) タイルをクリックします。
2. **[Security and Audit]** (セキュリティおよび監査) ダッシュボードで、**[Security Domains]** (セキュリティ ドメイン) の下にある **[Identity and Access]** (ID とアクセス) をクリックします。 次のように、**[Identity and Access]** (ID とアクセス) ダッシュボードが表示されます。

![ID とアクセス](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

通常の監視戦略の一環として、ID の監視を行う必要があります。 IT 管理者は、試行回数の多い特定の有効なユーザー名が見つかった場合には注意する必要があります。 この場合、実際のユーザー名を入手してブルート フォースを試みている攻撃者、またはハードコーディングされた期限切れのパスワードを使用する自動ツールの存在を示している可能性があります。

このダッシュボードを使用すると、IT 管理者は ID と会社のリソースへのアクセスに関連する潜在的な脅威をすばやく特定することができます。 また、潜在的な傾向を特定することも特に重要であり、たとえば [Logons Over Time] \(時間別のログオン) タイルでは、一定期間内にログオン試行の失敗が発生した回数を確認することができます。 この場合は、コンピューター **FileServer** が 35 件のログオン試行を受け取りました。 このコンピューターをクリックすると、コンピューターの詳細を表示することができます。 

![more details](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

このコンピューター対して生成されたレポートには、この傾向についての有益な詳細情報が表示されます。 **[ACCOUNT]** (アカウント) 列にはシステムへのアクセス試行に使用されたユーザー アカウント、**[TIMEGENERATED]** (生成された時間) 列にはアクセス試行が行われた期間、**[LOGONTYPENAME]** (ログオンの種類名) 列にはアクセス試行が行われた場所が表示されます。 これらがプログラムによってシステム内でローカルに実行されていた場合、 **[PROCESS]** (プロセス) 列にはプロセスの名前が表示されます。 ログオン試行がプログラムから行われたものである場合は、この時点で既にプロセス名が表示されているため、対象のシステムでより詳細な調査を進めることができます。

## <a name="see-also"></a>関連項目
このドキュメントでは、OMS のセキュリティと監査ソリューションを使用してリソースを監視する方法について説明しました。 OMS セキュリティの詳細については、次の記事を参照してください。

* [Operations Management Suite (OMS) overview (Operations Management Suite (OMS) の概要)](operations-management-suite-overview.md)
* [Operations Management Suite のセキュリティと監査ソリューションの概要](oms-security-getting-started.md)
* [Operations Management Suite のセキュリティと監査ソリューションでのセキュリティの警告に対する監視と対応](oms-security-responding-alerts.md)



---
title: "Azure Security Center の脅威インテリジェンス | Microsoft Docs"
description: "このドキュメントは、Azure Security Center の脅威インテリジェンス機能を使用して、VM およびコンピューターの潜在的な脅威を特定するために役立ちます。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: c492662aa3ee444347c55d9771790573ad969150
ms.contentlocale: ja-jp
ms.lasthandoff: 09/14/2017

---
# <a name="threat-intelligence-in-azure-security-center"></a>Azure Security Center の脅威インテリジェンス
このドキュメントは、Azure Security Center の脅威インテリジェンスを使用して、セキュリティ関連の問題に対処するために役立ちます。

## <a name="what-is-threat-intelligence"></a>脅威インテリジェンスとは
IT 管理者は、Security Center で利用できる脅威インテリジェンス オプションを利用することで、たとえば特定のコンピューターがボットネットの一部であるかどうかを識別するなど、環境に対するセキュリティの脅威を特定することができます。 コンピューターをひそかにコマンド アンド コントロール サーバーに接続するマルウェアが攻撃者によって不正にインストールされた場合、そのコンピューターはボットネットのノードになる可能性があります。 また、ダークネットのようなアンダーグラウンド通信チャネルに由来する潜在的な脅威を特定することもできます。

Security Center では、この脅威インテリジェンスを構築するために、Microsoft 内部の複数のソースから取得したデータが使用されています。 Security Center は、このデータを活用して、環境に対する潜在的な脅威を特定します。 [Threat Intelligence] \(脅威インテリジェンス\) ウィンドウは、次に示す 3 つの主要なオプションで構成されています。

- Detected threat types
- 脅威の発生元
- 脅威インテリジェンス マップ


## <a name="when-should-i-use-threat-intelligence"></a>脅威インテリジェンスを使用する状況
[セキュリティ インシデント対応プロセス](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response)の手順の 1 つは、システムの侵害の重大度を特定することです。 このフェーズでは、次のタスクを実行する必要があります。

- 攻撃の性質を特定する。
- 攻撃の発生ポイントを特定する。
- 攻撃の目的を特定する。 その攻撃が、特定の情報を取得するために自分の組織に向けられたものか、それともランダムなものかを特定します。
- 侵害されているシステムを特定する。
- アクセスされているファイルを特定して、そのファイルの機密度を判定する。脅威インテリジェンスの情報を Security Center で利用して、これらのタスクを支援することができます。 

## <a name="how-to-access-the-threat-intelligence"></a>脅威インテリジェンスにアクセスする方法
お使いの環境の現在の脅威インテリジェンスを視覚化するには、まず、情報が存在するワークスペースを選択する必要があります。 複数のワークスペースがない場合は、ワークスペース セレクターが表示されず、**[脅威インテリジェンス]** ダッシュボードに直接移動します。 脅威インテリジェンス ダッシュボードにアクセスするには、次の手順に従います。

1.  **[Security Center]** ダッシュボードを開きます。
2.  左側のウィンドウの **[検出]** で **[脅威インテリジェンス]** をクリックします。 **[脅威インテリジェンス]** ダッシュボードが表示されます。

    ![Threat Intel](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > 最後の列に **[UPGRADE PLAN]\(プランのアップグレード\)** と表示されている場合は、このワークスペースで無料のサブスクリプションが使用されています。この機能を使用するには、標準にアップグレードする必要があります。 [REQUIRES UPDATE]\(更新が必要\) と表示される場合は、この機能を使用するために [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) を更新する必要があります。 料金プランの詳細については、「Azure Security Center の価格」を参照してください。 
    > 
3. 調査するワークスペースが複数ある場合は、このワークスペース内の悪意のある IP の現在の数を示す **[悪意のある IP]** 列に基づいて調査に優先順位を付けることができます。 使用するワークスペースを選択すると、**[脅威インテリジェンス]** ダッシュボードが表示されます。

    ![Threat Intel](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. このダッシュボードは、次の 4 つのタイルに分かれています。
    * **[脅威の種類]**: 選択したワークスペースで検出された脅威の種類の要約です。
    * **[発信元の国]**: ソースの場所に基づいてトラフィックの量を集計します。
    * **[脅威の場所]**: 使用中の環境と通信する、世界の現在の場所を特定するために役立ちます。 このマップには、トラフィックの方向を示すオレンジ (受信側) と赤 (送信側) の矢印があり、これらの矢印の 1 つをクリックすると、脅威の種類とトラフィックの方向が表示されます。
    * **[脅威の詳細]**: マップで選択した脅威に関する詳細を表示します。

選択するオプション タイルに関係なく、表示されるダッシュボードは[ログ検索](https://docs.microsoft.com/azure/security-center/security-center-search)クエリに基づいています。唯一の違いはクエリの種類とその結果です。

### <a name="threat-types"></a>脅威の種類
**[脅威の種類]** タイルをクリックすると、**[ログ検索]** ダッシュボードが表示されます。左側にはフィルター オプション、右側にはクエリの結果があります。

![脅威インテリジェンス検索](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

クエリの結果には、脅威が名前ごとに集計されて表示されます。左側のウィンドウを使用して、フィルター処理する属性を選択できます。たとえば、現在マシンに接続されている脅威のみを表示する場合は、**[SESSIONSTATE]** で **[接続済み]** をクリックし、**[適用]** ボタンをクリックします。

![セッションの状態](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Azure VM の場合、エージェントを通じて送信されるネットワーク データだけが脅威インテリジェンス ダッシュボードに表示されます。 次のデータ型は、脅威インテリジェンスでも使用されます。

- CEF データ (種類 = CommonSecurityLog)
- WireData (種類 = WireData)
- IIS ログ (種類 = W3CIISLog)
- Windows ファイアウォール (種類 = WindowsFirewall)
- DNS イベント (種類 = DnsEvents)


## <a name="see-also"></a>関連項目
このドキュメントでは、Security Center で脅威インテリジェンスを使用して、疑わしいアクティビティの特定を支援する方法を学習しました。 Azure セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティの警告の管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 Security Center でアラートを管理し、セキュリティ インシデントに対応する方法を説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 さまざまな種類のセキュリティ アラートについて説明します。
* [Azure Security Center トラブルシューティング ガイド](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 Security Center における一般的な問題のトラブルシューティング方法について説明します。 
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)。 このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。



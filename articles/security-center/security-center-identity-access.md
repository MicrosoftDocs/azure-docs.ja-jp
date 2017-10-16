---
title: "Azure Security Center での ID とアクセスの監視 | Microsoft Docs"
description: "このドキュメントは、Azure Security Center で ID とアクセス機能を使用して、ユーザーのアクセス アクティビティと ID 関連の問題を監視するために役立ちます。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 2e351cd38e7baefc09fa36ceabffec92de22433b
ms.contentlocale: ja-jp
ms.lasthandoff: 09/14/2017

---
# <a name="monitoring-identity-and-access-in-azure-security-center"></a>Azure Security Center での ID とアクセスの監視
このドキュメントは、Azure Security Center を使用してユーザーの ID とアクセス アクティビティを監視するために役立ちます。

## <a name="why-monitor-identity-and-access"></a>ID とアクセスを監視する理由
ID は企業のコントロール プレーンであり、ID の保護は最優先事項である必要があります。 以前は組織の周りには境界が存在し、その境界が主な防衛線の 1 つとなっていましたが、最近ではクラウドに移行するデータやアプリが増加しているため、ID が新たな境界線となっています。

ID アクティビティを監視することにより、インシデントが発生する前に事前対応型のアクションを実行するか、攻撃が試みられた場合にそれを阻止する事後対応型のアクションを実行することができます。 [ID およびアクセス] ダッシュボードには、失敗したログオン試行の回数、そのログオン試行の際に使用されたユーザー アカウント、ロックアウトされたアカウント、パスワードが変更またはリセットされたアカウント、現在ログインしているアカウントの数など、ID の状態の概要が表示されます。

## <a name="how-to-monitor-identity-and-access-activities"></a>ID とアクセス アクティビティを監視する方法
次の手順に従って、現在のアクティビティに関連する ID とアクセスを視覚化します。**[ID およびアクセス]** ダッシュボードにアクセスする必要があります。

1.  **[Security Center]** ダッシュボードを開きます。
2.  左側のウィンドウの **[防止]** で **[ID およびアクセス]** をクリックします。 複数のワークスペースがある場合は、ワークスペースのセレクターが表示されます。

    ![ワークスペースの選択](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > 最後の列に **[UPGRADE PLAN]\(プランのアップグレード\)** と表示されている場合は、このワークスペースで無料のサブスクリプションが使用されています。この機能を使用するには、標準にアップグレードする必要があります。 [REQUIRES UPDATE]\(更新が必要\) と表示される場合は、この機能を使用するために [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) を更新する必要があります。 料金プランの詳細については、「Azure Security Center の価格」を参照してください。 
    > 
3. 調査するワークスペースが複数ある場合は、このワークスペースで失敗したログオン試行の現在の回数を示す **[失敗したログオン]** 列に基づいて調査に優先順位を付けることができます。 使用するワークスペースを選択すると、**[ID およびアクセス]** ダッシュボードが表示されます。

    ![identity and access](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. このダッシュボードに表示される情報は、潜在的な疑わしいアクティビティを特定するのにすぐに役立ちます。 このダッシュボードは、3 つの主な領域に分かれています。
    * **[ID ポスチャ]**: このワークスペースで発生している ID 関連のアクティビティを要約します。
    * **[失敗したログオン]**: 失敗したログオン試行の主な原因をすばやく特定できるようにし、ログオン試行に失敗した上位 10 件のアカウントの一覧を表示します。
    * **[ログオン (時系列)]**: ログオン量を時系列ですばやく特定できるようにし、ログオン試行数が上位のコンピューター アカウントの一覧を表示します。
    
選択するタイルに関係なく、表示されるダッシュボードは[ログ検索クエリ](https://docs.microsoft.com/azure/security-center/security-center-search)に基づいています。唯一の違いはクエリの種類とその結果です。 それでも、たとえばコンピューターなどの項目を選択してクリックすると、関連データが表示されます。 

## <a name="see-also"></a>関連項目
このドキュメントでは、Azure Security Center で ID とアクセスを監視する方法について説明しました。 Azure セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティの警告の管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 Security Center でアラートを管理し、セキュリティ インシデントに対応する方法を説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 さまざまな種類のセキュリティ アラートについて説明します。
* [Azure Security Center トラブルシューティング ガイド](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 Security Center における一般的な問題のトラブルシューティング方法について説明します。 
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)。 このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。



---
title: Azure Security Center のカスタム アラート ルール | Microsoft Docs
description: このドキュメントは、Azure Security Center でカスタム アラート ルールを作成する際に役立ちます。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f335d8c4-0234-4304-b386-6f1ecda07833
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: rkarlin
ms.openlocfilehash: 9bf4f604386b47e4ca36df69fb140124b097f548
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576898"
---
# <a name="custom-alert-rules-in-azure-security-center-preview"></a>Azure Security Center のカスタム アラート ルール (プレビュー)
このドキュメントは、Azure Security Center でカスタム アラート ルールを作成する際に役立ちます。

> [!NOTE]
> カスタム アラートは間もなく廃止されます。次に紹介する代替のサービスについて、詳細を参照してください。

## <a name="retirement-of-custom-alert-rules-in-azure-security-center"></a>Azure Security Center のカスタム アラート ルールの提供終了

カスタム アラート エクスペリエンスは、Azure Monitor クラシック アラート サービスの提供終了のため、2019 年 6 月 30 日に提供が終了されます。 非推奨となるまでの期間、ユーザーは、既存のカスタム アラート ルールを編集することはできますが、新しいものを追加することはできません。
ユーザーは、ワンクリック オンボードで、[Azure Sentinel](https://azure.microsoft.com/en-us/services/azure-sentinel/) を有効にし、既存のアラートを自動的に移行し、新しいアラートを作成するか、または Azure Monitor ログ アラートでアラートを再作成することをお勧めします。
                                     
既存のアラートを保持し、それらを Azure Sentinel に移行するには、[Azure Sentinel を起動](https://portal.azure.com/#create/Microsoft.ASI/preview)してください。 最初の手順で、カスタム アラートが格納されているワークスペースを選択し、[分析] メニュー項目を選択して、アラートを自動的に移行します。

> [!NOTE]
> Azure Sentinel へのカスタム アラートの移行は、選択したワークスペース内のすべてのカスタム アラートの 1 回限りの移行です。 移行の完了後、その選択したワークスペースのカスタム アラートは Azure Security Center を使用してアクセスできなくなります。
>
> [Search](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/search-queries) または [Union](https://docs-analytics-eus.azurewebsites.net/queryLanguage/query_language_unionoperator.html) ステートメント クエリを使用したカスタム アラートは、Azure Sentinel でサポートされておらず、移行されません。 移行を実行する前に、これらのアラートを編集してください。

Azure Monitor ログ アラートを使用してアラートを再作成するには、次を参照してください。ログ アラートを作成する方法の手順については、「[Azure Monitor を使用してログ アラートを作成、表示、管理する](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/alerts-log)」を参照してください。 Azure Monitor のログ アラートの一般的な概要については、[ここ](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/alerts-unified-log)をクリックしてください。

## <a name="what-are-custom-alert-rules-in-security-center"></a>Security Center のカスタム アラート ルールとは

Security Center には、事前定義された一連の[セキュリティ アラート](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)が用意されています。これらは、脅威または疑わしいアクティビティが発生した際にトリガーされます。 一部のシナリオでは、環境の特定のニーズに対応するために、カスタム アラートの作成が必要になる場合があります。

Security Center のカスタム アラート ルールを使用すると、環境から既に収集されているデータに基づいて、新しいセキュリティ アラートを定義できます。 クエリを作成し、これらのクエリの結果をカスタム ルールの条件として使用できます。ルールは、この条件が満たされたときに実行されます。 カスタム クエリの作成には、コンピューターのセキュリティ イベント、パートナーのセキュリティ ソリューション ログ、または API を使用して取り込まれたデータを使用できます。

> [!NOTE]
> カスタム アラートは、Security Center の[調査機能](security-center-investigation.md)ではサポートされていません。
>
>

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>Security Center でカスタム アラート ルールを作成する方法

**[セキュリティ センター]** ダッシュボードを開き、次の手順に従ってカスタム アラート ルールを作成します。

1.  左側のウィンドウの **[検出]** で、**[Custom alert rules (Preview)]\(カスタム アラート ルール (プレビュー)\)** をクリックします。
2.  **[Security Center – Custom alert rules (Preview)]\(Security Center – カスタム アラート ルール (プレビュー)\)** ページで、**[新しいカスタム アラート ルール]** をクリックします。

    ![カスタム アラート](./media/security-center-custom-alert/security-center-custom-alert-fig1.png)

3.  次のオプションを含む [カスタムのアラート ルールの作成] ページが表示されます。

    ![Create](./media/security-center-custom-alert/security-center-custom-alert-fig2.png)

4.  **[名前]** フィールドに、このカスタム ルールの名前を入力します。
5.  **[説明]** フィールドに、このルールの目的を示す簡単な説明を入力します。
6.  **[重大度]** フィールドで、必要性に応じた重大度レベル (高、中、低) を選択します。
7.  **[サブスクリプション]** フィールドで、このルールを適用できるサブスクリプションを選択します。
8.  **[ワークスペース]** フィールドで、このルールで監視するワークスペースを選択し、**[検索クエリ]** フィールドに、結果の取得に使用するクエリを入力します。

    > [!NOTE]
    > カスタム アラートの格納先として選択するワークスペースに対する書き込みアクセス許可が必要です。
    >
    >

    このクエリの結果によってアラートがトリガーされます。 有効なクエリを入力すると、このフィールドの右隅に緑色のチェック マークが表示されることに注意してください。

    ![Query](./media/security-center-custom-alert/security-center-custom-alert-fig3.png)

10. **[期間]** フィールドで、上記のクエリが実行される期間を選択します。 選択する期間に応じて、このフィールドの下部にある検索結果が変わることに注意してください。

    ![期間](./media/security-center-custom-alert/security-center-custom-alert-fig4.png)

11. **[評価]** フィールドで、このルールを評価して実行する頻度を選択します。
12. **[結果の数]** フィールドで、演算子を選択します ([次の値より大きい] または [次の値より小さい])。
13. **[しきい値]** フィールドに、先ほど選択した演算子の参照として使用される数を入力します。
14. このルールの次のアラートが Security Center によって送信されるまでの待ち時間を設定する場合は、**[アラートを表示しない]** オプションを有効にします。
15. **[OK]** をクリックして終了します。

新しいアラート ルールの作成が完了すると、カスタム アラート ルールの一覧にそれが表示されます。 そのルールの条件が満たされると、新しいアラートがトリガーされます。これは **[セキュリティ アラート]** ダッシュボードで確認できます。

![アラート:](./media/security-center-custom-alert/security-center-custom-alert-fig5.png)

ルールの作成時に指定したパラメーター (検索クエリやしきい値など) がこのカスタム ルールのアラートに表示されることに注意してください。

## <a name="see-also"></a>関連項目
このドキュメントでは、Azure Security Center でカスタム アラート ルールを作成する方法について説明しました。 Azure セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティの警告の管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 Security Center でアラートを管理し、セキュリティ インシデントに対応する方法を説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 さまざまな種類のセキュリティ アラートについて説明します。
* [Azure Security Center トラブルシューティング ガイド](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 Security Center における一般的な問題のトラブルシューティング方法について説明します。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)。 このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

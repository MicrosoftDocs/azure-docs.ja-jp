---
title: Azure Security Center でのインシデントとアラートの調査 | Microsoft Docs
description: このドキュメントは、Azure Security Center の調査機能を使用して、セキュリティ インシデントとアラートを調査する際に役立ちます。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: a8e894a9-8781-4749-ae8f-8c8e01658566
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: memildin
ms.openlocfilehash: 32cc4b01d68ee7a15ae7cfa2259d234cbcd143c4
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202048"
---
# <a name="investigate-incidents-and-alerts-in-azure-security-center-retired"></a>Azure Security Center でのインシデントとアラートの調査 (廃止)
このドキュメントは、Azure Security Center の調査機能 (プレビュー) を使用して、セキュリティ インシデントとアラートを調査するときに役立ちます。

> [!NOTE]
> アラートとインシデントの調査 (プレビュー) エクスペリエンスは、2019 年 7 月 31 日に廃止されました。 詳細および代替サービスについては、「[Security Center の機能の廃止 (2019 年 7 月)](security-center-features-retirement-july2019.md#menu_investigate)」を参照してください。

## <a name="what-is-investigation-in-security-center"></a>Security Center の調査とは
Security Center の調査機能を使用すると、潜在的な[セキュリティ インシデント](https://docs.microsoft.com/azure/security-center/security-center-incident)について、トリアージ、範囲の把握、根本原因の特定を行うことできます。

その目的は、調査中のインシデントに関するすべてのエンティティ ([セキュリティ アラート](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)、ユーザー、コンピューター、インシデント) を関連付けて、調査プロセスを容易にすることです。  Security Center では、重要なデータとすべての関連するエンティティを相互に関連付けたうえで、対象の検索と重要な情報の視覚化に役立つライブ グラフを使ってその相関関係を明らかにすることで、これを実現します。


> [!NOTE]
> * [カスタム アラート](security-center-custom-alert.md)は、Security Center の調査機能ではサポートされていません。
> * 調査は、Windows サーバーから収集されたデータに基づくアラートに対してのみサポートされています。


## <a name="how-investigation-works"></a>調査のしくみ
調査は、調査ダッシュボードの中央の領域を占めるグラフで構成されます。 このグラフでは、常に特定のエンティティに焦点が当てられており、それに関連するエンティティが表示されています。 エンティティは、セキュリティ アラート、ユーザー、コンピューター、またはインシデントの場合があります。

![マップ](./media/security-center-investigation/security-center-investigation-fig1.png)

ユーザーは、グラフ中のエンティティをクリックすることで、別のエンティティに移動できます。 選択したエンティティとその関連エンティティに自動的に中心になります。 関連がなくなったエンティティは、グラフから削除される場合があります。

### <a name="investigation-path"></a>調査パス
ユーザーが別のエンティティに移動する際には、調査のコンテキストを追跡するのに調査パスが役立ちます。これにより、迅速な移動が可能になります。 調査結果が含まれているインシデントは、常に調査パスの左端にあります。

![パス](./media/security-center-investigation/security-center-investigation-fig2.png)

### <a name="general-information"></a>一般情報
グラフにエンティティが表示されると、そのエンティティに関する追加の情報がタブに表示されます。 **[情報]** タブでは、利用可能なさまざまな情報ソースから取得されたエンティティの一般情報が表示されます。

![一般情報](./media/security-center-investigation/security-center-investigation-fig3.png)

この情報タブでは、マップで選択されているインシデントに関する情報が表示されます。 インシデントは、調査の結果が含まれているコンテナーです。 すべての調査は、インシデントのコンテキストで行われます。

インシデントが作成されるのは、ユーザーが特定のアラートの **[調査の開始]** ボタンをクリックした場合のみです。 調査担当者が利用できる基本的な機能として、ユーザー、コンピューター、アラートなどのエンティティのマークがあります。 エンティティを関連項目としてマークしたら、理由を入力します。 その後、このエンティティは、グラフのインシデントの直下とインシデント エンティティの一覧に表示されます。

### <a name="entities"></a>エンティティ

**[エンティティ]** タブでは、すべての関連エンティティが種類ごとに表示されます。 これは、グラフに表示されるエンティティが多すぎる場合と、エンティティ名が長すぎて表形式の方が確認しやすい場合の、2 つのケースで役に立ちます。

![エンティティ](./media/security-center-investigation/security-center-investigation-fig4.png)

### <a name="search"></a>Search

**[検索]** タブでは、エンティティで利用できるすべてのログの種類が表示されます。 ログの種類ごとに、利用できる記録の数を確認できます。 それぞれのログの種類をクリックすると、検索画面に移動します。 検索画面では、検索を絞り込んだり、さまざまな検索機能 (アラート設定など) を使用したりできます。 現在のリリースでは、[検索] タブはユーザーとコンピューターのエンティティにのみ使用できます。

![Search](./media/security-center-investigation/security-center-investigation-fig5.png)

### <a name="exploration"></a>探索

**[探索]** タブでは、調査担当者は、エンティティに関する各種の問題に関連したデータを確認できます。 たとえば、マシンを調査する場合、そこで実行されたプロセスの一覧が [探索] タブに表示されます。場合によっては、疑わしい問題を示すデータが [探索] タブに表示されることがあります。 調査担当者は、タブ内のデータを確認できるほか、それを検索画面で開いて、大量のデータを確認したり、高度な検索オプション (フィルター処理や Excel へのエクスポート) を使用したりできます。

![探索](./media/security-center-investigation/security-center-investigation-fig6.png)

### <a name="timeline"></a>タイムライン

グラフと各種タブに表示されるデータのほとんどは、ある特定の期間に関するものです。 この時間範囲は、グラフの左上にある時間範囲セレクターを使用して設定します。 調査担当者は、さまざまな方法で期間を選択できます。

![タイムライン](./media/security-center-investigation/security-center-investigation-fig7.png)

次の項目は時間範囲の影響を受けます。

- グラフ内のユーザーとコンピューターの関係: この時間範囲内にコンピューターにログインしたユーザーのみが表示されます。
- コンピューターとユーザーの確認時に表示されるアラート: 時間範囲内に発生したアラートのみが表示されます。
- [エンティティ] タブにはグラフと同じロジックが適用されます。

次の項目は、期間の選択に関係なく表示されます。

- アラートが表示された場合、それに含まれているすべてのエンティティ (ユーザーやコンピューター) が常に表示されます。
- インシデントにエンティティが含まれている場合、それが表示されます。

> [!NOTE]
> **[検索]** タブと **[探索]** タブでは、この時間範囲内の記録のみが表示されます。

## <a name="how-to-perform-an-investigation"></a>調査の実行方法

調査は、セキュリティ インシデントかアラートから開始できます。選択するオプションは必要に応じて異なります。 以下の手順は、アラートから調査を開始する際に使用します。

1.  **[Security Center]** ダッシュボードを開きます。
2.  **[セキュリティ アラート]** をクリックして、調査するインシデントを選択します。
3.  インシデントのページで **[調査の開始]** ボタンをクリックすると、 **[調査]** ダッシュボードが表示されます。

    ![アラート:](./media/security-center-investigation/security-center-investigation-fig8.png)

4. このダッシュボードから、マップのエンティティを選択できます。また、このエンティティについての関連情報が画面の右側に表示されます。

    ![調査ダッシュボード](./media/security-center-investigation/security-center-investigation-fig9.png)

この時点で、このインシデントに関連するエンティティを探索し、それぞれのエンティティについて詳しく調べることができるようになります。

## <a name="see-also"></a>関連項目
このドキュメントでは、Security Center の調査機能を使用する方法について説明しました。 セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティの警告の管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 Security Center でアラートを管理し、セキュリティ インシデントに対応する方法を説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 さまざまな種類のセキュリティ アラートについて説明します。
* [Azure Security Center トラブルシューティング ガイド](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 Security Center における一般的な問題のトラブルシューティング方法について説明します。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)。 このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

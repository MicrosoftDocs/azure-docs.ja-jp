---
title: Power BI アプリ オファー - Azure Marketplace | Microsoft Docs
description: Microsoft AppSource マーケットプレースに Power BI アプリを発行する方法を示します。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: pbutlerm
ms.openlocfilehash: f18a1b05e5a38a79945d8df6706dd2147d6b43df
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798104"
---
# <a name="power-bi-app-offer"></a>Power BI アプリ オファー

|              |                                |
|--------------|--------------------------------|
| この記事では、Power BI アプリを Microsoft [AppSource マーケットプレース](https://appsource.microsoft.com/)に発行する方法について説明します。  Power BI アプリには、データセット、レポート、およびダッシュボードを含むカスタマイズ可能な Power BI コンテンツがパッケージ化されます。 AppSource を通してアプリを他の Power BI テナントにデプロイし、開発者によって許可されている調整とカスタマイズを実行し、それをご自身のデータに接続できます。 | ![Power BI アイコン](./media/powerbi-icon.png) |


この記事は、次の 3 つの部分に分かれています。

-   [前提条件](./cpp-prerequisites.md)。 Power BI アプリ オファーを作成または発行するための技術要件およびビジネス要件です。
-   [Power BI アプリ オファーを作成する](./cpp-create-offer.md)。 [Cloud パートナー ポータル](https://cloudpartner.azure.com)を使用して、Power BI アプリ オファーのエントリを作成する方法です。
-   [Power BI アプリ オファーを発行する](./cpp-publish-offer.md)。 AppSource に発行する新しいオファーを送信する方法と、既存のオファーを更新する方法です。


## <a name="publishing-steps"></a>発行手順

Power BI アプリ オファーを発行するための手順の概要は、次のとおりです。

![Power BI アプリ オファーの発行手順](media/publishing-steps.png)

Power BI アプリ オファーの発行プロセスを次に示します。

1. Power BI でテンプレート アプリケーションを作成します。 このアクションによって、オファーの基本技術資産を表すパッケージ インストール URL が生成されます。 さらに、この時点で、テスト パッケージを実稼働前に昇格させます。 詳細については、「[Power BI テンプレート アプリとは](https://docs.microsoft.com/power-bi/service-template-apps-overview)」を参照してください。 
2. オファーの正式名称、説明、ロゴなどのマーケティング資料を収集するか作成します。 
3. オファーの法律文書とサポート文書を収集するか作成します ("*利用規約*"、"*プライバシー ポリシー*"、"*サポート ポリシー*"、ユーザー ヘルプなど)。
4. オファーを作成します。Cloud パートナー ポータルを使用して、オファーの説明、マーケティング資料、法務情報、サポート情報、資産の仕様など、オファーに関する詳細を構成します。  オファーを完全に指定した後、それを送信して発行します。
5. Cloud パートナー ポータルで発行プロセスを監視します。  この手順の間、AppSource オンボーディング チームによって、お客様のアプリケーションのテスト、検証、および認定が行われます。 
6. アプリが無事に認定されたら、テスト環境で確認してからリリースします。 
7. Power BI アプリが AppSource に表示されます ("一般公開" されます)。
8. Power BI で、実稼働前パッケージを運用に昇格させます。 詳細については、「[テンプレート アプリのリリースを管理する](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release)」をご覧ください。


## <a name="next-steps"></a>次の手順

Power BI アプリ オファーを作成して AppSource に発行するには、その前に、Power BI アプリを AppSource に発行するための[要件](./cpp-prerequisites.md)を満たしている必要があります。

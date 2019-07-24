---
title: Azure SaaS アプリケーション オファー体験版の構成 | Microsoft Docs
description: Azure Marketplace にある SaaS アプリケーション オファーの体験版を構成します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: b12ba53f847b46479b3100c088c29372b58c1b8e
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649035"
---
# <a name="saas-application-test-drive-tab"></a>SaaS アプリケーションの [体験版] タブ

顧客に試用体験を提供するには、[体験版] タブを使用します。

## <a name="test-drive-benefits"></a>体験版の利点

顧客向けの使用体験を作成することは、顧客が確実に信頼して購入できるようにするためのベスト プラクティスです。 使用できる試用オプションのうち、高品質のリードを生成し、それらのリードからのコンバージョン率を高めるうえで体験版は最も効果的です。

体験版では、実際の実装シナリオの中で製品の主な機能や利点を体験できる、実践的なセルフガイド型の試用版を顧客に提供します。

## <a name="how-a-test-drive-works"></a>体験版のしくみ

潜在顧客が Marketplace でアプリケーションを検索して発見します。 顧客がサインインし、使用条件に同意します。 この時点で顧客は、あらかじめ構成された環境を受け取り、決められた時間数だけ試すことができます。一方、発行元はフォローアップすべき見込みの高いリードを獲得します。 詳細については、「[体験版とは](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)」を参照してください。

## <a name="publishing-steps"></a>発行手順

体験版を追加する場合の主な発行手順は次のとおりです。

1. 体験版シナリオを定義する
2. Resource Manager テンプレートをビルドし、変更する
3. 体験版のステップバイステップのマニュアルを作成する
4. オファーの再発行

## <a name="setting-up-a-test-drive"></a>体験版をセットアップする

異なる 4 種類の体験版を利用できます。それぞれが、製品の種類、シナリオ、登録しているマーケットプレースに基づいています。

|  **Type**          |  **説明**  |  **セットアップの手順**  |
|  ---------------   |  ---------------  |  ---------------  |
|     Azure Resource Manager               |    Azure Resource Manager 体験版は、発行元によって構築されるソリューションを構成しているすべての Azure リソースが含まれたデプロイ テンプレートです。 この種類の体験版に適合する製品は、Azure リソースのみを使用する製品です。               |       [Azure Resource Manager の体験版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)            |
|       ホストされている             |       ホストされた体験版では、体験版のユーザー プロビジョニングとプロビジョニング解除を実行するサービスを Microsoft がホストして管理するので、セットアップの複雑さがなくなります。             |         [ホストされた体験版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive)          |
|      ロジック アプリ              |       ロジック アプリ体験版は、あらゆる複雑なソリューション アーキテクチャに対応するように意図されたデプロイ テンプレートです。 すべての Dynamics アプリケーションやカスタム製品には、この種類の体験版を使用する必要があります。            |      [ロジック アプリの体験版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)             |
|       Power BI             |         Power BI 体験版は、カスタム ビルドされたダッシュボードへの埋め込みリンクで構成されています。 インタラクティブな Power BI の視覚化のデモンストレーションを行う製品には、この種類の体験版を使用する必要があります。 必要なのは、埋め込み Power BI の URL をアップロードすることだけです。          |        [Power BI の体験版](#power-bi-test-drive)           |

### <a name="power-bi-test-drive"></a>Power BI の体験版

以下の手順に従って体験版を構成します。

1. [新しいプラン] の **[体験版]** を選択します。
2. [体験版] で、**[はい]** を選択します。

   ![体験版を有効にする](./media/saas-enable-test-drive.png)

   体験版を有効にすると、[詳細] フォームと [Technical Configuration]\(技術的構成) フォームが表示されます。それらは次の画面キャプチャに示されています。

   ![体験版の構成フォーム](./media/saas-test-drive-yes.png)

3. **[詳細]** で、以下のフィールドの情報を指定します。
  
   - [説明] – 体験版と、ユーザーがそれを使用して何を実行できるかについて説明します。 この説明には、基本的な HTML タグを使用して書式を設定できます。
   - [User Manual]\(ユーザー マニュアル) – 顧客が体験版を使用しているときに利用できるユーザー マニュアルのドキュメントをアップロードします。 このマニュアルは .pdf ファイルにする必要があります。
   - [Test Drive Demo Video (optional)]\(体験版のデモ ビデオ (オプション)) - 顧客が体験版を使用する前に見るビデオ (YouTube または Vimeo) を提供できます。 ビデオへの URL を指定します。

4. **[Technical Configuration]\(技術的構成)** では、以下のフィールドの情報を指定します。

   - [Type of Test Drive]\(体験版の種類) – ドロップダウン リストから **[Power BI]** を選択します。
   - Power BI ダッシュ ボードへのリンク – ダッシュ ボードへのリンクを提供します。

5. 体験版の構成が終了したら、**[保存]** を選択します。


## <a name="next-steps"></a>次の手順

[[ネットショップの詳細] タブ](./cpp-storefront-tab.md)
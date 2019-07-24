---
title: Azure アプリケーション プランの体験版 | Microsoft Docs
description: Azure Marketplace 上の Azure アプリケーション プランの体験版を構成する方法。
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
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: d51c7330acd37d8a861c136ea85a2ff456eb264e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57446488"
---
# <a name="azure-applications-test-drive-tab"></a>Azure アプリケーションの [体験版] タブ

お客様の顧客に試用エクスペリエンスを提供するには、[体験版] タブを使用します。

## <a name="test-drive-benefits"></a>体験版の利点

顧客向けの使用体験を作成することは、顧客が確実に信頼して購入できるようにするためのベスト プラクティスです。 使用できる試用オプションのうち、高品質のリードを生成し、それらのリードからのコンバージョン率を高めるうえで体験版は最も効果的です。

実際の実装シナリオの中で製品の主な機能や利点を体験できる、実践的なセルフガイド型の試用版を顧客に提供します。

## <a name="how-a-test-drive-works"></a>体験版のしくみ

潜在顧客が Marketplace でアプリケーションを検索して発見します。 顧客がサインインし、使用条件に同意します。 この時点で顧客は、あらかじめ構成された環境を受け取り、決められた時間数だけ試すことができます。一方、発行元はフォローアップすべき見込みの高いリードを獲得します。 詳細については、「[体験版とは](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)」を参照してください。

## <a name="setting-up-a-test-drive"></a>体験版の設定

体験版を有効にして構成するには、次の手順を使用します。

### <a name="to-enable-a-test-drive"></a>体験版を有効にするには:

1. **[新しいプラン]** の **[体験版]** タブを選択します。
2. **[体験版]** の **[Enable a Test Drive]\(体験版を有効にする\)** で **[はい]** を選択します。

   ![体験版を有効にする](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>体験版を構成するには:

体験版を有効にしたら、次のフォームに入力を行って体験版を設定します。
  
 - 詳細
 - 技術的構成
 - 体験版デプロイ サブスクリプションの詳細

次の画面キャプチャは、体験版のすべてのフォームを示しています。 フィールド名に付いているアスタリスク (*) は、それが必須であることを示します。 

![体験版を構成する](./media/managed-app-configure-testdrive.png)

次の表では、お客様のマネージド アプリケーションの体験版を設定するうえで必須のフィールドについて説明します。

|    **フィールド**       |  **説明**  |
|  ---------------   |  ---------------  |
|      説明              |   お客様の体験版でできることについて説明します。 この説明は、基本的な HTML タグを使用して書式設定できます。 たとえば、&lt;p&gt;、&lt;em&gt;、&lt;ul&gt;、&lt;li&gt;、&lt;ol&gt; のほか、見出しが使用できます。                |
|    ユーザー マニュアル                |     お客様の顧客が体験版を利用する際に使用できるユーザー マニュアルをアップロードします。 このドキュメントは .pdf ファイルでなければなりません。              |
|         Test Drive Demo Video (体験版のデモ ビデオ) (省略可)           |       お客様の体験版のビデオ チュートリアルを提供できます。 顧客は体験版を利用する前にこのビデオを視聴できます。 YouTube または Vimeo にあるビデオの URL を指定します。 **[+ ビデオの追加]** を選択した場合、次の情報を指定するように求められます。<ul><li>Name</li><li>URL</li><li>サムネイル (PNG 形式、533 x 324 ピクセル)</li></ul>            |
|       Instances             |        お客様が必要とするインスタンスの数、リージョン、お客様の顧客が体験版を取得できる速さを構成します。 詳細については、「[体験版の発行方法](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive)」を参照してください。           |
|       Test Drive Duration (hours) (体験版の期間 (時間))             |       時間数を整数で入力してください。 指定できる範囲は 1 から 999 です。            |
|        Test Drive ARM Template (体験版 ARM テンプレート)            |        お客様のアプリの Azure Resource Manager テンプレートが含まれた圧縮 (.zip) ファイルをアップロードします。 詳細については、「[Azure Resource Manager の体験版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)」を参照してください。            |
|        Access Information (アクセス情報)            |         お客様の顧客が体験版を入手した後のアクセス情報を指定します。 たとえば、体験版にアクセスするための URL やサインイン情報が該当します。 。 この説明は、基本的な HTML タグを使用して書式設定できます。 たとえば、&lt;p&gt;、&lt;em&gt;、&lt;ul&gt;、&lt;li&gt;、&lt;ol&gt; のほか、見出しが使用できます。          |
|       Access Subscription Id (アクセス サブスクリプション ID)             |       Azure サービスと Azure portal へのアクセス権を付与します。 このサブスクリプションで、リソースの使用状況がレポートされ、サービスが課金されます。 体験版専用の独立した Azure サブスクリプションがまだない場合は、サブスクリプションを作成してください。             |
|          Azure AD Tenant Id (Azure AD テナント ID)          |        Azure Active Directory の既存のテナントを指定するか、この体験版用にテナントを作成します。           |
|         Azure AD App Id (Azure AD アプリ ID)           |       新しいアプリケーションを作成して登録します。 Microsoft は、このアプリケーションを使用して、お客様の体験版インスタンスの操作を実行します。            |
|          Azure AD App Key (Azure AD アプリ キー)          |         アプリの認証キーを作成して、このフィールドに貼り付けてください。          |

すべての必須情報を指定した後、**[保存]** を選択すれば、体験版の設定は完了です。

## <a name="next-steps"></a>次の手順

[[Marketplace] タブ](./cpp-marketplace-tab.md)
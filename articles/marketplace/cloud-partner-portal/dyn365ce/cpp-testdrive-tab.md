---
title: Dynamics 365 for Customer Engagement アプリケーション オファーの [体験版] タブ - Azure Marketplace | Microsoft Docs
description: AppSource Marketplace で Dynamics 365 for Customer Engagement アプリケーション オファーの体験版を構成する方法です。
services: Azure, Marketplace, AppSource, Cloud Partner Portal, Dynamics 365 for Customer Engagement
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
ms.date: 12/25/2018
ms.author: pbutlerm
ms.openlocfilehash: 373312b4c7f05fe41c9ca8165b8ff6f1b0e56f1b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454971"
---
# <a name="dynamics-365-for-customer-engagement-application-test-drive-tab"></a>Dynamics 365 for Customer Engagement アプリケーションの [体験版] タブ

顧客向けの試用エクスペリエンスを作成するには、**[体験版]** タブを使用します。  実際の実装シナリオの中でオファーの主な機能や利点を体験できる、実践的なセルフガイド型の試用版を顧客に提供します。  使用できる試用オプションのうち、高品質のリードを生成し、それらのリードからのコンバージョン率を高めるうえで体験版は最も効果的です。  詳細については、「[体験版とは](../test-drive/what-is-test-drive.md)」を参照してください。

Dynamics 365 アプリケーションの体験版エクスペリエンスは、Microsoft ホスト ソリューションとして自動的に実行されます。  詳しくは、「[ホストされた体験版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive)」をご覧ください。

[体験版] タブには、次の 3 つのセクションが表示される可能性があります:**[体験版]**、**[詳細]**、**[Technical Configuration]\(技術構成\)**。  最後の 2 つのセクションは、体験版の機能を有効にした後でのみ表示されます。  フィールド名に付いているアスタリスク (*) は、それが必須であることを示します。 


## <a name="test-drive-section"></a>[体験版] セクション

この機能を有効にするには、**[Enable a Test Drive]\(体験版を有効にする\)** を **[はい]** に設定します。


## <a name="details-section"></a>[詳細] セクション

**[詳細]** セクションでは、体験版の基本的な情報を提供します。   

![[体験版] の [詳細] セクション](./media/test-drive-tab-details.png)

次の表では、Dynamics 365 アプリケーションの体験版を設定するために必要なフィールドについて説明します。

|      フィールド                    |    説明                  |
|    ---------                  |  ---------------                |
|      説明              |   お客様の体験版でできることについて説明します。 この説明は、基本的な HTML タグを使用して書式設定できます。 たとえば、&lt;p&gt;、&lt;em&gt;、&lt;ul&gt;、&lt;li&gt;、&lt;ol&gt; のほか、見出しが使用できます。  |
|  ユーザー マニュアル                  |   お客様の顧客が体験版を利用する際に使用できるユーザー マニュアルをアップロードします。 このドキュメントは .pdf ファイルでなければなりません。              |
|  Test Drive Demo Video (体験版のデモ ビデオ) (省略可) |  お客様の体験版のビデオ チュートリアルを提供できます。 顧客は体験版を利用する前にこのビデオを視聴できます。 YouTube または Vimeo にあるビデオの URL を指定します。 **[+ ビデオの追加]** を選択した場合、次の情報を指定するように求められます。<ul><li>Name</li><li>URL</li><li>サムネイル (PNG 形式、533 x 324 ピクセル)</li></ul>  |
|   |   |


## <a name="technical-configuration-section"></a>[Technical Configuration]\(技術構成\) セクション

このセクションでは、体験版についての技術的な詳細を提供します。

![[体験版] の [詳細] セクション](./media/test-drive-tab-tech-config.png)

各フィールドの目的は次のとおりです。

|      フィールド                    |    説明                  |
|    ---------                  |  ---------------                |
| Type of Test Drive (体験版の種類)            | **[Microsoft Hosted (Dynamics 365 for Customer Engagement)]\(Microsoft ホステッド (Dynamics 365 for Customer Engagement)\)** を選択します。  |
| Max Concurrent Test Drives (最大同時実行体験版数)    | 特定の時点でアクティブな体験版の同時実行インスタンスの数です。 体験版がアクティブになっている間、各ユーザーは Dynamics のライセンスを消費するので、少なくともここで指定する数の Dynamics ライセンスを体験版ユーザーに使用できることを確認する必要があります。 推奨値は 3 ～ 5 です。  |
| Test Drive Duration (hours) (体験版の期間 (時間))   | ユーザーの体験版インスタンスがアクティブになっている最大時間数です。 この期間を過ぎると、インスタンスはテナントからプロビジョニング解除されます。 アプリの複雑さに応じて、推奨値は 2 ～ 24 時間です。 この時間が経過した後でユーザーが再評価したい場合、ユーザーはいつでも別の体験版を要求できます。  |
| インスタンス URL                  | 体験版が最初に移動する URL です。 通常これは、アプリとサンプル データがインストールされている Dynamics 365 インスタンスの URL です。  |
| Azure AD Tenant ID (Azure AD テナント ID)            | Dynamics 365 インスタンスの Azure テナントの GUID です。 この値を取得するには、Azure portal にログインして、**[Azure Active Directory]** > **[Select Properties]\(プロパティの選択\)** > **[Copy the Directory ID]\(ディレクトリ ID のコピー\)** に移動します。  |
| Azure AD App ID (Azure AD アプリ ID)               | Azure AD アプリケーションの GUID です  |
| Azure AD App Key (Azure AD アプリ キー)              | Azure AD アプリケーションのシークレットです (例: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`) |
| Azure AD Tenant Name (Azure AD テナント名)          | Dynamics 365 インスタンスの Azure テナントの名前です。 <テナント名>.onmicrosoft.com という形式を使用します (例: `testdrive.onmicrosoft.com`)  |
| Instance Web API URL (インスタンスの Web API URL)          | Dynamics 365 インスタンスの Web API URL です。 この値を取得するには、Microsoft Dynamics 365 インスタンスにログインし、**[設定]** > **[カスタマイズ]** > **[開発者リソース]** > **[インスタンスの Web API]** に移動して、この URL をコピーします。 値の例: `https://testdrive.crm.dynamics.com/api/data/v9.0`  |
| ロール名                     | 体験版のために作成したカスタム Dynamics 365 セキュリティ ロールの名前です (例: `testdriveuser`)。実行時にユーザーに割り当てられます。 |
|  |  |

必要な情報をすべて入力したら、**[保存]** を選択します。


## <a name="next-steps"></a>次の手順

次に、[[Storefront Details]\(ネットショップの詳細\) タブ](./cpp-storefront-details-tab.md)でマーケティングと販売の情報を提供します。


---
title: Azure AD Power BI コンテンツ パックをインストールする | Microsoft Docs
description: Azure AD Power BI コンテンツ パックをインストールする方法について説明します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: fd5604eb-1334-4bd8-bfb5-41280883e2b5
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69a69732d8cb42c248fa954ef9047e5876f40837
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437426"
---
# <a name="quickstart-install-azure-active-directory-power-bi-content-pack"></a>クイック スタート:Azure Active Directory Power BI コンテンツ パックのインストール

|  |
|--|
|現在、Azure AD Power BI コンテンツ パックでは Azure AD Graph API を使用して Azure AD テナントからデータを取得します。 そのため、コンテンツ パック内のデータと[レポート用の Microsoft Graph API](concept-reporting-api.md) を使用して取得したデータに差異が発生する可能性があります。 |
|  |

Azure Active Directory (Azure AD) 用 Power BI コンテンツ パックを使用すると、お使いの環境から得られたレポート データを視覚化することができます。 既製のコンテンツ パックをダウンロードして使用することにより、Power BI に備わっている豊富なビジュアル エクスペリエンスを使用して、ディレクトリ内のアクティビティをすべてレポートすることができます。 また、そうして得た情報は、独自のダッシュボードを作成することで、社内のだれとでも簡単に共有することができます。 

このクイック スタートでは、コンテンツ パックをインストールする方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

* Power BI アカウント  これは、ご利用の O365 または Azure AD アカウントと同じアカウントです。 
* Azure AD テナント ID。 これは、Azure portal の[プロパティ ページ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)で確認できるディレクトリの ID (**[ディレクトリ ID]**) です。
* Azure AD Premium (P1/P2) ライセンス。 Azure Active Directory エディションにアップグレードするには、「[Azure Active Directory Premium の概要](../fundamentals/active-directory-get-started-premium.md)」を参照してください。

## <a name="install-azure-ad-power-bi-content-pack"></a>Azure AD Power BI コンテンツ パックをインストールする 

1. ご利用の Power BI アカウントで [Power BI](https://app.powerbi.com/groups/me/getdata/services) にログインします。 これは、ご利用の O365 または Azure AD アカウントと同じアカウントです。

2. **[アプリ]** ページで **[Azure Active Directory Activity Logs]\(Azure Active Directory のアクティビティ ログ\)** を検索し、**[今すぐ入手する]** を選択します。 

   ![Azure Active Directory Power BI コンテンツ パック](./media/quickstart-install-power-bi-content-pack/getitnow.png) 
    
3. ポップアップ ウィンドウで該当する Azure AD テナント ID を入力し、クエリの対象となる日数に「**7**」を入力して **[次へ]** を選択します。
    
   ![Azure Active Directory Power BI コンテンツ パック](./media/quickstart-install-power-bi-content-pack/connect.png) 

4. Azure Active Directory アクティビティ ログのダッシュボードが作成されたら、それを選択します。

   ![Azure Active Directory Power BI コンテンツ パック](./media/quickstart-install-power-bi-content-pack/dashboard.png) 
    
## <a name="next-steps"></a>次の手順

* [Power BI コンテンツ パックを使用する](howto-power-bi-content-pack.md)。
* [コンテンツ パックのエラーをトラブルシューティングする](troubleshoot-content-pack.md)。

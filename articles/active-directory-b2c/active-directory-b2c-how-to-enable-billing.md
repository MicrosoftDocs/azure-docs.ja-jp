---
title: "Azure サブスクリプションを Azure AD B2C にリンクする方法 | Microsoft Docs"
description: "Azure AD B2C テナントの Azure サブスクリプションへの課金を有効にするためのステップ バイ ステップ ガイドです。"
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: joroja
translationtype: Human Translation
ms.sourcegitcommit: 43224628d5726ac2fb0e8c27c649421af7ceeee4
ms.openlocfilehash: 6cf375fbfe265a53591b9987ebd32db6df73fcb4
ms.lasthandoff: 02/21/2017


---
# <a name="linking-an-azure-subscription-to-an-azure-b2c-tenant-to-pay-for-usage-charges"></a>利用料金支払いのため Azure サブスクリプションを Azure B2C テナントにリンクする

Azure Active Directory B2C (または Azure AD B2C) の現行の利用料金は、Azure サブスクリプションに課金されます。 テナント管理者は B2C テナント自体を作成した後、Azure AD B2C テナントを Azure サブスクリプションに明示的にリンクする必要があります。  リンクするには、ターゲットの Azure サブスクリプションに Azure AD "B2C テナント" リソースを作成します。 B2C テナントの多くは、他の Azure リソース (VM、データ ストレージ、LogicApps) と共に単一の Azure サブスクリプションにリンクすることができます。


> [!IMPORTANT]
> B2C 使用量の課金と価格に関する最新情報については、「[Azure Active Directory B2C の価格](
https://azure.microsoft.com/pricing/details/active-directory-b2c/)」をご覧ください。

## <a name="step-1---create-an-azure-ad-b2c-tenant"></a>手順 1 - Azure AD B2C テナントを作成する

B2C テナントの作成を先に完了する必要があります。 ターゲットの B2C テナントを既に作成している場合は、この手順はスキップしてください。 [Azure AD B2C の開始](https://azure.microsoft.com/documentation/articles/active-directory-b2c-get-started/)

## <a name="step-2---open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>手順 2 - Azure サブスクリプションを表示する Azure AD テナントで Azure Portal を開く
portal.azure.com に移動します。 使用する Azure サブスクリプションを表示する Azure AD テナントに切り替えます。 この Azure AD テナントは、B2C テナントとは異なります。 Azure Portal で、ダッシュボードの右上にあるアカウント名をクリックして Azure AD テナントを選択します。 続行するには、Azure サブスクリプションが必要です。 [Azure サブスクリプションを取得する](https://account.windowsazure.com/signup?showCatalog=True)

![Azure AD テナントへの切り替え](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="step-3---create-a-b2c-tenant-resource-in-azure-marketplace"></a>手順 3 - Azure Marketplace で B2C テナント リソースを作成する
Marketplace アイコンをクリックして Marketplace を開くか、ダッシュボードの左上隅の緑の [+] を選択します。  Azure Active Directory B2C を検索して選択します。 [作成] を選択します。
![[Marketplace] の選択](./media/active-directory-b2c-how-to-enable-billing/marketplace.png)

![AD B2C の検索](./media/active-directory-b2c-how-to-enable-billing/searchb2c.png)

Azure AD B2C リソースの作成ダイアログでは、次のパラメーターが取り扱われます。

1. Azure AD B2C テナント – ドロップダウンから Azure AD B2C テナントを選択します。  対象となる Azure AD B2C テナントのみが表示されます。  対象となる B2C テナントが満たす条件は、次のとおりです。B2C テナントのグローバル管理者であること。B2C テナントが現在 Azure サブスクリプションに関連付けられていないこと。

2. Azure AD B2C リソース名: B2C テナントのドメイン名に一致する名前があらかじめ選択されています。

3. サブスクリプション: 自身が管理者または共同管理者であるアクティブな Azure サブスクリプション。  複数の Azure AD B2C テナントを&1; つの Azure サブスクリプションに追加できます。

4. リソース グループとリソース グループの場所: このアーティファクトでは、複数の Azure リソースを整理できます。  この選択が、B2C テナントの場所、パフォーマンス、または課金状態に影響することはありません。

5. B2C テナント課金情報と B2C テナント設定に簡単にアクセスするためにダッシュボードにピン留めします。![B2C リソースの作成](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="step-4---manage-your-b2c-tenant-resources-optional"></a>手順 4 - B2C テナント リソースを管理する (省略可能)
デプロイが完了すると、新しい "B2C テナント" リソースがターゲットのリソース グループと関連する Azure サブスクリプションに作成されます。  他の Azure リソースと共に新しいリソースの種類 "B2C テナント" が追加されていることがわかります。

![B2C リソースの作成](./media/active-directory-b2c-how-to-enable-billing/b2cresourcedashboard.png)

B2C テナント リソースをクリックして、次のことを実行できます。
- 課金情報を確認するには、サブスクリプション名をクリックします。 [課金と使用量] を確認します。
- [Azure AD B2C Settings (Azure AD B2C 設定)] をクリックして、新しいブラウザー タブを B2C テナントの [設定] ブレードに直接開きます。
- サポート リクエストを送信します
- B2C テナント リソースを他の Azure サブスクリプション、または他のリソース グループに移動します。  この選択により、どの Azure サブスクリプションが利用料金を受け取るかが変更されます。

![B2C リソース設定](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>既知の問題
- B2C テナントの削除。 B2C テナントを作成、削除し、同じドメイン名で再作成した場合は、"リンクしている" リソースも削除し、同じドメイン名で再作成してください。  この "リンクしている" リソースは、Azure Portal のサブスクリプションのテナントの [すべてのリソース] に表示されます。
- リージョンのリソースの場所に対する自己制限。  ユーザーが Azure リソースの作成に関してリージョンの制限を定めていることがまれにあります。  この制限により、Azure サブスクリプションと B2C テナント間のリンクを作成できなくなる場合があります。 このような状況に対処するには、この制限を緩和してください。

## <a name="next-steps"></a>次のステップ
B2C テナントごとにこれらの手順を完了したら、Azure Direct または Enterprise Agreement の詳細に従って Azure サブスクリプションが課金されます。
- 選択した Azure サブスクリプションの使用量と課金を確認します
- [使用状況レポート API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-usage-reporting-api) を使用して詳しい使用状況レポートを確認します



<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    


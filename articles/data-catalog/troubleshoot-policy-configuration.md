---
title: Azure Data Catalog 用の Azure Active Directory ポリシーを構成する方法
description: Azure Data Catalog ポータルにはサインインできるものの、データ ソース登録ツールにサインインしようとするとエラー メッセージが表示されることがあります。
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/06/2019
ms.openlocfilehash: 558f8845f5469bf157188e20f1ec65a07ff8355f
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59362895"
---
# <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory ポリシーの構成

Azure Data Catalog ポータルにはサインインできるものの、データ ソース登録ツールにサインインしようとするとエラー メッセージが表示され、サインインできないという状況に直面することがあります。 このようなエラーは、企業ネットワーク上にいる場合や、企業ネットワークの外部から接続している場合に発生する可能性があります。

## <a name="registration-tool"></a>登録ツール

登録ツールでは、" *フォーム認証* " を使用して、Azure Active Directory に対するユーザーのサインインを検証します。 サインインを成功させるためには、Azure Active Directory 管理者が、" *グローバル認証ポリシー*" でフォーム認証を有効にする必要があります。

グローバル認証ポリシーでは、次の画像に示すように、イントラネット接続とエクストラネット接続で個別に認証を有効にすることができます。 サインイン エラーは、接続元のネットワークで、フォーム認証が有効にされていない場合に発生する可能性があります。

 ![Azure Active Directory global authentication policy](./media/troubleshoot-policy-configuration/global-auth-policy.png)

詳細については、「 [認証ポリシーの構成](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11))」を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure データ カタログを作成する](data-catalog-get-started.md)
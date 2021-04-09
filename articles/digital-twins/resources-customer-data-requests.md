---
title: Azure Digital Twins の顧客データ要求機能
titleSuffix: Azure Digital Twins
description: この記事では、Azure Digital Twins で個人データをエクスポートおよび削除するプロセスについて説明します。
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: digital-twins
services: digital-twins
ms.openlocfilehash: 9942056e59a46c61b4e9da32aab191d4b72db968
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "92461761"
---
# <a name="azure-digital-twins-customer-data-request-features"></a>Azure Digital Twins の顧客データ要求機能

Azure Digital Twins は、セキュリティで保護されたビジネス環境のデジタル表現を作成するための開発者向けプラットフォームです。 表現は、ユーザーが選択したデータ ソースからのライブ状態データによって駆動されます。

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Azure Digital Twins の *デジタル ツイン* と呼ばれるデジタル表現は、現実環境のエンティティを表し、識別子に関連付けられています。 Microsoft は、識別子とユーザーを関連付けることができる情報を保持せず、そのようなデータにアクセスすることもできません。 

Azure Digital Twins のデジタル ツインの多くは、個人のエンティティを直接表すものではありません。代表的なオブジェクトは、オフィスの会議室や工場のフロアなどです。 しかし、ユーザーが一部のエンティティで個人を特定できるようにすることを検討し、自己判断で、デジタル ツインと個人を結び付ける独自の資産またはインベントリ追跡方法を維持する場合があります。 Azure Digital Twins はデジタル ツインに関連付けられたすべてのデータを個人データであるかのように管理し、格納します。

データ主体の要求で参照される個人データを表示、エクスポート、および削除するために、Azure Digital Twins 管理者は、ユーザーとロール向けに [**Azure portal**](https://portal.azure.com/) を、また、デジタル ツイン向けに [**Azure Digital Twins REST API**](/rest/api/azure-digitaltwins/) を使用できます。 Azure portal と REST API には、ユーザーがこのようなデータ主体の要求を処理するためのさまざまな方法が用意されています。

## <a name="identifying-customer-data"></a>顧客データの識別

Azure Digital Twins は *個人データ* を管理者とユーザーに関連付けられたデータと見なします。 

Azure Digital Twins には、環境に対するアクセス権を持つユーザーの [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) *オブジェクト ID* が格納されます。 Azure portal の Azure Digital Twins にはユーザーの電子メール アドレスが表示されますが、これらの電子メール アドレスは Azure Digital Twins 内に格納されません。 これらのオブジェクトは、Azure Active Directory オブジェクト ID を使用して Azure Active Directory で動的に検索されます。

## <a name="deleting-customer-data"></a>顧客データの削除

Azure Digital Twins 管理者は、Azure portal を使用して、ユーザーに関連するデータを削除できます。 また、Azure Digital Twins REST API を使用して、個々のデジタル ツインに対して削除操作を実行することもできます。 使用可能な API の詳細については、[Azure Digital Twins REST API のドキュメント](/rest/api/azure-digitaltwins/)を参照してください。

## <a name="exporting-customer-data"></a>顧客データのエクスポート

Azure Digital Twins は、デジタル ツインに関連するデータを格納します。 ユーザーは、REST API を使用してこのデータを取得して表示し、コピーと貼り付けを使用してこのデータをエクスポートできます。 

ユーザー ロールやロールの割り当てなどの顧客データを Azure portal で選択、コピー、貼り付けることができます。 

## <a name="links-to-additional-documentation"></a>その他のドキュメントへのリンク

Azure Digital Twins サービス API の完全な一覧については、[Azure Digital Twins REST API のドキュメント](/rest/api/azure-digitaltwins/)を参照してください。
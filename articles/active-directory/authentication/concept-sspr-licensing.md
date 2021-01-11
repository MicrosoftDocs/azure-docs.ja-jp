---
title: セルフサービス パスワード リセットのライセンス - Azure Active Directory
description: Azure Active Directory のさまざまなセルフサービス パスワード リセット ライセンス要件について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: c24698736d611604c39b6fa91aef97f73576c6c6
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716209"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Azure Active Directory のセルフサービス パスワード リセットのライセンス要件

ユーザーがデバイスまたはアプリケーションにサインインできないときにヘルプ デスクへの問い合わせと生産性の低下を減らすには、Azure Active Directory (Azure AD) のユーザーアカウントでセルフサービス パスワード リセット (SSPR) を有効にすることができます。 SSPR を構成する機能には、パスワードの変更、リセット、ロック解除、オンプレミスのディレクトリへの書き戻しが含まれます。 Basic SSPR の機能は Microsoft 365 Business Standard 以上、すべての Azure AD Premium SKU で無料でご利用いただけます。

この記事では、セルフサービス パスワード リセットのライセンスを取得して使用するさまざまな方法について詳しく説明します。 価格と課金の詳細については、[Azure AD の価格に関するページ](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。

## <a name="compare-editions-and-features"></a>エディションと機能の比較

SSPR はユーザーごとにライセンス供与されます。 その遵守状況を維持するために、組織ではユーザーに適切なライセンスを割り当てる必要があります。

次の表では、パスワードの変更、リセット、またはオンプレミスの書き戻しのさまざまな SSPR シナリオと、機能が提供される SKU の概要を示します。

| 特徴量 | Azure AD Free | Microsoft 365 Business Standard | Microsoft 365 Business Premium | Azure AD Premium P1 または P2 |
| --- |:---:|:---:|:---:|:---:|
| **クラウドのみのユーザー パスワードの変更**<br />Azure AD のユーザーが自分のパスワードを知っていて、新しいパスワードへの変更を希望する場合。 | ● | ● | ● | ● |
| **クラウドのみのユーザー パスワードのリセット**<br />Azure AD のユーザーがパスワードを忘れて、リセットする必要がある場合。 | | ● | ● | ● |
| **オンプレミスの書き戻しを含む、ハイブリッド ユーザーのパスワードの変更またはリセット**<br />Azure AD Connect を使用してオンプレミスのディレクトリから同期されている Azure AD のユーザーが、パスワードを変更またはリセットし、オンプレミスに新しいパスワードを書き戻す場合。 | | | ● | ● |

> [!WARNING]
> スタンドアロンの Microsoft 365 Basic および Standard ライセンス プランでは、オンプレミスの書き戻しのある SSPR はサポートされていません。 オンプレミスの書き戻し機能には Azure AD Premium P1、Premium P2、または Microsoft 365 Business Premium が必要です。

追加のライセンス情報 (コストを含む) については、以下のページをご覧ください。

* [Azure Active Directory の料金](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory の機能と働き](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>グループまたはユーザー ベースのライセンスの有効化

Azure AD では、グループ ベースのライセンスがサポートされています。 管理者はライセンスを一度に 1 つずつユーザーに割り当てるのではなく、ユーザーのグループに一括して割り当てることができます。 詳細については、[ライセンスの割り当て、検証、および問題の解決](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)に関するページを参照してください。

Microsoft サービスの中には、すべての場所では利用できないものもあります。 ライセンスをユーザーに割り当てる前に、管理者はユーザーの **[利用場所]** プロパティを指定しておく必要があります。 ライセンスの割り当ては、Azure Portal の **[ユーザー]**  >  **[プロファイル]**  >  **[設定]** セクションで行います。 *グループでのライセンス割り当てを使用する場合、利用場所が指定されていないユーザーは、ディレクトリの場所を継承します。*

## <a name="next-steps"></a>次のステップ

SSPR の使用を開始するには、次のチュートリアルをご覧ください。

> [!div class="nextstepaction"]
> [チュートリアル:セルフサービス パスワード リセット (SSPR) を有効にする](tutorial-enable-sspr.md)
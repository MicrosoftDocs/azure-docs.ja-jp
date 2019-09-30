---
title: Azure Active Directory Domain Services の無効化 | Microsoft Docs
description: Azure Portal を使用して Azure Active Directory Domain Services を無効にする方法について説明します
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: iainfou
ms.openlocfilehash: 05c4e73c56b79c6e313ea15124bd0f3d17b2fc70
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842616"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure Portal を使用して Azure Active Directory Domain Services を無効にする

マネージド ドメインが不要になった場合、Azure Active Directory Domain Services (Azure AD DS) インスタンスを削除できます。 Azure AD DS マネージド ドメインをオフにしたり、一時的に無効にしたりすることはできません。 Azure AD DS マネージド ドメインを削除しても、Azure AD テナントが削除されたり、削除以外の悪影響が出たりすることはありません。 この記事では、Azure portal を使用して Azure AD DS マネージド ドメインを削除する方法について説明します。

> [!WARNING]
> **削除は永続的であり、元に戻すことはできません。**
> Azure AD DS マネージド ドメインを削除すると、次の処理が行われます。
>   * マネージド ドメインのドメイン コント ローラーはプロビジョニング解除され、仮想ネットワークから削除されます。
>   * マネージド ドメイン上のデータは完全に削除されます。 このデータには、作成したカスタム OU、GPO、カスタムの DNS レコード、サービス プリンシパル、GMSA などが含まれます。
>   * マネージド ドメインに参加しているコンピューターでは、ドメインとの信頼関係が失われ、ドメインへの参加を解消する必要があります。
>       * 企業の AD 資格情報を使用してこれらのコンピューターにサインインすることはできません。 代わりに、コンピューターのローカル管理者の資格情報を使用する必要があります。

## <a name="delete-the-managed-domain"></a>マネージド ドメインを削除する

Azure AD DS マネージド ドメインを削除するには、次の手順を実行します。

1. Azure portal で、**Azure AD Domain Services** を検索して選択します。
1. Azure AD DS マネージド ドメインの名前 (*contoso.com* など) を選択します。
1. **[概要]** ページで **[削除]** を選択します。 削除を確定するには、マネージド ドメインのドメイン名を再度入力し、 **[削除]** を選択します。

Azure AD DS マネージド ドメインを削除するには、15 から 20 分以上かかることがあります。

## <a name="next-steps"></a>次の手順

Azure AD DS に希望する機能があれば、[フィードバックの投稿][feedback]をご検討ください。

Azure AD DS を再開する場合、「[チュートリアル: Azure Active Directory Domain Services インスタンスを作成して構成する][create-instance]」を参照してください。

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md

---
title: Azure Germany のセキュリティ サービスと ID サービス | Microsoft Docs
description: Azure Germany のセキュリティ サービスと ID サービスを比較します
services: germany
cloud: na
documentationcenter: na
author: gitralf
manager: rainerst
ms.assetid: na
ms.service: germany
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2017
ms.author: ralfwi
ms.openlocfilehash: ee98e074b8bf21abe198506899b99be4e2908f14
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798727"
---
# <a name="azure-germany-security-and-identity-services"></a>Azure Germany のセキュリティ サービスと ID サービス
## <a name="key-vault"></a>Key Vault
Azure Key Vault サービスとその使用方法について詳しくは、[Key Vault のグローバル ドキュメント](../key-vault/index.yml)をご覧ください。

Key Vault は、Azure Germany で一般提供されています。 グローバル Azure と同様に、拡張機能は存在しません。Key Vault は、PowerShell と CLI を介してのみ利用できます。

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory は、Microsoft Azure で実行されている情報システムに ID およびアクセス機能を提供します。 ディレクトリ サービス、セキュリティ グループ、グループ ポリシーを使用して、Azure Active Directory を使用するマシンのアクセス ポリシーとセキュリティ ポリシーを制御できます。 アカウントとセキュリティ グループをロールベースのアクセス制御 (RBAC) と共に使用することで、情報システムへのアクセスを管理できます。 

Azure Active Directory は、Azure Germany で一般提供されています。

### <a name="variations"></a>バリエーション

* Azure Germany の Azure Active Directory は、グローバル Azure の Azure Active Directory から完全に分離されています。 
* Microsoft アカウントを使用して Azure Germany にサインインすることはできません。
* Azure Germany のログイン サフィックスは *onmicrosoft.de* です (グローバル Azure などで使用されている *onmicrosoft.com* ではありません)。
* Azure Germany を使用するには別個のサブスクリプションが必要になります。
* Azure Germany のお客様は、グローバル Azure のサブスクリプションや ID を必要とするリソースにアクセスできません。
* グローバル Azure のお客様は、Azure Germany のサブスクリプションや ID を必要とするリソースにアクセスできません。
* 追加ドメインは、いずれかのクラウド環境でのみ、追加したり、検証したりすることができます。
 
> [!NOTE]
> *Azure Germany 内に両方のテナント*を持つ他のテナントからのユーザーに権限を割り当てることはまだできません。


## <a name="next-steps"></a>次の手順
補足情報と更新情報については、[Azure Germany のブログ](https://blogs.msdn.microsoft.com/azuregermany/)を参照してください。





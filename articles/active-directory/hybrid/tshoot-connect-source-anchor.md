---
title: Azure AD Connect:インストール時のソース アンカーに関する問題のトラブルシューティング | Microsoft Docs
description: このトピックでは、インストール時のソース アンカーに関する問題をトラブルシューティングする方法の手順について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 4e6460015430850c11fbf70a005d7440ce1b92fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91306006"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>インストール時のソース アンカーに関する問題のトラブルシューティング
この記事では、インストール時に発生する可能性のあるソース アンカーに関するさまざまな問題について説明し、これらの問題を解決する方法を示します。

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Azure Active Directory の無効なソース アンカー

### <a name="custom-installation"></a>カスタム インストール

カスタム インストール時に、Azure AD Connect は Azure Active Directory からソース アンカーを読み取ります。 Azure Active Directory にポリシーが存在する場合、Azure AD Connect は、顧客によってオーバーライドされていない限り、同じポリシーを適用します。 読み取られた属性がウィザードに表示されます。 また、ユーザーがソース アンカー ポリシーをオーバーライドしようとするとウィザードに警告が表示されます。

この読み取り操作時に、Azure Active Directory のソース アンカー ポリシーが予期されない場合があります。 この場合、Azure AD Connect では、使用すべきソース アンカーを識別できないため、手動でオーバーライドする必要があります。</br>
![ソース アンカーを手動でオーバーライドする場所を示すスクリーンショット。](media/tshoot-connect-source-anchor/source1.png)

この問題を解決するために、特定の属性を選択することで、ソース アンカーを手動でオーバーライドできます。 この操作は、選択する属性を把握している場合にのみ続行してください。 不明な場合には、[Microsoft サポート](https://support.microsoft.com/contactus/)にお問い合わせください。 ソース アンカー ポリシーを変更すると、オンプレミス ユーザーとそのユーザーに関連付けられている Azure リソースの間の関連付けが解除される場合があります。</br>
![ソース アンカーをオーバーライドする属性を指定している画面のスクリーンショット。](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>高速インストール
高速インストール時に、Azure AD Connect は Azure Active Directory からソース アンカー ポリシーを読み取ります。 Azure Active Directory にポリシーが存在する場合、Azure AD Connect は同じポリシーを適用します。 手動でオーバーライドする方法はありません。

この読み取り操作時に、Azure Active Directory のソース アンカー ポリシーが予期されない場合があります。 この場合、Azure AD Connect では、使用すべきソース アンカーを識別できません。</br>
![Azure Active Directory のソース アンカーが予期されていないときの動作を示すスクリーンショット。](media/tshoot-connect-source-anchor/source3.png)

この問題を解決するには、カスタム モードを使用して再インストールし、特定の属性を選択することでソース アンカーを手動でオーバーライドします。 この操作は、選択する属性を把握している場合にのみ続行してください。 不明な場合には、[Microsoft サポート](https://support.microsoft.com/contactus/)にお問い合わせください。 ソース アンカー ポリシーを変更すると、オンプレミス ユーザーとそのユーザーに関連付けられている Azure リソースの間の関連付けが解除される場合があります。

### <a name="invalid-source-anchor-in-sync-engine"></a>同期エンジン内の無効なソース アンカー
インストール時に、Azure AD Connect が無効なソース アンカーを使用して同期エンジンの構成を試行する場合があります。 この操作は、製品の問題であると考えられ、Azure AD Connect のインストールは失敗します。 この問題が発生した場合は、[Microsoft サポート](https://support.microsoft.com/contactus/)にお問い合わせください。</br>
![予期しない](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。

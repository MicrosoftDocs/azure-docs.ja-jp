---
title: Azure AD Connect のインストールに関する問題のトラブルシューティング | Microsoft Docs
description: このトピックでは、Azure AD Connect のインストールに関する問題のトラブルシューティングの手順を示します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e077127681f8bd7b650ab22f2d036efd7f9733ee
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58876924"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>トラブルシューティング:Azure AD Connect のインストールの問題

## <a name="recommended-steps"></a>**推奨される手順**
適切な [Azure AD Connect のインストールの種類](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)を確認してください。 高速インストールの条件を満たしている場合は、高速インストールを使用することを強くお勧めします。 高速インストールでは、インストールを完了するのに必要な最小限のオプションが提供されます。したがって、問題が発生する可能性が低くなります。 

しかし、高速インストールの条件を満たしておらず、カスタム インストールを行う必要がある場合は、一般的な問題を回避するために従うことができるベスト プラクティスがいくつかあります。 わかりやすくするため、ここでは選択的オプションのみを示します。

* AAD Connect のインストール先となるコンピューターの管理者であることを確認します。 同じ管理者の資格情報を使用して、コンピューターにログインします。

* 以下のページのすべてのオプションが既定値になるようにします。既存の SQL Server を使用する場合は、"既存の SQL Server を使用する" を除きます。 カスタム インストール オプションの使用方法の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)を参照してください。 

    ![既存の SQL Server を使用する](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* 以下のページでは、既存のアカウントのアクセス許可に関する問題を回避するために、[新しい AD アカウントを作成] というオプションを選びます。

    ![AD フォレスト アカウント](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**一般的な問題**

* [オンプレミス Active Directory の接続に関する問題](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools)。

* [オンライン Azure Active Directory の接続に関する問題](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity)。

* [オンプレミス Active Directory のアクセス許可に関する問題](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account)。

## <a name="recommended-documents"></a>**推奨されるドキュメント**
* [Azure AD Connect の前提条件](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Azure AD Connect で使用するインストールの種類の選択](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [簡単設定を使用した Azure AD Connect の開始](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Azure AD Connect のカスタム インストール](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect:旧バージョンから最新バージョンにアップグレードする](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)」の記事を参照してください。
* [Azure AD Connect:ステージング サーバーとは](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [ADConnectivityTool PowerShell モジュールとは](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>次の手順
- [Azure AD Connect 同期](how-to-connect-sync-whatis.md)。
- [ハイブリッド ID とは](whatis-hybrid-identity.md)。






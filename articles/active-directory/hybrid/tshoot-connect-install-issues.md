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
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25314b4a306678dc877a95194907b3d73979e4f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89275863"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>トラブルシューティング: Azure AD Connect のインストールに関する問題

## <a name="recommended-steps"></a>**推奨される手順**
適切な [Azure AD Connect のインストールの種類](./how-to-connect-install-select-installation.md)を確認してください。 高速インストールの条件を満たしている場合は、高速インストールを使用することを強くお勧めします。 高速インストールでは、インストールを完了するのに必要な最小限のオプションが提供されます。したがって、問題が発生する可能性が低くなります。 

しかし、高速インストールの条件を満たしておらず、カスタム インストールを行う必要がある場合は、一般的な問題を回避するために従うことができるベスト プラクティスがいくつかあります。 わかりやすくするため、ここでは選択的オプションのみを示します。

* AAD Connect のインストール先となるコンピューターの管理者であることを確認します。 同じ管理者の資格情報を使用して、コンピューターにログインします。

* 以下のページのすべてのオプションが既定値になるようにします。既存の SQL Server を使用する場合は、"既存の SQL Server を使用する" を除きます。 カスタム インストール オプションの使用方法の詳細については、[こちら](./how-to-connect-install-custom.md)を参照してください。 

    ![既存の SQL Server を使用する](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* 以下のページでは、既存のアカウントのアクセス許可に関する問題を回避するために、[新しい AD アカウントを作成] というオプションを選びます。

    ![AD フォレスト アカウント](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**一般的な問題**

* [オンプレミス Active Directory の接続に関する問題](./reference-connect-adconnectivitytools.md)。

* [オンライン Azure Active Directory の接続に関する問題](./tshoot-connect-connectivity.md)。

* [オンプレミス Active Directory のアクセス許可に関する問題](./how-to-connect-configure-ad-ds-connector-account.md)。

## <a name="recommended-documents"></a>**推奨されるドキュメント**
* [Azure AD Connect の前提条件](./how-to-connect-install-prerequisites.md)
* [Azure AD Connect で使用するインストールの種類の選択](./how-to-connect-install-select-installation.md)
* [簡単設定を使用した Azure AD Connect の開始](./how-to-connect-install-express.md)
* [Azure AD Connect のカスタム インストール](./how-to-connect-install-custom.md)
* [Azure AD Connect:旧バージョンから最新バージョンにアップグレードする](./how-to-upgrade-previous-version.md)
* [Azure AD Connect: ステージング サーバーとは](./plan-connect-topologies.md#staging-server)
* [ADConnectivityTool PowerShell モジュールとは](./how-to-connect-adconnectivitytools.md)

## <a name="next-steps"></a>次のステップ
- [Azure AD Connect 同期](how-to-connect-sync-whatis.md)。
- [ハイブリッド ID とは](whatis-hybrid-identity.md)
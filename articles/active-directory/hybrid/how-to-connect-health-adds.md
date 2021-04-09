---
title: AD DS での Azure AD Connect Health の使用 | Microsoft Docs
description: Azure AD を監視する方法を説明する Azure AD Connect Health のページです。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 19e3cf15-f150-46a3-a10c-2990702cd700
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0686a4dafae17bafc65a03c985a65aecba70e60c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98017019"
---
# <a name="using-azure-ad-connect-health-with-ad-ds"></a>AD DS での Azure AD Connect Health の使用
次のドキュメントは、Azure AD Connect Health を使用した Active Directory Domain Services の監視について記述しています。 AD DS のサポートされているバージョンは、Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、および Windows Server 2016 です。

Azure AD Connect Health を使用して AD FS を監視する方法の詳細については、「[AD FS での Azure AD Connect Health の使用](how-to-connect-health-adfs.md)」を参照してください。 また、Azure AD Connect Health での Azure AD Connect (同期) の監視については、「 [Azure AD Connect Health for Sync の使用](how-to-connect-health-sync.md)」を参照してください。

![Azure AD Connect Health for AD DS](./media/how-to-connect-health-adds/domainservicesnapshot.PNG)

## <a name="alerts-for-azure-ad-connect-health-for-ad-ds"></a>Azure AD Connect Health for AD DS のアラート
Azure AD Connect Health for AD DS 内の [アラート] セクションには、ドメイン コントローラーに関してアクティブなアラートと解決済みのアラートが一覧表示されます。 アクティブなアラートまたは解決済みのアラートを選択すると、新しいブレードが開き、解決手順や関連文書へのリンクと共に詳細な情報が表示されます。 それぞれのアラート タイプには少なくとも 1 つのインスタンスが存在し、そのアラートに該当する各ドメイン コントローラーに対応しています。 アラート ブレードの下の方で、該当するドメイン コントローラーをダブルクリックすると、追加のブレードが開かれ、そのアラート インスタンスについての詳細が表示されます。

このブレードでは、アラートの電子メール通知を有効にしたり、表示する時間範囲を変更したりすることができます。 時間範囲を拡大することによって、過去に解決したアラートを確認することができます。

![Azure AD Connect sync error](./media/how-to-connect-health-adds/aadconnect-health-adds-alerts.png)

## <a name="domain-controllers-dashboard"></a>[ドメイン コントローラー] ダッシュボード
このダッシュボードには、ご使用の環境のトポロジー ビューが、監視対象となっている各ドメイン コントローラーの正常性状態や運用上の主要なメトリックと共に表示されます。 より詳しい調査を必要とするドメイン コントローラーがあれば、ここに表示されるメトリックによって迅速に特定できます。 既定では、列のサブセットのみが表示されます。 ただし、列コマンドをダブルクリックすると、使用可能な列のセット全体を検表示できます。 最も関心のある列のみを選択することで、AD DS 環境の正常性をこのダッシュボードでまとめて簡単に確認することができます。

![ドメイン コントローラー](./media/how-to-connect-health-adds/aadconnect-health-adds-domainsandsites-dashboard.png)

ドメイン コントローラーはドメインまたはサイトごとにグループ化すると、環境のトポロジが把握しやすくなります。 また、ブレードのヘッダーをダブルクリックすると、ダッシュボードが最大化され、利用可能な画面領域を最大限に活用することができます。 このより大きなビューは、複数の列を表示するときに便利です。

## <a name="replication-status-dashboard"></a>[レプリケーションの状態] ダッシュボード
このダッシュボードには、監視対象となるドメイン コントローラーのレプリケーション状態とレプリケーション トポロジが表示されます。 直近に試行されたレプリケーションの状態が一覧表示されます。エラーが検出された場合は、それに関連したドキュメントも表示されます。 エラーが発生したドメイン コント ローラーをダブルクリックすると、新しいブレードが開かれ、エラーの詳細、推奨される解決手順、トラブルシューティング ドキュメントへのリンクなどの情報が表示されます。

![レプリケーションの状態](./media/how-to-connect-health-adds/aadconnect-health-adds-replication.png)

## <a name="monitoring"></a>監視
監視対象となっている各ドメイン コントローラーから常時収集される各種パフォーマンス カウンターの傾向は、この機能によってグラフィカルに把握できます。 ドメイン コントローラーのパフォーマンスを、フォレスト内で監視対象となっている他のすべてのドメイン コントローラーと簡単に比較することができます。 また各種パフォーマンス カウンターを並べて表示することもでき、環境内の問題をトラブルシューティングするときに便利です。

![監視](./media/how-to-connect-health-adds/aadconnect-health-adds-monitoring.png)

あらかじめ既定で選択されているパフォーマンス カウンターは 4 つですが、[フィルター] コマンドをクリックし、必要なパフォーマンス カウンターを選択 (または選択解除) することで、表示対象のパフォーマンス カウンターを変更することができます。 また、パフォーマンス カウンター グラフをダブルクリックすると、新しいブレードが開かれ、監視対象となっている各ドメイン コントローラーのデータ ポイントが表示されます。

## <a name="related-links"></a>関連リンク
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health エージェントのインストール](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health の操作](how-to-connect-health-operations.md)
* [AD FS での Azure AD Connect Health の使用](how-to-connect-health-adfs.md)
* [Azure AD Connect Health for Sync の使用](how-to-connect-health-sync.md)
* [Azure AD Connect Health の FAQ](reference-connect-health-faq.md)
* [Azure AD Connect Health のバージョンの履歴](reference-connect-health-version-history.md)
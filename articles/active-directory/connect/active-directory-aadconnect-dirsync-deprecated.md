---
title: "DirSync と Azure AD Sync からのアップグレード |Microsoft Docs"
description: "DirSync と Azure AD Sync から Azure AD Connect にアップグレードする方法について説明します。"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: b9a3b64d9de48f17a295ca7a9ea58cf26e8f83ed
ms.openlocfilehash: f42dfce5409c1751c84ffb658a3c5464780a5a3d
ms.lasthandoff: 02/28/2017

---

# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Windows Azure Active Directory Sync と Azure Active Directory Sync のアップグレード
Azure AD Connect は、オンプレミス ディレクトリと Azure AD および Office 365 を接続するための最適な方法です。 Windows Azure Active Directory Sync (DirSync) および Azure AD Sync は廃止となり、2017 年 4 月 13 日でサポートが終了するため、Azure AD Connect へアップグレードする絶好の機会です。

廃止されるこの&2; つの ID 同期ツールは、単一フォレスト顧客用 (DirSync) および複数フォレストとその他の高度なユーザー用 (Azure AD Sync) に提供されていました。 これらの古いツールは、すべてのシナリオで使用できる Azure AD Connect という単一のソリューションに代わりました。 Azure AD Connect は、新機能、機能強化、および新しいシナリオのサポートを提供します。 Azure AD と Office 365 に対してオンプレミスの ID データの同期を継続するには、Azure AD Connect にアップグレードすることを強くお勧めします。

DirSync の最終リリースは 2014 年 7 月、Azure AD Sync の最新リリースは 2015 年 5 月にリリースされています。

## <a name="what-is-azure-ad-connect"></a>Azure AD Connect とは
Azure AD Connect は DirSync と Azure AD Sync の後継となります。 Azure AD Connect は、この&2; つがサポートされるすべてのシナリオを結合します。 詳細については、「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

## <a name="deprecation-schedule"></a>廃止のスケジュール
| Date | コメント |
| --- | --- |
| 2016 年 4 月13 日 |Windows Azure Active Directory Sync (“DirSync”) と Azure Active Directory Sync (“Azure AD Sync”) の廃止が発表されます。 |
| 2017 年 4 月 13 日 |サポートが終了します。 Azure AD Connect にアップグレードしていないと、サポート ケースを開くことができなくなります。 |

## <a name="how-to-transition-to-azure-ad-connect"></a>Azure AD Connect へ移行する方法
DirSync を実行している場合、2 つのアップグレード方法 (インプレース アップグレードと並列デプロイ) があります。 通常、最新のオペレーティング システムを使用しており、オブジェクトの数が 50,000 未満の場合は、インプレース アップグレードをお勧めします。 それ以外の場合は、並列デプロイを実行することをお勧めします。この方法では、Azure AD Connect を実行している新しいサーバーに DirSync の構成が移行されます。

Azure AD Sync を使用している場合、インプレース アップグレードをお勧めします。 目的に応じて、並列で新しい Azure AD Connect サーバーをインストールし、Azure AD Sync サーバーから Azure AD Connect へのスウィング移行を実行することもできます。

| 解決策 | シナリオ |
| --- | --- |
| [DirSync からのアップグレード](active-directory-aadconnect-dirsync-upgrade-get-started.md) |<li>既存の DirSync サーバーが既に実行されている場合に使用します。</li> |
| [Azure AD Sync からのアップグレード](active-directory-aadconnect-upgrade-previous-version.md) |<li>Azure AD Sync から移行する場合に使用します。</li> |

DirSync から Azure AD Connect へのインプレース アップグレードを実行する方法を確認するには、こちらの Channel 9 のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>FAQ
**Q: Azure チームからの電子メール通知や Office 365 メッセージ センターからのメッセージが届きましたが、現在は Connect を使用しています。**  
ビルド番号が 1.0.\*.0 (1.1 リリースより前) の Azure AD Connect を使用しているお客様にも通知が送信されています。 Microsoft ではお客様に、常に最新の Azure AD Connect リリースを使用することをお勧めしています。 1.1 には[自動アップグレード](active-directory-aadconnect-feature-automatic-upgrade.md)機能が搭載されているため、常に最新バージョンの Azure AD Connect がインストールされている状態を簡単に維持できます。

**Q: DirSync と Azure AD Sync は、2017 年 4 月 13 日に動作しなくなりますか。**  
いいえ。 これらの製品が Azure AD と通信できなくなる日付については、後日発表されます。 この情報が利用できるようになったら、このトピックで確認できます。

**Q: DirSync のどのバージョンからアップグレードできますか。**  
現在使用されているどの DirSync リリースからでもアップグレードできます。

**Q: FIM または MIM の Azure AD コネクタはどうなりますか。**  
FIM または MIM の Azure AD コネクタは、**廃止とは発表されていません**。 このコネクタは **機能凍結**状態で、新しい機能が追加されず、バグの修正プログラムも受信しません。 Microsoft は、このコネクタから Azure AD Connect への移行を計画することをお勧めします。 このコネクタを使用して新しいデプロイメントを開始しないことを強くお勧めします。 このコネクタは、将来廃止が発表される予定です。

## <a name="additional-resources"></a>その他のリソース
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)


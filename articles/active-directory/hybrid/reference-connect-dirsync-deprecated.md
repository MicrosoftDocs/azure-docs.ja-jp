---
title: DirSync と Azure AD Sync からのアップグレード |Microsoft Docs
description: DirSync と Azure AD Sync から Azure AD Connect にアップグレードする方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53d9e2ea10aefc96c2d4d72ad0020704ba48709b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539418"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Windows Azure Active Directory Sync と Azure Active Directory Sync のアップグレード
Azure AD Connect は、オンプレミス ディレクトリと Azure AD および Microsoft 365 を接続するための最適な方法です。 Windows Azure Active Directory Sync (DirSync) および Azure AD Sync (AADSync) は非推奨となり、2017 年 4 月 13 日以降はサポートされなくなったので、Azure AD Connect へアップグレードする絶好の機会です。

非推奨となるこの 2 つの ID 同期ツールは、単一フォレスト顧客用 (DirSync) および複数フォレストとその他の高度なユーザー用 (Azure AD Sync) に提供されていました。 これらの古いツールは、すべてのシナリオで使用できるAzure AD Connect という単一のソリューションに代わりました。 Azure AD Connect は、新機能、機能強化、および新しいシナリオのサポートを提供します。 Azure AD と Microsoft 365 に対してオンプレミスの ID データの同期を継続するには、Azure AD Connect にアップグレードすることを強くお勧めします。 2017 年 12 月 31 日を過ぎると、Microsoft はこれらの古いバージョンの動作を保証しません。

DirSync の最終リリースは 2014 年 7 月、Azure AD Sync の最新リリースは 2015 年 5 月にリリースされています。

## <a name="what-is-azure-ad-connect"></a>What is Azure AD Connect
Azure AD Connect は DirSync と Azure AD Sync の後継となります。Azure AD Connect は、この 2 つがサポートされるすべてのシナリオを結合します。 詳細については、「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。

## <a name="deprecation-schedule"></a>廃止のスケジュール
| Date | 解説 |
| --- | --- |
| 2016 年 4 月13 日 |Windows Azure Active Directory Sync (“DirSync”) と Azure Active Directory Sync (“Azure AD Sync”) の非推奨が発表されます。 |
| 2017 年 4 月 13 日 |サポートが終了します。 Azure AD Connect にアップグレードしていないと、サポート ケースを開くことができなくなります。 |
|2017 年 12 月 31 日|Azure AD は、Windows Azure Active Directory Sync (“DirSync”) と Microsoft Azure Active Directory Sync (“Azure AD Sync”) からの通信を受け入れなくなる予定です。
|2021 年 4 月 1 日| Windows Azure Active Directory Sync ("DirSync") と Microsoft Azure Active Directory Sync ("Azure AD Sync") は動作しなくなります。 |

## <a name="how-to-transition-to-azure-ad-connect"></a>Azure AD Connect へ移行する方法
DirSync を実行している場合は、インプレース アップグレードと並列デプロイの 2 つの方法でアップグレードできます。 通常、最新のオペレーティング システムを使用しており、オブジェクトの数が 50,000 未満の場合は、インプレース アップグレードをお勧めします。 それ以外の場合は、並列デプロイを実行することをお勧めします。この方法では、Azure AD Connect を実行している新しいサーバーに DirSync の構成が移行されます。

| 解決策 | シナリオ |
| --- | --- |
| [DirSync からのアップグレード](how-to-dirsync-upgrade-get-started.md) |<li>既存の DirSync サーバーが既に実行されている場合に使用します。</li> |
| [Azure AD Sync からのアップグレード](how-to-upgrade-previous-version.md) |<li>Azure AD Sync から移行する場合に使用します。</li> |

DirSync から Azure AD Connect へのインプレース アップグレードを実行する方法を確認するには、こちらの Channel 9 のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>よく寄せられる質問
**Q:Azure チームからの電子メール通知や Microsoft 365 メッセージ センターからのメッセージが届きましたが、現在は Connect を使用しています。**  
ビルド番号が 1.0.\*.0 (1.1 リリースより前) の Azure AD Connect を使用しているお客様にも通知が送信されています。 Microsoft ではお客様に、常に最新の Azure AD Connect リリースを使用することをお勧めしています。 1\.1 には[自動アップグレード](how-to-connect-install-automatic-upgrade.md)機能が搭載されているため、常に最新バージョンの Azure AD Connect がインストールされている状態を簡単に維持できます。

**Q:DirSync と Azure AD Sync は、2017 年 4 月 13 日に動作しなくなりますか。**  
DirSync/Azure AD Sync は、2017 年 4 月 13 日の時点では引き続き機能します。  ただし、2017 年 12 月 31 日を過ぎると、Azure AD は DirSync/Azure AD Sync からの通信を受け入れなくなる予定です。 2021 年 4 月 1 日以降、Dirsync と Azure AD Sync は動作しなくなります。

**Q:DirSync のどのバージョンからアップグレードできますか。**  
現在使用されているどの DirSync リリースからでもアップグレードできます。 

**Q:FIM または MIM の Azure AD コネクタはどうなりますか。**  
FIM または MIM の Azure AD コネクタは、**非推奨とは発表されていません**。 このコネクタは **機能凍結** 状態で、新しい機能が追加されず、バグの修正プログラムも受信しません。 Microsoft は、このコネクタから Azure AD Connect への移行を計画することをお勧めします。 このコネクタを使用して新しいデプロイメントを開始しないことを強くお勧めします。 このコネクタは、将来非推奨になると発表される予定です。

## <a name="additional-resources"></a>その他のリソース
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)

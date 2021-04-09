---
title: 'Azure AD Connect: インストールの種類の選択 | Microsoft Docs'
description: このトピックでは、Azure AD Connect で使用するインストールの種類を選択する方法について説明します
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7275d115210100bdd4a3a2eb683c867a6a4a4f4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996612"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Azure AD Connect で使用するインストールの種類の選択
Azure AD Connect には、簡易とカスタマイズの 2 種類のインストールが用意されています。 このトピックは、どちらのオプションでインストールするかを決める際に役立ちます。

## <a name="express"></a>Express
簡易は一般的なオプションで、新規インストールの約 90% で使用されます。 これは、最も一般的な顧客シナリオで使用できる構成を提供できるように設計されました。

次のことを前提としています。

- オンプレミスの単一 Active Directory フォレストがある。
- インストールに使用できるエンタープライズ管理者アカウントがある。
- オンプレミスの Active Directory のオブジェクト数が 100,000 未満である。

次を実現できます。

- シングル サインオンのためのオンプレミスから Azure AD への[パスワード ハッシュの同期](how-to-connect-password-hash-synchronization.md)。
- [ユーザー、グループ、連絡先、および Windows 10 コンピューター](concept-azure-ad-connect-sync-default-configuration.md)を同期する構成。
- すべてのドメインとすべての OU における対象オブジェクトすべての同期。
- [自動アップグレード](how-to-connect-install-automatic-upgrade.md)の有効化。これにより常に最新バージョンを使用できます。

簡易を引き続き使用できるオプション:

- 同期する必要があるのが一部の OU の場合は、最後のページで引き続き簡易を使用して、**[... 同期処理を開始してください]*** の選択を解除します。 次に、インストール ウィザードを実行し、[構成オプション](how-to-connect-installation-wizard.md#customize-synchronization-options)の OU を変更して、スケジュールされた同期を有効にします。
- パスワード ライトバックなど、Azure AD Premium のいずれかの機能を有効にする必要がある場合は、 最初に、簡易で初期インストールを完了します。 次に、インストール ウィザードを再度実行し、[構成オプション](how-to-connect-installation-wizard.md#customize-synchronization-options)を変更します。

## <a name="custom"></a>Custom
カスタマイズされたインストールを使用すると、簡易より多くのオプションを使用できます。 このインストールは、組織で主に使用されている構成が、前の簡易のセクションで説明した構成と異なるすべてのケースで使用します。

次の場合に使用します。

- Active Directory のエンタープライズ管理者アカウントへのアクセス権がない。
- 複数のフォレストが存在する。または今後複数のフォレストと同期する予定がある。
- Connect サーバーからアクセスできないドメインがフォレストにある。
- ユーザーのサインインにフェデレーションまたはパススルー認証を使用する予定がある。
- オブジェクト数が 100,000 を超え、完全な SQL Server を使用する必要がある。
- ドメインまたは OU ベースのフィルター処理だけでなく、グループ ベースでフィルター処理する予定がある。

## <a name="upgrade-from-dirsync"></a>DirSync からのアップグレード
DirSync を使用している場合は、[DirSync からのアップグレード](how-to-dirsync-upgrade-get-started.md)に関するページの手順に従って、既存の構成をアップグレードします。 次の異なる 2 つのアップグレード オプションがあります。

- インプレース アップグレード。Connect を同じサーバーにインストールします。
- 並列デプロイ。既存の DirSync サーバーを稼働させながら、Connect を新しいサーバーにインストールします。

## <a name="upgrade-from-azure-ad-sync"></a>Azure AD Sync からのアップグレード
Azure AD Sync を使用している場合は、Connect を新しいバージョンにアップグレードする場合と[同じ手順](how-to-upgrade-previous-version.md)でアップグレードできます。 次の異なる 2 つのアップグレード オプションがあります。

- インプレース アップグレード。Connect を同じサーバーにインストールします。
- スウィング移行。既存の Azure AD Sync サーバーを稼働させながら、Connect を新しいサーバーにインストールします。

## <a name="migrate-from-fim2010-or-mim2016"></a>FIM2010 または MIM2016 からの移行
Azure AD コネクタで Forefront Identity Manager 2010 または Microsoft Identity Manager 2016 を使用している場合、唯一のオプションは移行です。 [スウィング移行](how-to-upgrade-previous-version.md#swing-migration)に関するトピックで説明されている手順に従ってください。 この手順の Azure AD Sync の部分すべてを、FIM2010/MIM2016 に置き換えます。

## <a name="next-steps"></a>次のステップ
使用するオプションに応じて、左側の目次を使用して、詳細な手順が示されている記事を見つけてください。

---
title: デバイス ID とデスクトップ仮想化 - Azure Active Directory
description: VDI と Azure AD デバイス ID を一緒に使用する方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b431cee3b8e5fc168dec2766442d6f6b9869d1e
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900372"
---
# <a name="device-identity-and-desktop-virtualization"></a>デバイス ID とデスクトップ仮想化

管理者は、通常、Windows オペレーティング システムをホストする仮想デスクトップ インフラストラクチャ (VDI) プラットフォームを組織に展開します。 管理者が VDI を展開する目的:

- 管理の合理化。
- リソースの統合と一元化によるコストの削減。
- エンドユーザーのモビリティを確保し、任意のデバイスでいつでもどこからでも仮想デスクトップへの自由なアクセスを提供する。

仮想デスクトップには次の 2 つの主な種類があります。

- 永続的
- 非永続的

永続的バージョンは、ユーザーまたはユーザーのプールごとに一意のデスクトップ イメージを使用します。 これらの一意のデスクトップは、将来使用するためにカスタマイズして保存できます。 

非永続的バージョンは、ユーザーが必要に応じてアクセスできる一連のデスクトップを使用します。 これらの非永続的なデスクトップは、ユーザーがサインアウトした後、元の状態に戻ります。

この記事では、デバイス ID と VDI のサポートに関する、管理者向けの Microsoft のガイダンスについて説明します。 デバイスID の詳細については、「[デバイス ID とは](overview.md)」の記事を参照してください。

## <a name="supported-scenarios"></a>サポートされるシナリオ

VDI 環境の Azure AD でデバイス ID を構成する前に、サポートされているシナリオについて理解を深めてください。 次の表に、サポートされているプロビジョニング シナリオを示します。 このコンテキストでのプロビジョニングとは、管理者がエンドユーザーの操作を必要とせずに、デバイス ID を大規模に構成できることを意味します。

| デバイス ID の種類 | ID インフラストラクチャ | Windows デバイス | VDI プラットフォームのバージョン | サポートされています |
| --- | --- | --- | --- | --- |
| ハイブリッド Azure AD 参加済み | フェデレーション* | 最新の Windows*** とダウンレベルの Windows**** | 永続的 | はい |
|   |   | 最新の Windows | 非永続的 | いいえ |
|   |   | ダウンレベルの Windows | 非永続的 | はい |
|   | マネージド** | 最新の Windows とダウンレベルの Windows | 永続的 | はい |
|   |   | 最新の Windows | 非永続的 | いいえ |
|   |   | ダウンレベルの Windows | 非永続的 | はい |
| Azure AD 参加済み | フェデレーション | 最新の Windows | 永続的 | いいえ |
|   |   |   | 非永続的 | いいえ |
|   | マネージド | 最新の Windows | 永続的 | いいえ |
|   |   |   | 非永続的 | いいえ |
| Azure AD 登録済み | フェデレーション | 最新の Windows | 永続的 | いいえ |
|   |   |   | 非永続的 | いいえ |
|   | マネージド | 最新の Windows | 永続的 | いいえ |
|   |   |   | 非永続的 | いいえ |

\* **フェデレーション** ID インフラストラクチャ環境は、AD FS やその他のサードパーティ IDP などの ID プロバイダーを備えた環境を表します。

\*\* **マネージド** ID インフラストラクチャ環境は、[パスワード ハッシュ同期 (PHS)](../hybrid/whatis-phs.md) または [パススルー認証 (PTA)](../hybrid/how-to-connect-pta.md) のいずれかを[シームレス シングル サインオン](../hybrid/how-to-connect-sso.md)と共に指定して Azure AD が ID プロバイダーとしてデプロイされている環境を表します。

\*\*\* **最新の Windows** デバイスは、Windows 10、Windows Server 2016、および Windows Server 2019 を表します。

\*\*\*\* **ダウンレベルの Windows** デバイスは、Windows 7、Windows 8.1、Windows Server 2008 R2、Windows Server 2012、および Windows Server 2012 R2 を表します。 Windows 7 のサポート情報については、「[Windows 7 のサポート終了が近づいています](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support)」を参照してください。 Windows Server 2008 R2 のサポート情報については、「[Windows Server 2008 のサポート終了に備える](https://www.microsoft.com/cloud-platform/windows-server-2008)」を参照してください。

## <a name="microsofts-guidance"></a>Microsoft のガイダンス

管理者は、ID インフラストラクチャに基づいて次の記事を参照して、ハイブリッド Azure AD 参加を構成する方法を理解してください。

- [フェデレーション環境用のハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-federated-domains.md)
- [マネージド環境用のハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-managed-domains.md)

システム準備ツール (sysprep.exe) を使用していて、インストールに Windows 10 1809 より前のイメージを使用している場合は、そのイメージが、既にハイブリッド Azure AD 参加済みとして Azure AD に登録されているデバイスからのものではないことを確認します。

仮想マシン (VM) のスナップショットを利用して追加の VM を作成する場合は、そのスナップショットが、既にハイブリッド Azure AD 参加として Azure AD に登録されている VM からのものではないことを確認します。

非永続的 VDI を展開する場合、IT 管理者は Azure AD での古いデバイスの管理に細心の注意を払う必要があります。 Microsoft は、IT 管理者が以下のガイダンスを実施することをお勧めします。 そうしないと、非永続的 VDI プラットフォームから登録された古い Hybrid Azure AD 参加済みデバイスがディレクトリに多数存在することになります。

- デスクトップが VDI ベースであることを示す、コンピューターの表示名のプレフィックスを作成して使用します。
- 次のコマンドを、ログオフ スクリプトの一部として実行します。 このコマンドは、Azure AD 呼び出しをベスト エフォートでトリガーし、デバイスを削除します。
   - ダウンレベルの Windows デバイスの場合 - autoworkplace.exe /leave
- [古いデバイスの管理](manage-stale-devices.md)のプロセスを定義して実装します。
   - 非永続的 Hybrid Azure AD 参加済みデバイスを識別する方法を用意したら、これらのデバイスのクリーンアップをより積極的に実行して、ディレクトリが多くの古いデバイスで使用されないようにすることができます。
 
## <a name="next-steps"></a>次の手順

[フェデレーション環境用のハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-federated-domains.md)

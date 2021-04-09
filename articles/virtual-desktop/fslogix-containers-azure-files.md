---
title: Windows Virtual Desktop FSLogix プロファイル コンテナー ファイル - Azure
description: この記事では、Windows Virtual Desktop での FSLogix プロファイル コンテナーと Azure Files について説明します。
author: Heidilohr
ms.topic: conceptual
ms.date: 01/04/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0b977944f3a531365487b9655820e5d8f85e13d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99225917"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix プロファイル コンテナーと Azure のファイル

Windows Virtual Desktop サービスでは、ユーザー プロファイル ソリューションとして FSLogix プロファイル コンテナーが推奨されています。 FSLogix は、Windows Virtual Desktop などのリモート コンピューティング環境でプロファイルをローミングするように設計されています。 1 つのコンテナーに完全なユーザー プロファイルが格納されます。 サインイン時、このコンテナーは、ネイティブにサポートされた仮想ハード ディスク (VHD) と Hyper-V 仮想ハード ディスク (VHDX) を使用して、コンピューティング環境に動的に接続されます。 ユーザー プロファイルはすぐに利用できるようになり、ネイティブのユーザー プロファイルとまったく同じようにシステムに表示されます。 この記事では、Windows Virtual Desktop の Azure Files 機能で FSLogix プロファイル コンテナーが使用される方法について説明します。

>[!NOTE]
>Azure 上の FSLogix プロファイル コンテナーのさまざまなストレージ オプションに関する比較資料をお探しの場合は、「[FSLogix プロファイル コンテナー用のストレージ オプション](store-fslogix-profile.md)」を参照してください。

## <a name="user-profiles"></a>ユーザー プロファイル

ユーザー プロファイルには、デスクトップ設定、永続的なネットワーク接続、アプリケーション設定などの構成情報を含む、個人に関するデータ要素が入っています。 既定では、オペレーティング システムに緊密に統合されるローカル ユーザー プロファイルが Windows によって作成されます。

リモート ユーザー プロファイルでは、ユーザー データとオペレーティング システムの間にパーティションが設けられます。 これにより、ユーザー データに影響を与えることなく、オペレーティング システムを交換したり変更したりすることができます。 リモート デスクトップ セッション ホスト (RDSH) および仮想デスクトップ インフラストラクチャ (VDI) では、次の理由で、オペレーティング システムを置き換えることがあります。

- オペレーティング システムのアップグレード
- 既存の仮想マシン (VM) の交換
- ユーザーがプールされた (非永続的な) RDSH または VDI 環境に属している

Microsoft 製品は、リモート ユーザー プロファイルに対して次に示すようないくつかのテクノロジを使用して動作します。
- 移動ユーザー プロファイル (RUP)
- ユーザー プロファイル ディスク (UPD)
- Enterprise State Roaming (ESR)

UPD と RUP は、リモート デスクトップ セッション ホスト (RDSH) および仮想ハード ディスク (VHD) の環境のユーザー プロファイル向けに最も広く使用されているテクノロジです。

### <a name="challenges-with-previous-user-profile-technologies"></a>以前のユーザー プロファイル テクノロジでの課題

ユーザー プロファイル用の既存および以前の Microsoft ソリューションにはさまざまな課題がありました。 以前のソリューションでは、RDSH または VDI 環境で生じる、ユーザー プロファイルのすべてのニーズに対応しているわけではありませんでした。 たとえば、UPD では大きな OST ファイルを処理することができず、RUP では最新の設定が持続されません。

#### <a name="functionality"></a>機能

次の表に、以前のユーザー プロファイル テクノロジの利点と制限事項を示します。

| テクノロジ | 最新の設定 | Win32 設定 | OS 設定 | ユーザー データ | Server SKU でサポートされる | Azure 上のバックエンド ストレージ | オンプレミスのバックエンド ストレージ | バージョンのサポート | 以降のサインイン時間 |Notes|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **ユーザー プロファイル ディスク (UPD)** | はい | はい | はい | はい | はい | いいえ | はい | Win 7+ | はい | |
| **移動ユーザー プロファイル (RUP)、メンテナンス モード** | いいえ | はい | はい | はい | はい| いいえ | はい | Win 7+ | いいえ | |
| **Enterprise State Roaming (ESR)** | はい | いいえ | はい | いいえ | メモを参照 | はい | いいえ | Windows 10 | いいえ | サーバー SKU 上で機能するが、サポートしているユーザー インターフェイスはありません。 |
| **User Experience Virtualization (UE-V)** | はい | はい | はい | いいえ | はい | いいえ | はい | Win 7+ | いいえ |  |
| **OneDrive クラウド ファイル** | いいえ | いいえ | いいえ | はい | メモを参照 | メモを参照  | メモを参照 | Win 10 RS3 | いいえ | サーバー SKU ではテストされません。 Azure 上のバックエンド ストレージは同期クライアントに依存します。 オンプレミスのバックエンド ストレージには同期クライアントが必要です。 |

#### <a name="performance"></a>パフォーマンス

UPD では、パフォーマンス要件に対応するために[記憶域スペース ダイレクト (S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) が必要です。 UPD ではサーバー メッセージ ブロック (SMB) プロトコルが使用されます。 これによって、ユーザーがログインする VM にプロファイルがコピーされます。

#### <a name="cost"></a>コスト

S2D クラスターを使用すれば必要なパフォーマンスを実現できますが、その一方で、企業顧客にとってはこれは高価なものとなり、特に中小企業 (SMB) の顧客には高価です。 このソリューションの場合、企業は、ストレージ ディスクに対する支払いに加えて、共有用のディスクを使用する VM のコストを負担します。

#### <a name="administrative-overhead"></a>管理オーバーヘッド

S2D クラスターでは、オペレーティング システムが、修正プログラムおよび更新プログラムを適用され、セキュリティで保護された状態で維持される必要があります。 これらのプロセスと S2D ディザスター リカバリーの設定の複雑さにより、S2D は専任の IT スタッフを抱える企業にのみ適しています。

## <a name="fslogix-profile-containers"></a>FSLogix プロファイル コンテナー

2018 年 11 月 19 日、[Microsoft は FSLogix を買収しました](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/)。 FSLogix は多くのプロファイル コンテナーの課題に対処しています。 その中でも重要なものは次のとおりです。

- **パフォーマンス:** [FSLogix プロファイル コンテナー](/fslogix/configure-profile-container-tutorial/)はハイ パフォーマンスであり、これによって、これまで Exchange キャッシュ モードの妨げになっていたパフォーマンスの問題が解決されます。
- **OneDrive:** FSLogix プロファイル コンテナーがなければ、非永続的な RDSH または VDI 環境内で OneDrive for Business はサポートされません。 [OneDrive VDI のサポート ページ](/onedrive/sync-vdi-support)に、それらがやりとりする方法が説明されています。 詳細については、「[仮想デスクトップ上で同期クライアントを使用する](/deployoffice/rds-onedrive-business-vdi/)」を参照してください。
- **追加のフォルダー:** FSLogix には、追加のフォルダーを含められるようにユーザー プロファイルを拡張する機能が用意されています。

買収以降、Microsoft は、UPD などの既存のユーザー プロファイル ソリューションから FSLogix プロファイル コンテナーへの置き換えを開始しました。

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure Files と Azure Active Directory ドメイン サービスの統合

FSLogix プロファイル コンテナーのパフォーマンスおよび機能にはクラウドが活用されます。 2019 年 8 月 7 日、Microsoft Azure Files は [Azure Active Directory ドメイン サービス (AD DS) による Azure Files 認証](../storage/files/storage-files-active-directory-overview.md)の一般提供を発表しました。 コストと管理オーバーヘッドの両方に対処することで、Azure AD DS 認証を使用する Azure Files は、Windows Virtual Desktop サービスでのユーザー プロファイルに対する優れたソリューションになります。

## <a name="best-practices-for-windows-virtual-desktop"></a>Windows Virtual Desktop のベスト プラクティス

Windows Virtual Desktop では、顧客が使用している VM のサイズ、種類、および数を完全に制御できます。 詳細については、「[Windows Virtual Desktop とは](overview.md)」を参照してください。

ご利用の Windows Virtual Desktop がベスト プラクティスに確実に従うようにするには:

- Azure Files ストレージ アカウントをセッション ホスト VM と同じリージョンにする必要があります。
- Azure Files のアクセス許可を、「[Requirements - Profile Containers](/fslogix/fslogix-storage-config-ht)」 (要件 - プロファイル コンテナー) に記載されているアクセス許可と一致させる必要があります。
- 各ホスト プール VM を、同じマスター イメージに基づいて同じ種類および同じサイズの VM で構築する必要があります。
- 管理、スケーリング、および更新を容易にするために各ホスト プール VM を同じリソース グループに含める必要があります。
- 最適なパフォーマンスを得るには、ストレージ ソリューションと FSLogix プロファイル コンテナーを同じデータ センターの場所に置く必要があります。
- マスター イメージを含むストレージ アカウントを、VM がプロビジョニングされるのと同じリージョンおよびサブスクリプションに置く必要があります。

## <a name="next-steps"></a>次のステップ

次のガイドを使用して、Windows Virtual Desktop 環境を設定します。

- デスクトップ仮想化ソリューションの構築を開始するには、[Windows Virtual Desktop でのテナントの作成](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)に関するページを参照してください。
- ご利用の Windows Virtual Desktop テナント内にホスト プールを作成するには、「[Azure Marketplace を使用してホスト プールを作成する](create-host-pools-azure-marketplace.md)」を参照してください。
- クラウド内でフル マネージドファイル共有を設定するには、[Azure Files 共有の設定](/azure/storage/files/storage-files-active-directory-enable/)に関するページを参照してください。
- FSLogix プロファイル コンテナーを構成するには、「[ファイル共有を使用してホスト プール用のプロファイル コンテナーを作成する](create-host-pools-user-profile.md)」を参照してください。
- ユーザーをホスト プールに割り当てるには、[Windows Virtual Desktop のアプリ グループの管理](manage-app-groups.md)に関するページを参照してください。
- Web ブラウザーからご利用の Windows Virtual Desktop にアクセスするには、[Windows Virtual Desktop への接続](connect-web.md)に関するページを参照してください。

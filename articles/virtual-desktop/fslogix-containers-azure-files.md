---
title: Windows Virtual Desktop での FSLogix プロファイル コンテナーと Azure Files - Azure
description: この記事では、Windows Virtual Desktop での FSLogix プロファイル コンテナーと Azure のファイルについて説明します。
services: virtual-desktop
author: chjenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: v-chjenk
ms.openlocfilehash: b3032aa796b3c79572bbf8b2beb85efc252ff73b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497530"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix プロファイル コンテナーと Azure のファイル

Windows Virtual Desktop プレビュー サービスでは、ユーザー プロファイル ソリューションとして FSLogix プロファイル コンテナーが推奨されています。 FSLogix は、Windows Virtual Desktop などのリモート コンピューティング環境でプロファイルをローミングするように設計されています。 1 つのコンテナーに完全なユーザー プロファイルが格納されます。 サインイン時、このコンテナーは、ネイティブにサポートされた仮想ハード ディスク (VHD) と Hyper-V 仮想ハード ディスク (VHDX) を使用して、コンピューティング環境に動的に接続されます。 ユーザー プロファイルはすぐに利用できるようになり、ネイティブのユーザー プロファイルとまったく同じようにシステムに表示されます。

この記事では、Azure Files で使用する FSLogix プロファイル コンテナーについて説明します。 この情報は、[3 月 21 日に発表](https://www.microsoft.com/microsoft-365/blog/2019/03/21/windows-virtual-desktop-public-preview/)された Windows Virtual Desktop のコンテキストに基づいています。

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

UPD と RUP は、リモート デスクトップ セッション ホスト (RDSH) および仮想ハード ディスク (VHD) の環境のユーザー プロファイル向けに最も広く使用されていテクノロジです。

### <a name="challenges-with-previous-user-profile-technologies"></a>以前のユーザー プロファイル テクノロジでの課題

ユーザー プロファイル用の既存および以前の Microsoft ソリューションにはさまざまな課題がありました。 以前のソリューションでは、RDSH または VDI 環境で生じる、ユーザー プロファイルのすべてのニーズに対応しているわけではありませんでした。 たとえば、UPD では大きな OST ファイルを処理することができず、RUP では最新の設定が持続されません。

#### <a name="functionality"></a>機能

次の表に、以前のユーザー プロファイル テクノロジの利点と制限事項を示します。

| テクノロジ | 最新の設定 | Win32 設定 | OS 設定 | ユーザー データ | Server SKU でサポートされる | Azure 上のバックエンド ストレージ | オンプレミスのバックエンド ストレージ | バージョンのサポート | 以降のサインイン時間 |メモ|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **ユーザー プロファイル ディスク (UPD)** | はい | はい | はい | はい | はい | いいえ | はい | Win 7+ | はい | |
| **移動ユーザー プロファイル (RUP)、メンテナンス モード** | いいえ | 可能 | はい | はい | はい| いいえ | はい | Win 7+ | いいえ | |
| **Enterprise State Roaming (ESR)** | はい | いいえ | はい | いいえ | メモを参照 | はい | いいえ | Windows 10 | いいえ | サーバー SKU 上で機能するが、サポートしているユーザー インターフェイスはありません。 |
| **User Experience Virtualization (UE-V)** | はい | はい | はい | いいえ | はい | いいえ | はい | Win 7+ | いいえ |  |
| **OneDrive クラウド ファイル** | いいえ | いいえ | いいえ | はい | メモを参照 | メモを参照  | メモを参照 | Win 10 RS3 | いいえ | サーバー SKU ではテストされません。 Azure 上のバックエンド ストレージは同期クライアントに依存します。 オンプレミスのバックエンド ストレージには同期クライアントが必要です。 |

#### <a name="performance"></a>パフォーマンス

UPD では、パフォーマンス要件に対応するために[記憶域スペース ダイレクト (S2D)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) が必要です。 UPD ではサーバー メッセージ ブロック (SMB) プロトコルが使用されます。 これによって、ユーザーがログインする VM にプロファイルがコピーされます。 S2D と UPD の組み合わせは、Windows Virtual Desktop 用に RDS チームがサービスのプレビュー期間中に推奨するソリューションです。  

#### <a name="cost"></a>コスト

S2D クラスターを使用すれば必要なパフォーマンスを実現できますが、その一方で、企業顧客にとってはこれは高価なものとなり、特に中小企業 (SMB) の顧客には高価です。 このソリューションの場合、企業は、Premium Storage ディスクに対する支払いに加えて、共有用のディスクを使用する VM のコストを負担します。

#### <a name="administrative-overhead"></a>管理オーバーヘッド

S2D クラスターでは、オペレーティング システムが、修正プログラムおよび更新プログラムを適用され、セキュリティで保護された状態で維持される必要があります。 これらのプロセスと S2D ディザスター リカバリーの設定の複雑さにより、S2D は専任の IT スタッフを抱える企業にのみ適しています。

## <a name="fslogix-profile-containers"></a>FSLogix プロファイル コンテナー

2018 年 11 月 19 日、[Microsoft は FSLogix を買収しました](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/)。 FSLogix は多くのプロファイル コンテナーの課題に対処しています。 その中でも重要なものは次のとおりです。

- **パフォーマンス:** [FSLogix プロファイル コンテナー](https://fslogix.com/products/profile-containers)はハイ パフォーマンスであり、これによって、これまで Exchange キャッシュ モードの妨げになっていたパフォーマンスの問題が解決されます。
- **OneDrive:** FSLogix プロファイル コンテナーがなければ、非永続的な RDSH または VDI 環境内で OneDrive for Business はサポートされません。 「[OneDrive for Business and FSLogix best practices](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices)」 (OneDrive for Business と FSLogix のベストプラクティス) には、それらがやりとりする方法が説明されています。 詳細については、「[仮想デスクトップ上で同期クライアントを使用する](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi)」を参照してください。
- **追加のフォルダー:** FSLogix には、追加のフォルダーを含められるようにユーザー プロファイルを拡張する機能が用意されています。

買収以降、Microsoft は、UPD などの既存のユーザー プロファイル ソリューションから FSLogix プロファイル コンテナーへの置き換えを開始しました。

## <a name="azure-files-integration-with-azure-active-directory"></a>Azure Files と Azure Active Directory の統合

FSLogix プロファイル コンテナーのパフォーマンスおよび機能にはクラウドが活用されます。 2018 年 9 月 24 日、Microsoft Azure Files から、[Azure Active Directory 認証をサポートする Azure Files](https://azure.microsoft.com/blog/azure-active-directory-integration-for-smb-access-now-in-public-preview/) のパブリック プレビューが発表されました。 コストと管理オーバーヘッドの両方に対処することで、Azure Active Directory 認証を使用する Azure Files は、新しい Windows Virtual Desktop サービスでのユーザー プロファイルに対する優れたソリューションになります。

## <a name="best-practices-for-windows-virtual-desktop"></a>Windows Virtual Desktop のベスト プラクティス

Windows Virtual Desktop では、顧客が使用している VM のサイズ、種類、および数を完全に制御できます。 詳細については、「[Windows Virtual Desktop プレビューとは](https://docs.microsoft.com/azure/virtual-desktop/overview)」を参照してください。

ご利用の Windows Virtual Desktop がベスト プラクティスに確実に従うようにするには:

- Azure Files ストレージ アカウントをセッション ホスト VM と同じリージョンにする必要があります。
- Azure Files のアクセス許可を、「[Requirements - Profile Containers](https://docs.fslogix.com/display/20170529/Requirements+-+Profile+Containers)」 (要件 - プロファイル コンテナー) に記載されているアクセス許可と一致させる必要があります。
- 各ホストプールを、同じマスター イメージに基づいて同じ種類および同じサイズの VM で構築する必要があります。
- 管理、スケーリング、および更新を容易にするために各ホスト プール VM を同じリソースグループに含める必要があります。
- 最適なパフォーマンスを得るには、ストレージ ソリューションと FSLogix プロファイル コンテナーを同じデータ センターの場所に置く必要があります。
- マスター イメージを含むストレージ アカウントを、VM がプロビジョニングされるのと同じリージョンおよびサブスクリプションに置く必要があります。

## <a name="next-steps"></a>次の手順

次の手順を使用して、Windows Virtual Desktop 環境を設定します。

- デスクトップ仮想化ソリューションの構築を開始するには、[Windows Virtual Desktop でのテナントの作成](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory)に関するページを参照してください。
- ご利用の Windows Virtual Desktop テナント内にホスト プールを作成するには、「[Azure Marketplace を使用してホスト プールを作成する](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)」を参照してください。
- クラウド内でフル マネージドファイル共有を設定するには、[Azure Files 共有の設定](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable)に関するページを参照してください。
- FSLogix プロファイル コンテナーを構成するには、「[ホスト プールのユーザー プロファイル共有を設定する](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile)」を参照してください。
- ユーザーをホスト プールに割り当てるには、[Windows Virtual Desktop のアプリ グループの管理](https://docs.microsoft.com/azure/virtual-desktop/manage-app-groups)に関するページを参照してください。
- Web ブラウザーからご利用の Windows Virtual Desktop にアクセスするには、[Windows Virtual Desktop への接続](https://docs.microsoft.com/azure/virtual-desktop/connect-web)に関するページを参照してください。

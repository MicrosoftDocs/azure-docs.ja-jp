---
title: Windows 10 Enterprise マルチセッションに関する FAQ - Azure
description: Windows Virtual Desktop に Windows 10 Enterprise マルチセッションを使用するためのよく寄せられる質問とベスト プラクティス。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: helohr
ms.openlocfilehash: a44f1e93302b90b88ef63030fab17433855af4ff
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143270"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Windows 10 Enterprise マルチセッションに関する FAQ

この記事では、よく寄せられる質問に回答し、Windows 10 Enterprise マルチセッションのベスト プラクティスについて説明します。
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>Windows 10 Enterprise マルチセッションとは 

Windows 10 Enterprise マルチセッション (旧称 Windows 10 Enterprise for Virtual Desktops (EVD)) は、これまで Windows Server でしかできなかった、複数の対話型セッションを同時に実行できる新しいリモート デスクトップ セッション ホストです。 この機能により、ユーザーは使い慣れた Windows 10 エクスペリエンスを利用しつつ、IT はマルチセッションのコスト面での恩恵が受けられ、RDS クライアント アクセス ライセンス (CAL) ではなく、既存のユーザーごとの Windows ライセンスを使用することができます。 ライセンスと価格の詳細については、「[Windows Virtual Desktop の価格](https://azure.microsoft.com/pricing/details/virtual-desktop/)」を参照してください。 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Windows 10 Enterprise マルチセッションで対話型セッションを同時に実行できるユーザー数はいくつですか?

同時にアクティブにできる対話型セッションの数は、ご使用のシステムのハードウェア リソース (vCPU、メモリ、ディスク、vGPU)、ユーザーがセッションにサインインしている間にアプリを使用する方法、およびシステムのワークロードの大きさによって異なります。 ご使用のシステムのパフォーマンスを検証して、Windows 10 Enterprise マルチセッションで対応可能なユーザー数を把握することをお勧めします。 詳細については、「[Windows Virtual Desktop の価格](https://azure.microsoft.com/pricing/details/virtual-desktop/)」を参照してください。 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>アプリケーションで Windows 10 Enterprise マルチセッションがサーバー オペレーティング システムとして報告されるのはなぜですか?

Windows 10 Enterprise マルチセッションは、Windows 10 Enterprise の仮想エディションです。 違いの 1 つは、このオペレーティング システム (OS) では [ProductType](https://docs.microsoft.com/windows/desktop/cimwin32prov/win32-operatingsystem) の値が Windows Server と同じ 2 として報告されることです。 このプロパティにより、OS で、既存の RDSH 管理ツール、RDSH マルチセッション対応アプリケーション、および RDSH 環境の主に低レベルのシステム パフォーマンスの最適化に対する互換性が維持されます。 一部のアプリケーション インストーラーでは、ProductType がクライアントに設定されていることを検出するかどうかによって、Windows 10 マルチセッションへのインストールをブロックすることができます。 アプリがインストールされない場合は、アプリケーション ベンダーに更新バージョンを問い合わせてください。 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Windows 10 Enterprise マルチセッションをオンプレミスで実行できますか?

Windows 10 Enterprise マルチセッションは、Azure 用の Windows Virtual Desktop サービスに最適化されているため、オンプレミスの運用環境では実行できません。 運用目的で Azure の外部で Windows 10 Enterprise マルチセッションを実行することは、ライセンス契約違反です。 Windows 10 Enterprise マルチセッションは、オンプレミスのキー管理サービス (KMS) に対してアクティブ化されません。
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>自分の組織向けに Windows 10 Enterprise マルチセッション イメージをカスタマイズする方法を教えてください。

Windows 10 Enterprise マルチセッションを使用して Azure で仮想マシン (VM) を起動し、LOB アプリケーションをインストールしてカスタマイズし、sysprep/generalize を実行したら、Azure portal を使用してイメージを作成することができます。  
 
最初に、Windows 10 Enterprise マルチセッションを使用して Azure で VM を作成します。 Azure で VM を起動する代わりに、VHD を直接ダウンロードすることができます。 その後、ダウンロードした VHD を使用して、Hyper-V が有効になっている Windows 10 PC に新しい第 1 世代の VM を作成することができます。

LOB アプリケーションをインストールしてニーズに合わせてイメージをカスタマイズし、イメージに sysprep を実行します。 カスタマイズが完了したら、VHD が含まれたイメージを Azure にアップロードします。 その後、Azure Marketplace から Windows Virtual Desktop を取得し、それを使用して、カスタマイズされたイメージで新しいホスト プールをデプロイします。
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>デプロイ後に Windows 10 Enterprise マルチセッションを管理する方法を教えてください。

サポートされている任意の構成ツールを使用できますが、Windows 10 Enterprise マルチセッションをサポートしているという理由から、System Center Configuration Manager 1906 をお勧めします。 現在、Microsoft では、Microsoft Intune のサポートに取り組んでいます。
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Windows 10 Enterprise マルチセッションを Azure Active Directory (AD) に参加させることはできますか?

Windows 10 Enterprise マルチセッションでは、現在、ハイブリッド Azure AD への参加がサポートされています。 Windows 10 Enterprise マルチセッションがドメインに参加したら、既存のグループ ポリシー オブジェクトを使用して、Azure AD の登録を有効にします。 詳細については、「[Hybrid Azure Active Directory 参加の実装を計画する](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)」を参照してください。
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Windows 10 Enterprise マルチセッション イメージはどこで入手できますか?

Windows 10 Enterprise マルチセッションは Azure ギャラリーにあります。 これを見つけるには、Azure portal に移動し、Windows 10 Enterprise for Virtual Desktops のリリースを検索します。 Office Pro Plus と統合されたイメージの場合は、Azure portal にアクセスし、Microsoft Windows 10 + Office 365 ProPlus を検索します。

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>どの Windows 10 Enterprise マルチセッション イメージを使用すればよいですか?

Azure ギャラリーには、Windows 10 Enterprise マルチセッション、バージョン 1809、Windows 10 Enterprise マルチセッション、バージョン 1903 など、いくつかのリリースがあります。 パフォーマンスと信頼性を向上させるため、最新バージョンを使用することをお勧めします。
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>サポートされている Windows 10 Enterprise のマルチセッション バージョンはどれですか?

Windows 10 Enterprise マルチセッション、バージョン 1809 以降がサポートされており、Azure ギャラリーで入手できます。 これらのリリースは、Windows 10 Enterprise と同じサポート ライフサイクル ポリシーに従っています。つまり、春のリリースは 18 か月間サポートされ、秋のリリースは 30 か月間サポートされます。
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Windows 10 Enterprise マルチセッションには、どのプロファイル管理ソリューションを使用すればよいですか?

非永続的な環境または一元的に格納されたプロファイルを必要とするその他のシナリオで Windows 10 Enterprise を構成する場合は、FSLogix プロファイル コンテナーを使用することをお勧めします。 FSLogix を使用すると、ユーザーのすべてのセッションに対してユーザー プロファイルが確実に利用でき、最新の状態に保つことができます。 また、FSLogix プロファイル コンテナーを使用して、適切なアクセス許可を持つ任意の SMB 共有にユーザー プロファイルを格納することをお勧めしますが、必要に応じて、ユーザー プロファイルを Azure ページ BLOB ストレージに格納することもできます。 Windows Virtual Desktop ユーザーは、追加コストなしで FSLogix を使用できます。
 
FSLogix プロファイル コンテナーの構成方法の詳細については、「[FSLogix プロファイル コンテナーを構成する](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)」を参照してください。  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Windows 10 Enterprise マルチセッションにアクセスするには、どのライセンスが必要ですか?

適用可能なライセンスの完全な一覧については、「[Windows Virtual Desktop の価格](https://azure.microsoft.com/pricing/details/virtual-desktop/)」を参照してください。
 
## <a name="next-steps"></a>次の手順

Windows Virtual Desktop と Windows 10 Enterprise マルチセッションの詳細については、次をご覧ください。

- [Windows Virtual Desktop プレビューのドキュメント](overview.md)を確認する
- [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) にアクセスする
- [Windows Virtual Desktop のチュートリアル](tenant-setup-azure-active-directory.md)を使用して Windows Virtual Desktop のデプロイをセットアップする

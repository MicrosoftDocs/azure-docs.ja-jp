---
title: デバイスの Hybrid Azure AD 参加を制御する | Microsoft Docs
description: Azure Active Directory でデバイスの Hybrid Azure AD 参加を制御する方法を説明します。
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93afc6f748ca9f464261c59e037a603ab6113bf8
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518169"
---
# <a name="control-the-hybrid-azure-ad-join-of-your-devices"></a>デバイスのハイブリッド Azure AD 参加を制御する

Hybrid Azure Active Directory (Azure AD) 参加は、オンプレミスのドメインに参加しているデバイスを Azure AD に自動的に登録するプロセスです。 場合によっては、すべてのデバイスを自動的に登録したくないことがあります。 たとえば、すべてが期待どおりに機能することを確認するための最初のロールアウト中にこのような状況が発生します。

この記事では、デバイスの Hybrid Azure AD 参加を制御する方法に関するガイダンスを提供します。 


## <a name="prerequisites"></a>前提条件

この記事では、次の内容を熟知していることを前提としています。

-  [Azure Active Directory のデバイス管理の概要](../device-management-introduction.md)
 
-  [Hybrid Azure Active Directory 参加の実装の計画](hybrid-azuread-join-plan.md)

-  [マネージド ドメインの Hybrid Azure Active Directory 参加の構成](hybrid-azuread-join-managed-domains.md)または[フェデレーション ドメインの Hybrid Azure Active Directory 参加の構成](hybrid-azuread-join-federated-domains.md)



## <a name="control-windows-current-devices"></a>最新の Windows デバイスの制御

Windows デスクトップ オペレーティング システムを実行するデバイスの場合、サポートされるバージョンは Windows 10 Anniversary Update (Version 1607) 以降です。 ベスト プラクティスとして、Windows 10 の最新バージョンにアップグレードしてください。

最新のすべての Windows デバイスは、デバイスの起動時またはユーザーのサインイン時に Azure AD に自動的に登録されます。 この動作は、グループ ポリシー オブジェクト (GPO) または System Center Configuration Manager を使用して制御できます。

最新の Windows デバイスを制御するには、次の操作を行う必要があります。 


1.  **すべてのデバイス**:デバイスの自動登録を無効にします。
2.  **選択したデバイス**:デバイスの自動登録を有効にします。

すべてが期待どおりに機能することを確認した後、すべてのデバイスの自動デバイス登録を再度有効にする準備が整います。



### <a name="group-policy-object"></a>グループ ポリシー オブジェクト 

次の GPO をデプロイすることによって、デバイスのデバイス登録動作を制御できます:**ドメインに参加しているコンピューターをデバイスとして登録する**。

GPO を設定するには:

1.  **サーバー マネージャー**を開き、**[ツール]** > **[グループ ポリシーの管理]** の順に移動します。

2.  自動登録を無効または有効にするドメインに対応するドメイン ノードに移動します。

3.  **[グループ ポリシー オブジェクト]** を右クリックし、**[新規]** を選択します。

4.  グループ ポリシー オブジェクトの名前 (たとえば、"**Hybrid Azure AD 参加**") を入力します。 

5.  **[OK]** を選択します。

6.  新しい GPO を右クリックし、**[編集]** を選択します。

7.  **[コンピューターの構成]** > **[ポリシー]** > **[管理用テンプレート]** > **[Windows コンポーネント]** > **[デバイスの登録]** に移動します。 

8.  **[ドメインに参加しているコンピューターをデバイスとして登録する]** を右クリックし、**[編集]** を選択します。

    > [!NOTE] 
    > このグループ ポリシー テンプレートは、以前のバージョンのグループ ポリシー管理コンソールから変更されています。 以前のバージョンのコンソールを使用している場合は、**[コンピューターの構成]** > **[ポリシー]** > **[管理用テンプレート]** > **[Windows コンポーネント]** > **[デバイスの登録]** > **[Register domain joined computer as device]\(ドメイン参加コンピューターをデバイスとして登録\)** に移動します。 

9.  次の設定のいずれかを選択し、**[適用]** を選択します。

    - **無効**: デバイスの自動登録を無効にします。
    - **有効**: デバイスの自動登録を有効にします。

10. **[OK]** を選択します。

GPO を任意の場所にリンクする必要があります。 たとえば、組織内の現在のすべてのドメイン参加済みデバイスに対してこのポリシーを設定するには、GPO をドメインにリンクします。 制御されたデプロイを行うには、このポリシーを、組織単位またはセキュリティ グループに属するドメイン参加済みの最新の Windows デバイスに設定します。

### <a name="configuration-manager-controlled-deployment"></a>Configuration Manager を使用して制御するデプロイ 

現在のデバイスのデバイス登録動作を制御するには、次のクライアント設定を構成します:**[Automatically register new Windows 10 domain joined devices with Azure Active Directory]\(新しい Windows 10 ドメインに参加しているデバイスを自動的に Azure Active Directory に登録する\)**。

クライアント設定を構成するには:

1.  **Configuration Manager** を開き、**[管理]** を選択して、**[クライアントの設定]** に移動します。

2.  **[既定のクライアント設定]** のプロパティを開き、**[クラウド サービス]** を選択します。

3.  **[デバイスの設定]** の **[Automatically register new Windows 10 domain joined devices with Azure Active Directory]\(新しい Windows 10 ドメインに参加しているデバイスを自動的に Azure Active Directory に登録する\)** で、次のいずれかの設定を選択します。

    - **[いいえ]**:デバイスの自動登録を無効にします。
    - **[はい]**:デバイスの自動登録を有効にします。

4.  **[OK]** を選択します。

このクライアント設定を任意の場所にリンクする必要があります。 たとえば、組織内のすべての最新の Windows デバイスに対してこのクライアント設定を構成するには、クライアント設定をドメインにリンクします。 制御されたデプロイを行うには、このクライアント設定を、組織単位またはセキュリティ グループに属するドメイン参加済みの最新の Windows デバイスに構成します。

> [!Important]
> 上の構成では既存のドメイン参加済み Windows 10 デバイスが処理されますが、新しくドメインに参加するデバイスでは、デバイスでのグループ ポリシーまたは Configuration Manager の設定の適用が遅れることがあるため、まだ Hybrid Azure AD 参加の完了が試みられる可能性があります。 
>
> これを回避するには、(プロビジョニング方法の例として使用される) 新しい Sysprep イメージを作成することをお勧めします。 Hybrid Azure AD に参加したことがなく、グループ ポリシーの設定または Configuration Manager クライアントの設定が既に適用されているデバイスから、それを作成します。 また、組織のドメインに参加する新しいコンピューターのプロビジョニング用に、新しいイメージを使う必要もあります。 

## <a name="control-windows-down-level-devices"></a>ダウンレベルの Windows デバイスの制御

ダウンレベルの Windows デバイスを登録するには、Windows インストーラー パッケージ (.msi) をダウンロード センターの「[Microsoft Workplace Join for non-Windows 10 computers (非 Windows 10 コンピューター用の Microsoft Workplace Join)](https://www.microsoft.com/download/details.aspx?id=53554)」ページからダウンロードし、インストールする必要があります。

[System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager) などのソフトウェア ディストリビューション システムを使用して、このパッケージをデプロイできます。 パッケージは、quiet パラメーターを使用する、標準のサイレント インストール オプションをサポートしています。 Configuration Manager の Current Branch には、完了した登録を追跡する機能など、以前のバージョンにはない利点が追加されています。

インストーラーは、スケジュールされたタスクを、ユーザーのコンテキストで実行されるシステムに作成します。 このタスクは、ユーザーが Windows にサインインするとトリガーされます。 Azure AD によって認証が行われた後、ユーザーの資格情報を使ってサイレントにデバイスが Azure AD に登録されます。

デバイスの登録を制御するには、選択したダウンレベルの Windows デバイスのグループのみに Windows インストーラー パッケージをデプロイする必要があります。 すべてが期待どおりに機能することを確認できた時点で、パッケージをすべてのダウンレベル デバイスにデプロイする準備が整います。


## <a name="next-steps"></a>次の手順

* [Azure Active Directory のデバイス管理の概要](../device-management-introduction.md)




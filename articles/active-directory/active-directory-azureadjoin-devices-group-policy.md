---
title: "Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する | Microsoft Docs"
description: "デバイスを企業ネットワークのドメインに参加させることができるように管理者がグループ ポリシーを構成する方法を説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: 2ff29f3e-5325-4f43-9baa-6ae8d6bad3e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 39bd4fcbe1b9a197bcf5b8bb33bf6fffae2063fc
ms.lasthandoff: 03/11/2017


---
# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する
Domain Join は、過去 15 年以上にわたり、組織がデバイスを接続して作業を行ってきた従来の方法です。 ユーザーは Windows Server Active Directory (Active Directory) の職場または学校アカウントを使用して自分のデバイスにサインインでき、IT 部門はそれらのデバイスを完全に管理できます。 組織は、通常、イメージ作成方法を利用してユーザーにデバイスをプロビジョニングし、一般的に System Center Configuration Manager (SCCM) またはグループ ポリシーを使用してそのデバイスを管理します。


Windows 10 のドメイン参加では、Azure Active Directory (Azure AD) にデバイスを接続すると、次のような利点があります。

* どこからでも Azure AD リソースにシングル サインオン (SSO) できます
* 職場または学校アカウントを使用して、企業の Windows ストアにアクセスできます (Microsoft アカウントは不要)
* 職場または学校アカウントを使用して、企業に準拠した、デバイス間でのユーザー設定のローミングが可能です (Microsoft アカウントは不要)
* Windows Hello for Business と Windows Hello により、職場または学校アカウントの認証が強化され、サインインが便利になります
* 組織でのデバイスのグループ ポリシー設定に準拠しているデバイスのみにアクセスを制限できます

## <a name="prerequisites"></a>前提条件
Domain Join を引き続き利用できます。 ただし、SSO、職場または学校アカウントによる設定のローミング、職場または学校アカウントを使用した Windows ストアへのアクセスに関する Azure AD を利用するには、次のものが必要です。

* Azure AD サブスクリプション
* オンプレミス ディレクトリを Azure AD に拡張するための Azure AD Connect
* ドメイン参加済みデバイスを Azure AD に接続するように設定されたポリシー
* デバイス用の Windows 10 ビルド (ビルド 10551 以降)

Windows Hello for Business と Windows Hello を有効にするには、次のものも必要です。

- ユーザー証明書を発行するための**公開キー基盤 (PKI)**。

- **System Center Configuration Manager Current Branch** - バージョン 1606 以降をインストールする必要があります。  
詳細については、次を参照してください。 
    - [System Center Configuration Manager のドキュメント](https://technet.microsoft.com/library/mt346023.aspx)
    - [System Center Configuration Manager チームのブログ](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)
    - [System Center Configuration Manager での Windows Hello for Business の設定](https://docs.microsoft.com/sccm/protect/deploy-use/windows-hello-for-business-settings)

PKI のデプロイ要件の代替策として、次の操作を実行できます。

* Windows Server 2016 Active Directory ドメイン サービスを使用していくつかのドメイン コントローラーを用意します。

条件付きアクセスを有効にするために、追加でデプロイすることなく "ドメイン参加済み" デバイスへのアクセスを許可するグループ ポリシー設定を作成できます。 デバイスのコンプライアンスに基づいてアクセス制御を管理するには、次のものが必要です。

* Windows Hello for Business シナリオ用の System Center Configuration Manager の現在のブランチ (1606 またはそれ以降)

## <a name="deployment-instructions"></a>デプロイの手順

デプロイするには、「[Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の構成方法](active-directory-conditional-access-automatic-device-registration-setup.md)」で説明されている手順に従います。

## <a name="next-step"></a>次のステップ
* [エンタープライズ向け Windows 10: デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-user-upgrade.md)
* [Azure AD 参加の使用シナリオについて](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 エクスペリエンスのためのドメイン参加済みデバイスの Azure AD への接続](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD Join の設定](active-directory-azureadjoin-setup.md)



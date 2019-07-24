---
title: Data Science Virtual Machine の共通 ID を設定する - Azure | Microsoft Docs
description: 複数の Data Science Virtual Machine 間で使用できる共通のユーザー アカウントを作成する方法について説明します。 Azure Active Directory またはオンプレミスの Active Directory を使用して、データ サイエンス仮想マシンに対してユーザーを認証できます。
keywords: ディープ ラーニング, AI, データ サイエンス ツール, データ サイエンス仮想マシン, 地理空間分析, チーム データ サイエンス プロセス
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 0146ee6ee37c2eb9e98d831b54df2218d7de5b62
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754602"
---
# <a name="set-up-a-common-identity-on-the-data-science-virtual-machine"></a>Data Science Virtual Machine で共通 ID を設定する

Data Science Virtual Machine (DSVM) などの Azure 仮想マシン (VM) で、VM をプロビジョニングするときにローカル ユーザー アカウントを作成します。 その後、ユーザーはこれらの資格情報を使って VM に対する認証を行います。 複数の VM にアクセスする必要がある場合、この方法では資格情報の管理がすぐに面倒になりがちです。 共通のユーザー アカウントと標準ベースの ID プロバイダーを使用した管理により、単一の資格情報セットを使用して、複数の DSVM を含む Azure 上の複数のリソースにアクセスできます。 

Active Directory は一般的な ID プロバイダーであり、サービスとして Azure 上でも、オンプレミスでも、サポートされます。 Azure Active Directory (Azure AD) またはオンプレミスの Active Directory を使って、スタンドアロン DSVM 上で、または Azure 仮想マシン スケール セット内の DSVM のクラスター上で、ユーザーを認証できます。 これを行うには、DSVM インスタンスを Active Directory ドメインに参加させます。 

ID を管理する Active Directory が既にある場合は、それを共通の ID プロバイダーとして使用できます。 Active Directory がない場合は、[Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS) というサービスを使って、Azure 上で管理された Active Directory インスタンスを実行できます。 

[Azure AD](https://docs.microsoft.com/azure/active-directory/) のドキュメントでは、オンプレミスのディレクトリ (ある場合) への Azure AD の接続など、詳細な[管理手順](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)が説明されています。 

この記事では、Azure AD DS を使って Azure 上に完全に管理された Active Directory ドメイン サービスを設定する手順について説明します。 その後、管理された Active Directory ドメインに DSVM を参加させて、ユーザーが共通のユーザー アカウントと資格情報を使って DSVM (および他の Azure リソース) のプールにアクセスできるようにすることができます。 

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Azure 上にフル マネージドの Active Directory ドメインをセットアップする

Azure AD DS により、Azure 上でフル マネージドのサービスを提供して、ID を簡単に管理できます。 この Active Directory ドメインで、ユーザーとグループを管理します。 Azure でホストされる Active Directory ドメインとユーザー アカウントをディレクトリに設定する手順は次のとおりです。

1. Azure portal で、Active Directory にユーザーを追加します。 

   a. [Azure Active Directory 管理センター](https://aad.portal.azure.com)に、ディレクトリの全体管理者のアカウントでサインインします。
    
   b. **[Azure Active Directory]** を選択し、**[ユーザーとグループ]** を選択します。
    
   c. **[ユーザーとグループ]** で、**[すべてのユーザー]** を選択し、**[新しいユーザー]** を選択します。
   
      **[ユーザー]** ウィンドウが開きます。
      
      ![[ユーザー] ウィンドウ](./media/add-user.png)
    
   d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **名前**や**ユーザー名**など、ユーザーの詳細を入力します。 ユーザー名のドメイン名の部分は、既定の初期ドメイン名 "<ドメイン名>.onmicrosoft.com"、または検証済みの非フェデレーション [カスタム ドメイン名](../../active-directory/add-custom-domain.md) ("contoso.com" など) のいずれかである必要があります。
    
   e. このプロセスの完了後、ユーザーに提供できるように、生成されたユーザー パスワードをコピーするか、メモしておきます。
    
   f. 必要に応じて、ユーザーの **[プロファイル]**、**[グループ]**、または **[ディレクトリ ロール]** を開き、情報を入力します。 
    
   g. **[ユーザー]** で、**[作成]** を選択します。
    
   h. ユーザーがサインインできるように、新しいユーザーに生成されたパスワードを安全に配布します。

1. Azure AD DS インスタンスを作成します。 記事「[Azure Portal を使用して Azure Active Directory Domain Services を有効にする](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)」の手順 (タスク 1 からタスク 5) に従います。 Azure AD DS 内のパスワードが同期されるように、Active Directory 内の既存のユーザー パスワードを更新することが重要です。 また、記事のタスク 4 で説明されているように、DNS を Azure AD DS に追加することも重要です。 

1. 前述の手順のタスク 2 で作成した仮想ネットワーク内に、別の DSVM サブネットを作成します
1. DSVM サブネットに 1 つまたは複数の Data Science VM インスタンスを作成します。 
1. [手順](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm )に従って、DSVM を Active Directory に追加します。 
1. ホームまたはノートブック ディレクトリをホストするための Azure ファイル共有をマウントし、任意のマシンにワークスペースをマウントできるようにします。 (厳格なファイル レベルのアクセス許可が必要な場合は、1 つまたは複数の VM で NFS を実行する必要があります。)

   a. [Azure ファイル共有を作成します](../../storage/files/storage-how-to-create-file-share.md)。
    
   b. それを Linux DSVM にマウントします。 Azure portal のストレージ アカウントで Azure ファイル共有の **[接続]** ボタンを選択すると、Linux DSVM 上の Bash シェルで実行するコマンドが表示されます。 次のようなコマンドです。
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. たとえば、/data/workspace に Azure ファイル共有をマウントしたものとします。 共有内に各ユーザーのディレクトリを作成します (/data/workspace/user1、/data/workspace/user2 など)。 各ユーザーのワークスペースに、`notebooks` ディレクトリを作成します。 
1. `$HOME/userx/notebooks/remote` に `notebooks` のシンボリック リンクを作成します。   

これで、Azure でホストされている Active Directory インスタンスでユーザーが設定されるようになります。 ユーザーは、Active Directory の資格情報を使って、Azure AD DS に参加している任意の DSVM (SSH または JupyterHub) にログインできます。 ユーザー ワークスペースが Azure ファイル共有上にあるため、ユーザーは、JupyterHub の使用時に、任意の DSVM から自分のノートブックやその他の作業にアクセスできます。 

自動スケーリングの場合、仮想マシン スケール セットを使用して、この様式で、共有ディスクがマウントされているドメインにすべて参加している VM のプールを作成できます。 ユーザーは、仮想マシン スケール セット内の任意の使用可能なマシンにログインし、ノートブックが保存されている共有ディスクにアクセスできます。 

## <a name="next-steps"></a>次の手順

* [クラウド リソースにアクセスするための資格情報を安全に保存する](dsvm-secure-access-keys.md)




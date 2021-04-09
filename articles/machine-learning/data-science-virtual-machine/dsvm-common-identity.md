---
title: 共通 ID を設定する
titleSuffix: Azure Data Science Virtual Machine
description: 複数の Data Science Virtual Machine 間で使用できる共通のユーザー アカウントを作成する方法について説明します。 Azure Active Directory またはオンプレミスの Active Directory を使用して、データ サイエンス仮想マシンに対してユーザーを認証できます。
keywords: ディープ ラーニング, AI, データ サイエンス ツール, データ サイエンス仮想マシン, 地理空間分析, チーム データ サイエンス プロセス
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 43ef0f55f0b0f3ff9008f0ddf240ad0ea4f37df6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519730"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Data Science Virtual Machine で共通 ID を設定する

Data Science Virtual Machine (DSVM) などの Microsoft Azure 仮想マシン (VM) で、VM をプロビジョニングするときにローカル ユーザー アカウントを作成します。 その後、ユーザーはこれらの資格情報を使って VM に対する認証を行います。 ユーザーがアクセスする必要がある VM が複数ある場合は、資格情報の管理が非常に煩雑になることがあります。 優れたソリューションは、共通のユーザー アカウントと、標準ベースの ID プロバイダーを使用した管理をデプロイすることです。 このアプローチでは、単一の資格情報セットを使用して、複数の DSVM を含む Azure 上の複数のリソースにアクセスできます。

Active Directory は一般的な ID プロバイダーであり、Azure 上でクラウド サービスとしてもオンプレミスのディレクトリとしてもサポートされています。 Azure Active Directory (Azure AD) またはオンプレミスの Active Directory を使って、スタンドアロン DSVM 上で、または Azure 仮想マシン スケール セット内の DSVM のクラスター上で、ユーザーを認証できます。 これを行うには、DSVM インスタンスを Active Directory ドメインに参加させます。

Active Directory が既にある場合は、それを共通の ID プロバイダーとして使用できます。 Active Directory がない場合は、[Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) (Azure AD DS) を介して、Azure で管理された Active Directory インスタンスを実行できます。

[Azure AD](../../active-directory/index.yml) のドキュメントでは、オンプレミスのディレクトリ (ある場合) への Azure AD の接続に関するガイダンスなど、詳細な[管理手順](../../active-directory/hybrid/whatis-hybrid-identity.md)が説明されています。

この記事では、Azure AD DS を使って Azure 上に完全に管理された Active Directory ドメイン サービスを設定する方法について説明します。 その後、管理された Active Directory ドメインに DSVM を参加させることができます。 このアプローチでは、ユーザーは共通のユーザー アカウントと資格情報を介して DSVM (および他の Azure リソース) のプールにアクセスできます。

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Azure 上にフル マネージドの Active Directory ドメインをセットアップする

Azure AD DS により、Azure 上でフル マネージドのサービスを提供して、ID を簡単に管理できます。 この Active Directory ドメインで、ユーザーとグループを管理します。 Azure でホストされる Active Directory ドメインとユーザー アカウントをディレクトリに設定するには、これらの手順を実行します。

1. Azure portal で、Active Directory にユーザーを追加します。 

   1. [Azure Active Directory 管理センター](https://aad.portal.azure.com)に、ディレクトリの全体管理者のアカウントを使用してサインインします。
    
   1. **[Azure Active Directory]** を選択し、 **[ユーザーとグループ]** を選択します。
    
   1. **[ユーザーとグループ]** で、 **[すべてのユーザー]** を選択し、次に **[新しいユーザー]** を選択します。
   
        **[ユーザー]** ペインが開きます。
      
        ![[ユーザー] ウィンドウ](./media/add-user.png)
    
   1. **名前** や **ユーザー名** など、ユーザーの詳細を入力します。 ユーザー名のドメイン名の部分は、既定の初期ドメイン名 "<ドメイン名>.onmicrosoft.com"、または検証済みの非フェデレーション [カスタム ドメイン名](../../active-directory/fundamentals/add-custom-domain.md) ("contoso.com" など) のいずれかである必要があります。
    
   1. このプロセスの完了後、ユーザーに提供できるように、生成されたユーザー パスワードをコピーするか、メモしておきます。
    
   1. 必要に応じて、ユーザーの **[プロファイル]** 、 **[グループ]** 、または **[ディレクトリ ロール]** を開き、情報を入力します。 
    
   1. **[ユーザー]** で、 **[作成]** を選択します。
    
   1. 新しいユーザーがサインインできるように、それらのユーザーに生成されたパスワードを安全に配布します。

1. Azure AD DS インスタンスを作成します。 「[Azure portal を使用して Azure Active Directory Domain Services を有効にする](../../active-directory-domain-services/tutorial-create-instance.md)」(「インスタンスを作成して基本的な設定を構成する」セクション) の手順に従います。 Azure AD DS 内のパスワードが同期されるように、Active Directory 内の既存のユーザー パスワードを更新することが重要です。 そのセクションの「Azure portal の [基本] ウィンドウのフィールドに必要事項を入力して Azure AD DS インスタンスを作成します」で説明されているように、DNS を Azure AD DS に追加することも重要です。

1. 前述の手順の「仮想ネットワークを作成して構成する」セクションで作成した仮想ネットワーク内に、別の DSVM サブネットを作成します。
1. DSVM サブネットに 1 つまたは複数の DSVM インスタンスを作成します。
1. [手順](../../active-directory-domain-services/join-ubuntu-linux-vm.md)に従って、DSVM を Active Directory に追加します。 
1. ワークスペースを任意のマシンにマウントできるように、ホームまたはノートブック ディレクトリをホストするための Azure Files 共有をマウントします。 (厳格なファイル レベルのアクセス許可が必要な場合は、1 つまたは複数の VM でネットワーク ファイル システム [NFS] を実行する必要があります。)

   1. [Azure ファイル共有を作成します](../../storage/files/storage-how-to-create-file-share.md)。
    
   2.  この共有を Linux DSVM にマウントします。 Azure portal のストレージ アカウントで Azure Files 共有の **[接続]** を選択すると、Linux DSVM 上の bash シェルで実行するコマンドが表示されます。 次のようなコマンドです。
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. たとえば、/data/workspace に Azure Files 共有をマウントしたものとします。 次に、共有内に各ユーザーのディレクトリを作成します (/data/workspace/user1、/data/workspace/user2 など)。 各ユーザーのワークスペースに、`notebooks` ディレクトリを作成します。 
1. `$HOME/userx/notebooks/remote` に `notebooks` のシンボリック リンクを作成します。   

これで、Azure でホストされている Active Directory インスタンスでユーザーが設定されるようになります。 ユーザーは、Active Directory の資格情報を使って、Azure AD DS に参加している任意の DSVM (SSH または JupyterHub) にサインインできます。 ユーザー ワークスペースが Azure ファイル共有上にあるため、ユーザーは、JupyterHub の使用時に、任意の DSVM から自分のノートブックやその他の作業にアクセスできます。

自動スケーリングの場合、仮想マシン スケール セットを使用して、この様式で、共有ディスクがマウントされているドメインにすべて参加している VM のプールを作成できます。 ユーザーは、仮想マシン スケール セット内の任意の使用可能なマシンにサインインし、ノートブックが保存されている共有ディスクにアクセスできます。 

## <a name="next-steps"></a>次のステップ

* [クラウド リソースにアクセスするための資格情報を安全に保存する](dsvm-secure-access-keys.md)
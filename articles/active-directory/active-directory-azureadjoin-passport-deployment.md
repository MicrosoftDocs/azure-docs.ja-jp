---
title: "組織での Microsoft Windows Hello for Business の有効化 | Microsoft Docs"
description: "組織で Microsoft Passport を有効にするためのデプロイメント手順"
services: active-directory
documentationcenter: 
keywords: "Microsoft Passport、Microsoft Windows Hello for Business デプロイの構成"
author: MarkusVi
manager: femila
tags: azure-classic-portal
ms.assetid: 7dbbe3c6-1cd7-429c-a9b2-115fcbc02416
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 6f08fcdb4ec6dd124760cff54c522d998d5a0327
ms.lasthandoff: 12/29/2016


---
# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>組織での Microsoft Windows Hello for Business の有効化
[Windows 10 ドメイン参加済みデバイスを Azure Active Directory に接続](active-directory-azureadjoin-devices-group-policy.md)したら、以下の手順を実行して組織で Microsoft Windows Hello for Business を有効にします。

1. System Center Configuration Manager をデプロイする  
2. ポリシーの設定を構成する
3. 証明書プロファイルを構成する  

## <a name="deploy-system-center-configuration-manager"></a>System Center Configuration Manager をデプロイする
Windows Hello for Business のキーに基づいてユーザー証明書をデプロイするには、以下が必要です。

* **System Center Configuration Manager Current Branch** - バージョン 1606 以降をインストールする必要があります。 詳細については、「[System Center Configuration Manager のドキュメント](https://technet.microsoft.com/library/mt346023.aspx)」および [System Center Configuration Manager チームのブログ](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)を参照してください。
* **公開キー基盤 (PKI)**: ユーザー証明書を使用して Microsoft Windows Hello for Business を有効にするには、PKI が準備されている必要があります。 PKI がないか、PKI をユーザー証明書には使用しない場合は、Windows Server 2016 ビルド 10551 (以降) がインストールされている新しいドメイン コントローラーをデプロイできます。 [レプリカ ドメイン コントローラーを既存のドメインにインストール](https://technet.microsoft.com/library/jj574134.aspx)する手順に従うか、[新しい環境を作成する場合は、新しい Active Directory フォレストをインストール](https://technet.microsoft.com/library/jj574166)する手順に従います  (ISO は、[Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true) でダウンロードできます)。

## <a name="configure-policy-settings"></a>ポリシーの設定を構成する
Microsoft Windows Hello for Business ポシリー設定の構成オプションは 2 つあります。

* Active Directory のグループ ポリシー 
* System Center Configuration Manager 

Microsoft Windows Hello for Business ポリシー設定を構成する場合は、Active Directory のグループ ポリシーを使用することをお勧めします。 

System Center Configuration Manager を使って証明書もデプロイするときは、こちらの方法をお勧めします。 このシナリオは次のとおりです。

* 新しいデプロイメント シナリオとの互換性を確保します
* クライアント側の Windows 10 バージョン 1607 以降で必要です。

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>Active Directory のグループ ポリシーを使用して Microsoft Windows Hello for Business を構成する
**手順**:

1. サーバー マネージャーを開き、**[ツール]**  >  **[グループ ポリシーの管理]** に移動します。
2. [グループ ポリシーの管理] から、Azure AD 参加を有効にするドメインに対応するドメイン ノードに移動します。
3. **[グループ ポリシー オブジェクト]** を右クリックし、**[新規]** を選択します。 グループ ポリシー オブジェクトに Enable Windows Hello for Business などの名前を付けます。 **[OK]**をクリックします。
4. 新しいグループ ポリシー オブジェクトを右クリックし、 **[編集]**を選択します。
5. **[コンピューターの構成]**  >  **[ポリシー]**  >  **[管理用テンプレート]**  >  **[Windows コンポーネント]**  >  **[Windows Hello for Business]** の順に移動します。
6. **[Enable Windows Hello for Business (Windows Hello for Business を有効にする)]** を右クリックし、**[編集]** を選択します。
7. **[有効]** を選択し、**[適用]** をクリックします。 **[OK]**をクリックします。
8. これで、グループ ポリシー オブジェクトを選択した場所にリンクできるようになりました。 組織内のすべての Windows 10 ドメイン参加済みデバイスに対してこのポリシーを有効にするには、グループ ポリシーをドメインにリンクします。 次に例を示します。
   * Windows 10 ドメイン参加済みコンピューターを配置する Active Directory の特定の組織単位 (OU)
   * Azure AD に自動登録される Windows 10 ドメイン参加済みコンピューターを含む特定のセキュリティ グループ

### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>System Center Configuration Manager を使用し、Windows Hello for Business を構成する
**手順**:

1. **System Center Configuration Manager** を開き、**[資産とコンプライアンス] > [コンプライアンス設定] > [会社のリソースへのアクセス] > [ビジネス向け Windows Hello のプロファイル]** の順に移動します。
   
    ![Windows Hello for Business の構成](./media/active-directory-azureadjoin-passport-deployment/01.png)
2. 上部のツールバーの **[ビジネス向け Windows Hello プロファイルの作成]**をクリックします。
   
    ![Windows Hello for Business の構成](./media/active-directory-azureadjoin-passport-deployment/02.png)
3. **[全般]** ダイアログで、次の手順を実行します。
   
    ![Windows Hello for Business の構成](./media/active-directory-azureadjoin-passport-deployment/03.png)
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[名前]** ボックスに、プロファイルの名前を入力します。たとえば、「**My WHfB Profile**」と入力します。
   
    b. **[次へ]**をクリックします。
4. **[サポートされているプラットフォーム]** ダイアログで、このビジネス向け Windows Hello プロファイルでプロビジョニングするプラットフォームを選択し、**[次へ]** をクリックします。
   
    ![Windows Hello for Business の構成](./media/active-directory-azureadjoin-passport-deployment/04.png)
5. **[設定]** ダイアログで、次の手順を実行します。
   
    ![Windows Hello for Business の構成](./media/active-directory-azureadjoin-passport-deployment/05.png)
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[Windows Hello for Business の構成]** として、**[有効]** を選択します。
   
    b. **[トラステッド プラットフォーム モジュール (TPM) の使用]** として、**[必須]** を選択します。 
   
    c. **[認証方法]** として、**[証明書ベース]** を選択します。
   
    d. **[次へ]**をクリックします。
6. **[概要]** ダイアログで、**[次へ]** をクリックします。
7. **[完了]** ダイアログで、**[閉じる]** をクリックします。
8. 上部のツールバーの **[デプロイ]**をクリックします。
   
    ![Windows Hello for Business の構成](./media/active-directory-azureadjoin-passport-deployment/06.png)

## <a name="configure-the-certificate-profile"></a>証明書プロファイルを構成する
オンプレミスの認証に証明書ベースの認証を使用している場合は、証明書プロファイルを構成してデプロイする必要があります。 このタスクでは、System Center Configuration Manager で NDES サーバーと証明書登録ポイント サイトの役割を設定する必要があります。 詳細については、「 [Prerequisites for Certificate Profiles in Configuration Manager (Configuration Manager の証明書プロファイルの前提条件)](https://technet.microsoft.com/library/dn261205.aspx)」を参照してください。

1. **System Center Configuration Manager** を開き、**[資産とコンプライアンス] > [コンプライアンス設定] > [会社のリソースへのアクセス] > [証明書プロファイル]** の順に移動します。
2. スマート カード サインイン拡張キー使用法 (EKU) を含むテンプレートを選択します。

証明書プロファイルの **[SCEP 登録]** ページで、**[キー格納プロバイダー]** として **[Passport for Work にインストールする (それ以外は失敗)]** を選択する必要があります。

## <a name="next-steps"></a>次のステップ
* [エンタープライズ向け Windows 10: デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passport 経由でのパスワードを使用しない ID の認証](active-directory-azureadjoin-passport.md)
* [Azure AD 参加の使用シナリオについて](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 エクスペリエンスのためのドメイン参加済みデバイスの Azure AD への接続](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD Join の設定](active-directory-azureadjoin-setup.md)



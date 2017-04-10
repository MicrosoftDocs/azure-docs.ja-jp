---
title: "チュートリアル: 受信同期のための Workday の構成 | Microsoft Docs"
description: "Azure Active Directory で受信同期を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8fe96f0a-f142-4d66-b53d-3ac3eb41a661
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/19/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 50f75a05cd7e22316be5434c0b37f0f0a2ee8509
ms.openlocfilehash: 75c7565806c9737a464d9fa3fc34e4d15eb6a16b
ms.lasthandoff: 02/03/2017


---
# <a name="tutorial-configuring-workday-for-inbound-synchronization"></a>チュートリアル: 受信同期のための Workday の構成
> [!NOTE]
> Azure AD のワールドワイド インスタンスを使用している中国のお客様は、Azure Active Directory (AD) Premium を利用できます。    
> 中国の 21Vianet が運営する Microsoft Azure サービスでは、Azure AD Premium は現在サポートされていません。    
> 
> 

このチュートリアルでは、Workday から Microsoft Azure AD にユーザーをインポートするために、Workday と Microsoft Azure AD で実行する必要のある手順について説明します。    
 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。  

* 有効な Azure サブスクリプション  
* Workday のテナント  

このチュートリアルで説明するシナリオは、次の要素で構成されています。  

1. Workday のアプリケーション統合の有効化  
2. 統合システム ユーザーの作成  
3. セキュリティ グループの作成  
4. セキュリティ グループへの統合システム ユーザーの割り当て  
5. セキュリティ グループ オプションの構成  
6. セキュリティ ポリシーの変更のアクティブ化  
7. Microsoft Azure AD でのユーザー インポートの構成  

## <a name="enabling-the-application-integration-for-workday"></a>Workday のアプリケーション統合の有効化
このセクションでは、Workday のアプリケーション統合を有効にする方法について説明します。    

### <a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>Workday のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure 管理ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。    
   
   ![Active Directory](./media/active-directory-saas-inbound-synchronization-tutorial/IC700993.png "Active Directory")  
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。    
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。    
   
   ![アプリケーション](./media/active-directory-saas-inbound-synchronization-tutorial/IC700994.png "Applications")  
4. **アプリケーション ギャラリー**を開くには、**[アプリケーションの追加]**、**[組織で使用するアプリケーションを追加]** の順にクリックします。    
   
   ![実行する作業を選択してください。](./media/active-directory-saas-inbound-synchronization-tutorial/IC700995.png "実行する作業を選択してください。")  
5. **検索ボックス**に、「**Workday**」と入力します。    
   
   ![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701021.png "Workday")  
6. 結果ウィンドウで **[Workday]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。    
   
   ![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701022.png "Workday")  

## <a name="creating-an-integration-system-user"></a>統合システム ユーザーの作成
1. **Workday Workbench** で、検索ボックスに「**ユーザーの作成**」と入力し、**[Create Integration System User (統合システム ユーザーの作成)]** リンクをクリックします。     
   
   ![ユーザーの作成](./media/active-directory-saas-inbound-synchronization-tutorial/IC750979.png "ユーザーの作成")  
2. 新しい統合システム ユーザーのユーザー名とパスワードを指定して、統合システム ユーザーを作成する作業を完了します。  このユーザーはプログラムを使用してログオンするため、[次回のサインイン時に新しいパスワードを要求する] オプションはオフのままにしておきます。    

 [セッション タイムアウト (分)] は既定値の 0 のままにしておきます。これにより、ユーザーのセッションが有効期限前にタイムアウトするのを防ぎます。    
   
   ![統合システム ユーザーの作成](./media/active-directory-saas-inbound-synchronization-tutorial/IC750980.png "統合システム ユーザーの作成")  

## <a name="creating-a-security-group"></a>セキュリティ グループの作成
このチュートリアルで概説したシナリオの場合、制約のない統合システム セキュリティ グループを作成し、それにユーザーを割り当てる必要があります。    

1. 検索ボックスに「セキュリティ グループの作成」と入力し、[セキュリティ グループの作成] リンクをクリックします。     
   
   ![CreateSecurity グループ](./media/active-directory-saas-inbound-synchronization-tutorial/IC750981.png "CreateSecurity グループ")  
2. セキュリティ グループの作成タスクを完了します。  [テナント セキュリティ グループの種類] ドロップダウンから [統合システム セキュリティ グループ - 制約なし] を選択し、メンバーが明示的に追加されるセキュリティ グループを作成します。     
   
   ![CreateSecurity グループ](./media/active-directory-saas-inbound-synchronization-tutorial/IC750982.png "CreateSecurity グループ")  

## <a name="assigning-the-integration-system-user-to-the-security-group"></a>セキュリティ グループへの統合システム ユーザーの割り当て
1. 検索ボックスに「セキュリティ グループの編集」と入力し、**[Edit Security Group (セキュリティ グループの編集)]** リンクをクリックします。     
   
   ![セキュリティ グループの編集](./media/active-directory-saas-inbound-synchronization-tutorial/IC750983.png "セキュリティ グループの編集")  
2. 新しい統合セキュリティ グループを名前で検索し、選択します。    
   
   ![セキュリティ グループの編集](./media/active-directory-saas-inbound-synchronization-tutorial/IC750984.png "セキュリティ グループの編集")  
3. 新しいセキュリティ グループに新しい統合システム ユーザーを追加します。       
   
   ![システム セキュリティ グループ](./media/active-directory-saas-inbound-synchronization-tutorial/IC750985.png "システム セキュリティ グループ")  

## <a name="configuring-security-group-options"></a>セキュリティ グループ オプションの構成
この手順では、新しいセキュリティ グループに対して、次のドメイン セキュリティ ポリシーによってセキュリティ保護されるオブジェクトへの Get および Put 操作の権限を付与します。  

* 外部アカウントのプロビジョニング  
* Worker Data: Public Worker Reports  
* Worker Data: All Positions  
* Worker Data: Current Staffing Information  
* Worker Data: Business Title on Worker Profile  

&nbsp;  

1. 検索ボックスにドメインのセキュリティ ポリシーを入力し、[機能領域のドメイン セキュリティ ポリシー] リンクをクリックします。     
   
   ![ドメイン セキュリティ ポリシー](./media/active-directory-saas-inbound-synchronization-tutorial/IC750986.png "ドメイン セキュリティ ポリシー")  
2. システムを検索し、システムの機能領域を選択します。  [OK] ボタンをクリックします。     
   
   ![ドメイン セキュリティ ポリシー](./media/active-directory-saas-inbound-synchronization-tutorial/IC750987.png "ドメイン セキュリティ ポリシー")  
3. [システム] 機能領域のセキュリティ ポリシーの一覧で、[Security Administration] を展開し、ドメイン セキュリティ ポリシーの [外部アカウントのプロビジョニング] を選択します。     
   
   ![ドメイン セキュリティ ポリシー](./media/active-directory-saas-inbound-synchronization-tutorial/IC750988.png "ドメイン セキュリティ ポリシー")  
4. [権限の編集] ボタンをクリックし、次に [権限の編集] 画面で、Get と Put の統合アクセス権限を持つセキュリティ グループの一覧に新しいセキュリティ グループを追加します。     
   
   ![アクセス許可の編集](./media/active-directory-saas-inbound-synchronization-tutorial/IC750989.png "アクセス許可の編集")  
5. 上記の手順 1. を繰り返して、機能領域を選択する画面に戻ります。今回はスタッフを検索して、[スタッフ] 機能領域を選択し、[OK] ボタンをクリックします。    
   
   ![ドメイン セキュリティ ポリシー](./media/active-directory-saas-inbound-synchronization-tutorial/IC750990.png "ドメイン セキュリティ ポリシー")  
6. スタッフ機能領域のセキュリティ ポリシーの一覧で、[ Worker Data: Staffing] を展開し、残りの各セキュリティ ポリシーに対して上記の手順 4. を繰り返します。    
   
   * Worker Data: Public Worker Reports  
   * Worker Data: All Positions  
   * Worker Data: Current Staffing Information  
   * Worker Data: Business Title on Worker Profile    
   
   ![ドメイン セキュリティ ポリシー](./media/active-directory-saas-inbound-synchronization-tutorial/IC750991.png "ドメイン セキュリティ ポリシー")  

## <a name="activating-security-policy-changes"></a>セキュリティ ポリシーの変更のアクティブ化
1. 検索ボックスに「アクティブ化」と入力し、[保留中のセキュリティ ポリシーの変更をアクティブ化] をクリックします。    
   
   ![アクティブ化](./media/active-directory-saas-inbound-synchronization-tutorial/IC750992.png "アクティブ化")  
2. 監査用のコメントを入力し、[OK] ボタンをクリックして、[保留中のセキュリティ ポリシーの変更のアクティブ化] 作業を開始します。      
   
   ![保留中のセキュリティのアクティブ化](./media/active-directory-saas-inbound-synchronization-tutorial/IC750993.png "保留中のセキュリティのアクティブ化")  
3. [確認] チェックボックスをオンにして、[OK] ボタンをクリックし、次の画面で作業を完了します。     
   
   ![保留中のセキュリティのアクティブ化](./media/active-directory-saas-inbound-synchronization-tutorial/IC750994.png "保留中のセキュリティのアクティブ化")  

## <a name="configuring-user-import-in-microsoft-azure-ad"></a>Microsoft Azure AD でのユーザー インポートの構成
このセクションでは、Microsoft Azure AD で Workday からユーザーをインポートする方法を概説します。    

### <a name="to-configure-user-import-in-microsoft-azure-ad-perform-the-following-steps"></a>Microsoft Azure AD でユーザーのインポートを構成するには、次の手順を実行します。
1. **Workday** アプリケーション統合ページで、**[ユーザー インポートの構成]** をクリックして、**[プロビジョニングの構成]** ダイアログを開きます。    
2. **[設定と管理者資格情報]** ページで次の手順を実行し、[次へ] をクリックします。    
   
   ![設定と管理者の資格情報](./media/active-directory-saas-inbound-synchronization-tutorial/IC750995.png "設定と管理者の資格情報")    
   
 * **[WORKDAY 管理ユーザー名]** テキストボックスに、「 [統合システム ユーザーの作成](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) 」セクションで作成したユーザーの名前を入力します。    
 * **[WORKDAY 管理パスワード]** テキストボックスに、「 [統合システム ユーザーの作成](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) 」セクションで作成したユーザーのパスワードを入力します。    
 * **[WORKDAY テナント URL]** テキストボックスに、URL または Workday テナントを入力します。    
3. **[接続テスト]** ページで、**[テスト開始]** をクリックして接続を確認し、**[次へ]** をクリックします。    
   
   ![接続テスト](./media/active-directory-saas-inbound-synchronization-tutorial/IC750996.png "接続テスト")  
4. **[プロビジョニング オプション]** ページで、**[次へ]** をクリックします。    
   
   ![プロビジョニング オプション](./media/active-directory-saas-inbound-synchronization-tutorial/IC750997.png "プロビジョニング オプション")  
5. **[プロビジョニングの開始]** ダイアログで、**[完了]** をクリックします。    
   
   ![プロビジョニングの開始](./media/active-directory-saas-inbound-synchronization-tutorial/IC750998.png "プロビジョニングの開始")  

これで、 **[ユーザー]** セクションに移動して、Workday ユーザーがインポートされたかどうかを確認できます。    



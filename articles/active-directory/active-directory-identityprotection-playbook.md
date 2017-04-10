---
title: "Azure Active Directory Identity Protection プレイブック | Microsoft Docs"
description: "Azure AD Identity Protection を使用して、侵害された ID またはデバイスを攻撃者が悪用する能力を制限する方法、および以前に疑われた、または侵害を確認された ID またはデバイスを保護する方法について説明します。"
services: active-directory
keywords: "Azure Active Directory Identity Protection, Cloud App Discovery, アプリケーションの管理, セキュリティ, リスク, リスク レベル, 脆弱性, セキュリティ ポリシー"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 77031b3bbe2372bf2cac34bac45368ac40911641
ms.openlocfilehash: 87070d7680a6d48e94099b47f9dc336a75332f6c


---
# <a name="azure-active-directory-identity-protection-playbook"></a>Azure Active Directory Identity Protection プレイブック
このプレイブックは次の操作で役立ちます。

* リスク イベントと脆弱性をシミュレートし、ID 保護環境にデータを入力する
* リスクに基づく条件付きアクセス ポリシーを設定し、そのポリシーの影響をテストする

## <a name="simulating-risk-events"></a>リスク イベントのシミュレーション
このセクションでは、次の種類のリスク イベントをシミュレートするための手順を提供します。

* 匿名の IP アドレスからのサインイン (簡単)
* 未知の場所からのサインイン (普通)
* 特殊な場所へのあり得ない移動 (困難)

他のリスク イベントは、安全な方法でシミュレートすることはできません。

### <a name="sign-ins-from-anonymous-ip-addresses"></a>匿名の IP アドレスからのサインイン
このリスク イベントの種類は、匿名プロキシ IP アドレスとして識別されている IP アドレスからのサインインにユーザーが成功したことを示します。 このようなプロキシは、自分のデバイスの IP アドレスを隠したいユーザーによって使用され、悪意のある目的で使用される場合があります。

**匿名 IP からサインインをシミュレートするには、次の手順を実行します**。

1. [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en)をダウンロードします。
2. Tor Browser を使用し、 [https://myapps.microsoft.com](https://myapps.microsoft.com)に移動します。   
3. **匿名 IP アドレスからのサインイン** レポートに表示するアカウントの資格情報を入力します。

5 分以内に Identity Protection ダッシュボードにサインインが表示されます。 

### <a name="sign-ins-from-unfamiliar-locations"></a>未知の場所からのサインイン
未知の場所のリスクはリアルタイム サインイン評価メカニズムであり、過去のサインインの場所 (IP、緯度/経度、ASN) を考慮して、新規/未知の場所を決定します。 システムは、ユーザーの以前の IP、緯度/経度、および ASN を保存し、それを "既知の" 場所と見なします。 サインインの場所が既存の場所のいずれとも一致しない場合、そのサインインの場所は未知と見なされます。

Azure Active Directory Identity Protection:  

* 14 日間の初期学習期間があり、この間はどの新しい場所にも未知の場所としてのフラグは設定されません。
* 既知のデバイスからのサインイン、および既存の場所と地理的に近い場所からのサインインも無視します。

未知の場所をシミュレートするには、それ以前にアカウントがサインインしていない場所とデバイスからサインインする必要があります。 

**未知の場所からサインインをシミュレートするには、次の手順を実行します**。

1. 少なくとも 14 日間のサインイン履歴のあるアカウントを選択します。 
2. 次のどちらかの操作を行います。
   
   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 VPN を使用している状態で、[https://myapps.microsoft.com](https://myapps.microsoft.com) に移動し、リスク イベントをシミュレートするアカウントの資格情報を入力します。
   
   b. 別の場所にいる知り合いに、アカウントの資格情報を使用してサインインするよう頼みます (推奨されません)。

5 分以内に Identity Protection ダッシュボードにサインインが表示されます。

### <a name="impossible-travel-to-atypical-location"></a>特殊な場所へのあり得ない移動
アルゴリズムでは、機械学習を使用して、既知のデバイスからのあり得ない移動や、ディレクトリ内の他のユーザーによって使用される VPN からのサインインなどの誤検知が除去されるため、あり得ない移動の状態をシミュレートすることは困難です。 さらに、ユーザーには、リスク イベントの生成を開始する前に、3 ～ 14 日間のサインイン履歴が必要です。

**特殊な場所へのあり得ない移動をシミュレートするには、次の手順を実行します**。

1. 標準的なブラウザーを使用して、 [https://myapps.microsoft.com](https://myapps.microsoft.com)に移動します。  
2. あり得ない移動のリスク イベントを生成するアカウントの資格情報を入力します。
3. ユーザー エージェントを変更します。 ユーザー エージェントの変更は、Internet Explorer の開発者ツールから、あるいは Firefox または Chrome のユーザー エージェント切り替えアドオンを使用して、行うことができます。
4. IP アドレスを変更します。 IP アドレスの変更は、VPN または Tor アドオンを使用して、または別のデータセンターの Azure で新しいマシンを起動して、行うことができます。
5. 前と同じ資格情報を使用し、前のサインインから数分以内に、 [https://myapps.microsoft.com](https://myapps.microsoft.com) にサインインします。

2 ～ 4 時間以内に Identity Protection ダッシュボードにサインインが表示されます。<br>
複雑な機械学習モデルが含まれるため、取得されない可能性があります。<br> 複数の Azure AD アカウントでこの手順を繰り返すのがよい方法です。

## <a name="simulating-vulnerabilities"></a>脆弱性のシミュレーション
脆弱性は、悪意のあるユーザーによって悪用される可能性のある Azure AD 環境内の弱点です。 現在、Azure AD Identity Protection では、Azure AD の他の機能を利用する 3 種類の脆弱性が明らかになっています。 これらの脆弱性は、以下の機能がセットアップされると Identity Protection ダッシュボードに自動的に表示されます。

* Azure AD [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)
* Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md) 

## <a name="user-compromise-risk"></a>ユーザーの侵害リスク
**ユーザーの侵害リスクをテストするには、次の手順を実行します**。

1. テナントのグローバル管理者の資格情報を使用して [https://portal.azure.com](https://portal.azure.com) にサインインします。
2. **Identity Protection**に移動します。 
3. **Azure AD Identity Protection** のメイン ブレードで、**[設定]** をクリックします。 
4. **[ポータルの設定]** ブレードの **[セキュリティ規則]** で、**[ユーザーの侵害リスク]** をクリックします。 
5. **[サインインのリスク]** ブレードで、**[ルールの有効化]** をオフにして、**[保存]** をクリックします。
6. 特定のユーザー アカウントで、未知の場所または匿名 IP のリスク イベントをシミュレートします。 これにより、そのユーザーのユーザー リスク レベルが **中**に上がります。
7. 数分待った後、ユーザーのユーザー レベルが **中**であることを確認します。
8. **[ポータルの設定]** ブレードに移動します。
9. **[ユーザーの侵害リスク]** ブレードで、**[ルールの有効化]** の **[オン]** を選択します。 
10. 次のいずれかのオプションを選択します。
    
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 ブロックするには、**[サインインのブロック]** で **[中]** を選択します。
    
    b. セキュリティで保護されたパスワードの変更を強制するには、**[多要素認証を要求する]** で **[中]** を選択します。
11. **[保存]**をクリックします。
12. リスク レベルを上げたユーザーを使用してサインインすることにより、リスクに基づく条件付きアクセスをテストできます。 ユーザーのリスクが中の場合、ポリシーの設定に応じて、サインインがブロックされるか、パスワードの変更を強制されます。 
    <br><br>
    ![プレイブック](./media/active-directory-identityprotection-playbook/201.png "Playbook")
    <br>

## <a name="sign-in-risk"></a>サインイン リスク
**サインイン リスクをテストするには、次の手順を実行します。**

1. テナントのグローバル管理者の資格情報を使用して [https://portal.azure.com ](https://portal.azure.com) にサインインします。
2. **Identity Protection**に移動します。
3. **Azure AD Identity Protection** のメイン ブレードで、**[設定]** をクリックします。 
4. **[ポータルの設定]** ブレードの **[セキュリティ規則]** で、**[サインインのリスク]** をクリックします。
5. **[サインインのリスク]** ブレードで、**[ルールの有効化]** の **[オン]** を選択します。 
6. 次のいずれかのオプションを選択します。
   
   a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 ブロックするには、**[サインインのブロック]** で **[中]** を選択します
   
   b. セキュリティで保護されたパスワードの変更を強制するには、**[多要素認証を要求する]** で **[中]** を選択します。
7. ブロックするには、[リスクが指定された設定値以上の場合、サインインをブロックする] で [中] を選択します。
8. 多要素認証を強制するには、**[多要素認証を要求する]** で **[中]** を選択します。
9. **[Save]**をクリックします。
10. 未知の場所または匿名 IP のリスク イベントをシミュレートすることにより、リスクに基づく条件付きアクセスをテストできます。どちらも**中**リスク イベントと見なされるためです。


![プレイブック](./media/active-directory-identityprotection-playbook/200.png "Playbook")


## <a name="see-also"></a>関連項目
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)




<!--HONumber=Dec16_HO5-->



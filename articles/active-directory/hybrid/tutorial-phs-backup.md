---
title: チュートリアル:Azure AD Connect で AD FS のバックアップとして PHS を設定する | Microsoft Docs
description: バックアップとして、および AD FS に対してパスワード ハッシュの同期を有効にする方法を示します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 01/30/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2281bc451a5acf9e4e634a124161a3e8b0734deb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58090510"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>チュートリアル:Azure AD Connect で AD FS のバックアップとして PHS を設定する

次のチュートリアルでは、AD FS のバックアップおよびフェールオーバーとしてパスワード ハッシュの同期を設定する方法について説明します。  このドキュメントでは、AD FS で障害が発生した場合や AD FS が利用できなくなった場合にパスワード ハッシュ同期をプライマリ認証方法として有効にする方法も紹介します。

## <a name="prerequisites"></a>前提条件
このチュートリアルは、「[チュートリアル: 単一の AD フォレスト環境をクラウドに統合する](tutorial-federation.md)」が基になっています。これは、このチュートリアルを行うための前提条件です。  このチュートリアルを完了していない場合は、このドキュメントの手順を実行する前に完了してください。

## <a name="enable-phs-in-azure-ad-connect"></a>Azure AD Connect で PHS を有効にする
フェデレーションを使用する Azure AD Connect 環境を準備した後の最初の手順は、Azure AD Connect がハッシュを同期できるようにパスワード ハッシュの同期をオンにすることです。

以下の手順を実行します。

1.  デスクトップに作成された Azure AD Connect アイコンをダブルクリックします
2.  **[構成]** をクリックします。
3.  [追加のタスク] ページで **[同期オプションのカスタマイズ]** を選択し、**[次へ]** をクリックします。
4.  グローバル管理者のユーザー名とパスワードを入力します。  このアカウントは、前のチュートリアルの[ここ](tutorial-federation.md#create-a-global-administrator-in-azure-ad)で作成しました。
5.  **[ディレクトリの接続]** 画面で、**[次へ]** をクリックします。
6.  **[ドメインと OU のフィルタリング]** 画面で、**[次へ]** をクリックします。
7.  **[オプション機能]** 画面で、**[パスワード ハッシュの同期]** チェック ボックスをオンにし、**[次へ]** をクリックします。
![Select](media/tutorial-phs-backup/backup1.png)</br>
8.  **[構成の準備完了]** 画面で、**[構成]** をクリックします。
9.  構成が完了したら、**[終了]** をクリックします。
10. これで完了です。  完了しました。  パスワード ハッシュの同期が行われるようになり、AD FS が使用できなくなった場合のバックアップとして使用できます。

## <a name="switch-to-password-hash-synchronization"></a>パスワード ハッシュの同期に切り替える
次に、パスワード ハッシュの同期に切り替える方法を紹介します。 作業を始める前に、どのような状況で切り替えるべきかを検討してください。 ネットワークの機能停止、軽微な AD FS の問題、ユーザーの一部に影響する問題など、一時的な理由で切り替えることは避けてください。 問題の解消に時間がかかりすぎることから切り替えに踏み切る場合は、次の手順に従います。

1. デスクトップに作成された Azure AD Connect アイコンをダブルクリックします
2.  **[構成]** をクリックします。
3.  **[ユーザー サインインの変更]** を選択し、**[次へ]** をクリックします。
![変更点](media/tutorial-phs-backup/backup2.png)</br>
4.  グローバル管理者のユーザー名とパスワードを入力します。  このアカウントは、前のチュートリアルの[ここ](tutorial-federation.md#create-a-global-administrator-in-azure-ad)で作成しました。
5.  **[ユーザー サインイン]** 画面で、**[パスワード ハッシュの同期]** を選択し、**[ユーザー アカウントを変換しない]** チェック ボックスをオンにします。  
6.  **[シングル サインオンを有効にする]** を選択状態 (既定値) のままにし、**[次へ]** をクリックします。
7.  **[シングル サインオンを有効にする]** 画面で、**[次へ]** をクリックします。
8.  **[構成の準備完了]** 画面で、**[構成]** をクリックします。
9.  構成が完了したら、**[終了]** をクリックします。
10. これで、ユーザーが自分のパスワードを使用して Azure および Azure サービスにサインインできるようになりました。

## <a name="test-signing-in-with-one-of-our-users"></a>いずれかのユーザーでサインインをテストする

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) に移動します。
2. 新しいテナントで作成されたユーザー アカウントを使用してサインインします。  user@domain.onmicrosoft.com の形式を使用してサインインする必要があります。 ユーザーがオンプレミスでのサインインに使用するのと同じパスワードを使用します。</br>
   ![確認](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="next-steps"></a>次の手順


- [ハードウェアおよび前提条件](how-to-connect-install-prerequisites.md) 
- [簡単設定](how-to-connect-install-express.md)
- [パスワード ハッシュの同期](how-to-connect-password-hash-synchronization.md)|

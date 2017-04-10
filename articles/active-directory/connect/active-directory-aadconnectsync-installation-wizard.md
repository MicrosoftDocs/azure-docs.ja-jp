---
title: "Azure AD Connect インストール ウィザードの再実行 | Microsoft Docs"
description: "2 回目のインストール ウィザードの実行時の動作について説明します。"
keywords: "Azure AD Connect インストール ウィザードの 2 回目の実行では、メンテナンス設定を構成できます。"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: f18e11ac7482b78925d1885ceb20696146603ad2
ms.lasthandoff: 03/28/2017


---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect 同期: インストール ウィザードの 2 回目の実行
初めて Azure AD Connect のインストール ウィザードを実行すると、インストールの構成方法が手順に沿って表示されます。 インストール ウィザードをもう一度実行すると、メンテナンスのオプションが表示されます。

インストール ウィザードは、[スタート] メニューに **[Azure AD Connect]**という名前で表示されます。

![[スタート] メニュー](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

インストール ウィザードを開始すると、以下のオプションがあるページが表示されます。

![その他のタスクの一覧を含むページ](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Azure AD Connect を使用して ADFS をインストールした場合は、これら以外のオプションも表示されます。 ADFS の追加オプションについては、「 [ADFS management (ADFS 管理)](active-directory-aadconnect-federation-management.md#manage-ad-fs)」を参照してください。

いずれかのタスクを選択し、 **[次へ]** をクリックして続行します。

> [!IMPORTANT]
> インストール ウィザードが開いている間は、同期エンジンでのすべての操作が中断されます。 構成の変更を完了したら、すぐにインストール ウィザードを閉じてください。
>
>

## <a name="view-current-configuration"></a>現在の構成を表示する
このオプションでは、現在構成されているオプションの簡易ビューを表示します。

![すべてのオプションとそれらの状態の一覧を含むページ](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

戻るには、 **[前へ]** をクリックします。 **[終了]**を選択すると、インストール ウィザードが閉じます。

## <a name="customize-synchronization-options"></a>同期オプションをカスタマイズする
同期の構成を変更するには、このオプションを使用します。 カスタム構成のインストール パスからオプションのサブセットが表示されます。 このオプションは、最初に高速インストールを使用した場合でも表示されます。

* [ディレクトリを追加する](active-directory-aadconnect-get-started-custom.md#connect-your-directories)。 ディレクトリの削除については、 [コネクタの削除](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete)についての説明を参照してください。
* [ドメインと OU のフィルター処理を変更する](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering)。
* グループのフィルター処理を削除する。
* [オプションの機能を変更する](active-directory-aadconnect-get-started-custom.md#optional-features)。

最初のインストール時のその他のオプションは変更することができないため、使用できません。 オプションは次のとおりです。

* UserPrincipalName と sourceAnchor として使用する属性を変更する。
* 別のフォレストのオブジェクトに使用する結合方法を変更する。
* グループ ベースのフィルター処理を有効にする。

## <a name="refresh-directory-schema"></a>ディレクトリ スキーマの更新
このオプションは、オンプレミス AD DS フォレストのいずれかでスキーマを変更した場合に使用します。 たとえば、Exchange をインストールした場合や、デバイス オブジェクトを持つ Windows Server 2012 スキーマにアップグレードしたような場合です。 そのような場合は、AD DS からもう一度スキーマを読み取ってキャッシュを更新するよう、Azure AD Connect に指示する必要があります。 この操作により、同期規則も再生成されます。 例として、Exchange のスキーマを追加した場合は、Exchange の同期規則が構成に追加されます。

このオプションを選択すると、構成に含まれるすべてのディレクトリの一覧が表示されます。 既定の設定を保持し、すべてのフォレストを更新するか、一部のフォレストの選択を解除することができます。

![環境内のすべてのディレクトリの一覧を含むページ](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>ステージング モードの構成
このオプションを使用すると、サーバー上のステージング モードを有効または無効にすることができます。 ステージング モードとその使用方法の詳細については、「 [操作](active-directory-aadconnectsync-operations.md#staging-mode)」を参照してください。

このオプションでは、ステージングが現時点で有効になっているか無効になっているかが表示されます。  
![Option that is also showing the current state of staging mode](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

状態を変更するには、このオプションを選択し、チェック ボックスをオンまたはオフにします。  
![Option that is also showing the current state of staging mode](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>ユーザー サインインの変更
このオプションでは、パスワード同期からフェデレーションに、またはその逆に変更することができます。 **[構成しない]**に変更することはできません。

このオプションの詳細については、「 [ユーザーのサインイン](active-directory-aadconnect-user-signin.md#changing-the-user-sign-in-method)」を参照してください。

## <a name="next-steps"></a>次のステップ
* Azure AD Connect Sync の同期で使用される構成モデルについて詳しくは、「 [Understanding Declarative Provisioning (宣言型のプロビジョニングについて)](active-directory-aadconnectsync-understanding-declarative-provisioning.md)」をご覧ください。

**概要トピック**

* [Azure AD Connect sync: 同期を理解してカスタマイズする](active-directory-aadconnectsync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)


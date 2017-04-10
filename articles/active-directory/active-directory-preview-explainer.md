---
title: "Azure Active Directory プレビューの説明 | Microsoft Docs"
description: "このトピックでは、クラシック ポータルの Azure Active Directory と Azure Portal の Azure Active Directory の違いについて説明します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e717c11c-e7c9-4565-8e47-1950905e5b3c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 3b31bd036d9c3ff8036b314b93cbddd94874ff63
ms.openlocfilehash: 8f7a679dc5b5726107503a9f7363ab162b1770d0


---
# <a name="preview-of-the-azure-active-directory-management-experience-in-the-azure-portal"></a>Azure Portal での Azure Active Directory のプレビュー版の管理サービス
Azure Active Directory (Azure AD) 管理サービスが、Azure Portal でプレビュー段階になりました。 この機能を試すには、ディレクトリのグローバル管理者として [Azure Portal](https://portal.azure.com) にサインインします。 表示されている場合は、サービスの一覧で Azure Active Directory を選択します。一覧にこのサービスが表示されていない場合は、**[その他のサービス]** を選択してすべてのサービスの一覧を表示します。 Azure Portal で Azure AD の管理サービスを試すのに Azure サブスクリプションは必要ありません。

## <a name="learn-about-what-you-can-do-in-the-preview-experience"></a>プレビュー版で使用可能な操作について説明します。
プレビュー版では、ユーザー、グループ、アプリケーション、ディレクトリ設定など、多くのディレクトリ リソースを Azure Portal で管理することができます。 現在、こちらのサービスに [Azure クラシック ポータル](https://manage.windowsazure.com)の Azure AD 管理サービスで使用可能なすべての機能を含めるように改善中です。 この改善が完了するまでは、一部のディレクトリ管理タスクはクラシック ポータルで完了する必要があります。

## <a name="manage-the-same-azure-ad-tenants"></a>同じ Azure AD テナントを管理する
プレビュー版では、クラシック ポータルや Office 365 管理センターと同じ Azure Active Directory テナントの読み取りおよび書き込みを行います。 これらのポータルのいずれかで変更を行うと、ほかのすべてのポータルに反映されます。

## <a name="use-the-same-authorization-logic"></a>同じ承認ロジックを使用する
プレビュー サービスでは、既存の Active Directory クライアントと同じ承認ロジックを使用しています。 ユーザーには、グローバル管理者、ユーザー管理者、パスワード管理者などのディレクトリ ロールに基づいてディレクトリ リソースの変更が許可されます。 ユーザーが Azure リソースのロールや Azure サブスクリプションを持っていても、ディレクトリ リソースの管理は許可されません。 Azure AD の管理ロールの詳細については、「[Azure Active Directory の管理者ロールの割り当て](active-directory-assign-admin-roles.md)」を参照してください。

プレビュー サービスは、グローバル管理者用に最適化されています。 グローバル管理者ではないユーザーとしてサインインしプレビュー サービスを使用する場合、機能が低下する場合があります。 たとえば、あるボタンをクリックした場合に、ディレクトリ内では完了できないタスクが開始される可能性があります。 この点についてはまもなく改善される予定です。

## <a name="next-steps"></a>次のステップ
プレビュー サービスに関するフィードバックを、 [Azure AD フィードバック フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&filter=alltypes&sort=lastpostdesc)の管理ポータル セクションでお寄せください。



<!--HONumber=Feb17_HO3-->



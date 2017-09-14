---
title: "Azure Active Directory アクティビティ ログ コンテンツ パックのエラーのトラブルシューティングを行う | Microsoft Docs"
description: "Azure Active Directory アクティビティ ログ コンテンツ パックのエラー メッセージの一覧とエラーを修正するための手順を示します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: a47810d89fb3db839d1d59942ee90610afaeb6a8
ms.contentlocale: ja-jp
ms.lasthandoff: 06/17/2017

---

# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Azure Active Directory アクティビティ ログ コンテンツ パックのエラーのトラブルシューティングを行う 


Power BI Content Pack for Azure Active Directory (プレビュー) を操作しているときに、次のエラーが発生する可能性があります。 

- [更新が失敗しました](active-directory-reporting-troubleshoot-content-pack.md#refresh-failed) 
- [データ ソースの資格情報を更新できませんでした](active-directory-reporting-troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [データのインポートに時間がかかっています](active-directory-reporting-troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 
 
このトピックでは、考えられる原因とこれらのエラーを修正する方法について説明します。
 
## <a name="refresh-failed"></a>更新が失敗しました 
 
**このエラーがどのように表面化するか**: Power BI からの電子メールまたは更新履歴の失敗状態。 


| 原因 | 修正方法 |
| ---   | ---        |
| 更新失敗エラーは、コンテンツ パックに接続するユーザーの資格情報がリセットされたが、コンテンツ パックの接続設定が更新されていないときに発生することがあります。 | Power BI で、Azure Active Directory アクティビティ ログ ダッシュボード (Azure Active Directory のアクティビティ ログ) に対応するデータセットを検索し、[更新のスケジュール設定] を選択し、Azure AD 資格情報を入力します。 |
| 更新は、基になるコンテンツ パックのデータの問題が原因で失敗することがあります。 | サポート チケットを提出します。 詳細については、「[Azure Active Directory のサポートを得る方法](active-directory-troubleshooting-support-howto.md)」を参照してください。|
 
 
## <a name="failed-to-update-data-source-credentials"></a>データ ソースの資格情報を更新できませんでした 
 
**このエラーがどのように表面化するか**: Power BI で、Azure Active Directory アクティビティ ログ (プレビュー) コンテンツ パックに接続するとき。 

| 原因 | 修正方法 |
| ---   | ---        |
| 接続するユーザーが、グローバル管理者、セキュリティ閲覧者、またはセキュリティ管理者のいずれでもありません。 | コンテンツ パックにアクセスするには、グローバル管理者、セキュリティ閲覧者、またはセキュリティ管理者のいずれかのアカウントを使用します。 |
| テナントが Premium テナントでないか、Premium ライセンス ファイルに少なくとも 1 人のユーザーが登録されていません。 | サポート チケットを提出します。 詳細については、「[Azure Active Directory のサポートを得る方法](active-directory-troubleshooting-support-howto.md)」を参照してください。|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>データのインポートに時間がかかっています 
 
**このエラーがどのように表面化するか**: Power BI でコンテンツ パックに接続すると、Azure Active Directory アクティビティ ログ用のダッシュボードを準備するためのデータ インポート処理が開始されます。 *[データのインポート中...]* というメッセージが表示されます。  

| 原因 | 修正方法 |
| ---   | ---        |
| テナントのサイズに応じて、この手順は数分から 30 分かかることがあります。 | しばらくお待ちください。 1 時間以内にメッセージがダッシュボードの表示に変わらない場合は、サポート チケットを提出してください。 詳細については、「[Azure Active Directory のサポートを得る方法](active-directory-troubleshooting-support-howto.md)」を参照してください。|

## <a name="next-steps"></a>次のステップ

Power BI Content Pack for Azure Active Directory (プレビュー) をインストールするには、[こちら](https://powerbi.microsoft.com/en-us/blog/azure-active-directory-meets-power-bi/)をクリックします。




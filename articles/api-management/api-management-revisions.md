---
title: Azure API Management のリビジョン | Microsoft Docs
description: Azure API Management のリビジョンの概念について説明します。
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 9a20a7966daff372cf5c0abc9b7b1dbbfd459838
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91403240"
---
# <a name="revisions-in-azure-api-management"></a>Azure API Management のリビジョン

リビジョンを使用すると、制御された安全な方法で、API に変更を加えることができます。 変更を加える場合は、新しいリビジョンを作成します。 そうすれば、API のコンシューマーに影響を及ぼすことなく、API を編集してテストすることができます。 準備ができたら、そのリビジョンを現在のリビジョンにします。 それと同時に、必要に応じて変更ログにエントリを投稿しておくことで、API のコンシューマーが最新の変更内容を把握し続けることができます。 変更ログは、お使いの開発者ポータルに発行されます。

> [!NOTE]
> 開発者ポータルは、従量課金レベルではご利用いただけません。

リビジョンにより、次のことができます。

- 実稼働 API に影響を及ぼすことなく、API の定義とポリシーを安全に変更する。
- 変更内容を発行する前に試す。
- 開発者が最新の内容を把握できるように、行った変更を文書化する。
- 問題が見つかった場合にロールバックする。

[このチュートリアルに従って、リビジョンの使用を開始してください。](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>特定のリビジョンにアクセスする

API の各リビジョンには、特別な形式の URL を使用してアクセスできます。 クエリ文字列の前ではなく、対象の API の URL の末尾に `;rev={revisionNumber}` を追加することで、その API の特定のリビジョンにアクセスします。 たとえば、次の URL を使用すると、`customers` API のリビジョン 3 にアクセスできます。

`https://apis.contoso.com/customers;rev=3?customerId=123`

既定では、各リビジョンのセキュリティ設定は現在のリビジョンのものと同じです。 リビジョンごとに異なるセキュリティを適用したい場合、特定のリビジョンのポリシーを意図的に変更することができます。 たとえば、[IP フィルタリング ポリシー](./api-management-access-restriction-policies.md#RestrictCallerIPs)を追加して、外部の呼び出し元による開発中のリビジョンへのアクセスを防ぐことができます。

リビジョンはオフラインにすることができます。これにより、呼び出し元は、URL を介してリビジョンにアクセスしようとしてもできなくなります。 Azure portal を使用して、リビジョンをオフラインとしてマークできます。 PowerShell を使用する場合は、`Set-AzApiManagementApiRevision` コマンドレットを使用して、`Path` 引数を `$null` に設定できます。

> [!NOTE]
> リビジョンをオフラインにするのは、それらをテストに使用していないときをお勧めします。

## <a name="current-revision"></a>現在のリビジョン

特定のリビジョンを "*現在の*" リビジョンとして設定できます。 このリビジョンは、URL に明示的なリビジョン番号が指定されていないすべての API 要求で使用されます。 以前のリビジョンを現在のものとして設定することで、そのリビジョンにロールバックできます。

Azure portal を使用して、リビジョンを現在のリビジョンとして設定できます。 PowerShell を使用する場合は、`New-AzApiManagementApiRelease` コマンドレットを使用できます。

## <a name="revision-descriptions"></a>リビジョンの説明

リビジョンを作成するときに、独自の追跡の目的で、説明を設定できます。 API ユーザーが説明を目にすることはありません。

リビジョンを現在のリビジョンとして設定する際に、必要に応じて、パブリックな変更ログのメモを指定することもできます。 変更ログは、API ユーザーが確認できるように、開発者ポータルに含まれています。 `Update-AzApiManagementApiRelease` PowerShell コマンドレットを使用して、変更ログのメモを変更できます。

## <a name="versions-and-revisions"></a>バージョンとリビジョン

バージョンとリビジョンは別個の機能です。 各バージョンは、バージョン管理されていない API と同じように、複数のリビジョンを持つことができます。 リビジョンは、バージョンを使用せずに使用することも、その他の方法で使用することもできます。 通常、バージョンは、破壊的変更のある API のバージョンを区別するために使用される一方、リビジョンは、API に軽微な変更および非破壊的変更を加えるために使用されます。

リビジョンに破壊的変更があることが判明した場合、またはリビジョンを正式にベータ バージョンまたはテスト バージョンに変更する場合は、リビジョンからバージョンを作成することができます。 Azure portal を使用して、[リビジョン] タブのリビジョン コンテキスト メニューで [Create Version from Revision]\(リビジョンからバージョンを作成\) をクリックします。

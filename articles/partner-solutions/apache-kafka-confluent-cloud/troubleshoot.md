---
title: Apache Kafka for Confluent Cloud のトラブルシューティング - Azure パートナー ソリューション
description: この記事では、Confluent Cloud on Azure に関するトラブルシューティングとよく寄せられる質問 (FAQ) について説明します。
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/18/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: b1e4b06fcbecf11d7d5f58a583fe3bd6643d99ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709396"
---
# <a name="troubleshooting-apache-kafka-for-confluent-cloud-solutions"></a>Apache Kafka for Confluent Cloud ソリューションのトラブルシューティング

このドキュメントには、Apache Kafka for Confluent Cloud を使用するソリューションのトラブルシューティングに関する情報が含まれています。

回答が見つからない場合、または問題を解決できない場合は、[Azure portal を通じてリクエストを作成する](manage.md#get-support)か、[Confluent サポート](https://support.confluent.io)にお問い合わせください。

## <a name="cant-find-offer-in-the-marketplace"></a>Marketplace でオファーが見つからない

Azure Marketplace でオファーを見つけるには、次の手順を使用します。

1. [Azure Portal](https://portal.azure.com) で、 **[リソースの作成]** を選択します。
1. _Apache Kafka on Confluent Cloud_ を検索します。
1. アプリケーション タイルを選択します。

オファーが表示されない場合は、[Confluent サポート](https://support.confluent.io)にお問い合わせください。 Azure Active Directory テナント ID は、許可されているテナントの一覧にある必要があります。 テナント ID を見つける方法については、「[Azure Active Directory のテナント ID を見つける方法](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)」を参照してください。

## <a name="purchase-errors"></a>購入エラー

* 購入に失敗するのは、有効なクレジット カードが Azure サブスクリプションに接続されていないか、支払い方法がサブスクリプションに関連付けられていないためです。

  別の Azure サブスクリプションを使用してください。 または、サブスクリプションのクレジット カードまたは支払い方法を追加するか、更新してください。 詳細については、[クレジットと支払い方法の更新](../../cost-management-billing/manage/change-credit-card.md)に関するページを参照してください。

* この EA サブスクリプションでは、Marketplace での購入は許可されていません。

  別のサブスクリプションを使用してください。 または、お使いの EA サブスクリプションで Marketplace の購入が有効かどうかを確認してください。 詳細については、[Marketplace での購入を有効にする](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)方法に関する記事を参照してください。 これらの方法で問題が解決しない場合は、[Confluent のサポート](https://support.confluent.io)にお問い合わせください。

## <a name="conflict-error"></a>競合のエラー

以前に Confluent Cloud に登録していた場合は、新しいメール アドレスを使用して、別の Confluent Cloud 組織リソースを作成する必要があります。 以前に登録されたメール アドレスを使用すると、**競合** エラーが発生します。 しかし今回は新しいメール アドレスで再登録します。

## <a name="deploymentfailed-error"></a>DeploymentFailed エラー

**DeploymentFailed** エラーが発生した場合は、Azure サブスクリプションの状態を確認します。 中断されておらず、課金に関する問題がないことを確認してください。

## <a name="resource-isnt-displayed"></a>リソースが表示されない

ポータルに Confluent Cloud の **[概要]** や **[削除]** ブレードが表示されていない場合は、ページを更新してみてください。 このエラーは、ポータルに関する断続的な問題である可能性があります。 それでもうまくいかない場合は、[Confluent サポート](https://support.confluent.io)にお問い合わせください。

Azure の **[すべてのリソース]** 一覧に Confluent Cloud リソースが見つからない場合は、[Confluent サポート](https://support.confluent.io)にお問い合わせください。

## <a name="resource-creation-takes-long-time"></a>リソースの作成に時間がかかる

デプロイ プロセスの完了にかかる時間が 3 時間を超える場合は、サポートにお問い合わせください。

デプロイが失敗し、Confluent Cloud リソースの状態が `Failed` の場合は、リソースを削除します。 削除した後、もう一度リソースを作成してみてください。

## <a name="offer-plan-doesnt-load"></a>オファー プランが読み込まれない

このエラーは、Azure portal に関する断続的な問題である可能性があります。 もう一度オファーをデプロイしてみてください。

## <a name="unable-to-delete"></a>ハンドルが開いているため、

Confluent リソースを削除できない場合は、リソースを削除するためのアクセス許可があることを確認してください。 Microsoft.Confluent/*/Delete アクションの実行が許可されている必要があります。 アクセス許可の表示については、「[Azure portal を使用して Azure でのロールの割り当てを一覧表示する](../../role-based-access-control/role-assignments-list-portal.md)」を参照してください。

適切なアクセス許可があり、それでもリソースを削除できない場合は、[Confluent サポート](https://support.confluent.io)にお問い合わせください。 この状態は、Confluent の保持ポリシーに関連している可能性があります。 Confluent サポートでお客様の組織とメール アドレスを削除できます。

## <a name="unable-to-use-single-sign-on"></a>シングル サインオンを使用できない

Confluent Cloud SaaS ポータルで SSO が機能していない場合は、正しい Azure Active Directory 電子メールを使用していることを確認してください。 また、Confluent Cloud のサービスとしてのソフトウェア (SaaS) ポータルへのアクセスの許可に同意する必要もあります。 詳細については、[シングル サインオンのガイダンス](manage.md#single-sign-on)に関するページを参照してください。

問題が解決しない場合は、[Confluent サポート](https://support.confluent.io)にお問い合わせください。

## <a name="next-steps"></a>次の手順

Apache Kafka for Confluent Cloud の[インスタンスの管理](manage.md)について学習します。

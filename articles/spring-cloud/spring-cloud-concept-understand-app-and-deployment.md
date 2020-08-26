---
title: Azure Spring Cloud でのアプリとデプロイについて
description: Azure Spring Cloud でのアプリとデプロイの違い。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 81e1925810f374da6f02bf6c3a013b00b5bb9a2c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263850"
---
# <a name="understand-app-and-deployment-in-azure-spring-cloud"></a>Azure Spring Cloud でのアプリとデプロイについて

**アプリ**と**デプロイ**は、Azure Spring Cloud のリソース モデルにおける 2 つの重要な概念です。 Azure Spring Cloud では、*アプリ*は、1 つのビジネス アプリまたは 1 つのマイクロサービスを抽象化したものです。  *アプリ*としてデプロイされたコードまたはバイナリの 1 つのバージョンが*デプロイ*で実行されます。

 ![アプリとデプロイ](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

Azure Spring Cloud の Standard レベルでは、1 つのアプリに 1 つの運用環境デプロイと 1 つのステージング デプロイが割り当てられ、簡単にブルー/グリーン デプロイを行うことができます。

## <a name="app"></a>アプリ
以下の機能とプロパティは、アプリ レベルで定義されています。

| 列挙型 | 定義 |
|:--|:----------------|
| パブリック</br>エンドポイント | アプリにアクセスする URL |
| Custom</br>Domain | カスタム ドメインをセキュリティで保護する CNAME レコード |
| サービス</br>バインド | function.json ファイルと *ServiceBusTrigger* 属性で設定されたバインド構成 |
| マネージド</br>ID | アプリで Azure Active Directory によるマネージド ID を使用すると、他の Azure AD で保護されたリソース (Azure Key Vault など) に簡単にアクセスできます |
| 永続的</br>ストレージ | アプリの再起動後もデータを保持できるようにする設定 |

## <a name="deployment"></a>デプロイ

以下の機能とプロパティはデプロイ レベルで定義され、運用環境デプロイとステージング デプロイのスワップ時に交換されます。

| 列挙型 | 定義 |
|:--|:----------------|
| CPU | アプリ インスタンスあたりの仮想コアの数 |
| メモリ | デプロイのスケールアップまたはスケールアウトのためにメモリを割り当てる設定 |
| インスタンス</br>Count | 手動または自動で設定されるアプリ インスタンスの数 |
| Auto-Scale | 事前に定義されたルールとスケジュールに基づいてインスタンス数を自動的にスケーリングします |
| JVM</br>Options | 設定:JAVA_OPTS |
| 環境</br>変数 | Azure Spring Cloud 環境全体に適用する設定 |
| ランタイム</br>Version | Java 8 または Java 11|

## <a name="restrictions"></a>制限

* **1 つのアプリに 1 つの運用環境デプロイを割り当てる必要がある**:運用環境デプロイを削除しようとすると、API によってブロックされます。 削除する前にステージング デプロイにスワップする必要があります。
* **1 つのアプリに最大 2 つのデプロイを割り当てることができる**:3 つ以上のデプロイを作成しようとすると、API によってブロックされます。 新しいバイナリは、既存の運用環境デプロイまたはステージング デプロイのいずれかにデプロイします。
* **Basic レベルではデプロイ管理を使用できない**:ブルー/グリーン デプロイ機能を使用する場合は、Standard レベルを使用します。

## <a name="see-also"></a>関連項目
* [Azure Spring Cloud でステージング環境を設定する](spring-cloud-howto-staging-environment.md)
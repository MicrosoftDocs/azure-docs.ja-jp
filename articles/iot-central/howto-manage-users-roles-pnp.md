---
title: Azure IoT Central アプリケーションでユーザーとロールを管理する | Microsoft Docs
description: 管理者として Azure IoT Central アプリケーションでユーザーとロールを管理する方法
author: v-krghan
ms.author: v-krghan
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0c1a6b7370a4d1f8e01b22bfd52caa6cb6973947
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879259"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>IoT Central アプリケーションでユーザーとロールを管理する

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

この記事では、管理者として Azure IoT Central アプリケーションでユーザーを追加、編集、および削除する方法について説明し、さらに、Azure IoT Central アプリケーションでロールを管理する方法についても説明します。

**[Administration] (管理)** セクションにアクセスして使用するには、Azure IoT Central アプリケーションの**管理者**ロールが必要です。 Azure IoT Central アプリケーションを作成したユーザーは、自動的にそのアプリケーションの**管理者**ロールに割り当てられます。


## <a name="add-users"></a>Add users

ユーザーが Azure IoT Central アプリケーションにサインインしてアクセスするには、ユーザー アカウントが必要です。 Azure IoT Central では、Microsoft アカウント (MSA) と Azure Active Directory (AAD) アカウントがサポートされています。 Azure Active Directory グループは 現在 Azure IoT Central でサポートされていません。

詳細は、「[Microsoft アカウントのヘルプ](https://support.microsoft.com/products/microsoft-account?category=manage-account)」と「[クイック スタート: Azure Active Directory に新しいユーザーを追加する](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)」を参照してください。

1. IoT Central アプリケーションにユーザーを追加するには、 **[管理]** セクションの **[ユーザー]** ページに移動します。

    ![ユーザーの一覧](media/howto-manage-users-roles-pnp/image1.png)

1. **[Users] (ユーザー)** ページで、 **[+ Add user] (+ ユーザーの追加)** を選択してユーザーを追加します。

1. **ロール**ドロップダウン メニューからユーザーに対するロールを選択します。 ロールの詳細については、この記事の「[ロールの管理](#manage-roles)」セクションを参照してください。

    ![ロールの選択](media/howto-manage-users-roles-pnp/image3.png)

    > [!NOTE]
    >  ユーザーを一括で追加するには、追加するすべてのユーザーのユーザー ID をセミコロンで区切って入力します。 **[Role] (ロール)** ドロップダウンからロールを選択します｡ 次に、 **[保存]** を選択します。

### <a name="edit-the-roles-that-are-assigned-to-users"></a>ユーザーに割り当てられているロールを編集します。

割り当て後、ロールを変更することはできません。 ユーザーに割り当てられているロールを変更するには、ユーザーを削除してから、別のロールを使用してユーザーを追加します。

> [!NOTE]
> 割り当てられたロールは IoT Central アプリケーションに固有であり、Azure portal から管理することはできません。

## <a name="delete-users"></a>ユーザーを削除する

ユーザーを削除するには、**ユーザー**ページで 1 つまたは複数のチェック ボックスを選択します｡ 次に、 **[削除]** を選択します。

## <a name="manage-roles"></a>ロールの管理

ロールを使用すると、組織内で IoT Central のさまざまなタスクを実行できるユーザーを制御することができます。 アプリケーションのユーザーに割り当てることができるロールが 3 つあります。

### <a name="administrator"></a>管理者

**管理者**ロール内のユーザーは、アプリケーション内のすべての機能にアクセスできます。

アプリケーションを作成したユーザーは、自動的に**管理者**ロールに割り当てられます。 **管理者**ロールには、少なくとも 1 人のユーザーが常に存在している必要があります。

### <a name="application-builder"></a>Application Builder (アプリケーション ビルダー)

**Application Builder (アプリケーション ビルダー)** ロール内のユーザーは、アプリケーションの管理を除き、アプリケーション内のすべてを実行することができます。 ビルダーはデバイス テンプレートとデバイスの作成、編集、削除、デバイス セットの管理、および分析とジョブの実行が可能です。 ビルダーが、アプリケーションの **[管理]** セクションにアクセスすることはできません。

### <a name="application-operator"></a>Application Operator (アプリケーション オペレーター)

**Application Operator (アプリケーション オペレーター)** ロール内のユーザーはデバイス テンプレートを変更したり、アプリケーションを管理したりすることはできません。 オペレーターはデバイスの追加と削除、デバイス セットの管理、分析とジョブの実行が可能です。 オペレーターが、 **[Application Builder]\(アプリケーション ビルダー\)** ページと **[管理]** ページにアクセスすることはできません。

## <a name="next-steps"></a>次の手順

Azure IoT Central でのユーザーとロールの管理方法を学習したので、次の手順として、Azure IoT Central での[請求書の表示](howto-view-bill-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)方法を学習することをお勧めします。

---
title: 予約されたリソース名のエラー
description: 予約された単語を含むリソース名を指定するときのエラーを解決する方法について説明します。
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75474261"
---
# <a name="resolve-reserved-resource-name-errors"></a>予約されたリソース名のエラーを解決する

この記事では、名前に予約された単語が含まれるリソースをデプロイしているときに発生するエラーについて説明します。

## <a name="symptom"></a>症状

パブリック エンドポイント経由で使用可能なリソースをデプロイしているときに、次のエラーが表示されることがあります。

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>原因

パブリック エンドポイントが存在するリソースは、名前の中で予約された単語または商標を使用できません。

次の単語は予約されています。

* ACCESS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

次の単語は、名前の中の単語全体または部分文字列のどちらとしても使用できません。

* Login
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>解決策

いずれの予約された単語も使用していない名前を指定してください。
---
title: Azure Static Web Apps Preview を使用してフロントエンド フレームワークを構成する
description: Azure Static Web Apps に必要となる、一般的なフロントエンド フレームワークの設定
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 07/18/2020
ms.author: cshoe
ms.openlocfilehash: 14564b0591ef0146131b3f9324556b613e25daac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97901234"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>Azure Static Web Apps Preview を使用してフロントエンド フレームワークとライブラリを構成する

Azure Static Web Apps を使用するには、フロントエンド フレームワークまたはライブラリの[ビルド構成ファイル](github-actions-workflow.md)内に、適切な構成値が必要です。

## <a name="configuration"></a>構成

次の表は、一連のフレームワークとライブラリの設定を示しています<sup>1</sup>。

表の各列の意図は、次の項目によって説明されます。

- **出力場所**: `output_location` の値を表示します。これは、[ビルドされたバージョンのアプリケーション ファイル用のフォルダー](github-actions-workflow.md#build-and-deploy)です。

- **カスタム ビルド コマンド**:フレームワークで `npm run build` または `npm run azure:build` とは異なるコマンドが必要となる場合に、[カスタム ビルド コマンド](github-actions-workflow.md#custom-build-commands)を規定できます。

| フレームワーク | App artifact location (アプリ成果物の場所) | カスタム ビルド コマンド |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | 該当なし <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Angular Universal](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | 該当なし |
| [Backbone.js](https://backbonejs.org/) | `/` | 該当なし |
| [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) | `wwwroot` | 該当なし |
| [Ember](https://emberjs.com/) | `dist` | 該当なし |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Glimmer](https://glimmerjs.com/) | `dist` | 該当なし |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | 該当なし |
| [Hyperapp](https://hyperapp.dev/) | `/` | 該当なし |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | 該当なし |
| [jQuery](https://jquery.com/) | `/` | 該当なし |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | 該当なし |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | 該当なし |
| [Marko](https://markojs.com/) | `public` | 該当なし |
| [Meteor](https://www.meteor.com/) | `bundle` | 該当なし |
| [Mithril](https://mithril.js.org/) | `dist` | 該当なし |
| [Polymer](https://www.polymer-project.org/) | `build/default` | 該当なし |
| [Preact](https://preactjs.com/) | `build` | 該当なし |
| [React](https://reactjs.org/) | `build` | 該当なし |
| [Stencil](https://stenciljs.com/) | `www` | 該当なし |
| [Svelte](https://svelte.dev/) | `public` | 該当なし |
| [Three.js](https://threejs.org/) | `/` | 該当なし |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | 該当なし |
| [Vue.js](https://vuejs.org/) | `dist` | 該当なし |

<sup>1</sup> 上記の表は、Azure Static Web Apps で動作するフレームワークとライブラリを完全に網羅する一覧ではありません。

<sup>2</sup> 該当なし

## <a name="next-steps"></a>次のステップ

- [ビルドとワークフロー構成](github-actions-workflow.md)

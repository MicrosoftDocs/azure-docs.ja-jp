---
title: Azure Cosmos DB の MongoDB 用 API を使用して Angular アプリ UI を作成する (パート 3)
description: Angular と Node で MongoDB に使われる API をそのまま使用して、Azure Cosmos DB を対象とした MongoDB アプリを作成するチュートリアル シリーズのパート 3 です。
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: 181af5cf26d19a9f51e8d456e777badf7efa224d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93097823"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---build-the-ui-with-angular"></a>Azure Cosmos DB の MongoDB 用 API を使用して Angular アプリを作成する - Angular を使用して UI を構築する
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

複数のパートから成るこのチュートリアルでは、Express と Angular を使用して Node.js で新しいアプリを作成した後、[Cosmos DB の MongoDB 用 API を使用して構成された Cosmos アカウント](mongodb-introduction.md)にそれを接続する方法を紹介します。

本チュートリアルのパート 3 では、[パート 2](tutorial-develop-mongodb-nodejs-part2.md) の内容をベースとして、次のタスクについて取り上げます。

> [!div class="checklist"]
> * Angular UI を作成する
> * CSS を使って外観を設定する
> * アプリをローカルでテストする

## <a name="video-walkthrough"></a>ビデオ チュートリアル

> [!VIDEO https://www.youtube.com/embed/MnxHuqcJVoM]

## <a name="prerequisites"></a>前提条件

本チュートリアルのこのパートに取り組む前に、[パート 2](tutorial-develop-mongodb-nodejs-part2.md) の手順を済ませておいてください。

> [!TIP]
> このチュートリアルでは、アプリケーションを作成する手順を段階的に説明しています。 完成したプロジェクトをダウンロードしたい場合は、GitHub の [angular-cosmosdb リポジトリ](https://github.com/Azure-Samples/angular-cosmosdb)から完全なアプリケーションを取得できます。

## <a name="build-the-ui"></a>UI の作成

1. Visual Studio Code で [停止] ボタン (:::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part3/stop-button.png":::) をクリックして Node アプリを停止します。 

2. Windows のコマンド プロンプトまたは Mac のターミナル ウィンドウで、次のコマンドを入力して heroes コンポーネントを生成します。 このコードの g は生成 (generate) を、c はコンポーネント (component) を、heroes はコンポーネントの名前を表します。また、フラット ファイル構造 (--flat) を使用して、サブフォルダーの作成を抑制しています。

    ```
    ng g c heroes --flat 
    ```

    ターミナル ウィンドウに新しいコンポーネントの確認が表示されます。

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/install-heros-component.png" alt-text="hero コンポーネントのインストール":::

    作成されたファイルと更新されたファイルを見てみましょう。 

3. Visual Studio Code の **[エクスプローラー]** ウィンドウで新しい **src\app** フォルダーに移動し、app フォルダー内に生成された新しい **heroes.component.ts** ファイルを開きます。 この TypeScript コンポーネント ファイルは、前のコマンドで作成されたものです。

    > [!TIP]
    > Visual Studio Code に app フォルダーが表示されない場合は、CMD + SHIFT P キー (Mac) または Ctrl + Shift + P キー (Windows) を押してコマンド パレットを開き、「*Reload Window*」と入力してシステムの変更を読み込みます。

4. 同じフォルダーの **app.module.ts** ファイルを開き、`HeroesComponent` が 5 行目の宣言に追加され、10 行目でもインポートされていることを確認します。

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/app-module-file.png" alt-text="app-module.ts ファイルを開く":::

5. **heroes.component.html** ファイルに戻って、次のコードをコピーします。 `<div>` は、ページ全体のコンテナーです。 いずれかのヒーローをクリックして選択し、UI で編集したり削除したりできるよう、コンテナー内には作成するヒーローのリストが存在します。 またこの HTML には、どの項目が選択されたかを把握できるように、いくつかのスタイルが定義されています。 加えて、新しいヒーローを追加したり既存のヒーローを編集したりするための編集領域も存在します。 

    ```html
    <div>
      <ul class="heroes">
        <li *ngFor="let hero of heroes" (click)="onSelect(hero)" [class.selected]="hero === selectedHero">
          <button class="delete-button" (click)="deleteHero(hero)">Delete</button>
          <div class="hero-element">
            <div class="badge">{{hero.id}}</div>
            <div class="name">{{hero.name}}</div>
            <div class="saying">{{hero.saying}}</div>
          </div>
        </li>
      </ul>
      <div class="editarea">
        <button (click)="enableAddMode()">Add New Hero</button>
        <div *ngIf="selectedHero">
          <div class="editfields">
            <div>
              <label>id: </label>
              <input [(ngModel)]="selectedHero.id" placeholder="id" *ngIf="addingHero" />
              <label *ngIf="!addingHero" class="value">{{selectedHero.id}}</label>
            </div>
            <div>
              <label>name: </label>
              <input [(ngModel)]="selectedHero.name" placeholder="name" />
            </div>
            <div>
              <label>saying: </label>
              <input [(ngModel)]="selectedHero.saying" placeholder="saying" />
            </div>
          </div>
          <button (click)="cancel()">Cancel</button>
          <button (click)="save()">Save</button>
        </div>
      </div>
    </div>
    ```

7. HTML の準備が整ったら、それを **heroes.component.ts** ファイルに追加する必要があります。そうすることで、テンプレートを対話的に操作できるようになります。 次のコードは、コンポーネント ファイルにテンプレートを追加します。 いくつかのヒーローを取得し、ヒーロー サービス コンポーネントを初期化してすべてのデータを取得するためのコンストラクターが追加されています。 また、UI でのイベント処理に必要なメソッドもすべてこのコードによって追加されます。 次のコードは、**heroes.component.ts** の既存のコードに上書きする形でコピーしてください。 Hero および HeroService 領域では、まだ対応するコンポーネントがインポートされていないため、エラーが発生することが予想されます。これらのエラーは、次のセクションで修正します。 

    ```ts
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-heroes',
      templateUrl: './heroes.component.html',
      styleUrls: ['./heroes.component.scss']
    })
    export class HeroesComponent implements OnInit {
      addingHero = false;
      heroes: any = [];
      selectedHero: Hero;
    
      constructor(private heroService: HeroService) {}
    
      ngOnInit() {
       this.getHeroes();
      }

      cancel() {
        this.addingHero = false;
        this.selectedHero = null;
      }

      deleteHero(hero: Hero) {
        this.heroService.deleteHero(hero).subscribe(res => {
          this.heroes = this.heroes.filter(h => h !== hero);
          if (this.selectedHero === hero) {
            this.selectedHero = null;
          }
        });
      }

      getHeroes() {
        return this.heroService.getHeroes().subscribe(heroes => {
          this.heroes = heroes;
        });
      }

      enableAddMode() {
        this.addingHero = true;
        this.selectedHero = new Hero();
      }

      onSelect(hero: Hero) {
        this.addingHero = false;
        this.selectedHero = hero;
      }

      save() {
        if (this.addingHero) {
          this.heroService.addHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
            this.heroes.push(hero);
          });
        } else {
          this.heroService.updateHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
          });
        }
      }
    }
    ```

8. **エクスプローラー** で **app/app.module.ts** ファイルを開き、インポート セクションを更新し、さらに 14 行目を更新して、`FormsModule` のインポートを追加します。 インポート セクションは次のようになります。

    ```
    imports: [
      BrowserModule,
      FormsModule
    ],
    ```

9. **app/app.module.ts** ファイルで、新しい FormsModule モジュールのインポートを 3 行目に追加します。 

    ```
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    import { FormsModule } from '@angular/forms';
    ```

## <a name="use-css-to-set-the-look-and-feel"></a>CSS を使って外観を設定する

1. [エクスプローラー] ウィンドウで **src/styles.scss** ファイルを開きます。

2. **styles.scss** ファイルに次のコードをコピーし、ファイルの既存の内容と差し替えてください。

    ```css
    /* You can add global styles to this file, and also import other style files */

    * {
      font-family: Arial;
    }
    h2 {
      color: #444;
      font-weight: lighter;
    }
    body {
      margin: 2em;
    }
    
    body,
    input[text],
    button {
      color: #888;
      // font-family: Cambria, Georgia;
    }
    button {
      font-size: 14px;
      font-family: Arial;
      background-color: #eee;
      border: none;
      padding: 5px 10px;
      border-radius: 4px;
      cursor: pointer;
      cursor: hand;
      &:hover {
        background-color: #cfd8dc;
      }
      &.delete-button {
        float: right;
        background-color: gray !important;
        background-color: rgb(216, 59, 1) !important;
        color: white;
        padding: 4px;
        position: relative;
        font-size: 12px;
      }
    }
    div {
      margin: .1em;
    }

    .selected {
      background-color: #cfd8dc !important;
      background-color: rgb(0, 120, 215) !important;
      color: white;
    }

    .heroes {
      float: left;
      margin: 0 0 2em 0;
      list-style-type: none;
      padding: 0;
      li {
        cursor: pointer;
        position: relative;
        left: 0;
        background-color: #eee;
        margin: .5em;
        padding: .5em;
        height: 3.0em;
        border-radius: 4px;
        width: 17em;
        &:hover {
          color: #607d8b;
          color: rgb(0, 120, 215);
          background-color: #ddd;
          left: .1em;
        }
        &.selected:hover {
          /*background-color: #BBD8DC !important;*/
          color: white;
        }
      }
      .text {
        position: relative;
        top: -3px;
      }
      .saying {
        margin: 5px 0;
      }
      .name {
        font-weight: bold;
      }
      .badge {
        /* display: inline-block; */
        float: left;
        font-size: small;
        color: white;
        padding: 0.7em 0.7em 0 0.5em;
        background-color: #607d8b;
        background-color: rgb(0, 120, 215);
        background-color:rgb(134, 183, 221);
        line-height: 1em;
        position: relative;
        left: -1px;
        top: -4px;
        height: 3.0em;
        margin-right: .8em;
        border-radius: 4px 0 0 4px;
        width: 1.2em;
      }
    }

    .header-bar {
      background-color: rgb(0, 120, 215);
      height: 4px;
      margin-top: 10px;
      margin-bottom: 10px;
    }

    label {
      display: inline-block;
      width: 4em;
      margin: .5em 0;
      color: #888;
      &.value {
        margin-left: 10px;
        font-size: 14px;
      }
    }

    input {
      height: 2em;
      font-size: 1em;
      padding-left: .4em;
      &::placeholder {
          color: lightgray;
          font-weight: normal;
          font-size: 12px;
          letter-spacing: 3px;
      }
    }

    .editarea {
      float: left;
      input {
        margin: 4px;
        height: 20px;
        color: rgb(0, 120, 215);
      }
      button {
        margin: 8px;
      }
      .editfields {
        margin-left: 12px;
      }
    }
    ``` 
3. ファイルを保存します。 

## <a name="display-the-component"></a>コンポーネントの表示

必要なコンポーネントは揃いましたが、それを画面に表示する手段がまだありません。 **app.component.ts** で既定のコンポーネントを変更しましょう。

1. [エクスプローラー] ウィンドウで **/app/app.component.ts** を開き、タイトルを Heroes に変更します。さらに、**heroes.components.ts** に作成したコンポーネントの名前 (app-heroes) を配置して、その新しいコンポーネントを参照します。 ファイルの内容は次のようになります。 

    ```ts
    import { Component } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.scss'],
      template: `
      <h1>Heroes</h1>
      <div class="header-bar"></div>
      <app-heroes></app-heroes>
    `
    })
    export class AppComponent {
      title = 'app';
    }

    ```

2. **heroes.components.ts** には、他にも参照するコンポーネント (Hero コンポーネントなど) が存在するので、それも作成する必要があります。 Angular CLI のコマンド プロンプトで次のコマンドを使って、ヒーロー モデルと **hero.ts** という名前のファイルを作成します。g は生成 (generate) を、cl はクラス (class) を、hero はクラスの名前を表します。

    ```bash
    ng g cl hero
    ```

3. [エクスプローラー] ウィンドウで **src\app\hero.ts** を開きます。 **hero.ts** で、ファイルの内容を次のコードに差し替えて、識別子 (id)、名前 (name)、台詞 (saying) を備えた Hero クラスを追加します。

    ```ts
      export class Hero {
      id: number;
      name: string;
      saying: string;
    }
    ```

4. **heroes.components.ts** に戻って、`selectedHero: Hero;` という行 (10 行目) の `Hero` に表示されている赤色の下線に注目します。 

5. `Hero` という単語をクリックすると、Visual Studio によってコード ブロックの左側に電球アイコンが表示されます。 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/light-bulb.png" alt-text="Visual Studio Code の電球アイコン":::

6. 電球をクリックして **[Import Hero from "/app/hero".]\(Hero を "/app/hero" からインポートします。\)** または **[Import Hero from "./hero".]\(Hero を "./hero" からインポートします。\)** をクリックします (メッセージは実際の設定によって異なります)。

    新しいコード行が 2 行目に表示されます。 2 行目で /app/hero が参照されている場合は、ローカル フォルダーの hero ファイル (./hero) を参照するように変更してください。 変更後の 2 行目は、次のようになります。

   ```
   import { Hero } from "./hero";
   ``` 

    モデルを管理するコードは以上です。引き続きサービスを作成する必要があります。

## <a name="create-the-service"></a>サービスの作成

1. Angular CLI のコマンド プロンプトに次のコマンドを入力して、**app.module.ts** に hero サービスを作成します。g は生成 (generate) を、s はサービス (service) を、hero はサービスの名前を表します。また、-m で、app.module を作成先に指定します。

    ```bash
    ng g s hero -m app.module
    ```

2. Visual Studio Code で **heroes.components.ts** に戻ります。 `constructor(private heroService: HeroService) {}` という行 (13 行目) の `HeroService` に表示されている赤色の下線に注目してください。 `HeroService` をクリックすると、コード ブロックの左側に電球アイコンが表示されます。 電球をクリックして **[Import HeroService from "./hero.service ".]\(HeroService を "./hero.service " からインポートします。\)** または **[Import HeroService from "/app/hero.service ".]\(HeroService を "/app/hero.service " からインポートします。\)** をクリックします。

    電球をクリックすると、2 行目に新しいコード行が挿入されます。 2 行目で /app/hero.service フォルダーが参照されている場合は、ローカル フォルダーの hero ファイル (./hero.service) を参照するように変更してください。 変更後の 2 行目は、次のようになります。
    
    ```javascript
    import { HeroService } from "./hero.service"
    ```

3. Visual Studio Code で **hero.service.ts** を開き、次のコードをコピーしてファイルの内容を差し替えます。

    ```ts
    import { Injectable } from '@angular/core';
    import { HttpClient } from '@angular/common/http';
    
    import { Hero } from './hero';
    
    const api = '/api';

    @Injectable()
    export class HeroService {
      constructor(private http: HttpClient) {}

      getHeroes() {
        return this.http.get<Array<Hero>>(`${api}/heroes`)
      }

      deleteHero(hero: Hero) {
        return this.http.delete(`${api}/hero/${hero.id}`);
      }

      addHero(hero: Hero) {
        return this.http.post<Hero>(`${api}/hero/`, hero);
      }

      updateHero(hero: Hero) {
        return this.http.put<Hero>(`${api}/hero/${hero.id}`, hero);
      }
    }
    ```

    このコードでは、Angular が提供する HttpClient の最新バージョンを使用します。HttpClient モジュールは、自分で指定する必要があるので、それを次に行いましょう。

4. Visual Studio Code で **app.module.ts** を開き、HttpClientModule をインポートします。HttpClientModule が含まれるように import セクションを変更してください。

    ```ts
    imports: [
      BrowserModule,
      FormsModule,
      HttpClientModule
    ],
    ```

5. **app.module.ts** の一連の import ステートメントに、HttpClientModule の import ステートメントを追加します。

    ```ts
    import { HttpClientModule } from '@angular/common/http';
    ```

6. Visual Studio Code ですべてのファイルを保存します。

## <a name="build-the-app"></a>アプリのビルド

1. コマンド プロンプトに次のコマンドを入力して、Angular アプリケーションをビルドします。 

    ```bash
    ng b
    ``` 

    問題が発生した場合、修正すべきファイルについての情報がターミナル ウィンドウに表示されます。 ビルドが完了すると、新しいファイルが **dist** フォルダーに生成されます。 必要に応じて **dist** フォルダーの新しいファイルを確認してください。

    ここで、アプリを実行してみましょう。

2. Visual Studio Code で、左側の **[デバッグ]** ボタン (:::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png":::) をクリックしてから、 **[デバッグの開始]** ボタン (:::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part3/start-debugging-button.png":::) をクリックします。

3. インターネット ブラウザーを開いて **localhost:3000** に移動すると、アプリがローカルで実行されているようすを確認できます。

     :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/azure-cosmos-db-mongodb-mean-app.png" alt-text="ローカルで実行中のヒーロー アプリケーション":::

## <a name="next-steps"></a>次のステップ

本チュートリアルのこのパートでは、次の手順を行いました。

> [!div class="checklist"]
> * Angular UI を作成しました
> * ローカルでアプリをテストしました

引き続き Azure Cosmos DB アカウントを作成します。本チュートリアルの次のパートに進んでください。

> [!div class="nextstepaction"]
> [Azure CLI を使用して Azure Cosmos DB アカウントを作成する](tutorial-develop-mongodb-nodejs-part4.md)

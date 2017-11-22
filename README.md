# Ionic-com-tema-dinamico
Exemplo de como definir um tema dinâmico pra projetos em Ionic.

# Criar o projeto

```sh
ionic start ThemeIonic blank
cd ThemeIonic
```

Crie um provider onde iremos gerenciar nossas configurações
```sh
ionic g provider settings
```
Adicione o provider `SettingsProvider` no arquivo abaixo:
### app.module
```sh
import { BrowserModule } from '@angular/platform-browser';
import { ErrorHandler, NgModule } from '@angular/core';
import { IonicApp, IonicErrorHandler, IonicModule } from 'ionic-angular';
import { SplashScreen } from '@ionic-native/splash-screen';
import { StatusBar } from '@ionic-native/status-bar';

import { MyApp } from './app.component';
import { HomePage } from '../pages/home/home';
import { SettingsProvider } from '../providers/settings/settings';

@NgModule({
  declarations: [
    MyApp,
    HomePage
  ],
  imports: [
    BrowserModule,
    IonicModule.forRoot(MyApp)
  ],
  bootstrap: [IonicApp],
  entryComponents: [
    MyApp,
    HomePage
  ],
  providers: [
    StatusBar,
    SplashScreen,
    {provide: ErrorHandler, useClass: IonicErrorHandler},
    SettingsProvider
  ]
})
export class AppModule {}
```



Na pasta `src/theme` criar os arquivos abaixo:
### theme.light.scss

```sh
.light-theme {
  ion-content {
    background-color: #fff;
  }
 
  .toolbar-background {
    background-color: #fff;
  }
}
```

### theme.dark.scss

```sh
.dark-theme {
  ion-content {
    background-color: #090f2f;
    color: #fff;
  }
 
  .toolbar-title {
    color: #fff;
  }
 
  .header .toolbar-background {
    border-color: #ff0fff;
    background-color: #090f2f;
  }
}  
```

Abra o arquivo `src/theme/variables.scss` e importe os arquivos criados
```sh
@import "theme.light";
@import "theme.dark";
```

Vamos editar o serviço responsável por realizar as configurações do tema.

### src/providers/settings/settings.ts

```sh
import { Injectable } from '@angular/core';
import { BehaviorSubject } from 'rxjs/Rx';
 
@Injectable()
export class SettingsProvider {
 
    private theme: BehaviorSubject<String>;
 
    constructor() {
        this.theme = new BehaviorSubject('dark-theme');
    }
 
    setActiveTheme(val) {
        this.theme.next(val);
    }
 
    getActiveTheme() {
        return this.theme.asObservable();
    }
}
```

Iremos agora trabalhar na página Home onde iremos adicionar um botão que será responsável por alterar o tema do app.

### src/pages/home/home.ts
```sh

import { SettingsProvider } from './../../providers/settings/settings';
import { Component } from '@angular/core';
import { NavController } from 'ionic-angular';
 
@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {
  selectedTheme: String;
 
  constructor(public navCtrl: NavController, private settings: SettingsProvider) {
    this.settings.getActiveTheme().subscribe(val => this.selectedTheme = val);
  }
 
    toggleAppTheme() {
    if (this.selectedTheme === 'dark-theme') {
      this.settings.setActiveTheme('light-theme');
    } else {
      this.settings.setActiveTheme('dark-theme');
    }
  }
 
}
```
### src/pages/home/home.html

```sh
<ion-header>
  <ion-navbar>
    <ion-title>
      Night & Day
    </ion-title>
  </ion-navbar>
</ion-header>
 
<ion-content padding>
  <p text-center>I shine at night and glow at day.</p>
  <button ion-button full icon-left (click)="toggleAppTheme()">
    <ion-icon  name="bulb"></ion-icon>Toggle Theme
  </button>
</ion-content>
```

Vamos agora setar o tema ao iniciar o projeto
### src/app/app.component.ts

```sh
import { SettingsProvider } from './../providers/settings/settings';
import { Component } from '@angular/core';
import { Platform } from 'ionic-angular';
import { StatusBar } from '@ionic-native/status-bar';
import { SplashScreen } from '@ionic-native/splash-screen';
 
import { HomePage } from '../pages/home/home';
@Component({
  templateUrl: 'app.html'
})
export class MyApp {
  rootPage:any = HomePage;
  selectedTheme: String;
  
  constructor(platform: Platform, statusBar: StatusBar, splashScreen: SplashScreen, private settings: SettingsProvider) {
    this.settings.getActiveTheme().subscribe(val => this.selectedTheme = val);
 
    platform.ready().then(() => {
      statusBar.styleDefault();
      splashScreen.hide();
    });
  }
}
```
Ajuste o arquivo 
### src/app/app.html

```sh
<ion-nav [root]="rootPage" [class]="selectedTheme"></ion-nav>
```

Execute o comando no prompt `ionic serve` e veja o aplicativo em ação!

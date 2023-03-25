# 在 Angular 2/4/5 应用程序中实现 ng 文件上传

> 原文：<https://medium.com/hackernoon/implementing-ng-file-upload-in-angular-2-app-397bae30ae99>

![](img/8eb10a065bfb41b564625bc82015bbc4.png)

credit: [http://www.uploadify.com/](http://www.uploadify.com/)

我们需要[在我们的](https://hackernoon.com/tagged/implement) [Angular 2](https://angular.io/) 应用程序中实现 [拖放文件输入功能。](https://hackernoon.com/tagged/drag)

我们为此选择了 [ng-file-upload](https://github.com/valor-software/ng2-file-upload) 。

我们试图跟随[帮助页面](http://valor-software.com/ng2-file-upload/)。如建议的那样，实现了如下的`[drag-upload-input.html](https://github.com/valor-software/ng2-file-upload/blob/development/demo/src/app/components/file-upload/simple-demo.html)` & `[drag-upload-input.component.ts](https://github.com/valor-software/ng2-file-upload/blob/development/demo/src/app/components/file-upload/simple-demo.ts)`:

**drag-upload-input.html**

```
<!-- we only need single file upload --> <input type="file" ng2FileSelect [uploader]="uploader" />
```

**拖拽-上传-输入.组件. ts**

```
import { Component } from '[@angular/core](http://twitter.com/angular/core)';
import { FileUploader } from 'ng2-file-upload';// const URL = '/api/';
const URL = '[https://evening-anchorage-3159.herokuapp.com/api/'](https://evening-anchorage-3159.herokuapp.com/api/');[@Component](http://twitter.com/Component)({
  moduleId: module.id,
  selector: 'drag-upload-input',
  templateUrl: './drag-upload-input.html'
})
export class DragUploadInput {
  public uploader: FileUploader = new FileUploader({ url: URL });
  public hasBaseDropZoneOver: boolean = false;
  public hasAnotherDropZoneOver: boolean = false;public fileOverBase(e: any): void {
    this.hasBaseDropZoneOver = e;
  }public fileOverAnother(e: any): void {
    this.hasAnotherDropZoneOver = e;
  }
}
```

`[app.module.ts](https://github.com/valor-software/ng2-file-upload/blob/development/demo/src/app/app.module.ts)`有这样的`FileUploadModule`:

```
// File upload modules
import { FileUploadModule } from 'ng2-file-upload';
import { DragUploadInput } from './file-upload/drag-upload-input.component';//other imports[@NgModule](http://twitter.com/NgModule)({
  imports: [ ... other imports
FileUploadModule
],
  declarations: [  ... other declarations
DragUploadInput],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

而`[systemjs.config.js](http://stackoverflow.com/a/37167153/2404470)`看起来是这样的:

```
(function (global) {
  System.config({
    // map tells the System loader where to look for things
    map: {
      // other libraries
      'ng2-file-upload': 'node_modules/ng2-file-upload',
    },
    packages: {
   // other packages
      ng2-file-upload': {
        main: 'ng2-file-upload.js',
        defaultExtension: 'js'
      }
    }
  });
})(this);
```

*原载于*[*xameeramir . github . io*](http://xameeramir.github.io/Implementing-ng-file-upload-in-Angular-2-app/)*。*
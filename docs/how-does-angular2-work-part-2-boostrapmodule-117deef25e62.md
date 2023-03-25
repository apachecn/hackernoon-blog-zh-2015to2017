# Angular2 是如何工作的？第 2 部分— bootstrapModule()

> 原文：<https://medium.com/hackernoon/how-does-angular2-work-part-2-boostrapmodule-117deef25e62>

如果您错过了它，请查看本系列的第 1 部分！

[继续前进！现在我们开始](https://hackernoon.com/tagged/movin)[引导](https://hackernoon.com/tagged/bootstrapping)一个应用程序。因为我们关注的是 ng cli 给出的基本模型，所以我们将看看 application_ref.ts 中包含的简单引导程序。

在 ApplicationRef_ 中，bootstrapModule 的[实现实际上只包含一个对私有方法 _bootstrapModuleWithZone 的简单调用。如果您错过了我对区域的简要概述，您可以在这里找到它](https://github.com/angular/angular/blob/master/modules/%40angular/core/src/application_ref.ts#L296)，但是我也会在本系列的某个地方做一篇更深入的文章。

首先，我们注意到现在我们实例化了一个编译器；

```
const compilerFactory: CompilerFactory =  
  this.injector.get(CompilerFactory);
const compiler = compilerFactory.createCompiler(        
  Array.isArray(compilerOptions) ? 
    compilerOptions : [compilerOptions]
);
```

如果您还记得第 1 部分，dynamicBrowserPlatform 附带的编译器是 [RuntimeCompilerFactory](https://github.com/angular/angular/blob/42a287fabf6b035d51e00cf3006c27fec00f054a/modules/%40angular/compiler/src/compiler.ts#L93) 。查看 createCompiler 方法，代码生成一个注入器，它提供通过工厂的构造函数传入的配置，然后从注入器获取编译器。

编译器是什么样子的？嗯，虽然 COMPILER_PROVIDERS 明确地调用了许多编译器，但是[这一行](https://github.com/angular/angular/blob/42a287fabf6b035d51e00cf3006c27fec00f054a/modules/%40angular/compiler/src/compiler.ts#L82)指定了我们正在寻找的编译器。这里提供的编译器是 [RuntimeCompiler](https://github.com/angular/angular/blob/42a287fabf6b035d51e00cf3006c27fec00f054a/modules/%40angular/compiler/src/runtime_compiler.ts) ，它是 Angular2 框架的一个内部类，可以动态编译模板和组件，以便在应用中使用。RuntimeCompiler 是一个子类[编译器](https://github.com/angular/angular/blob/dd03bf12e1c035597d2fab252e393846efca5ea1/modules/%40angular/core/src/linker/compiler.ts#L57)。记住 RuntimeCompiler，因为我们很快就会回到它。

继续浏览 [_bootstrapModuleWithZone](https://github.com/angular/angular/blob/master/modules/%40angular/core/src/application_ref.ts#L301) ，第三个参数是 componentFactoryCallback。调用 bootstrapmodulewithszone in _ bootstrapModule 的方法是这样调用的；

```
this._bootstrapModuleWithZone(moduleType, compilerOptions, null);
```

换句话说，没有回调。

相反，该方法返回一个对编译器的调用， [compileModuleAsync](https://github.com/angular/angular/blob/42a287fabf6b035d51e00cf3006c27fec00f054a/modules/%40angular/compiler/src/runtime_compiler.ts#L55) ，带有一个 moduleType 的参数，在我们的例子中是我们的 AppModule。没错，我们实际提供的第一段代码已经进入了等式。

compileModuleAsync 又调用一个私有方法 _compileModuleAndComponents，第一个参数是我们的 AppModule，第二个参数是一个布尔值，它告诉编译器是否应该异步执行编译。

该方法如下所示，

```
const componentPromise = this._compileComponents(
  moduleType, 
  isSync
);    
const ngModuleFactory = this._compileModule(moduleType);return new SyncAsyncResult(
  ngModuleFactory, 
  componentPromise.then(() => ngModuleFactory)
);
```

深入到 [_compileComponents](https://github.com/angular/angular/blob/42a287fabf6b035d51e00cf3006c27fec00f054a/modules/%40angular/compiler/src/runtime_compiler.ts#L132) 方法，不是简单声明的第一行是这样的；

```
const ngModule = this._metadataResolver.getNgModuleMetadata(mainModule);
```

其中 mainModule 是 ng-cli 的样板文件提供的 AppModule。

_metadataResolver 作为从编译器的构造函数注入的私有属性进入 RuntimeCompiler。在 [CompileMetadataResolver](https://github.com/angular/angular/blob/42a287fabf6b035d51e00cf3006c27fec00f054a/modules/%40angular/compiler/src/metadata_resolver.ts#L27) 中，让我们看看[获取模块元数据](https://github.com/angular/angular/blob/42a287fabf6b035d51e00cf3006c27fec00f054a/modules/%40angular/compiler/src/metadata_resolver.ts#L200)会发生什么，以及响应是什么样子的。

首先我们解决了 eForwardRef，Angular2 的开发者很好地给出了一个 [plnkr 的例子](http://plnkr.co/edit/GU72mJrk1fiodChcmiDR?p=preview)。这种前向引用允许解析不必在 CompileMetadataResolver 之前内联的类。

接着，代码检查 moduleType 是否已经编译过，并从缓存中检索它，

```
var compileMeta = this._ngModuleCache.get(moduleType);
```

由于这是我们第一次执行代码，我们假设这是未定义的，并继续。然后，代码试图通过另一个注入的私有属性[_ ngmodulesolver](https://github.com/angular/angular/blob/42a287fabf6b035d51e00cf3006c27fec00f054a/modules/%40angular/compiler/src/metadata_resolver.ts#L204)解析模块的元数据。

[ngmodulesolver](https://github.com/angular/angular/blob/42a287fabf6b035d51e00cf3006c27fec00f054a/modules/%40angular/compiler/src/ng_module_resolver.ts)将我们带到一个实际上相对简单的类，简单到我可以在下面复制它；

```
@Injectable()
export class NgModuleResolver {
  constructor(private _reflector: ReflectorReader = reflector) {}     resolve(type: Type<any>, throwIfNotFound = true): NgModule {
    const ngModuleMeta: NgModule = 
      this._reflector.annotations(type).find(_isNgModuleMetadata);
    if (isPresent(ngModuleMeta)) {
      return ngModuleMeta;
    } else {
      if (throwIfNotFound) {
        throw new Error(`No NgModule metadata found for '${stringify(type)}'.`);
      }
      return null;
    }
  }
}
```

因此，这要求[反射器](https://github.com/angular/angular/blob/42a287fabf6b035d51e00cf3006c27fec00f054a/modules/%40angular/core/src/reflection/reflector.ts#L33)提供关于我们模块的特定注释的信息。Reflector 实际上是在我们的 platform_core_providers 中注入和实例化的(从第 1 部分开始)，reflection capabilities(reflection capabilities，Reflector 从中提取)实际上只是这里的默认实现。

我们现在最好奇的是[注解](https://github.com/angular/angular/blob/42a287fabf6b035d51e00cf3006c27fec00f054a/modules/%40angular/core/src/reflection/reflection_capabilities.ts#L91)，所以让我们来看看。在看这段代码之前，看一下 Paschal Precht 关于注释与装饰者的文章可能会有所帮助。因此，我们的 AppModule *实际上有一个注释，反射器将寻找。注释或。decorators 并返回相关的 NgModule 注释。*

退回到这段代码；

```
function _isNgModuleMetadata(obj: any): obj is NgModule {
  return obj instanceof NgModule;
}...const ngModuleMeta: NgModule = this._reflector.annotations(type).find(_isNgModuleMetadata);
```

我们发现特定的注释是 NgModule 的一个实例，对应于

```
@NgModule({
  ...
})
export class AppModule {}
```

来自我们自己的代码。NgModule 注释的内容现在被分配给 [ngModuleMeta](https://github.com/angular/angular/blob/42a287fabf6b035d51e00cf3006c27fec00f054a/modules/%40angular/compiler/src/ng_module_resolver.ts#L26) 并从 NgModuleResolver 返回。

我们现在回到[这里](https://github.com/angular/angular/blob/42a287fabf6b035d51e00cf3006c27fec00f054a/modules/%40angular/compiler/src/metadata_resolver.ts#L204)，成功解析了 NgModule 注释提供的元信息。我将把它留在这里，因为我认为这篇文章已经写得够长了。当我们深入研究 NgModule 元数据的解析时，请留意第 3 部分！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)，[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！
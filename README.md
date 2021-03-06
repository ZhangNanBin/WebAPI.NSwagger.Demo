# WebAPI.NSwagger.Demo
## 使用NSwag生成前端TypeScript调用文件 

生成文件 app/WebAPIClient.ts  
app目录中其他为扩展配置文件，可根据自己需求进行添加修改  
WebAPI.NSwagger.Demo.nswag 为NSwag配置文件  

## 在项目配置文件中添加，在项目构建时执行NSwag  
```
<Target Name="NSwag" AfterTargets="Build">
  <Copy SourceFiles="@(ReferencePath)" DestinationFolder="$(OutDir)References" />
  <Exec Command="$(NSwagExe_Core31) run  WebAPI.NSwagger.Demo.nswag /variables:Configuration=$(Configuration),OutDir=$(OutDir)" />
  <RemoveDir Directories="$(OutDir)References" />
</Target>
```

## NSwagExtensions/Templates为模板文件  
我在这里使用了Axios,为其添加了超时处理，简单修改了Client.RequestUrl.liquid和AxiosClient.liquid文件，并在ClientBase中重写了transformOptions方法  

### Client.RequestUrl.liquid文件中  
修改前
```
let url_ = this.baseUrl + "/{{ operation.Path }}{% if operation.HasQueryParameters %}?{% endif %}";
```

修改后
```
const apiName_ = "{{ operation.Path }}";
const api_ = apiName_.replace("/", ".");2
let url_ = this.baseUrl + `/{{ operation.Path }}?_api=${api_}{% if operation.HasQueryParameters %}&{% endif %}`;
```

### AxiosClient.liquid文件中  
修改前  
```
    return this.transformOptions(options_).then(transformedOptions_ => {
```

修改后  
```
    return this.transformOptions(options_, apiName_).then(transformedOptions_ => {
```
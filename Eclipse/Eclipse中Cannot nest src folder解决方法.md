# 1. Eclipse中Cannot nest src folder解决方法
## 1.1. 出现的问题
:+1:下面是工程中出现的问题:  
```md
Cannot nest 'Project/src/main/java' inside library 'Project/src 
```
如果你的classpath中导入了web app libraries中可能就会找到project/src,本来这里是放包的，这样他还会造成另一个结果，就是WEB-INF/classes中编译后还包含了源文件。  
## 1.2. 解决办法
网上找到解决方法，修改eclipse相应工程文件中的.setting文件`org.eclipse.wst.common.component `如果你发现下面这句
```xml
<wb-resource deploy-path="/WEB-INF/classes" source-path="/src"/> 
```
就把他改成下面的形式即可
```xml
<wb-resource deploy-path="/WEB-INF/classes" source-path="/src/main/java"/>
```
  如果这时你重新编译，项目中依然还是有The project cannot be built until build path errors are resolved这种错误提示，但你确实又没看到相应的具体提示。
根据提示，如果是java类型的错误，建议一个一个的把你的source路径重新添加一遍，问题大部分都能搞定。

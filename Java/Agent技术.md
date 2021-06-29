# Agent技术
```puml
!theme metal
|Java编译环境|
:源码文件;
:编译器编译;
:字节码;
|中间层|
:加载字节码;
:加载类库;
|Java平台运行时环境|
:类装载器字节码验证;
partition Java虚拟机 {

fork
     :java解释器;
fork again
     :即时编译器;
endfork
:运行期系统;
}
:操作系统底层;
:系统硬件;
```
# CodeBlock配置

## 编译器配置

1. `Settings` -> `Compiler` ->`Toolchain executables`
2. 在个人电脑中找到MinGW64的文件夹路径==D:\Program Files (x86)\Dev-Cpp\MinGW64==, 在`Auto-detect`的左侧框中输入
3. 点击`ok`完成编译器的配置

## debug配置

1.  `Settings`->`Debugger`->`Default`
2. 在`executable path`框中输入gdb.exe的文件路径==D:\Program Files (x86)\Dev-Cpp\MinGW64\bin\gdb.exe==
3. 点击`ok`完成debug的配置

## 配置支持C++11

1. `Settings`-> `Compiler`
2. 在`Compiler Flags`下的选项框中找到`C++ 11 ISO`并勾选
3. 点击`ok`即可支持C++11

## 配置快捷键

1. `Settings` -> `Editor` -> `Keyboard shortcuts` -> `Plugins` -> `Source code formatter (AStyle)`
2. 选中后, 在键盘上敲入自定义快捷键, 点击`Add`, 再点击`ok`即可配置==代码格式化==的快捷键
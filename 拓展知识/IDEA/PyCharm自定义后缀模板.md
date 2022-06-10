# PyCharm中自定义后缀模板

1. 下载插件`Custome Postfix Templates`

2. 在文件路径
   `C:\Users\24563\AppData\Roaming\JetBrains\PyCharm2021.1\plugins\intellij-postfix-templates_templates\ templates\python`中新建文件`python.postfixTemplates`

3. 模仿`Custom Postfix Templates`中给出的示例编写模板

   ```postfixTemplates
   # .type表示输入.type后缀
   # :Type of value表示对.type功能的解释
   .type : Type of value
   	ANY   →   type($expr$)
   ```

   
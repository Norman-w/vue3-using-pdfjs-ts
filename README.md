# 在vue3中使用pdfjs库浏览pdf文件

在实际使用中我们如果用浏览器自带的pdf插件来浏览pdf文件，那么我们就无法对pdf文件进行一些操作,事件监听等.

比如我们实际的需求就是监测用户浏览了文件的百分比,如果达到指定的百分比,我们就可以展示已阅读等组件进行后续的操作.

#### Vue 3 + TypeScript + Vite


## 步骤
1. 安装pdfjs-dist```pnpm i pdfjs-dist```
2. 拷贝pdfjs-dist/build/pdf.worker.min.js到public目录下
3. 在App.vue或其他关键入口全局设置pdf阅读器的参数 ```GlobalWorkerOptions.workerSrc = '/pdf.worker.mjs';```
4. 参照App.vue进行其他的编码.
5. 准备名为demo.pdf的文件放到public测试(自备)
6. npm dev启动项目访问```http://localhost:5173/``` 查看效果

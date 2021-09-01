## minipack源码解释

### 大逻辑

![minipack](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/94b9edbd-0499-45ab-9a69-590218cfe3a1/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210901%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210901T025219Z&X-Amz-Expires=86400&X-Amz-Signature=d2ca2f558a4f94e72102d2ddc1778c808fb03ee336b0148a3a2cacdc948fdcc3&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

它做了两件事情：

1. 构建dependency graph；
2. 返回一个浏览器中可执行的bundle（此处bundle为名词）；



### 细节
![details](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/10eeaba9-ee58-4921-90a1-1ed95bab2f02/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210901%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210901T030105Z&X-Amz-Expires=86400&X-Amz-Signature=f7318cdd6665389f679adc46c2e4ed30bbbf334f288b0dfba633d02db0520c59&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

`createAsset` 函数做4件事情：

- 读文件；

- 文件内容解析为AST状态；

- Traverse AST，根据import节点收集dependencies信息；

- 返回该文件的节点数据：

  ```javascript
  return {   
    id,   
    filename,   
    dependencies,   
    code,  
  };
  ```

`createGraph` ：

- 构建依赖树，用数组表达的队列做数据结构存储，通过节点的 mapping 属性做关联；

`Bundle`：

- 在一个IIFE环境内，执行require(0)。因为 entry file还依赖了其他，所以模拟了localRequire，因为babel打出的是CommonJS，所以还模拟了module、module.export。才发现 [fn, mapping]，是提前精心准备好的，一个是函数体，一个用来定位依赖。
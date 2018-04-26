## 可扩展性
## 组件组合
## 组件通信和职责
- 通信数据结构
- 可追踪的组件通信

## 路由
- URI的结构和模式
- 将资源映射到URI

## 用户偏好和默认设置
- 主要关心的偏好: 地区(cookie/后端/uri)，行为和外观

## 加载时间和响应速度
- 模块加载机制会遍历整个依赖树，直到得到所有需要的模块
- 模块的延迟加载(懒加载): 其他组件显式请求某个模块时，才会加载该模块到浏览器(`System.import`, 加快初始化速度)
- 如何处理模块加载的延迟: 主应用加载后先加载通知模块(通知用户正在加载的内容以及加载可能持续的时间),`for (let i = 0; i < 1e9; i++) {}`。
- 让UI看起来永远是可响应的，即使实际上并不是
- 在不牺牲模块化的前提下提高组件间通信性能

## 可移植性和测试
- mock api: mockjax/HTTP服务器
- 单元测试: mock数据可用于单元测试
- e2e test和持续集成: CI工具链(生成mock数据->执行测试用例->编译文件->结果)

## 缩小规模
- 文件体积: Code Spliting(分离业务代码和第三方库/延迟加载)
- vendor: 如何提取公共代码, 延迟加载可能会导致第三方库多次加载
- 网络带宽
- 内存消耗: 避免垃圾回收器进行不必要的回收
- CPU消耗
- 移除冗余的功能
- 尽量保证标签的语义化: 测试应该被放在p标签, 可点的按钮应该用button元素表示, 页面区域应该使用section划分

## 异常处理
- 快速失效: 系统/组件在出错时停止运行, 并显式地提醒用户某些功能出现了故障(提示应简明扼要)
- 容错机制: 关键组件永远不需要快速失效，当关键组件出错时应该立即被修复; 不关键但必须正常工作的组件则需要快速失效，避免出现更严重的错误
- 组件是低耦合的，那么组件间的错误处理也应该是低耦合的
- 故障恢复: 失败重试，重启组件
- 记录日志和调试: 有意义的错误日志，为潜在故障发出警告，通知和指导用户

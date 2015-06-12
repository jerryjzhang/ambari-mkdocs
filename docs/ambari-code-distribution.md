## Ambari 2.0代码分析

### Ambari Server

**按功能划分**

- REST资源接口

- Agent通讯协议与接口

- 数据库持久化

- 内存数据结构维护

- 服务与组件的管理：新增/删除，启动/停止，状态检查

- 主机管理：新增/删除，新增/删除/启动/停止组件，状态检查

- 配置管理：保存、下发、历史查询、配置组

- 告警与指标管理：收集、查询

- 元数据管理：定义、解析、查询


**按源码包划分**

- API: REST资源接口, 12925

	- services: RESTFul接口实现，6925

	- resources: RESTFul资源对象定义，1946

	- query/predicate/handler: RESTFul请求过滤、转换，3576

- ORM: 数据库持久化, 15219
  
	- Entity: 9010, 90多个Entity

	- DAO: 4544

- Agent: Agent通讯协议与接口，3047

- State: 内部数据结构维护（cluster/service/component/host/alert/等），17969

- Controller: 39207
  
	- internal: 各种资源的管理（增删改查）, 22164

	- metrics: 指标的管理, 2386

	- AmbariManagementController: 总控类, 3021

	- Request&Response: 模块间交互的数据抽象

- View: 自定义Web界面框架，3033

- Upgrade: 数据库升级，3382

- Stack: 元数据管理，2032
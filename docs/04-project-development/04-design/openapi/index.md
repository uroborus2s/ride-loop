# OpenAPI 契约目录

本目录存放面向前端联调的 OpenAPI 3.1 设计合同，按端拆分：

- `passenger-miniapp.openapi.yaml`
- `driver-light-miniapp.openapi.yaml`
- `driver-heavy-app.openapi.yaml`
- `ops-web.openapi.yaml`

使用原则：

- 这些文件是设计阶段的正式契约，不等于后端代码已实现。
- 字段、状态、错误码、分页格式必须与 `api-design.md`、端规格文档、数据库设计保持一致。
- 任一端增加或删除接口时，优先修改对应 OpenAPI 文件，再同步总接口设计文档。

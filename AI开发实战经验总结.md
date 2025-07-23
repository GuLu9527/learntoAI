# AI开发实战经验总结

> 基于实际项目开发总结的AI协作开发最佳实践指南

## 📋 目录

- [项目规划阶段](#项目规划阶段)
- [开发准备阶段](#开发准备阶段)
- [AI协作技巧](#ai协作技巧)
- [调试与优化](#调试与优化)
- [质量控制](#质量控制)
- [用户体验](#用户体验)
- [部署与运维](#部署与运维)
- [实用技巧补充](#实用技巧补充)
- [AI Agent开发最佳实践](#ai-agent开发最佳实践)
- [团队协作](#团队协作)

---

## 🎯 项目规划阶段

### 1. 项目需求梳理与方案设计

**核心原则**：确定项目整体内容，优先使用免费AI工具进行需求分析

**推荐工具**：
- DeepSeek（免费，代码能力强）
- 豆包（免费，中文理解好）
- Claude（逻辑分析能力强）

**操作流程**：
1. 让AI生成详细的任务文档
2. 通过多轮对话确定最终执行方案
3. 建立需求优先级矩阵，区分核心功能和扩展功能
4. 制定技术选型标准，考虑团队技能栈和项目复杂度

**最佳实践**：
```markdown
# 项目需求文档模板
## 项目概述
- 项目目标
- 核心功能
- 技术栈选择

## 功能模块
- [ ] 核心功能1（优先级：高）
- [ ] 核心功能2（优先级：高）
- [ ] 扩展功能1（优先级：中）

## 技术架构
- 前端技术栈
- 后端技术栈
- 数据库选择
- 部署方案
```

### 2. 方案审查与优化

**操作要点**：
- 选定主力AI助手后，投喂前期设计文档
- 让AI审查整体架构并提出优化建议
- 进行技术风险评估，识别潜在的技术难点
- 制定备选方案，应对开发过程中的变更需求

---

## 🔧 开发准备阶段

### 3. 功能模块分析

**核心策略**：将项目拆分为独立的功能模块，明确各模块间的依赖关系

**实施步骤**：
1. 绘制功能流程图，可视化业务逻辑
2. 定义统一的接口规范和数据格式
3. 建立模块开发优先级，优先开发核心基础模块

### 4. 数据库设计

**⚠️ 关键经验**：仔细设计数据库表结构，逐一审查每个字段的必要性、数据类型和约束条件，避免开发阶段频繁修改浪费时间

**设计原则**：
- 考虑数据库性能优化，合理设置索引
- 预留扩展字段，应对未来需求变更
- 建立数据字典，统一字段命名规范

**数据库设计检查清单**：
```sql
-- 表结构审查要点
1. 字段类型是否合适？
2. 是否设置了合适的主键和外键？
3. 索引是否合理？
4. 是否考虑了数据迁移？
5. 字段命名是否规范？
```

---

## 🤖 AI协作技巧

### 5. 建立开发规范

**核心要点**：为前后端分别制定开发规范（rules），AI会严格按照提示词规范进行代码生成

**规范内容**：
- 代码注释规范，提高代码可维护性
- 统一错误处理机制和日志记录格式
- 建立代码审查清单，确保代码质量

### 6. 上下文管理策略

**⚠️ 重要经验**：将数据库表结构等关键信息始终保持在AI可访问的位置，在对话开始时明确告知，避免生成与数据库不匹配的代码

**管理策略**：
- 建立项目文档库，包含所有关键设计文档
- 定期更新AI的上下文信息，保持信息同步
- 使用文档模板，标准化信息传递格式

### 7. 技术栈更新

**推荐工具**：利用Context 7等MCP工具获取最新技术文档，避免AI使用过时的技术方案

**更新策略**：
- 定期检查依赖包版本，及时更新安全补丁
- 关注技术社区动态，了解最佳实践变化
- 建立技术选型评估流程

---

## 🐛 调试与优化

### 8. 高效Bug修复

**⚠️ 关键技巧**：遇到Bug时，提供完整的前后端错误信息、堆栈跟踪和复现步骤，提高AI修复的效率和准确性

**错误处理最佳实践**：

#### 🔍 前端错误捕获
```javascript
// 全局错误监听
window.addEventListener('error', (event) => {
  console.error('Global error:', {
    message: event.message,
    filename: event.filename,
    lineno: event.lineno,
    colno: event.colno,
    error: event.error
  });
});

// Promise错误捕获
window.addEventListener('unhandledrejection', (event) => {
  console.error('Unhandled promise rejection:', event.reason);
});
```

#### 🏗️ 后端错误处理
```javascript
// 自定义错误类
class AppError extends Error {
  constructor(message, statusCode, isOperational = true) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = isOperational;
    Error.captureStackTrace(this, this.constructor);
  }
}

// 集中式错误处理中间件
app.use((err, req, res, next) => {
  logger.error(err);

  if (!err.isOperational) {
    process.exit(1); // 程序错误，需要重启
  }

  res.status(err.statusCode || 500).json({
    error: process.env.NODE_ENV === 'production' ? 'Something went wrong' : err.message
  });
});
```

**Bug报告模板**：
```markdown
## Bug描述
简要描述问题

## 错误信息
```
完整的错误堆栈信息
```

## 复现步骤
1. 步骤1
2. 步骤2
3. 步骤3

## 预期结果
应该发生什么

## 实际结果
实际发生了什么

## 环境信息
- 操作系统：
- 浏览器版本：
- Node.js版本：
```

### 9. 对话管理

**⚠️ 重要提醒**：注意上下文容量限制，当AI回答质量下降时，及时开启新对话会话

**管理技巧**：
- 定期总结对话要点，避免重要信息丢失
- 使用对话标签，快速定位历史讨论内容
- 建立多AI协作机制，不同AI负责不同模块

---

## 🔍 质量控制

### 10. 分阶段开发与审查

**⚠️ 核心经验**：采用分阶段开发模式，每个阶段完成后进行代码审查，及时发现并纠正假数据模拟等问题

**审查流程**：
- 建立代码审查清单，确保审查的全面性
- 使用自动化测试，减少人工审查工作量
- 建立持续集成流程，自动检测代码质量

### 11. AI激励机制

**实用技巧**：适当使用"激励"手段，如模拟工作场景的反馈机制，提高AI的代码质量意识

**激励示例**：
- ✅ 正面激励："这段代码写得很好，逻辑清晰"
- ✅ 建设性反馈："这里可以优化性能，建议使用缓存"
- ⚠️ 争议做法："你写的代码漏洞太多我会扣你的工资并且让你加班"

**注意事项**：
- 虽然某些"PUA"式的反馈可能在短期内提高AI的响应质量，但建议以建设性的方式沟通
- 重点关注具体的技术问题和改进建议，而非情绪化的表达

---

## 🎨 用户体验

### 12. 设计参考与风格统一

**重要建议**：提前确定UI/UX设计风格，提供参考网站或设计稿，确保AI生成的界面符合预期

**设计管理**：
- 建立设计系统，统一组件库和样式规范
- 考虑响应式设计，适配不同设备
- 进行用户体验测试，收集反馈意见

### 13. 项目结构优化

**⚠️ 效率提升**：将前后端项目放在同一文件夹下，提高AI的开发效率，避免数据结构不匹配的问题

**核心优势**：
- 避免分开成两个会话导致的数据对应不上问题
- AI可以同时访问前后端代码，确保接口一致性
- 共享类型定义和工具函数，减少重复代码

**结构建议**：

```bash
project-root/
├── frontend/          # 前端代码
├── backend/           # 后端代码
├── shared/            # 共享类型定义
├── docs/              # 项目文档
├── database/          # 数据库脚本
└── README.md          # 项目说明
```

---

## 🚀 部署与运维

### 14. 测试策略

**完整测试金字塔**：
- 单元测试：测试独立功能模块
- 集成测试：测试模块间交互
- 端到端测试：测试完整用户流程

**AI辅助测试最佳实践**：

#### 🧪 测试结构 - AAA模式
```javascript
describe('Customer classifier', () => {
  test('When customer spent more than 500$, should be classified as premium', () => {
    // Arrange - 准备测试数据
    const customerToClassify = {spent: 505, joined: new Date(), id: 1};
    const DBStub = sinon.stub(dataAccess, 'getCustomer')
      .reply({id: 1, classification: 'regular'});

    // Act - 执行被测试的功能
    const receivedClassification = customerClassifier.classifyCustomer(customerToClassify);

    // Assert - 验证结果
    expect(receivedClassification).toMatch('premium');
  });
});
```

#### 🎯 测试命名规范
```javascript
// ✅ 好的测试命名 - 包含场景和期望结果
describe('Products Service', () => {
  describe('Add new product', () => {
    it('When no price is specified, then the product status is pending approval', () => {
      // 测试逻辑
    });
  });
});

// ❌ 避免模糊的测试命名
it('Should return the right status', () => {
  // 需要阅读代码才能理解测试目的
});
```

#### 🔧 错误流程测试
```javascript
// 测试异常抛出
describe("Facebook chat", () => {
  it("Notifies on new chat message", () => {
    const chatService = new chatService();
    chatService.participants = getDisconnectedParticipants();
    expect(chatService.sendMessage.bind({ message: "Hi" })).to.throw(ConnectionError);
  });
});

// 测试API错误响应
test("When exception is thrown during request, Then logger reports the mandatory fields", async () => {
  // Arrange
  sinon.stub(OrderRepository.prototype, "addOrder")
    .rejects(new AppError("saving-failed", "Order could not be saved", 500));
  const loggerDouble = sinon.stub(logger, "error");

  // Act
  const receivedResponse = await axiosAPIClient.post("/order", orderToAdd);

  // Assert
  expect(receivedResponse.status).toBe(500);
  expect(loggerDouble.lastCall.firstArg).toMatchObject({
    name: "saving-failed",
    status: 500,
    stack: expect.any(String),
    message: expect.any(String),
  });
});
```

**AI生成测试用例提示词**：
```markdown
请为以下函数生成完整的测试用例：
- 使用AAA模式（Arrange, Act, Assert）
- 包含正常情况和边界情况
- 测试错误处理逻辑
- 使用描述性的测试名称

函数代码：
[粘贴函数代码]
```

### 15. 部署与运维

**容器化部署**：
```dockerfile
# 多阶段构建示例
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/node_modules ./node_modules
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

**监控告警**：
- 建立监控告警机制，及时发现生产问题
- 制定灾备方案，确保服务可用性

---

## � 实用技巧补充

### 额外经验要点

基于实际开发过程中的细节经验，以下是一些重要的补充技巧：

#### 🎯 AI沟通策略
- **适度的"激励"机制**：可以适当使用工作场景模拟来提高AI的代码质量意识
- **具体化反馈**：避免模糊的评价，给出具体的改进建议
- **保持专业性**：重点关注技术问题而非情绪化表达

#### 📁 项目组织最佳实践
- **统一项目结构**：前后端放在同一文件夹下，避免跨会话的数据不一致
- **共享资源管理**：建立共享的类型定义、工具函数和配置文件
- **文档同步**：确保所有关键信息都在AI可访问的范围内

#### 🎨 UI/UX协作要点
- **提前确定设计风格**：在开发前明确UI设计方向和参考标准
- **提供具体参考**：给AI提供参考网站或设计稿，确保输出符合预期
- **建立设计系统**：统一组件库和样式规范，保持界面一致性

---

## �👥 团队协作

### 16. 协作流程

**代码审查流程**：
1. 功能开发完成
2. 自测通过
3. 提交Pull Request
4. 代码审查
5. 修改反馈
6. 合并主分支

**项目管理**：
- 使用项目管理工具，跟踪开发进度
- 定期进行技术分享，提升团队整体水平
- 建立知识库，沉淀项目经验

---

## 🤖 AI Agent开发最佳实践

### Agent架构设计

基于Google ADK和最新AI开发实践，以下是构建高质量AI Agent的关键要点：

#### 🏗️ Agent结构设计
```python
from google.adk.agents import LlmAgent, SequentialAgent, LoopAgent

# 单一职责Agent
code_writer_agent = LlmAgent(
    name="CodeWriterAgent",
    model="gemini-2.0-flash",
    instruction="""你是一个Python代码生成器。
    基于用户需求，生成完整的Python代码。
    只输出代码块，不添加其他文字。""",
    description="根据规范生成初始Python代码",
    output_key="generated_code"
)

# 代码审查Agent
code_reviewer_agent = LlmAgent(
    name="CodeReviewerAgent",
    model="gemini-2.0-flash",
    instruction="""你是专业的代码审查员。
    审查代码的正确性、可读性、效率和最佳实践。
    提供具体的改进建议。""",
    description="审查代码并提供反馈",
    output_key="review_comments"
)

# 组合Agent - 顺序执行
code_pipeline_agent = SequentialAgent(
    name="CodePipelineAgent",
    sub_agents=[code_writer_agent, code_reviewer_agent],
    description="执行代码生成、审查、重构的完整流程"
)
```

#### 🔄 错误处理与重试机制
```python
# 带错误处理的Agent
class RobustAgent(BaseAgent):
    async def process(self, context: InvocationContext) -> AsyncGenerator[Event, None]:
        max_retries = 3
        retry_count = 0

        while retry_count < max_retries:
            try:
                async for event in super().process(context):
                    yield event
                break
            except Exception as e:
                retry_count += 1
                if retry_count >= max_retries:
                    yield Event.error(f"Agent failed after {max_retries} retries: {e}")
                else:
                    await asyncio.sleep(2 ** retry_count)  # 指数退避
```

#### 🎯 提示词工程最佳实践
```python
# 结构化提示词模板
STRUCTURED_PROMPT = """
# 角色定义
你是一个{role}，专门负责{responsibility}。

# 任务描述
{task_description}

# 输入格式
{input_format}

# 输出要求
1. 格式：{output_format}
2. 约束：{constraints}
3. 质量标准：{quality_standards}

# 示例
输入：{example_input}
输出：{example_output}

# 当前任务
{current_task}
"""

# 使用结构化提示词
agent = LlmAgent(
    name="StructuredAgent",
    instruction=STRUCTURED_PROMPT.format(
        role="Python开发专家",
        responsibility="生成高质量的Python代码",
        task_description="根据需求生成符合PEP8规范的Python代码",
        input_format="自然语言描述的功能需求",
        output_format="完整的Python代码块",
        constraints="必须包含错误处理和文档字符串",
        quality_standards="代码可读性高、性能优化、安全性好",
        example_input="创建一个计算斐波那契数列的函数",
        example_output="def fibonacci(n): ...",
        current_task="{user_input}"
    )
)
```

### Agent协作模式

#### 🔀 并行处理模式
```python
# 并行研究Agent
research_agents = ParallelAgent(
    name="ResearchTeam",
    sub_agents=[
        create_research_agent("renewable_energy", "可再生能源"),
        create_research_agent("ev_technology", "电动汽车技术"),
        create_research_agent("carbon_capture", "碳捕获技术")
    ],
    description="并行执行多个研究任务"
)

# 结果合成Agent
synthesis_agent = LlmAgent(
    name="SynthesisAgent",
    instruction="""
    将多个研究结果合成为结构化报告。
    严格基于提供的输入摘要，不添加外部知识。

    输入摘要：
    - 可再生能源：{renewable_energy_result}
    - 电动汽车：{ev_technology_result}
    - 碳捕获：{carbon_capture_result}
    """,
    description="合成研究结果为完整报告"
)
```

#### 🔁 循环优化模式
```python
# 迭代改进Agent
improvement_loop = LoopAgent(
    name="ImprovementLoop",
    sub_agents=[critic_agent, reviser_agent],
    max_iterations=3,
    description="迭代改进内容质量"
)
```

### 生产环境部署

#### 🚀 Agent部署配置
```python
# 生产环境Agent配置
production_agent = LlmAgent(
    name="ProductionAgent",
    model="gemini-2.0-flash",
    instruction=production_prompt,
    tools=[
        VertexAiSearchTool(data_store_id=DATASTORE_ID),
        CustomFunctionTool(validate_input),
        CustomFunctionTool(log_interaction)
    ],
    before_model_callback=security_guardrail,  # 安全检查
    after_model_callback=quality_check        # 质量验证
)

# 安全防护回调
def security_guardrail(context: CallbackContext, request: LlmRequest) -> Optional[LlmResponse]:
    # 检查敏感信息
    if contains_sensitive_data(request.contents):
        return LlmResponse.error("请求包含敏感信息，已被阻止")

    # 检查恶意输入
    if is_malicious_input(request.contents):
        return LlmResponse.error("检测到恶意输入，已被阻止")

    return None  # 继续处理
```

#### 📊 监控与日志
```python
# Agent性能监控
class MonitoredAgent(BaseAgent):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.metrics = {
            'total_requests': 0,
            'successful_requests': 0,
            'failed_requests': 0,
            'average_response_time': 0
        }

    async def process(self, context: InvocationContext) -> AsyncGenerator[Event, None]:
        start_time = time.time()
        self.metrics['total_requests'] += 1

        try:
            async for event in super().process(context):
                yield event
            self.metrics['successful_requests'] += 1
        except Exception as e:
            self.metrics['failed_requests'] += 1
            logger.error(f"Agent {self.name} failed: {e}")
            raise
        finally:
            response_time = time.time() - start_time
            self.update_average_response_time(response_time)
```

---

## 📚 参考资源

### 推荐工具

#### 🤖 AI开发工具
- **DeepSeek**：免费，代码生成能力强，支持长上下文
- **豆包**：免费，中文理解好，适合中文项目
- **Claude**：逻辑分析能力强，代码审查质量高
- **ChatGPT**：通用性好，插件生态丰富

#### 🔧 开发辅助工具
- **Context 7**：获取最新技术文档，避免AI使用过时信息
- **GitHub Copilot**：实时代码补全和建议
- **Cursor**：AI驱动的代码编辑器
- **Replit**：在线AI编程环境

#### 📊 项目管理工具
- **GitHub Projects**：代码仓库集成的项目管理
- **Notion**：文档和项目管理一体化
- **Trello**：简单直观的看板管理
- **Linear**：现代化的问题跟踪工具

#### 🔍 监控与调试工具
- **Sentry**：错误监控和性能分析
- **DataDog**：全栈监控解决方案
- **New Relic**：应用性能监控
- **LogRocket**：前端会话重放和监控

### 学习资源
- [Node.js最佳实践](https://github.com/goldbergyoni/nodebestpractices)
- [Google ADK文档](https://adk.wiki/)
- [前端开发规范](https://github.com/airbnb/javascript)

---

## 🎯 总结

AI开发的核心是**人机协作**，不是简单的工具使用。通过建立规范的开发流程、有效的沟通机制和持续的质量控制，可以显著提高开发效率和代码质量。

**记住三个关键点**：
1. **规范先行**：建立清晰的开发规范和文档
2. **迭代优化**：分阶段开发，及时反馈和调整
3. **质量为本**：代码审查和测试不可省略

---

## 🛠️ 实用代码模板

### AI提示词模板

#### 代码生成提示词
```markdown
# 角色设定
你是一个资深的[技术栈]开发工程师

# 任务描述
请帮我实现[具体功能]

# 技术要求
- 使用[具体技术栈]
- 遵循[编码规范]
- 包含错误处理
- 添加详细注释

# 数据库结构
[粘贴相关表结构]

# 接口规范
[粘贴API接口定义]

# 预期输出
请提供完整的代码实现，包括：
1. 核心逻辑代码
2. 错误处理
3. 单元测试
4. 使用示例
```

#### Bug修复提示词
```markdown
# 问题描述
[详细描述问题现象]

# 错误信息
```
[完整的错误堆栈]
```

# 相关代码
```[语言]
[粘贴相关代码片段]
```

# 预期行为
[描述应该如何工作]

# 环境信息
- Node.js版本：
- 依赖版本：
- 操作系统：

请帮我分析问题原因并提供修复方案。
```

### 项目配置模板

#### package.json 最佳实践
```json
{
  "name": "project-name",
  "version": "1.0.0",
  "description": "项目描述",
  "main": "dist/index.js",
  "scripts": {
    "dev": "nodemon src/index.ts",
    "build": "tsc",
    "start": "node dist/index.js",
    "test": "jest",
    "test:watch": "jest --watch",
    "lint": "eslint src/**/*.ts",
    "lint:fix": "eslint src/**/*.ts --fix"
  },
  "dependencies": {
    "express": "^4.18.0"
  },
  "devDependencies": {
    "@types/node": "^18.0.0",
    "typescript": "^4.8.0",
    "nodemon": "^2.0.0",
    "jest": "^28.0.0",
    "eslint": "^8.0.0"
  },
  "engines": {
    "node": ">=16.0.0"
  }
}
```

#### TypeScript配置
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "**/*.test.ts"]
}
```

---

## 🔧 常见问题解决方案

### 1. AI生成代码质量问题

**问题**：AI生成的代码缺乏错误处理
**解决方案**：
```javascript
// ❌ AI常见的不完整代码
async function getUserData(id) {
  const user = await db.user.findById(id);
  return user;
}

// ✅ 改进后的完整代码
async function getUserData(id) {
  try {
    if (!id) {
      throw new Error('User ID is required');
    }

    const user = await db.user.findById(id);

    if (!user) {
      throw new Error('User not found');
    }

    return user;
  } catch (error) {
    logger.error('Failed to get user data:', error);
    throw error;
  }
}
```

### 2. 数据库设计不一致

**问题**：前后端对数据结构理解不一致
**解决方案**：使用共享类型定义
```typescript
// shared/types.ts
export interface User {
  id: string;
  email: string;
  name: string;
  createdAt: Date;
  updatedAt: Date;
}

export interface CreateUserRequest {
  email: string;
  name: string;
  password: string;
}

export interface UserResponse {
  user: Omit<User, 'password'>;
  token: string;
}
```

### 3. API接口不规范

**问题**：接口返回格式不统一
**解决方案**：统一响应格式
```typescript
// utils/response.ts
export interface ApiResponse<T = any> {
  success: boolean;
  data?: T;
  message?: string;
  error?: string;
  code?: number;
}

export const successResponse = <T>(data: T, message?: string): ApiResponse<T> => ({
  success: true,
  data,
  message
});

export const errorResponse = (error: string, code?: number): ApiResponse => ({
  success: false,
  error,
  code
});
```

---

## 📊 性能优化指南

### 1. 数据库优化

```sql
-- 索引优化
CREATE INDEX idx_user_email ON users(email);
CREATE INDEX idx_post_user_created ON posts(user_id, created_at);

-- 查询优化
-- ❌ 避免 N+1 查询
SELECT * FROM posts WHERE user_id IN (SELECT id FROM users);

-- ✅ 使用 JOIN
SELECT p.*, u.name
FROM posts p
JOIN users u ON p.user_id = u.id;
```

### 2. 前端性能优化

```javascript
// 组件懒加载
const LazyComponent = React.lazy(() => import('./HeavyComponent'));

// 防抖处理
const debouncedSearch = useMemo(
  () => debounce((query) => {
    // 搜索逻辑
  }, 300),
  []
);

// 缓存优化
const memoizedValue = useMemo(() => {
  return expensiveCalculation(data);
}, [data]);
```

### 3. 后端性能优化

```javascript
// Redis缓存
const getCachedUser = async (id) => {
  const cached = await redis.get(`user:${id}`);
  if (cached) {
    return JSON.parse(cached);
  }

  const user = await db.user.findById(id);
  await redis.setex(`user:${id}`, 3600, JSON.stringify(user));
  return user;
};

// 连接池优化
const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  max: 20,
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
});
```

---

## 🔒 安全最佳实践

### 1. 输入验证

```javascript
const Joi = require('joi');

const userSchema = Joi.object({
  email: Joi.string().email().required(),
  password: Joi.string().min(8).required(),
  name: Joi.string().min(2).max(50).required()
});

const validateUser = (data) => {
  const { error, value } = userSchema.validate(data);
  if (error) {
    throw new Error(error.details[0].message);
  }
  return value;
};
```

### 2. 身份认证

```javascript
const jwt = require('jsonwebtoken');

const generateToken = (user) => {
  return jwt.sign(
    { id: user.id, email: user.email },
    process.env.JWT_SECRET,
    { expiresIn: '24h' }
  );
};

const verifyToken = (req, res, next) => {
  const token = req.headers.authorization?.split(' ')[1];

  if (!token) {
    return res.status(401).json({ error: 'No token provided' });
  }

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    return res.status(401).json({ error: 'Invalid token' });
  }
};
```

### 3. 环境变量管理

```javascript
// .env.example
NODE_ENV=development
PORT=3000
DATABASE_URL=postgresql://user:password@localhost:5432/dbname
JWT_SECRET=your-super-secret-key
REDIS_URL=redis://localhost:6379

// config/index.js
require('dotenv').config();

module.exports = {
  port: process.env.PORT || 3000,
  nodeEnv: process.env.NODE_ENV || 'development',
  database: {
    url: process.env.DATABASE_URL
  },
  jwt: {
    secret: process.env.JWT_SECRET
  }
};
```

---

*本文档基于实际项目开发经验总结，持续更新中...*

**贡献指南**：欢迎提交Issue和PR来完善这份文档！

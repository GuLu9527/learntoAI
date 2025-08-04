# DWMcpServer - Spring AI MCP 服务器教学演示

本项目是一个基于 Spring Boot 和 Spring AI 的 Model Context Protocol (MCP) 服务器教学演示项目，展示如何创建和配置一个响应式的 MCP 服务器。

## 项目简介

MCP (Model Context Protocol) 是一个用于 AI 模型与外部工具和资源进行交互的协议。本项目演示了如何使用 Spring AI 框架创建一个支持天气查询功能的 MCP 服务器。

## 技术栈

- **Spring Boot 3.5.4** - 主框架
- **Spring AI 1.0.0** - AI 集成框架
- **Spring WebFlux** - 响应式 Web 框架
- **Java 17** - 编程语言
- **Maven** - 构建工具

## 快速开始

### 环境要求

- Java 17 或更高版本
- Maven 3.6+ (或使用项目提供的 Maven Wrapper)

### 克隆并运行项目

```bash
# 克隆项目
git clone [repository-url]
cd DWMcpServer

# 编译项目
./mvnw clean compile

# 运行项目
./mvnw spring-boot:run
```

项目启动后，MCP 服务器将在默认端口运行，提供 SSE 消息端点：`/mcp/messages`

## 如何创建 MCP 服务器

### 步骤 1：创建 Spring Boot 项目

使用 [Spring Initializr](https://start.spring.io) 创建项目，或使用以下 Maven 依赖：

```xml
<dependencies>
    <!-- Spring AI MCP 服务器启动器 -->
    <dependency>
        <groupId>org.springframework.ai</groupId>
        <artifactId>spring-ai-starter-mcp-server</artifactId>
    </dependency>
    
    <!-- Spring AI MCP WebFlux 支持 -->
    <dependency>
        <groupId>org.springframework.ai</groupId>
        <artifactId>spring-ai-starter-mcp-server-webflux</artifactId>
    </dependency>
    
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.ai</groupId>
            <artifactId>spring-ai-bom</artifactId>
            <version>1.0.0</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

### 步骤 2：配置 MCP 服务器

在 `application.yml` 中配置 MCP 服务器：

```yaml
spring:
  application:
    name: DWMcpServer
  ai:
    mcp:
      server:
        name: webflux-mcp-server
        version: 1.0.0
        type: ASYNC  # 推荐用于响应式应用
        instructions: "This reactive server provides weather information tools and resources"
        sse-message-endpoint: /mcp/messages
        capabilities:
          tool: true      # 启用工具支持
          resource: true  # 启用资源支持
          prompt: true    # 启用提示支持
          completion: true # 启用完成支持
```

### 步骤 3：创建 MCP 工具

创建服务类并使用 `@Tool` 注解定义工具：

```java
@Service
public class WeatherService {
    
    @Tool(description = "根据城市名称获取天气预报信息")
    public String getWeather(@ToolParam(description = "城市") String cityName) {
        // 实际应用中可以调用真实的天气 API
        return cityName + "天气阳光明媚～";
    }

    @Tool(description = "根据城市名称获取空气质量")
    public String getAirQuality(@ToolParam(description = "城市") String cityName) {
        return cityName + "空气质量很好 by: lcry～";
    }
}
```

### 步骤 4：注册工具

在主应用类中注册工具：

```java
@SpringBootApplication
public class DwMcpServerApplication {
    
    public static void main(String[] args) {
        SpringApplication.run(DwMcpServerApplication.class, args);
    }

    @Bean
    public ToolCallbackProvider weatherTools(WeatherService weatherService) {
        return MethodToolCallbackProvider.builder()
                .toolObjects(weatherService)
                .build();
    }
}
```

### 步骤 5：运行和测试

```bash
# 启动服务器
./mvnw spring-boot:run

# 服务器将在 SSE 端点提供 MCP 服务：
# http://localhost:8080/mcp/messages
```

## 项目结构

```
src/
├── main/
│   ├── java/com/dw/
│   │   ├── DwMcpServerApplication.java  # 主应用程序
│   │   ├── ToolConfig.java             # 工具配置类
│   │   └── WeatherService.java         # 天气服务工具
│   └── resources/
│       └── application.yml             # 应用配置
└── test/
    └── java/com/dw/
        └── DwMcpServerApplicationTests.java
```

## 核心概念

### MCP 工具 (Tools)
- 使用 `@Tool` 注解标记方法
- 使用 `@ToolParam` 注解描述参数
- 工具方法可以返回任何可序列化的对象

### MCP 服务器类型
- **SYNC**: 同步处理模式
- **ASYNC**: 异步处理模式（推荐用于 WebFlux）

### MCP 能力 (Capabilities)
- **tool**: 工具执行能力
- **resource**: 资源访问能力
- **prompt**: 提示处理能力
- **completion**: 补全生成能力

## 扩展功能

### 添加新工具

1. 在服务类中创建新方法
2. 添加 `@Tool` 注解和描述
3. 使用 `@ToolParam` 描述参数
4. 工具会自动注册到 MCP 服务器

### 集成外部 API

```java
@Tool(description = "获取实时天气数据")
public WeatherInfo getRealWeather(@ToolParam(description = "城市名称") String city) {
    // 调用第三方天气 API
    // 返回结构化的天气数据
    return weatherApiClient.getWeather(city);
}
```

## 常用命令

```bash
# 编译项目
./mvnw clean compile

# 运行测试
./mvnw test

# 打包应用
./mvnw clean package

# 运行打包后的应用
java -jar target/DWMcpServer-0.0.1-SNAPSHOT.jar
```

## 参考资源

- [Spring AI 官方文档](https://docs.spring.io/spring-ai/reference/)
- [MCP 服务器文档](https://docs.spring.io/spring-ai/reference/api/mcp/mcp-server-boot-starter-docs.html)
- [Spring Boot 官方文档](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/)

## 许可证

本项目仅用于教学演示目的。

## 贡献

欢迎提交 Issue 和 Pull Request 来改进本教学项目。
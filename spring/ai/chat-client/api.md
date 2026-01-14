# Chat Client API

The `ChatClient` offers a fluent API for communicating with an AI model. It supports both a synchronous and streaming programming model.

## Creating a ChatClient

The `ChatClient` is created using a `ChatClient.Builder` object. You can obtain an autoconfigured `ChatClient.Builder` instance for any `ChatModel` Spring Boot autoconfiguration or create one programmatically.

### Autoconfigured `ChatClient.Builder`

In the simplest use case, Spring AI provides Spring Boot autoconfiguration, creating a prototype `ChatClient.Builder` bean for you to inject into your class.

### Working with Multiple Chat Models

By default, Spring AI autoconfigures a single `ChatClient.Builder` bean. You need to disable the `ChatClient.Builder` autoconfiguration by setting the property `spring.ai.chat.client.enabled=false`.

#### Multiple ChatClients with a Single Model Type

```kotlin
// Create ChatClient instances programmatically
val myChatModel: ChatModel = ... // already autoconfigured by Spring Boot
val chatClient = ChatClient.create(myChatModel)

// Or use the builder for more control
val builder = ChatClient.builder(myChatModel)
val customChatClient = builder
    .defaultSystemPrompt("You are a helpful assistant.")
    .build()
```

#### ChatClients for Different Model Types

When working with multiple AI models, you can define separate `ChatClient` beans for each model:

```kotlin
@Configuration
class ChatClientConfig {
    @Bean
    fun openAiChatClient(chatModel: OpenAiChatModel): ChatClient {
        return ChatClient.create(chatModel)
    }

    @Bean
    fun anthropicChatClient(chatModel: AnthropicChatModel): ChatClient {
        return ChatClient.create(chatModel)
    }
}
```

You can then inject these beans into your application components using the `@Qualifier` annotation.

#### Multiple OpenAI-Compatible API Endpoints

The `OpenAiApi` and `OpenAiChatModel` classes provide a `mutate()` method that allows you to create variations of existing instances with different properties.

## ChatClient Fluent API

The `ChatClient` fluent API allows you to create a prompt in three distinct ways using an overloaded `prompt` method to initiate the fluent API:

- `prompt()`: This method with no arguments lets you start using the fluent API, allowing you to build up user, system, and other parts of the prompt.
- `prompt(prompt: Prompt)`: This method accepts a `Prompt` argument, letting you pass in a `Prompt` instance that you have created using the Prompt's non-fluent APIs.
- `prompt(content: String)`: This is a convenience method similar to the previous overload. It takes the user's text content.

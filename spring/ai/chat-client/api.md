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

## ChatClient Responses

The `ChatClient` API offers several ways to format the response from the AI Model using the fluent API.

### Returning a ChatResponse

The response from the AI model is a rich structure defined by the type `ChatResponse`. It includes metadata about how the response was generated and can also contain multiple responses, known as `Generation`s, each with its own metadata. The metadata includes the number of tokens (each token is approximately 3/4 of a word) used to create the response.

```kotlin
val chatResponse = chatClient.prompt()
    .user("Tell me a joke")
    .call()
    .chatResponse()
```

### Returning an Entity

You often want to return an entity class that is mapped from the returned `String`. The `entity()` method provides this functionality.

For example, given

```kotlin
data class ActorFilms(val actor: String, val movies: List<String>)
```

You can map the AI model's output to this data class using the `entity()` method, as shown below:

```kotlin
val actorFilms = chatClient.prompt()
    .user("Generate the filmography for a random actor.")
    .call()
    .entity<ActorFilms>()
```

There is also an overloaded `entity` method with the signature `entity(ParameterizedTypeReference<T> type)` that allows you to specify types such as generic lists:

```kotlin
val actorFilms = chatClient.prompt()
    .user("Generate the filmography of 5 movies for Tom Hanks and Bill Murray.")
    .call()
    .entity<List<ActorFilms>>()
```

#### Native Structured Output

As more AI models support structured output natively, you can take advantage of this feature by using the `AdvisorParams.ENABLE_NATIVE_STRUCTURED_OUTPUT` advisor parameter when calling the `ChatClient`. You can use the `defaultAdvisors()` method on the `ChatClient.Builder` to set this parameter globally for all calls or set it per call.

### Streaming Responses

## Prompt Templates

## call() return values

## Advisors

The Advisors API provides a flexible and powerful way to intercept, modify, and enhance AI-driven interactions.

### Advisor Configuration in ChatClient

The ChatClient fluent API provides an `AdvisorSpec` interface for configuring advisors. This interface offers methods to add parameters, set multiple parameters at once, and add one or more advisors to the chain.

```kotlin
interface AdvisorSpec {
    fun param(k: String, v: Any): AdvisorSpec
    fun params(p: Map<String, Any>): AdvisorSpec
    fun advisors(vararg advisors: Advisor): AdvisorSpec
    fun advisors(advisors: List<Advisor>): AdvisorSpec
}
```

> [!IMPORTANT]
> The order in which advisors are added to the chain is crucial, as it determines the sequence of their execution. Each advisor modifies the prompt or the context in some way, and the changes made by one advisor are passed on to the next in the chain.

```kotlin
ChatClient.builder(chatModel)
    .prompt()
    .advisors(
        MessageChatMemoryAdvisor.builder(chatMemory).build(),
        QuestionAnswerAdvisor.builder(vectorStore).build()
    )
    .user(userText)
    .call()
    .content()
```

In this configuration, the `MessageChatMemoryAdvisor` will be executed first, adding the conversation history to the prompt. Then, the `QuestionAnswerAdvisor` will perform its search based on the user's question and the added conversation history, potentially providing more relevant results.

### Logging

## Chat Memory

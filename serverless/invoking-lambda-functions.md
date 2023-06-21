# Invoking lambda functions

AWS Lambda can be invoked in two different ways:
- synchrnously
- asynchronously

Let's take an example:
```javascript
exports.handler = async function(event, context) {
  return "hello, world!";
}
```

```yaml
service: demo

provider:
  name: aws
  runtime: nodejs16.x

functions:
  hello:
    handler: handler.handler
```
After deploying, we can invoke the function.

##  Synchronous invocation

The `serverless` CLI tool provides an easy way to invoke a function defined in `serverless.yml`:
```bash
$ serverless invoke -f hello
"Hello, world!"
```
The command results in a synchronous invocation.

<img width="1005" alt="Testing out editing feature in Github" src="https://github.com/giwankim/til/assets/14240606/119aef50-5504-4fea-af8a-4eaa4e6f7bdb">

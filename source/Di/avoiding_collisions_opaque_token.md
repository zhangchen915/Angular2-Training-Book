# 避免注入冲突：OpaqueToken

由于Angular允许使用令牌作为其依赖注入系统的标识符，潜在问题之一是使用相同的令牌来表示不同的实体。 例如，如果字符串`'token'`用于注入一个实体，可能完全不相关的东西也使用`'token'`注入一个不同的实体。 当Angular解析这些实体之一时，它可能正在解决错误的实体。 这种行为可能很少发生，或者当它在一个小团队中发生时很容易解决 - 但是当涉及到在同一代码库上单独工作的多个团队或来自不同来源的第三方模块时，这些冲突成为一个更大的问题。

考虑这个例子，其中主应用程序是两个模块的消费者：一个提供电子邮件服务，另一个提供日志服务。

*app/email/email.service.ts*

```typescript
export const apiConfig = 'api-config';

@Injectable()
export class EmailService {
  constructor(@Inject(apiConfig) public apiConfig) { }
}
```

*app/email/email.module.ts*

```typescript
@NgModule({
  providers: [ EmailService ],
})
export class EmailModule { }
```

电子邮件服务api需要一些由字符串`api-config`标识的配置设置，由DI系统提供。 此模块应足够灵活，以便其可由不同模块在不同应用程序中使用。 这意味着这些设置应该由应用程序特性决定，因此由导入`EmailModule`的`AppModule`提供。

*app/logger/logger.service.ts*

```typescript
export const apiConfig = 'api-config';

@Injectable()
export class LoggerService {
  constructor(@Inject(apiConfig) public apiConfig) { }
}
```

*app/logger/logger.module.ts*

```typescript
@NgModule({
  providers: [ LoggerService ],
})
export class LoggerModule { }
```

另一个服务`LoggerModule`由与创建EmailModule的团队不同的团队创建，并且还需要一个配置对象。 不出所料，他们决定对它们的配置对象使用相同的令牌，即字符串`api-config`。 为了避免两个具有相同名称的令牌之间的冲突，我们可以尝试重命名导入，如下所示。

*app/app.module.ts*

```typescript
import { apiConfig as emailApiConfig } from './email/index';
import { apiConfig as loggerApiConfig } from './logger/index';

@NgModule({
  ...
  providers: [
    { provide: emailApiConfig, useValue: { apiKey: 'email-key', context: 'registration' } },
    { provide: loggerApiConfig, useValue: { apiKey: 'logger-key' } },
  ],
  ...
})
export class AppModule { }
```

[View Example](https://plnkr.co/edit/QrvjsucT6lF6dnFUb2ag?p=preview)

当应用程序运行时，它会遇到一个冲突问题，导致两个模块获得相同的配置值，在本例中为`{apiKey：'logger-key'}`。 当主应用程序指定这些设置时，Angular将使用`loggerApiConfig`值覆盖第一个`emailApiConfig`值，因为它是最后提供的。 在这种情况下，模块实现细节泄露到父模块。 不仅如此，这些细节通过模块导出被混淆，这可能导致有问题的调试。现在该轮到`OpaqueToken`上场了。

## OpaqueToken

`OpaqueTokens`是独特的和不可变的值，允许开发人员避免注入令牌id冲突。

```typescript
import { OpaqueToken } from '@angular/core';

const name = 'token';
const token1 = new OpaqueToken(name);
const token2 = new OpaqueToken(name);

console.log(token1 === token2); // false
```

这里，不管是否将相同的值传递给令牌的构造器，它将不会导致相同的符号。

*app/email/email.module.ts*

```typescript
export const apiConfig = new OpaqueToken('api-config');

@Injectable()
export class EmailService {
  constructor(@Inject(apiConfig) public apiConfig: EmailConfig) { }
}
```

```typescript
export const apiConfig = new OpaqueToken('api-config');

@Injectable()
export class LoggerService {
  constructor(@Inject(apiConfig) public apiConfig: LoggerConfig) { }
}
```

[View Example](https://plnkr.co/edit/SHfTH9R6JVDwJKnzRFSH?p=preview)

在将标识令牌转换为`OpaqueTokens`而不改变任何其他内容之后，避免了冲突。 每个服务从根模块获取正确的配置对象，Angular现在能够区分使用相同字符串的两个令牌。
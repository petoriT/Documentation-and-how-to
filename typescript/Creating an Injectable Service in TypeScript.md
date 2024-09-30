# Creating an Injectable Service in TypeScript

This guide demonstrates how to create a service that can be injected using TypeScript, following best practices. We'll use decorators and a simple dependency injection container to achieve this.

## 1. Set up the project

First, ensure your `tsconfig.json` has the following options enabled:

```json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    // ... other options
  }
}
```

## 2. Install necessary dependencies

Install reflect-metadata, which is required for decorator metadata:

```bash
npm install reflect-metadata
npm install --save-dev @types/reflect-metadata
```

## 3. Create a simple dependency injection container

Create a file named `di-container.ts`:

```typescript
// src/di-container.ts

type Constructor<T = any> = new (...args: any[]) => T;

export class DIContainer {
  private static instance: DIContainer;
  private dependencies: Map<string, any>;

  private constructor() {
    this.dependencies = new Map();
  }

  public static getInstance(): DIContainer {
    if (!DIContainer.instance) {
      DIContainer.instance = new DIContainer();
    }
    return DIContainer.instance;
  }

  public register<T>(key: string, dependency: T): void {
    this.dependencies.set(key, dependency);
  }

  public resolve<T>(target: Constructor<T>): T {
    const tokens = Reflect.getMetadata('design:paramtypes', target) || [];
    const injections = tokens.map((token: Constructor) => this.resolve(token));
    
    return new target(...injections);
  }
}

export const container = DIContainer.getInstance();
```

## 4. Create decorators for dependency injection

Create a file named `decorators.ts`:

```typescript
// src/decorators.ts

import 'reflect-metadata';
import { container } from './di-container';

export function Injectable() {
  return function (target: any) {
    const params = Reflect.getMetadata('design:paramtypes', target) || [];
    container.register(target.name, target);
  };
}
```

## 5. Create an injectable service

Now you can create an injectable service. Let's create a simple logging service as an example:

```typescript
// src/services/logger.service.ts

import { Injectable } from '../decorators';

@Injectable()
export class LoggerService {
  log(message: string): void {
    console.log(`[LOG]: ${message}`);
  }

  error(message: string): void {
    console.error(`[ERROR]: ${message}`);
  }
}
```

## 6. Use the injectable service

Here's how you can use the injectable service in another class:

```typescript
// src/user.service.ts

import { Injectable } from './decorators';
import { LoggerService } from './services/logger.service';

@Injectable()
export class UserService {
  constructor(private logger: LoggerService) {}

  createUser(username: string): void {
    // Some user creation logic
    this.logger.log(`User created: ${username}`);
  }
}
```

## 7. Resolve and use the service

Finally, you can resolve and use the service like this:

```typescript
// src/index.ts

import 'reflect-metadata';
import { container } from './di-container';
import { UserService } from './user.service';

const userService = container.resolve(UserService);
userService.createUser('JohnDoe');
```

## Best Practices

1. **Single Responsibility**: Each service should have a single responsibility. For example, the `LoggerService` is only responsible for logging.

2. **Interface Segregation**: Consider defining interfaces for your services and injecting the interface rather than the concrete class. This allows for easier mocking in tests and switching implementations.

3. **Avoid Circular Dependencies**: Be careful not to create circular dependencies between your services. If you need to, consider restructuring your code or using a mediator pattern.

4. **Use Meaningful Names**: Name your services clearly based on their functionality. For example, `UserService`, `AuthenticationService`, etc.

5. **Keep Services Stateless**: As much as possible, keep your services stateless. This makes them easier to reason about and test.

6. **Unit Testing**: Write unit tests for your services. Dependency injection makes this easier as you can mock dependencies.

By following these practices, you can create a modular, testable, and maintainable codebase using dependency injection in TypeScript.
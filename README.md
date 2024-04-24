# nestjs with clerk 

This repository is use clerk as authentication tool backend

## pre-install

```shell
pnpm i -S @clerk/clerk-sdk-node@4.13.14 cookie-parser
pnpm i -D @types/cookie-parser
pnpm i -S @nestjs/config
```

## apply clerk secret key from clerk.com

1. go to clerk.com register account for app
2. copy CLERK_SECRET_KEY to .env


## use clerk sdk in service 

```typescript
import { Injectable } from '@nestjs/common';
import clerkClient from '@clerk/clerk-sdk-node';
@Injectable()
export class AppService {
  async getUsers() {
    return clerkClient.users.getUserList();
  }
}
```
## use clerk sdk in authorization guard

```typescript
import clerkClient from '@clerk/clerk-sdk-node';
import {
  CanActivate,
  ExecutionContext,
  Injectable,
  Logger,
} from '@nestjs/common';

@Injectable()
export class ClerkAuthGuard implements CanActivate {
  private readonly logger = new Logger(ClerkAuthGuard.name);
  async canActivate(context: ExecutionContext) {
    const request = context.switchToHttp().getRequest();
    try {
      await clerkClient.verifyToken(request.cookies.__session);
      return true;
    } catch (error) {
      this.logger.error(error);
      return false;
    }
  }
}
```
---
title: "Integrate NestJS AuthGuard & RolesGuard"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### 1. Install Cognito JWT Verification Package

Inside your NestJS backend directory (`apps/server`), install `aws-jwt-verify`:

```bash
pnpm add aws-jwt-verify
```

#### 2. Create AuthGuard (`src/auth/auth.guard.ts`)

`AuthGuard` extracts the `Authorization: Bearer <token>` header, verifies token signature against Cognito JWKS, and attaches the user payload to `request.user`:

```typescript
import { CanActivate, ExecutionContext, Injectable, UnauthorizedException } from '@nestjs/common';
import { CognitoJwtVerifier } from 'aws-jwt-verify';

@Injectable()
export class AuthGuard implements CanActivate {
  private verifier = CognitoJwtVerifier.create({
    userPoolId: process.env.COGNITO_USER_POOL_ID!,
    tokenUse: 'access',
    clientId: process.env.COGNITO_CLIENT_ID!,
  });

  async canActivate(context: ExecutionContext): Promise<boolean> {
    const request = context.switchToHttp().getRequest();
    const authHeader = request.headers.authorization;

    if (!authHeader || !authHeader.startsWith('Bearer ')) {
      throw new UnauthorizedException('Missing or invalid Authorization header');
    }

    const token = authHeader.split(' ')[1];

    try {
      const payload = await this.verifier.verify(token);
      request.user = {
        sub: payload.sub,
        email: payload.username,
        role: payload['custom:role'] || 'TENANT',
      };
      return true;
    } catch (error) {
      throw new UnauthorizedException('Invalid or expired Cognito JWT Token');
    }
  }
}
```

#### 3. Create Roles Decorator & RolesGuard (`src/auth/roles.guard.ts`)

```typescript
// roles.decorator.ts
import { SetMetadata } from '@nestjs/common';
export const Roles = (...roles: string[]) => SetMetadata('roles', roles);

// roles.guard.ts
import { CanActivate, ExecutionContext, Injectable, ForbiddenException } from '@nestjs/common';
import { Reflector } from '@nestjs/core';

@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const requiredRoles = this.reflector.get<string[]>('roles', context.getHandler());
    if (!requiredRoles) return true;

    const { user } = context.switchToHttp().getRequest();
    if (!user || !requiredRoles.includes(user.role)) {
      throw new ForbiddenException('Access denied: insufficient permissions');
    }
    return true;
  }
}
```

#### 4. Protecting Controller Routes

Apply `@UseGuards(AuthGuard, RolesGuard)` and `@Roles('MANAGER')` on protected endpoints in `property.controller.ts`:

```typescript
@Controller('properties')
export class PropertyController {

  @Post()
  @UseGuards(AuthGuard, RolesGuard)
  @Roles('MANAGER')
  async createProperty(@Body() dto: CreatePropertyDto, @Req() req: any) {
    return this.propertyService.create(dto, req.user.sub);
  }
}
```

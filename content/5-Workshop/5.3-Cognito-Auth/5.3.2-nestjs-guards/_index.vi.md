---
title: "Tích hợp NestJS AuthGuard & RolesGuard"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

#### 1. Cài đặt thư viện xác thực JWT của Cognito

Tại thư mục backend NestJS (`apps/server`), cài đặt thư viện `aws-jwt-verify`:

```bash
pnpm add aws-jwt-verify
```

#### 2. Xây dựng AuthGuard (`src/auth/auth.guard.ts`)

`AuthGuard` bóc tách header `Authorization: Bearer <token>`, xác thực chữ ký của token với Cognito JWKS public key và đính kèm payload vào đối tượng `request.user`:

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
      throw new UnauthorizedException('Thiếu hoặc sai định dạng Authorization header');
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
      throw new UnauthorizedException('Cognito JWT Token không hợp lệ hoặc đã hết hạn');
    }
  }
}
```

#### 3. Tạo Decorator & RolesGuard (`src/auth/roles.guard.ts`)

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
      throw new ForbiddenException('Truy cập bị từ chối: không đủ quyền hạn');
    }
    return true;
  }
}
```

#### 4. Bảo vệ router controller

Đính kèm `@UseGuards(AuthGuard, RolesGuard)` và `@Roles('MANAGER')` vào các API tạo bất động sản trong `property.controller.ts`:

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

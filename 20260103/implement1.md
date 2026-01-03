# 단계 1 상세 구현 명세서: 개발 환경 구성 및 헬로 월드

작성일: 2026-01-03

목표: Turborepo 기반의 모노레포를 구축하고, 로컬 Docker DB를 실행하며, Next.js(Frontend)와 NestJS(Backend) 간의 데이터 통신을 성공시킨다.

## 0. 사전 준비 사항 (Prerequisites)

개발 시작 전 다음 도구들이 설치되어 있어야 합니다.

- **Node.js:** v20.x (LTS) 이상
    
- **Docker Desktop:** 최신 버전 (PostgreSQL 실행용)
    
- **Package Manager:** `pnpm` (Monorepo 관리에 효율적, `npm i -g pnpm`)
    
- **IDE:** VS Code (권장 확장 프로그램: ESLint, Prettier, Docker)
    

## 1. 프로젝트 구조 초기화 (Monorepo)

Turborepo를 사용하여 프론트엔드와 백엔드를 하나의 저장소에서 관리합니다.

### 1-1. Turborepo 생성

터미널에서 다음 명령어를 실행하여 프로젝트를 생성합니다.

```
# 프로젝트 생성 (패키지 매니저는 pnpm 선택 권장)
npx create-turbo@latest my-project
cd my-project
```

### 1-2. 디렉토리 정리 및 앱 생성

기본 생성된 `apps/docs`, `apps/web`을 제거하고 우리가 사용할 프레임워크로 다시 생성합니다.

```
# 기존 앱 제거
rm -rf apps/*

# 1. Backend 생성 (NestJS)
cd apps
npx @nestjs/cli new api --package-manager pnpm
# (설정: 기본값 선택)

# 2. Frontend 생성 (Next.js)
npx create-next-app@latest web
# (설정: TypeScript: Yes, ESLint: Yes, Tailwind: Yes, src/: Yes, App Router: Yes)

cd ..
```

### 1-3. 최종 디렉토리 구조

```
my-project/
├── apps/
│   ├── api/ (NestJS - Port 8080 예정)
│   └── web/ (Next.js - Port 3000)
├── packages/
│   ├── ui/
│   └── ...
├── docker-compose.yml (추가 예정)
├── turbo.json
└── package.json
```

## 2. Docker를 이용한 데이터베이스 설정

로컬 개발 환경의 일관성을 위해 Docker Compose로 PostgreSQL을 실행합니다.

### 2-1. docker-compose.yml 작성

프로젝트 루트(`my-project/`)에 파일을 생성합니다.

```
version: '3.8'

services:
  postgres:
    image: postgres:16-alpine
    container_name: my-project-db
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

### 2-2. 실행 및 확인

```
# 백그라운드 실행
docker-compose up -d

# 실행 확인
docker ps
# -> 'my-project-db' 컨테이너가 Up 상태인지 확인
```

## 3. Backend 구현 (NestJS)

API 서버의 포트를 변경하고, CORS를 허용하며, 헬스 체크 API를 작성합니다.

### 3-1. 포트 및 CORS 설정 (`apps/api/src/main.ts`)

기본 3000번 포트는 Next.js와 겹치므로 8080으로 변경합니다.

```
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  
  // CORS 설정: 프론트엔드(3000)에서의 요청 허용
  app.enableCors({
    origin: 'http://localhost:3000',
    credentials: true,
  });

  await app.listen(8080);
  console.log(`Application is running on: http://localhost:8080`);
}
bootstrap();
```

### 3-2. Health Check API (`apps/api/src/app.controller.ts`)

기본 컨트롤러를 수정하여 간단한 JSON을 반환하게 합니다.

```
import { Controller, Get } from '@nestjs/common';

@Controller()
export class AppController {
  @Get('health')
  getHealth(): { status: string; timestamp: string } {
    return { 
      status: 'OK', 
      timestamp: new Date().toISOString() 
    };
  }
}
```

## 4. Frontend 구현 (Next.js)

백엔드 API를 호출하여 화면에 데이터를 표시합니다.

### 4-1. 환경 변수 설정 (`apps/web/.env.local`)

```
NEXT_PUBLIC_API_URL=http://localhost:8080
```

### 4-2. API 호출 및 UI 표시 (`apps/web/src/app/page.tsx`)

서버 컴포넌트(Server Component)에서 데이터를 fetch 합니다.

```
export default async function Home() {
  // 백엔드 API 호출 (캐시 방지 옵션 추가)
  const res = await fetch(`${process.env.NEXT_PUBLIC_API_URL}/health`, {
    cache: 'no-store',
  });
  
  // 에러 처리
  if (!res.ok) {
    return <div>API Server Error</div>;
  }

  const data = await res.json();

  return (
    <main className="flex min-h-screen flex-col items-center justify-center p-24">
      <h1 className="text-4xl font-bold mb-8">System Status</h1>
      <div className="p-6 border rounded-lg shadow-lg bg-gray-50">
        <p className="text-xl">
          Backend Status: <span className="text-green-600 font-bold">{data.status}</span>
        </p>
        <p className="text-sm text-gray-500 mt-2">
          Timestamp: {data.timestamp}
        </p>
      </div>
    </main>
  );
}
```

## 5. 실행 및 검증 (Verification)

### 5-1. 전체 실행

루트 디렉토리에서 Turbo를 이용해 동시에 실행합니다.

```
pnpm dev
```

- `apps/api`: localhost:8080 실행됨
    
- `apps/web`: localhost:3000 실행됨
    

### 5-2. 최종 체크리스트

1. [ ] **DB:** 터미널에서 `docker ps` 입력 시 postgres 컨테이너가 실행 중인가?
    
2. [ ] **Backend:** 브라우저에서 `http://localhost:8080/health` 접속 시 JSON(`{"status":"OK", ...}`)이 보이는가?
    
3. [ ] **Frontend:** 브라우저에서 `http://localhost:3000` 접속 시 "Backend Status: OK"가 화면에 렌더링 되는가?
    
4. [ ] **Hot Reload:** `apps/web/src/app/page.tsx`의 텍스트를 수정하고 저장했을 때, 브라우저에 즉시 반영되는가?
    

**Troubleshooting Tip:**

- **CORS 에러:** `apps/api/src/main.ts`의 `origin` 설정이 `http://localhost:3000`과 정확히 일치하는지 확인하세요 (끝에 슬래시 `/` 없음).
    
- **Fetch 에러:** Next.js 서버(Node.js 환경)가 `localhost:8080`을 찾지 못할 경우, `http://127.0.0.1:8080`으로 변경해 보세요.
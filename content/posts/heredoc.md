---
title: "Heredoc (Here Document) 완전 가이드"
date: "2024-08-09T16:07:19+09:00"
author: "soomtong"
authorTwitter: "soomtong"
tags: ["shell", "hererdoc"]
keywords: ["shell", "pipe", "terminal"]
description: "Heredoc은 쉘 스크립팅에서 손쉽게 다중 라인 텍스트를 처리하는 강력한 기능입니다."
showFullContent: false
readingTime: true
hideComments: false
---

긴 말 필요 없습니다. 빠르게 예제를 본 후 자세히 알아보겠습니다.

> 라인 입력 이벤트를 기준으로 동작하기 때문에 마지막 글자 타이핑 후 엔터 키 누르기가 필요합니다.

## 사용 예시

```bash
# 파일에 내용 쓰기
# 'EOF' 대신 EOF 사용 가능
# ' 사용 미사용 차이는 있음
# XXX end 등 임의의 식별자를 사용할 수 있음
cat <<'EOF' > output.txt
This is line 1
This is line 2
EOF

# 또는 이렇게도 사용
# 파이프 연산자 주변 공백은 무시됨
cat > output.txt << 'END'
This is line 1
This is line 2
END

# cat 이 아니어도 pipe 가능한 어떤 명령이라도 사용할 수 있음
xh http://localhost:1337/api/chat/completions <<EOF
{ "model": "gemma-3n-e4b-it-lm-4bit", 
  "messages": [ { "role": "user", "content": "Why is the sky blue?" } ] }
EOF

# 변수와 함께 사용
NAME="John"
cat <<EOF
Hello, $NAME!
Today is $(date)
EOF

# 스크립트에서 도움말 출력
cat <<'HELP'
Usage: script.sh [options]
Options:
  -h    Show this help
  -v    Verbose mode
HELP
```

### 장점

1. 다중 라인 텍스트를 쉽게 처리할 수 있습니다.
2. 임시 파일 생성 없이 긴 텍스트를 명령에 전달할 수 있습니다.
3. 스크립트 가독성이 향상됩니다.
4. 특수 문자 이스케이핑을 걱정할 필요가 없습니다.

이 문법은 쉘 스크립팅에서 매우 유용하며, __설정 파일 생성__, 도움말 출력, 템플릿 처리 등에 자주 사용됩니다.

## 기본 문법

```bash
command <<DELIMITER
content line 1
content line 2
DELIMITER
```

## 주요 특징

### 1. 구분자(Delimiter) 규칙
- 구분자는 임의의 문자열을 사용할 수 있습니다. 관례적으로 `EOF`, `END`, `HEREDOC` 등을 사용
- 종료 구분자는 **반드시 줄의 맨 앞**에 위치해야 합니다

```bash
# 다양한 구분자 예시
cat <<END
This is content
END

cat <<MARKER
Another content
MARKER

cat <<123
Numbers as delimiter
123
```

### 2. Quoted vs Unquoted Heredoc

#### Unquoted Heredoc (`<<EOF`)
```bash
NAME="Alice"
AGE=30

cat <<EOF
Hello, $NAME!
You are $AGE years old.
Today is $(date +%Y-%m-%d)
Current directory: $(pwd)
EOF
```

출력:
```
Hello, Alice!
You are 30 years old.
Today is 2024-01-15
Current directory: /home/user
```

#### Quoted Heredoc (`<<'EOF'`)
```bash
NAME="Alice"
# bat 을 사용하는 경우 예시
cat -l perl <<'EOF'
Hello, $NAME!
Current directory: $(pwd)
Special characters: \ $ ` "
EOF
```

출력:
```
Hello, $NAME!
Current directory: $(pwd)
Special characters: \ $ ` "
```

### 3. 들여쓰기 제거 (`<<-`)

```bash
# 탭으로 들여쓰기된 heredoc
if true; then
    cat <<- EOF
	This line starts with a tab
	This line also starts with a tab
	EOF
fi
```

**주의:** `<<-`는 **탭만** 제거하고, 공백은 제거하지 않습니다.

## 실용적인 사용 예시

### 1. 설정 파일 생성

```bash
# nginx 설정 파일 생성
cat > /etc/nginx/sites-available/mysite << 'NGINX_CONF' 
server {
    listen 80;
    server_name example.com;
    
    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
NGINX_CONF
```

### 2. SQL 스크립트 실행

```bash
mysql -u root -p <<'SQL'
CREATE DATABASE IF NOT EXISTS myapp;
USE myapp;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO users (username, email) VALUES 
('john_doe', 'john@example.com'),
('jane_smith', 'jane@example.com');
SQL
```

### 3. 도움말 메시지

```bash
show_help() {
    cat <<'HELP'
MyScript v1.0 - A sample script

USAGE:
    myscript.sh [OPTIONS] <input_file>

OPTIONS:
    -h, --help      Show this help message
    -v, --verbose   Enable verbose output
    -o, --output    Specify output file
    -f, --force     Force overwrite existing files

EXAMPLES:
    myscript.sh input.txt
    myscript.sh -v -o output.txt input.txt
    myscript.sh --force --output result.txt data.txt

For more information, visit: https://example.com/docs
HELP
}
```

### 4. JSON/XML 데이터 생성

```bash
# JSON 생성
USER_ID=123
USER_NAME="John Doe"

cat <<EOF > user.json
{
    "id": $USER_ID,
    "name": "$USER_NAME",
    "email": "${USER_NAME,,}@example.com",
    "created_at": "$(date -Iseconds)",
    "preferences": {
        "theme": "dark",
        "notifications": true
    }
}
EOF
```

### 5. 이메일 템플릿

```bash
send_notification() {
    local recipient=$1
    local subject=$2
    local message=$3
    
    mail -s "$subject" "$recipient" <<EMAIL
Dear User,

$message

This is an automated message from our system.
Generated at: $(date)

Best regards,
System Administrator

---
This email was sent automatically. Please do not reply.
EMAIL
}
```

## 고급 사용법

### 1. 함수와 함께 사용

```bash
generate_dockerfile() {
    local base_image=$1
    local app_name=$2
    
    cat <<DOCKERFILE
FROM $base_image

WORKDIR /app
COPY package*.json ./
RUN npm install

COPY . .
EXPOSE 3000

CMD ["npm", "start"]

# Generated for $app_name at $(date)
DOCKERFILE
}

# 사용
generate_dockerfile "node:lts-alpine" "my-app" > Dockerfile
```

### 2. 배열과 루프 활용

```bash
SERVICES=("web" "api" "database" "cache")

for service in "${SERVICES[@]}"; do
    cat <<SERVICE_CONF > "docker-compose-$service.yml"
version: '3.8'
services:
  $service:
    image: $service:latest
    container_name: ${service}_container
    restart: unless-stopped
    environment:
      - NODE_ENV=production
      - SERVICE_NAME=$service
SERVICE_CONF
done
```

### 3. 조건부 내용

```bash
DEBUG=${DEBUG:-false}

cat <<SCRIPT > run.sh
#!/bin/bash
set -e

echo "Starting application..."

$([ "$DEBUG" = "true" ] && cat <<'DEBUG_SECTION'
echo "Debug mode enabled"
set -x
DEBUG_SECTION
)

echo "Application started successfully"
SCRIPT
```

## 주의사항과 팁

### 1. 들여쓰기 주의
```bash
# 잘못된 예: 종료 구분자가 들여쓰기됨
if true; then
    cat <<EOF
Content here
    EOF  # 이것은 작동하지 않음
fi

# 올바른 예
if true; then
    cat <<EOF
Content here
EOF
fi
```

### 2. 특수 문자 처리
```bash
# Quoted heredoc에서는 모든 것이 문자 그대로 처리됨
cat <<'SPECIAL'
Backslash: \
Dollar: $
Backtick: `
Quote: "
SPECIAL
```

### 3. 파이프와 리다이렉션
```bash
# 파이프 사용
cat << EOF | grep "pattern"
line with pattern
another line
line with pattern again
EOF

# 파일로 리다이렉션
cat > output.txt <<EOF 
This goes to file
EOF

cat <<EOF > output.txt
This goes to file
EOF

# 추가 모드로 리다이렉션
cat <<EOF >> log.txt
This is appended
EOF
```

### 4. 변수에 heredoc 할당
```bash
# 변수에 heredoc 내용 저장
TEMPLATE=$(cat <<'TEMPLATE'
Hello, {{NAME}}!
Your order #{{ORDER_ID}} is ready.
Total: ${{AMOUNT}}
TEMPLATE
)

echo "$TEMPLATE"
```

Heredoc은 쉘 스크립팅에서 매우 유용한 기능으로, 복잡한 텍스트 처리나 설정 파일 생성, 템플릿 처리 등에 광범위하게 활용됩니다. 익숙해지긴 힘들지도 모르지만 알고 있으면 유용하게 사용할 수 있습니다.
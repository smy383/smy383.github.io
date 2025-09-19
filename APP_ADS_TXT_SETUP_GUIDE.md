# app-ads.txt 설정 가이드 (AdMob 광고 인증)

## 문제 상황
Google AdMob을 사용하기 위해서는 app-ads.txt 파일을 개발자 웹사이트의 루트 디렉토리에 배치해야 합니다. 이 파일은 광고 사기를 방지하고 승인된 광고 네트워크만 앱에서 광고를 게재할 수 있도록 합니다.

## 일반적인 오류 케이스
GitHub Pages를 사용할 때 다음과 같은 오류가 발생할 수 있습니다:
- `https://username.github.io/repository-name/` 형태의 URL을 사용하는 경우
- AdMob이 `/repository-name/app-ads.txt`가 아닌 루트 `/app-ads.txt`에서 파일을 찾는 문제

## 해결 방법: username.github.io 저장소 생성

### 1단계: 새 저장소 생성 및 초기화

```bash
# 임시 디렉토리 생성 및 이동
cd /tmp
mkdir username.github.io  # username을 실제 GitHub 사용자명으로 변경
cd username.github.io

# Git 저장소 초기화
git init
```

### 2단계: app-ads.txt 파일 생성

```bash
# app-ads.txt 파일 생성
# AdMob에서 제공하는 게시자 ID를 아래 형식에 맞춰 입력
echo "google.com, pub-XXXXXXXXXXXXXXXX, DIRECT, f08c47fec0942fa0" > app-ads.txt
```

**중요**: `pub-XXXXXXXXXXXXXXXX`를 실제 AdMob 게시자 ID로 교체하세요.

### 3단계: 간단한 index.html 생성 (선택사항)

```bash
cat > index.html << 'EOF'
<!DOCTYPE html>
<html>
<head>
    <title>Developer Site</title>
    <meta charset="utf-8">
</head>
<body>
    <h1>Developer Site</h1>
    <p>This page hosts app-ads.txt for AdMob verification.</p>
</body>
</html>
EOF
```

### 4단계: Git 커밋

```bash
# 파일 추가
git add .

# 커밋
git commit -m "Initial commit with app-ads.txt for AdMob"
```

### 5단계: GitHub 저장소 생성 및 푸시

GitHub CLI(gh)를 사용하는 방법:

```bash
# GitHub 저장소 생성 및 푸시 (한 번에 처리)
gh repo create username/username.github.io --public --source=. --remote=origin --push
```

또는 수동으로:

```bash
# 1. GitHub 웹사이트에서 username.github.io 저장소 생성
# 2. 원격 저장소 추가
git remote add origin https://github.com/username/username.github.io.git

# 3. 푸시
git branch -M main
git push -u origin main
```

### 6단계: GitHub Pages 활성화 확인

대부분의 경우 `username.github.io` 이름의 저장소는 자동으로 GitHub Pages가 활성화됩니다. 만약 활성화되지 않았다면:

```bash
# GitHub CLI로 Pages 활성화
gh api repos/username/username.github.io/pages -X POST -f source.branch=main -f source.path=/
```

또는 GitHub 웹사이트에서:
1. Settings → Pages
2. Source: Deploy from a branch
3. Branch: main, Folder: / (root)

### 7단계: 배포 확인

```bash
# 1-2분 정도 대기 후 확인
curl -I https://username.github.io/app-ads.txt

# 정상 응답시: HTTP/2 200
# 파일 내용 확인
curl https://username.github.io/app-ads.txt
```

## 검증 체크리스트

### ✅ 성공 기준
- [ ] `https://username.github.io/app-ads.txt` URL이 HTTP 200 응답
- [ ] 파일 내용이 정확한 형식: `google.com, pub-XXXXXXXXXXXXXXXX, DIRECT, f08c47fec0942fa0`
- [ ] AdMob 대시보드에서 "app-ads.txt 파일을 찾을 수 없음" 오류 해결

### 🔍 문제 해결

#### 404 오류가 계속 발생하는 경우
1. 저장소 이름이 정확히 `username.github.io`인지 확인
2. GitHub Pages가 활성화되어 있는지 확인
3. 배포에 시간이 걸릴 수 있으므로 5-10분 대기

#### AdMob이 여전히 파일을 찾지 못하는 경우
1. Google Play Console에서 개발자 웹사이트 URL 확인
2. URL 끝에 슬래시(/)가 있는지 없는지 확인
3. AdMob 크롤링에 최대 24시간이 걸릴 수 있음

## 추가 팁

### 여러 앱을 위한 app-ads.txt 관리
같은 개발자 계정의 여러 앱을 위해 하나의 app-ads.txt 파일에 여러 줄을 추가할 수 있습니다:

```
google.com, pub-XXXXXXXXXXXXXXXX, DIRECT, f08c47fec0942fa0
google.com, pub-YYYYYYYYYYYYYYYY, DIRECT, f08c47fec0942fa0
```

### 파일 업데이트 방법

```bash
cd /tmp/username.github.io
# app-ads.txt 수정
echo "google.com, pub-NEWPUBLISHERID, DIRECT, f08c47fec0942fa0" >> app-ads.txt
git add app-ads.txt
git commit -m "Update app-ads.txt with new publisher ID"
git push
```

## 실제 사용 예시

이 가이드로 실제 설정한 예:
- GitHub 저장소: https://github.com/smy383/smy383.github.io
- app-ads.txt URL: https://smy383.github.io/app-ads.txt
- AdMob Publisher ID: pub-3519978756490462

## 참고 자료
- [IAB Tech Lab app-ads.txt 사양](https://iabtechlab.com/app-ads-txt/)
- [Google AdMob app-ads.txt 가이드](https://support.google.com/admob/answer/9363762)
- [GitHub Pages 문서](https://docs.github.com/en/pages)
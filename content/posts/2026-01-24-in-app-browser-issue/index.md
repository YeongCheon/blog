---
layout: post
title:  "카카오톡, 네이버 인앱 브라우저 호환성 문제"
date:   "2026-01-24 12:00:00 +0900"
tags: ["in-app-browser", "angular"]
---
# 카카오톡, 네이버 인앱 브라우저 호환성 문제

## 개요
카카오톡, 네이버와 같은 앱에서는 인앱 브라우저를 지원합니다(~~안했으면 좋겠다~~). 다만 웹표준을 완벽하게 지키는 것은 아니고 [Notification](https://caniuse.com/notifications), [Geolocation](https://caniuse.com/geolocation)같은 일부 API를 지원하지 않습니다. 만약 이를 고려하지 않고 firebase의 [getMessaging](https://firebase.google.com/docs/cloud-messaging/send-message?hl=ko)같은 함수를 앱 init 단계에서 사용한다면 의도치 않은 오류가 발생하여 앱이 제대로 동작하지 않을 수 있습니다.

## 문제 재현
```typescript
import { Component, OnInit } from '@angular/core';
import { Messaging } from '@angular/fire/messaging';
import { getMessaging, Messaging } from 'firebase/messaging';
import { environment } from 'src/environments/environment';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  ngOnInit(): void {
    const firebaseConfig: FirebaseOptions = environment.firebase;
    const app = initializeApp(firebaseConfig);
    
    const messaging = getMessaging(app); // 인 앱 브라우저에서 error 발생!
  }
}
```

## 해결 방법
실제로 사용하지 전에 해당 API를 지원하는지 확인해야 합니다.

```typescript
import { Component, OnInit } from '@angular/core';
import { Messaging } from '@angular/fire/messaging';
import { getMessaging, Messaging } from 'firebase/messaging';
import { environment } from 'src/environments/environment';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  ngOnInit(): void {
    const firebaseConfig: FirebaseOptions = environment.firebase;
    const app = initializeApp(firebaseConfig);

    // Notification API 지원 여부 확인
    if(window?.Notification) {
      const messaging = getMessaging(app); // 이제 안전하게 사용 가능
    }    
  }
}
```

## 결론
Notification API는 사실 대부분의 브라우저에서 지원하기에 무심코 넘어가기 쉽지만 앱에서는 반드시 지원 여부를 확인해야 합니다. Geolocation API도 마찬가지입니다. 앱에서 사용하는 모든 API를 지원 여부를 확인하고 사용해야 합니다.

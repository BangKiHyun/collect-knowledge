# [Spring Boot] FCM Push 메시지 보내기

푸시 알림은 Server에서 유저의 device로 정보를 보내는 것을 말한다.

구글이 제공하는 `Firebase Cloud Messaging`(**FCM**)을 이용하면, 서버와 같은 외부에서 내가 소유한 앱이 설치된 기기로 1개 이상의 메시지를 전송할 수 있다.

FCM은 완전히 무제한으로 무료 제공된다. 크로스 플랫폼을 지원하여 **Android**, **iOS**, **Chrome** 기기에 메시지를 전송할 수 있다.

이번 포스팅에서는 **Spring Boot** 기반 프로젝트에서 **FCM**을 이용한 메시지 전송 방법을 적어보려 한다.

</br >

## Firebase 설정

Firebase에서 앱 서버로 푸시를 보내려면 Access Token을 발급받아야 한다.

- 우선 Firebase 콘솔에 접속하여 메시지를 전송할 앱을 선택한다. 없다면 앱을 새로 만들어야한다.
- 다음으로 설정을 클릭한 후, 새 비공개 키 생성을 클릭한 후, 키 생성을 클릭하면 Servcie Account JSON 파일을 다룬로드할 수 있다.
- 다운로드한 파일은 프로젝트 내 **/src/main/resources**에 복사해주면 된다.

![image](https://user-images.githubusercontent.com/43977617/133068743-0b074d22-49f2-41b9-83b7-e8401b93e263.png)

</br >

## Device Token을 이용한 메시지 보내는 방법

Device Token을 이용한 메시지 보내는 방법에 대해서 설명해보고자 한다.

해당 방법은 특정 Device Token에만 Push 메시지를 보낸다.

</br >

## build.bradle

build.gradle에 다음 내용을 추가하자.

![image](https://user-images.githubusercontent.com/43977617/133199597-9bd4eec5-1f43-4003-8151-8991e15e2849.png)

</br >

## FirebaseConfig 작성

FCM이 HTTP v1으로 업데이트 되면서, 기존 API KEY를 이용하던 방법에서 AccessToken을 통한 인증을 하도록 변경되었다.

그래서 Firebase로 부터 AccessToken을 가져온뒤, 그것을 Header에 포함시켜 Push알림을 요청할 것이다.

AccessToken을 `GoogleCredentials`에서 얻어올 수 있다. `GoogleCredentials`은 GoogleApi를 사용하기 위해서 oauth2를 이용해 인증한 대상을 나타내는 객체다.

`GoogleCredentials`은 다음과 같이 설정해줬다.

```java
@Configuration
public class FirebaseConfig {

    @Bean
    public GoogleCredentials googleCredentials() throws IOException {
        String firebaseConfigPath = "firebase/firebase_service_key.json";
        return GoogleCredentials
                .fromStream(new ClassPathResource(firebaseConfigPath).getInputStream())
                .createScoped(Arrays.asList("https://www.googleapis.com/auth/cloud-platform"));
    }

    @Bean
    public OkHttpClient okHttpClient() {
        return new OkHttpClient();
    }
}
```

`GoogleCredentials`에 앞서 다운받은 JSON 파일을 넣어줬다.

그리고, createdScoped를 통해 서버에서 필요로 하는 권한을 지정해줬다. 권한의 종류는 아래와 같다.

![image](https://user-images.githubusercontent.com/43977617/133070776-43a5f387-69b8-4f43-8d1a-4e7adbcba27d.png)

</br >

## Push 메시지 설정

Push 메시지를 보내기 전에, FCM에서 설정한 Request Body를 만들어야 한다.

Request Body JSON 형식은 다음과 같다.

![image](https://user-images.githubusercontent.com/43977617/133072361-0256b46c-e0b6-4fb7-b030-0d5fed6d5544.png)

위의 형식을 만족하는 클래스를 만들어주자.

```java
import com.google.firebase.messaging.Message;

@Getter
public class FcmMessage {

    @Key(value = "validate_only")
    private boolean validateOnly;

    @Key(value = "message")
    private Message message;

    @Builder
    public FcmMessage(boolean validateOnly, Message message) {
        this.validateOnly = validateOnly;
        this.message = message;
    }
}
```

</br >

## 메시지 전송 서비스

자 이제 준비는 끝났다. 이제 메시지 전송을 위한 코드를 작성해볼 차례다.

```java
@Service
@RequiredArgsConstructor
public class FirebaseCloudMessageService {

    private static final String FCM_SEND_ENDPOINT = "https://fcm.googleapis.com/v1/projects/toy-matcherloper/messages:send";

    private final GoogleCredentials googleCredentials;
    private final OkHttpClient client;
    private final ObjectMapper objectMapper;

    public void sendMessageTo(String targetToken, String title, String body) throws IOException {
        RequestBody requestBody = makeRequestBody(makeMessage(targetToken, title, body));
        final Request request = makeRequest(requestBody);
        Response response = client.newCall(request)
                .execute();
        log.info(response.body().string());
        }
    }

    private String makeMessage(String targetToken, String title, String body) throws JsonProcessingException {
        FcmMessage fcmMessage = FcmMessage.builder()
                .message(Message.builder()
                        .setToken(targetToken)
                        .setNotification(new Notification(title, body))
                        .build())
                .validateOnly(false)
                .build();
        return objectMapper.writeValueAsString(fcmMessage);
    }

    private RequestBody makeRequestBody(String message) {
        return RequestBody.create(message,
                MediaType.get("application/json; charset=utf-8"));
    }

    private Request makeRequest(RequestBody requestBody) throws IOException {
        return new Request.Builder()
                .url(FCM_SEND_ENDPOINT)
                .post(requestBody)
                .addHeader(HttpHeaders.AUTHORIZATION, "Bearer " + googleCredentials.getAccessToken())
                .addHeader(HttpHeaders.CONTENT_TYPE, "application/json; UTF-8")
                .build();
    }
}
```

위 코드에 send endpoint가 있다.send endpoint는 다음과 같이 구성된다.

~~~java
POST https://fcm.googleapis.com/v1/projects/[myproject-name]/messages:send
~~~

</br >

## 사용방법

사용 방법은 `FirebaseCloudMessageService`객체를 생성하고 `sendMessageTo()`메서드를 호출하면 된다.

`sendMessageTo()`메서드에는 Push 알림을 보낼 Deviece Token, 메시지에 보여줄 Title, Body를 넣어주면 된다.

</br >

참고로 FCM은 topic이라는 개념을 통해 멀티캐스트 전송을 지원한다. topic을 이용한 방법은 다음 포스팅을 통해 설명해야겠다.


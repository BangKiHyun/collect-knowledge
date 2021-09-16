

# [Spring Boot] FCM Push 메시지 보내기 (Topic)

이전 포스팅에서 Device Token을 통해 FCM Push 메시지 보내는 방법에 대해 설명했었다.

이번에는 Topic이라는 개념을 통해 메시지를 보내는 방법에 대해 설명하고자 한다.

설명하기에 앞서 해당 포스팅([FCM Push 메시지 보내기](https://roomenergy.tistory.com/64))에 정리한 Firebase 관련 설정을 해줘야 한다.

</br >

## build.gradle

Firebase 설정을 해줬다면 build.gradle에 다음 내용을 추가하자.

![image](https://user-images.githubusercontent.com/43977617/133068904-8b4e239d-046b-4270-bea6-1d22ebb77cb5.png)

</br >

`FirebaseConfig`를 작성하기에 앞서 `FcmSubscribeService`를 봐보자.

```java
@Service
public class FcmSubscribeService {
    public String subscribeToTopic(List<String> tokens, String topic) {
        ApiFuture<TopicManagementResponse> response = FirebaseMessaging
                .getInstance()
                .subscribeToTopicAsync(tokens, topic);
        return response.toString();
    }

    public String unSubscribeToTopic(List<String> tokens, String topic) {
        ApiFuture<TopicManagementResponse> response = FirebaseMessaging
                .getInstance()
                .unsubscribeFromTopicAsync(tokens, topic);
        return response.toString();
    }
}
```

특정 Topic을 구독하기 위해 `FirebaseMessaging` 코드를 작성했다.

`FirebaseMessaging`에서 `getInstance()`메서드로 해당 인스턴스를 얻어오고 `subscribeTopicAsync()`를 통해 구독할 토큰과 Topic을 넣어주면 된다.

그럼 여기서 `FirebaseMesaging`이 `getInstance()`할 때 어떤 과정을 거치는지 봐보자.

![image](https://user-images.githubusercontent.com/43977617/133566864-abf74486-7f10-4bbf-ae4b-5193a507dbfa.png)

다음과 같이 `FirebaseApp`의 인스턴스를 가져온 후 다시 한번 `getInstance()`를 호출한다.

우리는 여기있는 `FirebaseApp`을 초기화시켜줘야 한다. `FirebaseApp`은 다음과 같이 초기화시켜줬다.

</br >

## FirebaseConfig 작성

```java
@Configuration
public class FirebaseConfig {
    
    @Bean
    public FirebaseApp firebaseApp() throws IOException {
        String firebaseConfigPath = "firebase/firebase_service_key.json";
        FirebaseOptions options = new FirebaseOptions.Builder()
                .setCredentials(GoogleCredentials
                        .fromStream(new ClassPathResource(firebaseConfigPath).getInputStream())
                        .createScoped(Arrays.asList("https://www.googleapis.com/auth/cloud-platform")))
                .build();

        return FirebaseApp.initializeApp(options);
    }
}
```

`FirebaseApp`을 초기화할 때 `FirebaseOptions`을 넣어줘야 한다. `FirebaseOptions`을 생성할 때 필요한 `GoogleCredentials`에 `Firebase`을 설정한 후 다운로드한 JSON 파일을 넣어줬다.

그리고 `createdScoped()`를 통해 서버에서 필요로 하는 권한을 지정해줬다. 권한의 종류는 아래와 같다.

![image](https://user-images.githubusercontent.com/43977617/133070776-43a5f387-69b8-4f43-8d1a-4e7adbcba27d.png)

</br >

자 이제 Topic 구독 및 구독해제에 관한 설정은 끝났다.

`FcmSubscribeService`의 `subscribeToTopic()`메서드와 `unSubscribeToTopic()`메서드를 통해 특정 Topic에 대한 구독과 구독해제를 해줄 수 있다.

마지막으로 메시지 전송을 위한 코드를 작성해보자.

```java
@Slf4j
@Service
public class FirebaseCloudMessageService {

    public void sendMessageBy(String topic, String title, String body) throws FirebaseMessagingException {
        final Message message = Message.builder()
                .setNotification(new Notification(title, body))
                .setTopic(topic)
                .build();
        final String response = FirebaseMessaging.getInstance()
                .send(message);
        log.info(response);
    }
}
```

사용 방법은 `FirebaseCloudMessageService`에 작성한 `sendMessageBy()`메서드를 통해 메시지를 보낼 topic, 메시지 구성을 위한 title, body 파라미터를 넣어주면 된다.


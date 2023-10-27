# 코루틴과 Continuation

다음과 같은 비즈니스 로직이 있다고 가정해보자.

```kotlin
class UserService {
    private val userProfileRepository = UserProfileRepository()
    private val userImageRepository = UserImageRepository()
    
    suspend fun findUser(userId: Long): UserDto {
        // 0단계 - 초기 시작
        println("프로필을 가져오겠습니다")
        val profile = userProfileRepository.findProfile(userId)
        
        // 1단계 - 1차 중단 후 재시작
        println("이미지를 가져오겠습니다.")
        val image = userImageRepository.findImage(profile)
        
        // 2단계 - 2차 중단 후 재시작
        return UserDto(profile, image) 
    }
}

class UserProfileRepository {
    suspend fun findProfile(userId: Long): Profile {
        delay(100L)
        return Profile()
    }
}

class UserImageRespository {
    suspend fun findImage(profile: Profile): Image {
        delay(100L)
        return Image()
    }
}
```

과연 코루틴이 어떻게 함수를 중단하는지 알아보자

```kotlin
interface Continuation {
}


class UserService {
    private val userProfileRepository = UserProfileRepository()
    private val userImageRepository = UserImageRepository()
    
    suspend fun findUser(userId: Long): UserDto {
        val sm = object: Continuation {
            var label = 0
            var profile:Profile? = null
            var image: Image? = null
        }
        
        when (sm.label) {
            0 -> { // 0단계 - 초기 시작
                println("프로필을 가져오겠습니다")
                sm.label = 1
                val profile = userProfileRepository.findProfile(userId)
                sm.profile = profile
            }
            1 -> { // 1단계 - 1차 중단 후 재시작
                println("이미지를 가져오겠습니다.")
                sm.labe = 2
                val image = userImageRepository.findImage(sm.profile!!)
                sm.image = image
            }
            2 -> { // 2단계 - 2차 중단 후 재시작
                return UserDto(sm.profile!!, sm.image!!) 
            }
    }
}
```

라벨을 이용해서 특정 로직을 수행할 수 있도록 변경했다.

핵심은 Continuation을 전달하는 것이다.

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

findUser의 Continuation(여기서는 label = 0)을 이후에 findProfile에도 전달하고,

findProfile이 종료되면 다시 Continuation(label = 1)을 findUser에 전달한다.

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

이제 다시 코드를 개선해보자.

```kotlin
class UserProfileRepository {
    suspend fun findProfile(userId: Long, continuation: Continuation): Profile {
        delay(100L)
        continuation.resumeWith(Profile())
    }
}

class UserImageRespository {
    suspend fun findImage(profile: Profile, continuation: Continuation): Image {
        delay(100L)
        continuation.resumeWith(Image())
    }
}

interface Continuation {
    suspend fun resumeWith(data: Any?)
}

class UserService {
    private val userProfileRepository = UserProfileRepository()
    private val userImageRepository = UserImageRepository()
    
    private abstract class FindUserContinuation() : Continuation {
        var label = 0
        var profile:Profile? = null
        var image: Image? = null
    }
    
    suspend fun findUser(userId: Long, continuation: Continuation?): UserDto {
        val sm = continuation as? FindUserContinuation ?: object: FindUserContinuation {
            var label = 0
            var profile:Profile? = null
            var image: Image? = null
            
            override suspend fun resumeWith(data: Any?) {
                when (label) {
                0 -> {
                    profile = data as Profile
                    label = 1
                }
                1 -> {
                    image = data as Image
                    label = 2
                }
                findUser(userId, this)
            }
        }
        
        when (sm.label) {
            0 -> { // 0단계 - 초기 시작
                println("프로필을 가져오겠습니다.")
                sm.label = 1
                val profile = userProfileRepository.findProfile(userId,sm)
                sm.profile = profile
            }
            1 -> { // 1단계 - 1차 중단 후 재시작
                println("이미지를 가져오겠습니다.")
                sm.labe = 2
                val image = userImageRepository.findImage(sm.profile!!,sm)
                sm.image = image
            }
        return UserDto(sm.profile!!,sm.image!!)
    }
}

suspend fun main() {
    val service = UserService()
    println(service.findUser(1L, null)
}

// 프로필을 가져오겠습니다.
// 이미지를 가져오겠습니다.
// UserDto(profile=coroutine.Profile@7fde3e1e, image = coroutine.Image@23540d91)
```

위처럼 하면, 콜백 형식으로 이루어진다.

이처럼 코루틴이 어떻게 suspend되는지 확인할 수 있었고, 실제 코루틴 라이브러리를 살펴보면,

```kotlin
public interface Continuation<in T> {
    public val context: CoroutineContext
    
    public fun resumeWith(result: Result<T>)
}
```


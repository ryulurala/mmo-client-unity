---
title: "About Transform"
category: Unity-Framework
tags:
  [
    unity,
    transform,
    gameObject,
    position,
    rotation,
    scale,
    eulerAngls,
    quaternion,
    inputManager,
  ]
date: "2021-02-12"
---

## Transform

### Player

- 모든 Player, 게임 물체는 `GameObject`고 이를 제어해 사용한다.
- `Transform` 컴포넌트로 `GameObject`의 `Position`(위치), `Rotation`(회전), `Scale`(크기)를 제어한다.

- In code
  - |         `GameObject`          |            `Transform`             |
    | :---------------------------: | :--------------------------------: |
    | `transform.gameObject`로 접근 |  `transform.position`로 위치 조절  |
    |                               |  `transform.rotation`로 회전 조절  |
    |                               | `transform.localScale`로 크기 조절 |

### Position

- `transform.position`으로 Object를 움직인다.
- 3차원 좌표 `Vector3`를 사용한다.
- `position` vs `localPosition`

  - | transform.postion | transform.localPosition |
    | :---------------: | :---------------------: |
    |   World 좌표계    |      Local 좌표계       |
    |    World 기준     |    해당 Object 기준     |

- World 좌표계 기준으로 움직이기

  - ![Preview](/uploads/transfrom/move-world-base.gif)

  ```cs
  using System.Collections;
  using System.Collections.Generic;
  using UnityEngine;

  public class PlayerController : MonoBehaviour
  {
      // [SerializeField] 를 이용하면 Editor에서 Value 조절 가능
      [SerializeField] float _speed = 10.0f;

      // 1 frame마다 호출된다, 보통 60 Frame = 1 sec
      void Update()
      {
          if (Input.GetKey(KeyCode.W)) // Input: Key 입력과 연관된 Class
              transform.position += Vector3.forward * Time.deltaTime * _speed;
              // Vector3.forword=(0.0f, 0.0f, 1.0f): World 좌표계 기준
          if (Input.GetKey(KeyCode.S))
              transform.position += Vector3.back * Time.deltaTime * _speed;
              // Vector3.back=(0.0f, 0.0f, -1.0f)
          if (Input.GetKey(KeyCode.A))
              transform.position += Vector3.left * Time.deltaTime * _speed;
              // Vector3.left=(-1.0f, 0.0f, 0.0f)
          if (Input.GetKey(KeyCode.D))
              transform.position += Vector3.right * Time.deltaTime * _speed;
              // Vector3.right=(1.0f, 0.0f, 0.0f)
      }
  }
  ```

#### Input Class

- Key 입력에 대한 Class.
- |    `GetKey(KeyCode k)`    |    `GetKeyDown(KeyCode k)`    |  `GetKeyUp(KeyCode k)`   |
  | :-----------------------: | :---------------------------: | :----------------------: |
  | 입력되는 중="ing"(`N 번`) | 입력이 최초로 눌릴 때(`1 번`) | 입력이 끝났을 때(`1 번`) |

#### Time.deltaTime

- 1 Frame당 실행하는 시간.
- (현재 프레임 시간 - 지난 프레임 시간)
- 곱하는 이유

  > 시간과 비례해서 증가  
  > 만약, 100 FPS 컴퓨터와 10 FPS 컴퓨터는 1초당 이동 거리가 다를 것이다.  
  > 따라서 걸린 시간(Time.deltaTime)을 곱해주면 동일한 이동거리의 결과를 보여준다.

#### TransformDirection()

- 오브젝트 기준 좌표계 -> 월드 기준 좌표계
- Local 좌표계-> World 좌표계

  > `InverseTransformDirection()`: World -> Local 좌표

- `TransformDirection()`을 이용한 오브젝트 기준 움직이기

  - ![Preview](/uploads/transfrom/move-object-base1.gif)

  ```cs
  void Update()
  {
      if (Input.GetKey(KeyCode.W))
          transform.position += transform.TransformDirection(Vector3.forward * Time.deltaTime * _speed);
          // 오브젝트 기준 좌표계 -> 월드 기준 좌표계
      if (Input.GetKey(KeyCode.S))
          transform.position += transform.TransformDirection(Vector3.back * Time.deltaTime * _speed);
      if (Input.GetKey(KeyCode.A))
          transform.position += transform.TransformDirection(Vector3.left * Time.deltaTime * _speed);
      if (Input.GetKey(KeyCode.D))
          transform.position += transform.TransformDirection(Vector3.right * Time.deltaTime * _speed);
  }
  ```

#### tramsform.Translate()

- Local 좌표계로 이동해줌.

- 오브젝트 기준으로 움직이기

  - ![Preview](/uploads/transfrom/move-object-base2.gif)

  ```cs
  using System.Collections;
  using System.Collections.Generic;
  using UnityEngine;

  public class PlayerController : MonoBehaviour
  {
      [SerializeField] float _speed = 10.0f;

      void Update()
      {
          if (Input.GetKey(KeyCode.W))
              transform.Translate(Vector3.forward * Time.deltaTime * _speed);
              // 오브젝트 기준으로 Position을 변경한다.
          if (Input.GetKey(KeyCode.S))
              transform.Translate(Vector3.back * Time.deltaTime * _speed);
          if (Input.GetKey(KeyCode.A))
              transform.Translate(Vector3.left * Time.deltaTime * _speed);
          if (Input.GetKey(KeyCode.D))
              transform.Translate(Vector3.right * Time.deltaTime * _speed);
      }
  }

  ```

### Vector3

- `Transform`은 `Vector3`를 사용한다.
- 멤버변수는 `float x`, `float y`, `float z` 로 구성됨.
- `Vector3` 용도

  1. 위치 벡터
     > 좌표로 위치 정의
  2. 방향 벡터

     > (다음 위치 - 현재 위치)  
     > "거리(크기)", "방향"을 알아냄

     - transform.position.magnitude
       > Vector의 크기 반환  
       > 피타고라스 정리 이용(`x*x + y*y + z*z`)
     - transform.position.normalized
       > Vector의 방향 단위 벡터: 크기(`Magnitude`)가 1  
       > 각 `x`, `y`, `z`를 `magnitude`로 나눠준 값

### Rotation

- `transform.rotation(Quaternion)`
  > `eulerAngle`보다 사용 권장
- `transform.eulerAngles(Vector3)`

  > 절댓값으로 넣어줘야 함  
  > **`+=(Increment)` 사용 X**  
  > **공식 문서**: `Increment`를 사용할 경우 360도가 넘어가 오작동 발생

- |           `EulerAngles`           |           `Quaternion`            |
  | :-------------------------------: | :-------------------------------: |
  |              x, y, z              |        x, y, z, w: 사원수         |
  |           Vector3 사용            |      Vector3를 변환해서 사용      |
  |    `Gimbal Lock` 문제 **발생**    |    `Gimbal Lock` 문제 **해결**    |
  | `transform.eulerAngles = Vector3` | `transform.rotation = Quaternion` |

- `Gimbal Lock` 문제
  > 각 축의 연관성으로 회전을 계속하면 축들이 겹쳐 회전이 먹통이 되는 문제  
  > `Quaternion` 으로 해결

#### Y축을 기준으로 회전 예제

- ![Preview](/uploads/transfrom/rotate-y.gif)

1. `transform.eulerAngles`

   > 절대적으로 `Vector3`를 대입한다.  
   > 상태 회전 변수 필요

   ```cs
   float yAngle = 0.0f;
   void Update()
   {
     yAngle += Time.deltaTime * 100.0f;   // 상태 회전 변수 갱신
     transform.eulerAngles = new Vector3(0.0f, yAngle, 0.0f);  // 절대적으로 대입
     // transform.eulerAngles += yAngle;    // 사용 X
   }
   ```

2. `transform.Rotate(Vector3)`

   > `Vector3` 대입  
   > 상대적으로 회전: 현재 회전값 기준

   ```cs
   void Update()
   {
     // 현재 회전 상태 기준으로 회전(상대적)
     transform.Rotate(new Vector3(0.0f, Time.deltaTime * 100.0f, 0.0f));
   }

   ```

3. `Quaternion.Euler(Vector3)`

   ```cs
   float yAngle = 0.0f;
   void Update()
   {
     yAngle += Time.deltaTime * 100.0f;   // 상태 회전 변수 갱신

     // Euler -> Quaternion 변환
     transform.rotation = Quaternion.Euler(new Vector3(0.0f, _yAngle, 0.0f));
   }
   ```

#### 특정 방향 바라보기 예제

- `Quaternion.LookRotation(Vector3)`

  > 원하는 방향을 바라보게 함

  - ![Preview](/uploads/transfrom/look.gif)

  ```cs
  void Update()
  {
      // 월드좌표 기준 바라보게 함
      transform.rotation = Quaternion.LookRotation(Vector3.forward);
  }
  ```

- `Quaternion.Slerp(Vector3, Vector3, float)`

  > 부드럽게 바라보게 함.  
  > Slerp(현재 방향, 목표 방향, 비율)

  - ![Preview](/uploads/transfrom/look-slerp.gif)

  ```cs
  void Update()
  {
      // 현재 방향에서 월드 기준 forward(앞)으로 부드럽게 바라보게 함.
      transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(Vector3.forward), 0.3f);
  }
  ```

### Input-Manager

- `Listener` 역할
- 사용자 입력을 체크하고 이벤트로 실행한다.

1. `InputManager` Class 작성

   ```cs
   public class InputManager   // MonoBehaviour를 상속 X
   {
       public Action KeyAction = null;

       public void OnUpdate()  // Listener
       {
           if (!Input.anyKey) return;  // 아무 키가 입력되지 않으면

           if (KeyAction != null)  // 이벤트가 등록돼있는지 확인
               KeyAction.Invoke(); // 모두 알려주고 실행하도록 함.
       }
   }
   ```

2. `Manager`에서 관리

   ```cs
   public class Manager : MonoBehaviour
   {
       static Manager instance;    // 유일성 보장
       static Manager Instance { get { if (instance == null) init(); return instance; } }    // get, Property 이용

       InputManager input = new InputManager();
       public static InputManager Input { get { return Instance.input; } }

       void Update()
       {
           input.OnUpdate();   // 이벤트를 체크
       }
   }
   ```

3. `PlayerController`에서 등록

   ```cs
   public class PlayerController : MonoBehaviour
   {
       [SerializeField] float _speed = 10.0f;

       void Start()
       {
           // 리스너 등록
           Manager.Input.KeyAction -= OnKeyboard;  // 두 번 등록 방지
           Manager.Input.KeyAction += OnKeyboard;
       }

       void OnKeyboard()
       {
           if (Input.GetKey(KeyCode.W))
           {
               transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(Vector3.forward), 0.3f);
               transform.position += Vector3.forward * Time.deltaTime * _speed;
           }
           if (Input.GetKey(KeyCode.S))
           {
               transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(Vector3.back), 0.3f);
               transform.position += Vector3.back * Time.deltaTime * _speed;
           }
           if (Input.GetKey(KeyCode.A))
           {
               transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(Vector3.left), 0.3f);
               transform.position += Vector3.left * Time.deltaTime * _speed;
           }
           if (Input.GetKey(KeyCode.D))
           {
               transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(Vector3.right), 0.3f);
               transform.position += Vector3.right * Time.deltaTime * _speed;
           }
       }
   }
   ```

---

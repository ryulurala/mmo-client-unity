---
title: "Unity Skills"
category: Unity-Framework
tags:
  [
    unity,
    skills,
    terrain,
    light,
    navigation,
    nav-mesh-agent,
    stat,
    mouse-cursor,
  ]
date: "2021-03-01"
---

## Unity Skills

### Terrain

- Unity Engine에서 지형을 Brush Tool로 만들 수 있다.
- 자동 `LOD`(`Level Of Detail`) 기능
  > `Level Of Detail`: Object 모습을 카메라와의 거리를 기준으로 교체하면서 시스템 부하를 줄이기 위한 기능
- ![terrain](/uploads/skills/terrain.png)

### Light

- Game에서 조명을 담당

  - Directional Light
    > 무한히 멀리 있는 조명  
    > Scene에 모든 것에 영향을 준다.
  - Point Light
    > 하나의 Point 지점에서 일정 범위 부분까지 모든 방향으로 균등하게 비친다.
  - Spot Light
    > 원뿔 형태로 원뿔 안의 Object들만 영향을 미침

- Mode

  - ![light-mode](/uploads/skills/light-mode.png)
  - Realtime
    > 실시간으로 해당 조명으로 감지되는 Object를 계산한다.  
    > ex) 그림자 등
  - Mixed
    > 실시간과 미리 구운 Light map을 적절히 섞어 사용  
    > Light Window에서 여러 Sub Mode 설정
  - Baked
    > Object의 Static 옵션이 체크된 것을 기반으로 Light map을 Generate.

- Auto Generate
  > Unity Editor에서 Light Map을 자동으로 생성할 것인지.  
  > Light와 그 범위에 비춰진 Object가 많을 경우, 시스템 부하가 심히다.
  - |                          Auto Generate                          |                    Static Object                    |
    | :-------------------------------------------------------------: | :-------------------------------------------------: |
    | ![light-auto-generate](/uploads/skills/light-auto-generate.png) | ![static-object](/uploads/skills/static-object.png) |

### Navigation

- 길찾기 AI를 만들 수 있는 기능

1. Navigation Path를 Bake
2. 움직일 Object에 Nav Mesh Agent 컴포넌트 연결

   |                1. `Window`-`AI`-`Navigation`                |                    2. Nav Mesh Agent                     |
   | :---------------------------------------------------------: | :------------------------------------------------------: |
   | ![navigation-window](/uploads/skills/navigation-window.png) | ![nav-mesh-agent](/uploads/skills/navigation-window.png) |

3. Script 작성

   ```cs
   // 컴포넌트 불러오기
   NavMeshAgent nma = gameObject.GetOrAddComponent<NavMeshAgent>();

   // Agent 움직이기
   nma.Move(dir.normalized * moveDist);
   ```

### Stat

- 스탯 정보

```cs
public class Stat : MonoBehaviour
{
    [SerializeField] protected int _level;
    [SerializeField] protected int _hp;
    [SerializeField] protected int _maxHp;
    [SerializeField] protected int _attack;
    [SerializeField] protected int _defence;
    [SerializeField] protected float _moveSpeed;

    public int Level { get { return _level; } set { _level = value; } }
    public int HP { get { return _hp; } set { _hp = value; } }
    public int MaxHp { get { return _maxHp; } set { _maxHp = value; } }
    public int Attack { get { return _attack; } set { _attack = value; } }
    public int Defence { get { return _defence; } set { _defence = value; } }
    public float MoveSpeed { get { return _moveSpeed; } set { _moveSpeed = value; } }

    void Start()
    {
        // 나중에는 Data Sheet(.json)로 초기화
        _level = 1;
        _hp = 100;
        _maxHp = 100;
        _attack = 10;
        _defence = 5;
        _moveSpeed = 5.0f;
    }
}

```

#### PlayerStat

- 상속 매커니즘을 이용.

```cs
public class PlayerStat : Stat
{
    [SerializeField] int _exp;
    [SerializeField] int _gold;

    public int Exp { get { return _exp; } set { _exp = value; } }
    public int Gold { get { return _gold; } set { _gold = value; } }

    void Start()
    {
        // 나중에는 Data Sheet(.json)로 초기화
        _level = 1;
        _hp = 100;
        _maxHp = 100;
        _attack = 10;
        _defence = 5;
        _moveSpeed = 10.0f;
        _exp = 0;
        _gold = 0;
    }
}
```

### Mouse Cursor

- 필요에 따라 마우스 커서 변경

1. Cursor Texture 설정

   - |                        Texture type 설정                        |              Default Cursor Texture 설정              |
     | :-------------------------------------------------------------: | :---------------------------------------------------: |
     | ![cursor-texture-type](/uploads/skills/cursor-texture-type.png) | ![default-cursor](/uploads/skills/default-cursor.png) |

2. Script 작성

   - `Ray`, `Texture2D` 이용
   - `SetCursor(Texture2D, Vector3 hotSpot, CursorMode)`
     > `hotSpot`: 마우스 커서 끝이 가리키는 곳
   - |                     `hotSpot` 지점                      |
     | :-----------------------------------------------------: |
     | ![cursor-hotspot](../uploads/skills/cursor-hotspot.png) |

   ```cs
   // Cursor Texture
   Texture2D _attackIcon;
   Texture2D _handIcon;

   // 매번 Cursor가 바뀌는 것을 방지
   CursorType _cursorType = CursorType.None;

   void Start()
   {
       // Cursor Texture Load
       _attackIcon = GameManager.Resource.Load<Texture2D>("Textures/Cursors/Attack");
       _handIcon = GameManager.Resource.Load<Texture2D>("Textures/Cursors/Hand");
   }

   enum CursorType
   {
       None,
       Hand,
       Attack,
   }

   void UpdateMouseCursor()
   {
       Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
       RaycastHit hit;

       if (Physics.Raycast(ray, out hit, 100f, LayerMask.GetMask("Ground") | LayerMask.GetMask("Monster")))
       {
           // Ground, Monster의 Collider와 Ray가 부딪혔을 경우
           if (hit.collider.gameObject.layer == LayerMask.NameToLayer("Monster"))
           {
               // Monster를 가리켰을 경우
               if (_cursorType != CursorType.Attack)
               {
                   // Attack Cursor가 아닐 경우만
                   Cursor.SetCursor(_attackIcon, new Vector2(_attackIcon.width / 5, 0), CursorMode.Auto);
                   _cursorType = CursorType.Attack;
               }
           }
           else
           {
               // 그 외를 가리켰을 경우
               if (_cursorType != CursorType.Hand)
               {
                   // Hand Cursor가 아닐 경우만
                   Cursor.SetCursor(_handIcon, new Vector2(_handIcon.width / 3, 0), CursorMode.Auto);
                   _cursorType = CursorType.Hand;
               }
           }
       }
   }
   ```

---